---
title: Don't Microservice Every Integration
description: A practical take on when adapters are better than splitting every integration into its own service.
date: 2026-07-27
tags:
  - designpatterns
  - architecture
---

A month and a half ago, my team had to answer a deceptively simple question:
**Should every carrier integration be its own microservice, or should all of them live behind a single Adapter interface?**
At first glance the answer looked obvious. After all, we’d built dozens of marketplace integrations before.
We were wrong.

We are on the road to build our first Transport Management System and we were working on its carrier integrator module at that time. The idea was to create a microservice that will act as a single touch point for multi-carrier workflows, carrier allocation rules for shipments as well as take care of miscellaneous dashboards. This seemed like a classic problem we face at Increff. 

At Increff, we’ve built dozens of marketplace integrations over the years. Every marketplace has slightly different APIs, authentication flows and edge cases, so we’ve traditionally isolated each integration into its own microservice. The reasoning is simple: every external partner has its own quirks, and keeping partner-specific logic outside the core system makes the rest of the architecture much cleaner.
Naturally, our first instinct was to follow the same approach.
Then we paused.
This wasn’t just another integration. It was a brand new product. If there was ever a good time to question our conventions, this was it.
## The Problem Looked Simpler Than It Really Was

Generally these carrier partners have very similar flows:
1. Forward and Return order create
2. AWB generation
3. Order tracking
4. Estimated Delivery Date Calculation

These are not as many different flows that would require different individual microservices. Though after diving deep into the API docs, we found that the integrations are not that simple as they seem to be. The processes are identical but the nature of their working are quite different.
Some carriers used OAuth, while others relied on static API keys or token-based authentication with periodic refreshes. Some exposed webhook-based tracking events, while others expected us to poll their APIs periodically because webhook support simply didn’t exist.
Bulk AWB generation also varied significantly. Some carriers supported pooled AWBs, others required batching, while a few generated labels only during shipment creation.
Thus writing adaptors would require a very intelligent and precise approach, as even a single mess up will disorient and destroy the whole structure of the code. This would require standardisation wherever it is possible (like in the authentication, token management and Awb pool management), proper error handling and heavy use of strategy and factory pattern along with the initial adaptor implementation. 

## **The Obvious Solution: One Microservice Per Carrier**

This was our default approach.
Every carrier would have its own dedicated service.
```
Shipment Service
       │
 ┌─────┴─────┐
 │           │
Delhivery   XpressBees
 Service      Service
```
The above points made us to lean towards our old approach of micro-servicing the integrations.
There are obvious benefits.
- Every carrier evolves independently.
- Teams can deploy integrations separately.
- Authentication, dependencies and SDKs remain isolated.
- A production issue in one carrier doesn’t directly affect another.
More importantly, this was an architecture we already understood well.
But experience had also taught us its downsides.
As the number of carriers grows, so does the operational overhead.
N deployments, N on-call surfaces, and cross-service calls for what could be a single factory lookup — pure overhead for no isolation benefit. Each service needs its own compute allocation, even if idle most of the time. These are just a few problems out of many that we experienced over the years. 
The isolation was nice.
The operational cost wasn’t.

## **Could Adapters Be Enough?**

Looking at both the approaches, we figured that we must give the first approach a try, as it is something new and currently not implemented in any of our products in our organisation. 
We started with identifying the base adaptor (the interface), what methods could we have in it and came up witht he following structure.
```java
public interface CarrierAdapter {  
  
    String carrierCode();  
  
    ShippingLabelResult createOrder(Long clientId, SpsOrderForm form) throws ApiException;  
  
    ShippingLabelResult createMpsOrder(Long clientId, MpsOrderForm form) throws ApiException;  
  
    ShippingLabelResult getOrder(Long clientId, String referenceNumber) throws ApiException;  
  
    ShippingLabelResult createReturnOrder(Long clientId, ReturnOrderForm form) throws ApiException;  
  
    List<SlaResult> getExpectedDeliveryDate(Long clientId, List<ExpectedDeliveryForm> forms) throws ApiException;  
  
    CancellationResult cancelOrder(Long clientId, CancelOrderForm form) throws ApiException;  
}
```

This gave us a baseline to work on. 
One adapter per carrier — translates the common contract into that carrier's specific API.
Something like this
```java
class DelhiveryAdapter implements CarrierAdapter {
      public String carrierCode() { return "DELHIVERY"; }

      public ShippingLabel createOrder(Order order) {
          DelhiveryRequest request = toDelhiveryRequest(order);
          DelhiveryResponse response = delhiveryClient.createOrder(request);
          return toShippingLabel(response);
      }                                   

      public OrderStatus trackOrder(String referenceNumber) {
          DelhiveryResponse response = delhiveryClient.track(referenceNumber);
          return toOrderStatus(response); 
      }
  }

```

```java
 class XpressBeesAdapter implements CarrierAdapter {
      public String carrierCode() { return "XPRESSBEES"; }
      // same shape, XpressBees-specific translation
      ...
  }

```

 The factory: picks the right adapter at runtime
 ```java
 class CarrierAdapterFactory {
      private final Map<String, CarrierAdapter> adapters;

      CarrierAdapterFactory(List<CarrierAdapter> allAdapters) {
          adapters = allAdapters.stream()
              .collect(toMap(CarrierAdapter::carrierCode, a -> a));
      }

      CarrierAdapter get(String carrierCode) {
          return adapters.get(carrierCode);
      }                                   
  }

 ```

The caller never branches on carrier at all
```java
class ShipmentService {                 
      ShippingLabel createOrder(String carrierCode, Order order) {
          CarrierAdapter adapter = factory.get(carrierCode);
          return adapter.createOrder(order);
      }
  }

```

Adding a new carrier implementation mean  writing one new CarrierAdapter class — ShipmentService and the factory don't change at all.
No `if-else`.
No giant switch statements.
No carrier-specific logic leaking into the business layer.

## **The Hard Part Wasn’t the Adapter**

Design patterns are the easy part.
The real challenge was deciding **what actually belonged inside the abstraction.**
Should authentication be standardized?
Should token management be shared?
Could AWB pooling be generalized?
What about retry policies?
How should errors be normalized when every carrier returns a completely different response structure?
We spent considerably more time answering these questions than writing the adapters themselves.

The success of the design depended less on the Adapter pattern and more on drawing the abstraction boundaries correctly.

## **The Trade-off We Accepted**

This architecture isn’t perfect. Because all adapters live in one service, a dependency bump or library issue for one carrier's client can force a redeploy — and potential regression risk — for every carrier, even ones untouched by the change.

## **Designing for an Exit Strategy**

Most of these are exactly the pressure points that would eventually justify carving one specific carrier out into its own service —not because "microservices are better," but because that carrier's needs (authmodel, scale, release cadence) genuinely stopped fitting the shared abstraction.
When that day comes, extracting it into its own microservice shouldn’t require months of refactoring.
With that in mind, we intentionally organized the project so that shared DTOs, clients, request models and common utilities lived in separate packages from the beginning.
If we ever decide to split a carrier into its own service, most of the groundwork is already done.

The adapter simply moves out.

## **Final Thoughts**

This project wasn’t really about implementing the Adapter pattern.
It was about resisting the temptation to blindly follow an architecture simply because it had worked before.
Microservices weren’t the wrong answer.Adapters weren’t the universally correct one either.
For the current scale of the product, a single service with well-defined adapters gives us simpler deployments, lower operational overhead, and cleaner business logic while preserving the flexibility to evolve later.
Maybe a year from now we’ll extract a few carriers into dedicated services.
Maybe we won’t.
Either way, the goal isn’t to predict the future perfectly.
It’s to make sure the future is easy to adapt to.
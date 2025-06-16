
Simply put, a Servlet is a class that handles requests, processes them and reply back with a response.
Servlets are under the control of another Java application called a ***Servlet Container.*** When an application running in a web server receives a request, the Server hands the request to the Servlet Container – which in turn passes it to the target Servlet.

Maven Dependency

```
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.1.0</version>
</dependency>

```

### Servlet Life Cycle

- init()
    - The *init* method is designed to be called only once. If an instance of the servlet does not exist, the web container:
        1. Loads the servlet class
        2. Creates an instance of the servlet class
        3. Initializes it by calling the *init* method
- service()
    - This method is only called after the servlet’s *init()* method has completed successfully_._
        
        The Container calls the *service()* method to handle requests coming from the client, interprets the HTTP request type (*GET*, *POST*, *PUT*, *DELETE*, etc.) and calls *doGet*, *doPost*, *doPut*, *doDelete*, etc. methods as appropriate.
        
- destroy()
    - Called by the Servlet Container to take the Servlet out of service.
        
        This method is only called once all threads within the servlet’s *service* method have exited or after a timeout period has passed. After the container calls this method, it will not call the *service* method again on the Servlet.
        

---

To use them, servlets need to be [registered](https://www.baeldung.com/register-servlet) first so that a container, either JEE or Spring-based, can pick them up at start-up. In the beginning, the container instantiates a servlet by calling its *init()* method.

Once its initialization is complete, the servlet is ready to accept incoming requests. Subsequently, the container directs these requests for processing in the servlet’s *service()* method. After that, it further delegates the request to the appropriate method such as *doGet()* or *doPost()* based on the HTTP request type.

## Containers

container, such as [Apache Tomcat](https://www.baeldung.com/tomcat) or [Jetty](https://www.baeldung.com/deploy-to-jetty). At start-up, it creates an object of [*ServletContext*](https://www.baeldung.com/context-servlet-initialization-param). The job of the *ServletContext* is to function as the server or container’s memory and remember all the servlets, filters, and listeners associated with the web application, as described in its *web.xml* or equivalent annotations. Until we stop or terminate the container, *ServletContext* stays with it.
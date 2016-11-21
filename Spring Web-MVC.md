
### Spring Web MVC Framework

 -   The *Model* encapsulates the application data and in general they will consist of *POJO*.
 -  The *View* is responsible for rendering the model data and in general it generates HTML output that the client's browser can interpret.
 - The *Controller* is responsible for processing user requests and building appropriate model and passes it to the view for rendering.

#### The DispatcherServlet

The Spring Web model-view-controller (MVC) framework is designed around a DispatcherServlet that handles all the HTTP requests and responses. 
Following is the sequence of events corresponding to an incoming HTTP request to DispatcherServlet:


-   After receiving an HTTP request, DispatcherServlet consults the HandlerMapping to call the appropriate Controller.
-  The Controller takes the request and calls the appropriate service methods based on used GET or POST method. The service method will set model data based on defined business logic and returns view name to the DispatcherServlet.
- The DispatcherServlet will take help from ViewResolver to pickup the defined view for the request.

-   Once view is finalized, The DispatcherServlet passes the model data to the view which is finally rendered on the browser.



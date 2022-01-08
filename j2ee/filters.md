If you want to intercept request before servlet can process, then use **Filters**.

Filters are Java components - very similar to servlets -  that you can use to intercept and process requests before they are sent to the servlet, or to process response after the servlet has completed, but before the response goes back to the client.

The Container decides when to invoke your filters based on declarations in the DD. In the DD, the deployer maps which filters will be called for which request URL patterns. So it's the deployer, not the programmer, who decides which subset of requests or responses should be processed by which filters.

## Things to do with Filters

Request filters can:
- perform security checks
- reformat request headers or bodies
- audit or log requests

Response filters can:
- compress the response stream
- append or alter the response stream
- create a different response altogether

## Filters are modular, and configurable in the DD

Filters can be chained together, to run one after the other. Filters are designed to be totally self-contained. A filter doesn't care which filters ran before it did. and it doesn't care which one will run next. The DD controls the order in which filters run.

You map filters with servlet

## Three ways filters are like servlets
### The Container knows their API
Filters have their own API. When a Java class implements the Filter interface, its striking a deal with the Container, and it goes from being a plain old class to being an official J2EE Filter. Other members of the filter API allow filters to get access to the ServletContext, and to be linked to other filters.

### The Container manages their lifecycle
Like servlets, they have init() and destroy() methods. Similar to a servlet's doGet()/doPost() method, filters have a doFilter() method.

### They're declared in the DD
A web app can have lots of filters, and a given request can cause more than one filter to execute. The DD is the place where you declare which filters will run in response to which requests, and in which order.

```java
package com.dude;

import java.io.*;
import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;

public class BeerRequestFilter implements Filter {

    private FilterConfig fc;

    public void init(FilterConfig config) throws ServletException {
        this.fc = config;
    }

    public void doFilter(
        ServletRequest req,
        ServletResponse res,
        FilterChain chain
    ) throws    ServletException,
                IOException {

        HttpServletRequest httpReq = (HttpServletRequest) req;
        String name = httpReq.getRemoteUser();

        if(name != null) {
            fc.getServletContext().log("User "+name+" is updating");
        }

        chain.doFilter(req, res);
    }

    public void destroy() {
        // do cleanup stuff
    }

}
```

`chain.doFilter` passes on to container.

## Declaring and ordering filters

When you configure filters in the DD, you'll usually do three things:
- Declare your filter
```xml
<filter>
    <filter-name>BeerRequest</filter-name>
    <filter-class>com.dude.BeerRequestFilter</filter-class>

    <init-param>
    .....
    </init-param>
</filter>
```
- Declaring a filter mapping to a URL pattern
```xml
<filter-mapping>
    <filter-name>BeerRequest</filter-name>
    <url-pattern>*.do</url-pattern>
</filter-mapping>
```
- Declaring a filter mapping to a servlet name
```xml
<filter-mapping>
    <filter-name>BeerRequest</filter-name>
    <servlet-name>AdviceServlet</servlet-name>
</filter-mapping>
```

## Declaring a filter mapping for request-dispatched web resources
```xml
<filter-mapping>
    <filter-name>MonitorFilter</filter-name>
    <url-pattern>*.do</url-pattern>
    <dispatcher>REQUEST</dispatcher>
    <dispatcher>INCLUDE</dispatcher>
    <dispatcher>FORWARD</dispatcher>
    <dispatcher>ERROR</dispatcher>
</filter-mapping>
```

- A `REQUEST` value activates the filter for client requests. If no `<dispatcher>` element is present, REQUEST is the default.
- A `FORWARD` value activates the filter for request dispatching from a forward() called.
- An `INCLUDE` value activates the filter for request dispatching from a include() called.
- An `ERROR` value activates the filter for resources called by the error handler.


## Custom Response

The Container already implements the HttpServletResponse interface; that's what you get in the doFilter() and service() methods. But to get this compression filter working, we have to make our own custom implementation of the HttpServletResponse interface and pass that to the servlet via the chain.doFilter() call.

There are Wrapper classes where they implement all the methods needed for the thing you're trying to wrap, delegating all cals to the underlying request or response object. All you need to do is extend oe of the wrappers, and override just the methods you need to do your custom work. Why wrappers and not adapters? They dont just provide an interface implementation, they actually hold a reference to an object of the same interface type to which they delegate method calls.

- ServletRequestWrapper
- HttpServletRequestWrapper
- ServletResponseWrapper
- HttpServletResponseWrapper

## Example of Wrapper
```java
class CompressionResponseWrapper extends HttpServletResponseWrapper {}

class CompressionFilter implements Filter {
    public void init(FilterConfig fc) {}

    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
        CompressionResponseWrapper wrappedResp = new CompressionResponseWrapper(response);

        chain.doFilter(request, wrappedResp);
    }

    public void destroy() {}
}
```
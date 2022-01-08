jaxrs

1. @Produces(MediaType.JSON) for JSON response & @Consumes(MediaType.JSON) for JSON request.

2. Annotate class with `@XmlRootElement` for XML response.

3. `jersey-media-moxy` library converts to json.

4. QueryParam
```java
@GET
public List<Message> getMessages(@QueryParam("year") int year) {}
```

5. HeaderParam
```java
public String getParam(@HeaderParam("Location") String location) {}
```

6. CookieParam
```java
public String getParam(@CookieParam("name") String name) {}
```

7. @Context
public String getParam(@Context UriInfo uriInfo, @Context HttpHeaders httpHeaders) {}

8. @BeanParam
public String getParam(@BeanParam MessageFilterBean anyBeanName) {}


// POJO of parameters
public class MessageFilterBean {
	
	private String page;
	private String length;
	private String totalCount;

}

9. 
return Response.status(Status.CREATED).entity(new Message()).build();

10. WebApplicationException
Response resopnse = Response.status(Status.NOT_FOUND).entity(new Message()).build();

if(someCondition) {	
	throw new WebApplicationException(response);
}

11. There are lot of Custom Exceptions in JAXRS
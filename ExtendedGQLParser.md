# Introduction #

A part of AuDAO runtime libraries is an _Extended GQL Parser_, which dynamically parses GQL string statements and converts them into GAE datastore API calls (GAE datastore API itself does not provide such functionality).

The _extended_ means that the statements are not limited to "SELECT" queries only, but you can use also other SQL commands like "INSERT", "DELETE" and "UPDATE" and also use so called soft conditions, expressions and nested queries.

We do not recommend to use this library in the real application as the way to access GAE's datastore. Instead of that we think that this library can be useful for administrators or can be used by some admin tasks to preprocess, migrate or download data.

Example of extended GQL:
```
DELETE FROM MyKind WHERE id IN (SELECT id FROM MyKindType WHERE name=:1)
```

The full documentation including Javadoc is [here](http://audao.spoledge.com/doc-gae-features.html#gqlext).

# Using the library #

The library is packed in the `audao-runtime-gqlext-*.jar` and has the following dependencies:
  * [Apache Commons Logging Library](http://commons.apache.org/) - `commons-logging*.jar`
  * [ANTLR3](http://www.antlr.org) Parser Generator Runtime Library - antlr-runtime-3.2.jar

**Example** - parses GQL string and executes prepared query:
```
  import com.spoledge.audao.parser.gql.GqlExtDynamic;
  import com.spoledge.audao.parser.gql.PreparedGql;

  GqlExtDynamic gqld = new GqlExtDynamic( DatastoreServiceFactory.getDatastoreService());
  PreparedGql pq = gqld.prepare( "SELECT prop2 FROM MyEntity WHERE prop=:1" );

  for (String val : Arrays.asList( "value1", "value2" )) {
      for (Entity ent : pq.executeQuery( val )) {
          ...
      }
  } 
```


# Extended GQL console - gqlext.jsp #

A simple tool - a JSP page "gqlext.jsp" - you can find in the archive `audao-sources-*.zip` (see the downloads tab).

If you pack this JSP to your appengine web-app, then you can simply execute the extended GQL commands directly in both appengine environments - the development and the production ones:
  1. put gqlext.jsp to your's web application directory (the main war directory)
  1. put commons-logging-1.1.1.jar to your's web application WEB-INF/lib directory
  1. put antlr-runtime-3.2.jar to your's web application WEB-INF/lib directory
  1. put `audao-runtime-gqlext-*.jar` to your's web application WEB-INF/lib directory

To limit acccess to the GQL console, you should add this section to your WEB-INF/web.xml config:
```
  <security-constraint>
    <web-resource-collection>
      <web-resource-name>Admin Area</web-resource-name>
      <url-pattern>/gqlext.jsp</url-pattern>
    </web-resource-collection>
    <auth-constraint>
      <role-name>admin</role-name>
    </auth-constraint>
  </security-constraint>
```

Point your web-browser to the 'gqlext.jsp' page.

**NOTE:**  Unlike the standard GQL consle, the extended GQL console allows you to change data in the datastore. Be carefull !!!
How can one limit what is logged (from a response code perspective)?

 

http://logback.qos.ch/manual/filters.html

 

In logback-access.xml

 
```
<filter class="ch.qos.logback.core.filter.EvaluatorFilter"
                       <evaluator name="rest_eval">
                               <matcher>
                                      <Name>status</Name>
                                      <regex>200|201|204|404</regex>
                                      <caseSensitive>false</caseSensitive>
                               </matcher>

                               <Expression>
                                       !status.matches(Integer.toString(event.getStatusCode()))
                               </Expression>
                       </evaluator>
                       <OnMatch>ACCEPT</OnMatch>
                       <OnMismatch>DENY</OnMismatch>
               </filter>

```

In standalone.xml (Wildfly)

 

https://undertow.io/undertow-docs/undertow-docs-2.0.0/predicates-attributes-handlers.html

 ```
<subsystem xmlns="urn:jboss:domain:undertow:3.1" >
            <buffer-cache name="default"/>
            <server name="default-server">
                <http-listener name="default" socket-binding="http" record-request-start-time="true" redirect-socket="https"/>
                <host name="default-host" alias="localhost">
                    <access-log  use-server-log="true" predicate="not equals('200', %s) and not equals('404', %s)" pattern="%t %h request:&quot;%r&quot; response_code:%s response_time_ms:%D bytes_sent:%b query_string:&quot;%q&quot;"/>
                    <filter-ref name="server-header"/>
                    <filter-ref name="x-powered-by-header"/>
                </host>
            </server>
            <servlet-container name="default">
                <jsp-config/>
                <websockets/>
            </servlet-container>
            <filters>
                <response-header name="server-header" header-name="Server" header-value="JBoss-EAP/7"/>
                <response-header name="x-powered-by-header" header-name="X-Powered-By" header-value="Undertow/1"/>
            </filters>
        </subsystem>
```
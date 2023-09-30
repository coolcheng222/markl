```java
/** 
 * The Web Socket Endpoint represents an object that can handle websocket conversations. 
 * Developers may extend this class in order to implement a programmatic websocket 
 * endpoint. The Endpoint class holds lifecycle methods that may be 
 * overridden to intercept websocket open, error and close events. By implementing 
 * the {@link Endpoint#onOpen(javax.websocket.Session, javax.websocket.EndpointConfig) onOpen} method, the programmatic endpoint gains access to the {@link Session} object, 
 * to which the developer may add {@link MessageHandler} implementations in order to 
 * intercept incoming websocket messages. Each instance 
 * of a websocket endpoint is guaranteed not to be called by more than one thread 
 * at a time per active connection.
 *
 * <p>If deployed as a client endpoint, it will be instantiated once for the 
 * single connection to the server. 
 *
 * <p>When deployed as a server endpoint, the implementation uses the 
 * {@link javax.websocket.server.ServerEndpointConfig.Configurator#getEndpointInstance} 
 * method to obtain the 
 * endpoint instance it will use for each new client connection.  If the developer uses 
 * the default {@link javax.websocket.server.ServerEndpointConfig.Configurator}, 
 * there will be precisely one 
 * endpoint instance per active client connection. Consequently, in this typical 
 * case, when implementing/overriding the methods of Endpoint, the developer is 
 * guaranteed that there will be at most one thread calling each endpoint instance
 * at a time.
 * 
 * <p>If the developer provides a custom {@link javax.websocket.server.ServerEndpointConfig.Configurator}
 * which overrides the default policy for endpoint instance creation, for example, 
 * using a single Endpoint instance for multiple client connections, the developer
 * may need to write code that can execute concurrently.
 *
 * <p>Here is an example of a simple endpoint that echoes any incoming text message back to the sender.
 * <pre><code>
 * public class EchoServer extends Endpoint {
 *
 *     public void onOpen(Session session, EndpointConfig config) {
 *         final RemoteEndpoint remote = session.getBasicRemote();
 *         session.addMessageHandler(String.class, new MessageHandler.Whole&lt;String>() {
 *             public void onMessage(String text) {
 *                 try {
 *                     remote.sendString("Got your message (" + text + "). Thanks !");
 *                 } catch (IOException ioe) {
 *                     // handle send failure here
 *                 }
 *             }
 *         });
 *     }
 *
 * }
 * </code></pre>
 *
 * @author dannycoward
 */
```


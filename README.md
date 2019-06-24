# rest-api-access
Methods to consume produce secured rest serveices

## Consuming a secured API
To consume a secured API, following approaches can be used

by configuring the public certificate in java keystore
consuming programatically
Detailed explanations are below

### Call the secured API via java code
Call s secured API using spring http client without validating server ssl certificate using SSLContext

```java
public void consumeSecuredRESTAPIUsingHttpClient() throws IOException, KeyStoreException, NoSuchAlgorithmException, KeyManagementException {
    SSLContext sslContext = new SSLContextBuilder().loadTrustMaterial(null, (certificate, authType) -> true).build();
    CloseableHttpClient client = HttpClients.custom()
            .setSSLContext(sslContext)
            .setSSLHostnameVerifier(new NoopHostnameVerifier())
            .build();
     
    String urlOverHttps = "https://twitter.com/api/keepalive";
    HttpGet httpGet = new HttpGet(urlOverHttps);
    httpGet.setHeader("Accept", "text/plain");
    HttpResponse response = client.execute(httpGet);
    log.info("entity details :"+response.getEntity().toString());
    log.info("entity content:"+ EntityUtils.toString(response.getEntity()));
    log.info("status code:"+response.getStatusLine().getStatusCode());
}
```

### Call s secured API using spring Rest Template and SSLContext

```java
public void consumeSecuredRESTAPIUsingRestTemplate() throws IOException, KeyStoreException, NoSuchAlgorithmException, KeyManagementException {
    SSLContext sslContext = new SSLContextBuilder().loadTrustMaterial(null, (certificate, authType) -> true).build();
    CloseableHttpClient httpClient = HttpClients.custom()
            .setSSLContext(sslContext)
            .setSSLHostnameVerifier(new NoopHostnameVerifier())
            .build();
 
    HttpComponentsClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory();
    requestFactory.setHttpClient(httpClient);
 
    String urlOverHttps = "https://twitter.com/api/keepalive";
    ResponseEntity<String> responseEntity = new RestTemplate(requestFactory).exchange(urlOverHttps, HttpMethod.GET, null, String.class);
    log.info("resttemplate Status :"+responseEntity.getStatusCode());
    log.info("resttemplate body :"+responseEntity.getBody());
}
```


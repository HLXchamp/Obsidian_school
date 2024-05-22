### 介绍

![[Pasted image 20240518214800.png]]

### 入门案例

```java
@SpringBootTest  
public class HttpClientTest {  
  
    /**  
     * 测试通过httpclient发送GET方式的请求  
     */  
    @Test  
    public void testGET() throws Exception{  
        //创建httpclient对象  
        CloseableHttpClient httpClient = HttpClients.createDefault();  
  
        //创建请求对象  
        HttpGet httpGet = new HttpGet("http://localhost:8080/user/shop/status");  
  
        //发送请求，接受响应结果  
        CloseableHttpResponse response = httpClient.execute(httpGet);  
  
        //获取服务端返回的状态码  
        int statusCode = response.getStatusLine().getStatusCode();  
        System.out.println("服务端返回的状态码为：" + statusCode);  
  
        HttpEntity entity = response.getEntity();  
        String body = EntityUtils.toString(entity);  
        System.out.println("服务端返回的数据为：" + body);  
  
        //关闭资源  
        response.close();  
        httpClient.close();  
    }  
  
  
    /**  
     * 测试通过httpclient发送POST方式的请求  
     */  
    @Test  
    public void testPOST() throws Exception{  
        // 创建httpclient对象  
        CloseableHttpClient httpClient = HttpClients.createDefault();  
  
        //创建请求对象  
        HttpPost httpPost = new HttpPost("http://localhost:8080/admin/employee/login");  
  
        JSONObject jsonObject = new JSONObject();  
        jsonObject.put("username","admin");  
        jsonObject.put("password","123456");  
  
        StringEntity entity = new StringEntity(jsonObject.toString());  
        //指定请求编码方式  
        entity.setContentEncoding("utf-8");  
        //数据格式  
        entity.setContentType("application/json");  
        httpPost.setEntity(entity);  
  
        //发送请求  
        CloseableHttpResponse response = httpClient.execute(httpPost);  
  
        //解析返回结果  
        int statusCode = response.getStatusLine().getStatusCode();  
        System.out.println("响应码为：" + statusCode);  
  
        HttpEntity entity1 = response.getEntity();  
        String body = EntityUtils.toString(entity1);  
        System.out.println("响应数据为：" + body);  
  
        //关闭资源  
        response.close();  
        httpClient.close();  
    }  
}
```

HttpClientUtils：

```java
/**  
 * Http工具类  
 */  
public class HttpClientUtil {  
  
    static final int TIMEOUT_MSEC = 5 * 1000;  
  
    /**  
     * 发送GET方式请求  
     * @param url  
     * @param paramMap  
     * @return  
     */  
    public static String doGet(String url,Map<String,String> paramMap){  
        // 创建Httpclient对象  
        CloseableHttpClient httpClient = HttpClients.createDefault();  
  
        String result = "";  
        CloseableHttpResponse response = null;  
  
        try{  
            URIBuilder builder = new URIBuilder(url);  
            if(paramMap != null){  
                for (String key : paramMap.keySet()) {  
                    builder.addParameter(key,paramMap.get(key));  
                }  
            }  
            URI uri = builder.build();  
  
            //创建GET请求  
            HttpGet httpGet = new HttpGet(uri);  
  
            //发送请求  
            response = httpClient.execute(httpGet);  
  
            //判断响应状态  
            if(response.getStatusLine().getStatusCode() == 200){  
                result = EntityUtils.toString(response.getEntity(),"UTF-8");  
            }  
        }catch (Exception e){  
            e.printStackTrace();  
        }finally {  
            try {  
                response.close();  
                httpClient.close();  
            } catch (IOException e) {  
                e.printStackTrace();  
            }  
        }  
  
        return result;  
    }  
  
    /**  
     * 发送POST方式请求  
     * @param url  
     * @param paramMap  
     * @return  
     * @throws IOException  
     */    public static String doPost(String url, Map<String, String> paramMap) throws IOException {  
        // 创建Httpclient对象  
        CloseableHttpClient httpClient = HttpClients.createDefault();  
        CloseableHttpResponse response = null;  
        String resultString = "";  
  
        try {  
            // 创建Http Post请求  
            HttpPost httpPost = new HttpPost(url);  
  
            // 创建参数列表  
            if (paramMap != null) {  
                List<NameValuePair> paramList = new ArrayList();  
                for (Map.Entry<String, String> param : paramMap.entrySet()) {  
                    paramList.add(new BasicNameValuePair(param.getKey(), param.getValue()));  
                }  
                // 模拟表单  
                UrlEncodedFormEntity entity = new UrlEncodedFormEntity(paramList);  
                httpPost.setEntity(entity);  
            }  
  
            httpPost.setConfig(builderRequestConfig());  
  
            // 执行http请求  
            response = httpClient.execute(httpPost);  
  
            resultString = EntityUtils.toString(response.getEntity(), "UTF-8");  
        } catch (Exception e) {  
            throw e;  
        } finally {  
            try {  
                response.close();  
            } catch (IOException e) {  
                e.printStackTrace();  
            }  
        }  
  
        return resultString;  
    }  
  
    /**  
     * 发送POST方式请求  
     * @param url  
     * @param paramMap  
     * @return  
     * @throws IOException  
     */    public static String doPost4Json(String url, Map<String, String> paramMap) throws IOException {  
        // 创建Httpclient对象  
        CloseableHttpClient httpClient = HttpClients.createDefault();  
        CloseableHttpResponse response = null;  
        String resultString = "";  
  
        try {  
            // 创建Http Post请求  
            HttpPost httpPost = new HttpPost(url);  
  
            if (paramMap != null) {  
                //构造json格式数据  
                JSONObject jsonObject = new JSONObject();  
                for (Map.Entry<String, String> param : paramMap.entrySet()) {  
                    jsonObject.put(param.getKey(),param.getValue());  
                }  
                StringEntity entity = new StringEntity(jsonObject.toString(),"utf-8");  
                //设置请求编码  
                entity.setContentEncoding("utf-8");  
                //设置数据类型  
                entity.setContentType("application/json");  
                httpPost.setEntity(entity);  
            }  
  
            httpPost.setConfig(builderRequestConfig());  
  
            // 执行http请求  
            response = httpClient.execute(httpPost);  
  
            resultString = EntityUtils.toString(response.getEntity(), "UTF-8");  
        } catch (Exception e) {  
            throw e;  
        } finally {  
            try {  
                response.close();  
            } catch (IOException e) {  
                e.printStackTrace();  
            }  
        }  
  
        return resultString;  
    }  
    private static RequestConfig builderRequestConfig() {  
        return RequestConfig.custom()  
                .setConnectTimeout(TIMEOUT_MSEC)  
                .setConnectionRequestTimeout(TIMEOUT_MSEC)  
                .setSocketTimeout(TIMEOUT_MSEC).build();  
    }  
  
}
```

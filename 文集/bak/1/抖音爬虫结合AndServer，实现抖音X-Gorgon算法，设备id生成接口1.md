# 抖音爬虫结合AndServer，实现抖音X-Gorgon算法，设备id生成接口


# 结合AndServer实现接口开发
- Gradle引入依赖
```
implementation 'com.yanzhenjie.andserver:api:2.0.5'
annotationProcessor 'com.yanzhenjie.andserver:processor:2.0.5'
implementation 'com.alibaba:fastjson:1.1.71.android'
```
>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


- 接口类编写
```
package com.yf.douyintool.controller;
 
import android.text.TextUtils;
import android.util.Log;
 
import com.alibaba.fastjson.JSONObject;
import com.bytedance.frameworks.core.encrypt.TTEncryptUtils;
import com.google.gson.Gson;
import com.ss.sys.ces.a;
import com.yanzhenjie.andserver.annotation.GetMapping;
import com.yanzhenjie.andserver.annotation.PostMapping;
import com.yanzhenjie.andserver.annotation.RequestBody;
import com.yanzhenjie.andserver.annotation.RestController;
import com.yanzhenjie.andserver.util.MediaType;
import com.yf.douyintool.DeviceUtil;
import com.yf.douyintool.bean.DeviceBean;
 
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.Calendar;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.TimeZone;
import java.util.UUID;
import java.util.concurrent.TimeUnit;
import java.util.zip.GZIPOutputStream;
 
import okhttp3.ConnectionPool;
import okhttp3.FormBody;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Protocol;
import okhttp3.Request;
import okhttp3.Response;
 
@RestController
public class RequestController {
    private static final String NULL_MD5_STRING = "00000000000000000000000000000000";
    public String sessionid = "";
    public String xtttoken = "";
 
    @PostMapping(value = "/request", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
    public String test(@RequestBody String requestBodyString) {
        JSONObject jsonObject = new JSONObject();
        JSONObject requestBody = JSONObject.parseObject(requestBodyString);
        String ck = requestBody.getString("cookie");
        String url = requestBody.getString("url");
        String postData = requestBody.getString("postData");
        if (ck == null || url == null) {
            jsonObject.put("status_code", 0);
            jsonObject.put("status_msg", "缺少参数!");
            return jsonObject.toJSONString();
        }
        if (url.contains("douplus/order/create")) {
            // 如果是投放订单每次生产不同的DeviceData
            JSONObject deviceData = getNewDeviceData();
            url = replaceUrlParam(url, "device_id", deviceData.getString("device_id"));
            url = replaceUrlParam(url, "iid", deviceData.getString("install_id"));
//            Log.i("orderCreate", "替换device_id成功!");
        }
//        String _ricket = System.currentTimeMillis() + "";
        long time = System.currentTimeMillis() / 1000;
        String p = url.substring(url.indexOf("?") + 1, url.length());
        boolean isPost = postData != null && !postData.equals("");
        String result;
        if (isPost) {
            FormBody.Builder formBody = new FormBody.Builder();
            String STUB = encryption(postData);
            Map<String, String> map = new HashMap<>();
            String[] ks = postData.split("&");
            for (int i = 0; i < ks.length; i++) {
                String[] ur = ks[i].split("=");
                if (ur.length == 1) {
                    map.put(ur[0], "");
                } else {
                    map.put(ur[0], ur[1]);
 
                }
            }
            for (Map.Entry<String, String> m : map.entrySet()) {
                formBody.add(m.getKey(), m.getValue());
            }
            String s = getXGon(p, STUB, ck, sessionid);
            String XGon = ByteToStr(a.leviathan((int) time, StrToByte(s)));
            result = doPostNet(url, formBody.build(), time, XGon, STUB, ck);
        } else {
            String s = getXGon(p, "", ck, sessionid);
            String XGon = ByteToStr(a.leviathan((int) time, StrToByte(s)));
            result = doGetNet(url, time, XGon, ck);
        }
        jsonObject = JSONObject.parseObject(result);
        if (jsonObject == null) {
            jsonObject = new JSONObject();
            jsonObject.put("status_code", -2);
            jsonObject.put("status_msg", "未知错误!");
        }
        return jsonObject.toJSONString();
    }
 
 
    @GetMapping(value = "/getDeviceData", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
    public String getDeviceData() {
        return getNewDeviceData().toJSONString();
    }
 
    @GetMapping(value = "/getQuery", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
    public String getQuery() {
        String uuid = DeviceUtil.getRanInt(15);  //设备id
        String openudid = DeviceUtil.getRanInt(16);  //android_id
        String _rticket = System.currentTimeMillis() + "";   //获取当前时间
        String url = "https://log.snssdk.com/service/2/device_register/?mcc_mnc=46000&ac=wifi&channel=aweGW&aid=1128&app_name=aweme&version_code=550&version_name=5.5.0&device_platform=android&ssmix=a&device_type=SM-G925F&device_brand=samsung&language=zh&os_api=22&os_version=5.1.1&uuid=" + uuid + "&openudid=" + openudid + "&manifest_version_code=550&resolution=720*1280&dpi=192&update_version_code=5502&_rticket=" + _rticket + "&tt_data=a&config_retry=b";
        String stb = url.substring(url.indexOf("?") + 1, url.length());
        String STUB = encryption(stb).toUpperCase();
        String ck = "odin_tt=9c1e0ebae55f3c2d9f71ab2aadce63126022e8960819bace07d441d977ad60eff6312161f546ebfe747528d03d53a161728250938c4287a588d86aa599c284b3; qh[360]=1; install_id=66715314288; ttreq=1$0b4589453328800ed93e002538883aa52da3e1d5";
        int time = (int) (System.currentTimeMillis() / 1000);
        String s = getXGon(url, STUB, ck, null);
        String XGon = ByteToStr(a.leviathan(time, StrToByte(s)));
        String device = getDevice(openudid, uuid);
        JSONObject deviceJson = JSONObject.parseObject(device);
        final okhttp3.RequestBody formBody = okhttp3.RequestBody.create(okhttp3.MediaType.parse("application/octet-stream;tt-data=a"), this.toGzip(device));
        String result = doPostNet(url, formBody, time, XGon, "", ck);
        JSONObject deviceResult = JSONObject.parseObject(result);
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("status_code", 0);
        JSONObject headerJson = deviceJson.getJSONObject("header");
        String data = String.format("os_api=22&device_type=SM-G925F&ssmix=a&manifest_version_code=911&dpi=320&uuid=%s&app_name=aweme&version_name=9.1.1&ts=%d&app_type=normal&ac=wifi&update_version_code=9104&channel=huawei_1&_rticket=%s&device_platform=android&iid=%s&version_code=911&cdid=%s&openudid=%s&device_id=%s&resolution=720*1280&os_version=5.1.1&language=zh&device_brand=OPPO&aid=1128&mcc_mnc=46007",
                uuid, time, _rticket, deviceResult.getString("install_id_str"), headerJson.getString("clientudid"), openudid, deviceResult.getString("device_id_str"));
        jsonObject.put("data", data);
        Log.i("data", data);
        return jsonObject.toJSONString();
    }
 
    public JSONObject getNewDeviceData() {
        String uuid = DeviceUtil.getRanInt(15);  //设备id
        String openudid = DeviceUtil.getRanInt(16);  //android_id
        String _rticket = System.currentTimeMillis() + "";   //获取当前时间
        String url = "https://log.snssdk.com/service/2/device_register/?mcc_mnc=46000&ac=wifi&channel=aweGW&aid=1128&app_name=aweme&version_code=550&version_name=5.5.0&device_platform=android&ssmix=a&device_type=SM-G925F&device_brand=samsung&language=zh&os_api=22&os_version=5.1.1&uuid=" + uuid + "&openudid=" + openudid + "&manifest_version_code=550&resolution=720*1280&dpi=192&update_version_code=5502&_rticket=" + _rticket + "&tt_data=a&config_retry=b";
        String stb = url.substring(url.indexOf("?") + 1, url.length());
        String STUB = encryption(stb).toUpperCase();
        String ck = "odin_tt=9c1e0ebae55f3c2d9f71ab2a12ce63c46022e8912819bace07d441d977ad60eff6301161f546ebfe747528d03d53a161728250938c4287a588d86aa599c284b3; qh[360]=1; install_id=66715314288; ttreq=1$0b4589453328800ed93e002538883aa52da3e1d5";
        int time = (int) (System.currentTimeMillis() / 1000);
        String s = getXGon(url, STUB, ck, null);
        String XGon = ByteToStr(a.leviathan(time, StrToByte(s)));
        final okhttp3.RequestBody formBody = okhttp3.RequestBody.create(okhttp3.MediaType.parse("application/octet-stream;tt-data=a"), this.toGzip(this.getDevice(openudid, uuid)));
        String result = doPostNet(url, formBody, time, XGon, "", ck);
        JSONObject jsonObject = JSONObject.parseObject(result);
        if (jsonObject == null) {
            jsonObject = new JSONObject();
            jsonObject.put("status_code", -2);
            jsonObject.put("status_msg", "未知错误!");
        }
        return jsonObject;
    }
 
    public String getDevice(String openudid, String udid) {
        //DeviceBean
        String Serial_number = DeviceUtil.getRanInt(8);
        DeviceBean deviceBean = new DeviceBean();
        deviceBean.set_gen_time(System.currentTimeMillis() + "");
        deviceBean.setMagic_tag("ss_app_log");
        //HeaderBean
        DeviceBean.HeaderBean headerBean = new DeviceBean.HeaderBean();
        headerBean.setDisplay_name("抖音短视频");
        headerBean.setUpdate_version_code(5502);
        headerBean.setManifest_version_code(550);
        headerBean.setAid(1128);
        headerBean.setChannel("aweGW");
        headerBean.setAppkey("59bfa27c67e59e7d920028d9"); //appkey
        headerBean.setPackageX("com.ss.android.ugc.aweme");
        headerBean.setApp_version("5.5.0");
        headerBean.setVersion_code(550);
        headerBean.setSdk_version("2.5.5.8");
        headerBean.setOs("Android");
        headerBean.setOs_version("5.1.1");
        headerBean.setOs_api(22);
        headerBean.setDevice_model("SM-G925F");
        headerBean.setDevice_brand("samsung");
        headerBean.setDevice_manufacturer("samsung");
        headerBean.setCpu_abi("armeabi-v7a");
        headerBean.setBuild_serial(Serial_number);  android.os.Build.SERIAL
        headerBean.setRelease_build("2132ca7_20190321");  // release版本
        headerBean.setDensity_dpi(192);
        headerBean.setDisplay_density("mdpi");
        headerBean.setResolution("1280x720");
        headerBean.setLanguage("zh");
        headerBean.setMc(DeviceUtil.getMac());  //mac 地址
        headerBean.setTimezone(8);
        headerBean.setAccess("wifi");
        headerBean.setNot_request_sender(0);
        headerBean.setCarrier("China Mobile GSM");
        headerBean.setMcc_mnc("46000");
        headerBean.setRom("eng.se.infra.20181117.120021");  //Build.VERSION.INCREMENTAL
        headerBean.setRom_version("samsung-user 5.1.1 20171130.276299 release-keys");  //Build.DISPLAY
        headerBean.setSig_hash("aea615ab910015038f73c47e45d21466");  //app md5加密  固定
        headerBean.setDevice_id("");   //获取之后的设备id
        headerBean.setOpenudid(openudid);  //openudid
        headerBean.setUdid(udid);  //真机的imei
        headerBean.setClientudid(UUID.randomUUID().toString());  //uuid
        headerBean.setSerial_number(Serial_number);  //android.os.Build.SERIAL
        headerBean.setRegion("CN");
        headerBean.setTz_name("Asia\\/Shanghai");  //timeZone.getID();
        headerBean.setTimezone(28800);  //String.valueOf(timeZone.getOffset(System.currentTimeMillis()) / 1000)
        headerBean.setSim_region("cn");
        List<DeviceBean.HeaderBean.SimSerialNumberBean> sim_serial_number = new ArrayList<>();
        DeviceBean.HeaderBean.SimSerialNumberBean bean = new DeviceBean.HeaderBean.SimSerialNumberBean();
        bean.setSim_serial_number(DeviceUtil.getRanInt(20));
        sim_serial_number.add(bean);
        headerBean.setSim_serial_number(sim_serial_number);
//        Log.i("deviceHeader", headerBean.toString());
        deviceBean.setHeader(headerBean);
        TimeZone timeZone = Calendar.getInstance().getTimeZone();
        timeZone.getID();
        //r
        Gson gson = new Gson();
        return gson.toJson(deviceBean);
    }
 
    public byte[] toGzip(String r) {
        try {
            byte[] bArr2 = r.getBytes("UTF-8");
 
            ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream(8192);
            GZIPOutputStream gZIPOutputStream = new GZIPOutputStream(byteArrayOutputStream);
            gZIPOutputStream.write(bArr2);
            gZIPOutputStream.close();
            bArr2 = byteArrayOutputStream.toByteArray();
            bArr2 = TTEncryptUtils.a(bArr2, bArr2.length);
            return bArr2;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
 
 
    public class RetryIntercepter implements Interceptor {
 
        public int maxRetry;//最大重试次数
        private int retryNum = 0;//假如设置为3次重试的话，则最大可能请求4次（默认1次+3次重试）
 
        public RetryIntercepter(int maxRetry) {
            this.maxRetry = maxRetry;
        }
 
        @Override
        public Response intercept(Chain chain) throws IOException {
            Request request = chain.request();
//            System.out.println("retryNum=" + retryNum);
            boolean isSuccessful;
            Response response = null;
            try {
                response = chain.proceed(request);
                isSuccessful = response.isSuccessful();
            } catch (Exception e) {
                isSuccessful = false;
            }
            while (!isSuccessful && retryNum < maxRetry) {
                retryNum++;
                System.out.println("retryNum=" + retryNum);
                response = chain.proceed(request);
            }
 
            return response;
        }
    }
 
    public String doGetNet(String url, long time, String XGon, String ck) {
//        Log.i("XGon", XGon);
//        Log.i("time", String.valueOf(time));
        Request request = new Request.Builder()
                .url(url)
                .get()
                .addHeader("X-SS-REQ-TICKET", System.currentTimeMillis() + "")
                .addHeader("X-Khronos", time + "")
                .addHeader("X-Gorgon", XGon)
                .addHeader("sdk-version", "1")
                .addHeader("Cookie", ck)
                .addHeader("X-Pods", "")
                .addHeader("Connection", "Keep-Alive")
                .addHeader("User-Agent", "okhttp/3.10.0.1")
                .addHeader("x-tt-token", xtttoken)
                .addHeader("Accept-Encoding", "identity")
                .addHeader("Connection", "Upgrade, HTTP2-Settings")
                .addHeader("Upgrade", "h2c")
                .build();
        List<Protocol> protocols = new ArrayList<>();
// protocols.add(Protocol.H2_PRIOR_KNOWLEDGE);
        protocols.add(Protocol.HTTP_2);
        protocols.add(Protocol.HTTP_1_1);
        OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .retryOnConnectionFailure(true)
                .protocols(protocols)
                .connectionPool(new ConnectionPool(10, 30, TimeUnit.SECONDS))
                .addInterceptor(new RetryIntercepter(3))
                .build();
        Response response = null;
        try {
            response = okHttpClient.newCall(request).execute();
        } catch (IOException e) {
            e.printStackTrace();
        }
        //响应成功
        if (response.isSuccessful()) {
            try {
                return response.body().string();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("status_code", -2);
        jsonObject.put("status_msg", "未知错误!");
        return jsonObject.toJSONString();
    }
 
    public String doPostNet(String url, okhttp3.RequestBody requestBody, long time, String XGon, String stub, String ck) {
        Request request = new Request.Builder()
                .url(url)
                .post(requestBody)
                .addHeader("X-SS-STUB", stub)
                .addHeader("X-SS-REQ-TICKET", System.currentTimeMillis() + "")
                .addHeader("X-Khronos", time + "")
                .addHeader("X-Gorgon", XGon)
                .addHeader("sdk-version", "1")
                .addHeader("Cookie", ck)
                .addHeader("X-Pods", "")
                .addHeader("Connection", "Keep-Alive")
                .addHeader("User-Agent", "okhttp/3.10.0.1")
                .addHeader("Content-Type", "application/x-www-form-urlencoded; charset=UTF-8")
                .addHeader("x-tt-token", xtttoken)  //登录成功头部返回的数据
                .build();
        List<Protocol> protocols = new ArrayList<>();
// protocols.add(Protocol.H2_PRIOR_KNOWLEDGE);
        protocols.add(Protocol.HTTP_2);
        protocols.add(Protocol.HTTP_1_1);
        OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .retryOnConnectionFailure(true)
                .protocols(protocols)
                .connectionPool(new ConnectionPool(10, 30, TimeUnit.SECONDS))
                .addInterceptor(new RetryIntercepter(3))
                .build();
        Response response = null;
        try {
            response = okHttpClient.newCall(request).execute();
        } catch (IOException e) {
            e.printStackTrace();
        }
        //响应成功
        if (response.isSuccessful()) {
            try {
                return response.body().string();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("status_code", -2);
        jsonObject.put("status_msg", "未知错误!");
        return jsonObject.toJSONString();
    }
 
    public static byte[] StrToByte(String str) {
        String str2 = str;
        Object[] objArr = new Object[1];
        int i = 0;
        objArr[0] = str2;
 
        int length = str.length();
        byte[] bArr = new byte[(length / 2)];
        while (i < length) {
            bArr[i / 2] = (byte) ((Character.digit(str2.charAt(i), 16) << 4) + Character.digit(str2.charAt(i + 1), 16));
            i += 2;
        }
        return bArr;
    }
 
    public static String ByteToStr(byte[] bArr) {
 
        int i = 0;
 
        char[] toCharArray = "0123456789abcdef".toCharArray();
        char[] cArr = new char[(bArr.length * 2)];
        while (i < bArr.length) {
            int i2 = bArr[i] & 255;
            int i3 = i * 2;
            cArr[i3] = toCharArray[i2 >>> 4];
            cArr[i3 + 1] = toCharArray[i2 & 15];
            i++;
        }
        return new String(cArr);
 
    }
 
    public String encryption(String str) {
        String re_md5 = null;
        try {
            MessageDigest md = MessageDigest.getInstance("MD5");
            md.update(str.getBytes());
            byte b[] = md.digest();
 
            int i;
 
            StringBuffer buf = new StringBuffer("");
            for (int offset = 0; offset < b.length; offset++) {
                i = b[offset];
                if (i < 0)
                    i += 256;
                if (i < 16)
                    buf.append("0");
                buf.append(Integer.toHexString(i));
            }
 
            re_md5 = buf.toString();
 
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }
        return re_md5.toUpperCase();
    }
 
    public String getXGon(String url, String stub, String ck, String sessionid) {
        StringBuilder sb = new StringBuilder();
        if (TextUtils.isEmpty(url)) {
            sb.append(NULL_MD5_STRING);
        } else {
            sb.append(encryption(url).toLowerCase());
        }
 
        if (TextUtils.isEmpty(stub)) {
            sb.append(NULL_MD5_STRING);
        } else {
            sb.append(stub);
        }
 
        if (TextUtils.isEmpty(ck)) {
            sb.append(NULL_MD5_STRING);
        } else {
            sb.append(encryption(ck).toLowerCase());
        }
 
        if (TextUtils.isEmpty(sessionid)) {
            sb.append(NULL_MD5_STRING);
        } else {
            sb.append(encryption(sessionid).toLowerCase());
//            sb.append(sessionid);
        }
        return sb.toString();
    }
 
    /**
     * 替换url中的参数
     *
     * @param url
     * @param name
     * @param value
     * @return
     */
    public static String replaceUrlParam(String url, String name, String value) {
        int index = url.indexOf(name + "=");
        if (index != -1) {
            StringBuilder sb = new StringBuilder();
            sb.append(url.substring(0, index)).append(name).append("=").append(value);
            int idx = url.indexOf("&", index);
            if (idx != -1) {
                sb.append(url.substring(idx));
            }
            url = sb.toString();
        }
        return url;
    }
}
```

- AndServer服务类
```
package com.yf.douyintool;
 
import android.content.Context;
import android.util.Log;
 
import com.yanzhenjie.andserver.AndServer;
import com.yanzhenjie.andserver.Server;
 
import java.net.InetAddress;
import java.net.UnknownHostException;
import java.util.concurrent.TimeUnit;
 
/**
 * Created by Zhenjie Yan on 2018/6/9.
 */
public class ServerManager {
 
    private static final String TAG = "ServerManager";
 
    private Server mServer;
 
    /**
     * Create server.
     */
    public ServerManager(Context context) {
        InetAddress inetAddress = null;
        try {
            inetAddress = InetAddress.getByName("0.0.0.0");
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
        mServer = AndServer.serverBuilder(context)
                .inetAddress(inetAddress)
                .port(8080)
                .timeout(10, TimeUnit.SECONDS)
                .listener(new Server.ServerListener() {
                    @Override
                    public void onStarted() {
                        // TODO The server started successfully.
                        Log.d(TAG, "onStarted: ");
                    }
 
                    @Override
                    public void onStopped() {
                        // TODO The server has stopped.
                        Log.d(TAG, "onStarted: ");
                    }
 
                    @Override
                    public void onException(Exception e) {
                        Log.e(TAG, "onException: ",e );
                        // TODO An exception occurred while the server was starting.
                    }
                })
                .build();
    }
 
    /**
     * Start server.
     */
    public void startServer() {
        if (mServer.isRunning()) {
            // TODO The server is already up.
        } else {
            mServer.startup();
        }
    }
 
    /**
     * Stop server.
     */
    public void stopServer() {
        if (mServer.isRunning()) {
            mServer.shutdown();
        } else {
            Log.w("AndServer", "The server has not started yet.");
        }
    }
}
```
 

- 在MainActivity.onCreate方法中加入
```
serverManager = new ServerManager(this);
serverManager.startServer();
Log.i("address", NetUtils.getLocalIPAddress()+":8080");
application = this;
Thread.setDefaultUncaughtExceptionHandler(handler);
```
 

- 崩溃重启
```
private Thread.UncaughtExceptionHandler handler = (t, e) -> {
        restartApp(); //发生崩溃异常时,重启应用
    };
```

## 测试接口


- getDeviceData
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1607219197331-e1cbb75b-282a-4771-bb09-504e899e682e.png#align=left&display=inline&height=488&originHeight=488&originWidth=1240&size=0&status=done&style=none&width=1240)


- getQuery
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1607219197378-6a801f34-39bc-4787-9718-68fa04044a03.png#align=left&display=inline&height=368&originHeight=368&originWidth=1240&size=0&status=done&style=none&width=1240)


- request
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1607219197321-736535d7-cdf0-44a9-af65-f96c65dd1fe3.png#align=left&display=inline&height=644&originHeight=644&originWidth=1240&size=0&status=done&style=none&width=1240)

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


## Cookie获取方式
有爬取登录接口实现的，但其实有一种简单的方式就是二维码登录，但是涉及到一些隐秘性这里就不公开说明了。




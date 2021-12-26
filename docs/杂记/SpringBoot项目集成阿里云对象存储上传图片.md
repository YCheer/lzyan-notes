## 导入坐标

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.10.2</version>
</dependency>
```

## 开通阿里云oss服务，然后创建Bucket（存储空间）

![](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832925076331429888.png)

## 绑定自己的域名

这里记得选择一下自动添加 `CNAME` 记录，选择了就会帮你自动去域名解析那里添加多一条记录呢

![image-20200827152938828.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832925147282276352.png)

## 修改配置文件application.yml

```yaml
aliyun-oss:
  bucketApp: XXX #存储空间bucket的名称 
  domainApp: https://resource.lzyan.fun #自己的域名
  region: xxx #文件夹名称
  #后面这三个属性，可用去查看阿里云的文档去找到
  endpoint: oss-cn-shenzhen.aliyuncs.com
  accessKeyId: xxx
  accessKeySecret: xxx
```

## 读取配置文件的属性

```java
/**
 * 基本配置
 */
@Component
@ConfigurationProperties(prefix = "aliyun-oss")
@Data
public class AliyunOssProperties {
    /**
     * 服务器地点
     */
    private String region;
    /**
     * 服务器地址
     */
    private String endpoint;
    /**
     * OSS身份id
     */
    private String accessKeyId;
    /**
     * 身份密钥
     */
    private String accessKeySecret;

    /**
     * App文件bucketName
     */
    private String bucketApp;
    /**
     * App包文件地址前缀
     */
    private String domainApp;
}
```

### 写一个工具类

```java
/**
 * 上传文件的工具类
 */
@Slf4j
@Component
public class AliyunOssUtils {
    
    @Autowired
    private AliyunOssProperties aliyunOssProperties;
    public OSS initOSSClient(){
        String endpoint = aliyunOssProperties.getEndpoint();
        String accessKeyId = aliyunOssProperties.getAccessKeyId();
        String accessKeySecret = aliyunOssProperties.getAccessKeySecret();
        OSS ossClient = new OSSClientBuilder().build(endpoint,accessKeyId,accessKeySecret);
        return ossClient;
    }

    /**
     * 上传图片
     * 返回配置的域名
     * @param file
     * @return
     */
    public String uploadImage(MultipartFile file,String path) {
        try {
            String bucketName = aliyunOssProperties.getBucketApp();
            String domain = aliyunOssProperties.getDomainApp();
            // 初始化对象
            OSS ossClient = initOSSClient();
            InputStream in = file.getInputStream();
            // meta设置请求头
            ObjectMetadata meta = new ObjectMetadata();
            meta.setContentType("image/jpg");
            ossClient.putObject(bucketName, path, in, meta);
            //关闭
            ossClient.shutdown();
            //返回可访问地址，自己的域名加路径
            return domain + "/" + path;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

}

```

然后再需要调用的地方进行调用就行了，目前只是实现了上传图片，其他的功能待研究。


## 遇到的问题

在创建 `Bucket` 的时候记得把权限私有改为公共读，不然上传上去的图片文件访问时 `URL` 后面会携带了个签名的。

## 域名配置ssl证书

证书申请，可以申请20个，这里先前申请过一个是别的域名的证书，所以点击创建证书再申请一个为 `resource.lzyan.fun` 域名的证书，申请之后就可以点击部署，在部署里面就可以选择已经绑定了这个域名的 `Bucket`
![](https://resource.lzyan.fun/PigGo/2.png)
![](https://resource.lzyan.fun/PigGo/3.png)
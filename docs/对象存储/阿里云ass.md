# 阿里云ass

## 配置流程

**配置凭证**

1. 在CMD中运行以下命令。

    

   ```bash
   setx OSS_ACCESS_KEY_ID "YOUR_ACCESS_KEY_ID"
   setx OSS_ACCESS_KEY_SECRET "YOUR_ACCESS_KEY_SECRET"
   ```

2. 运行以下命令，检查环境变量是否生效。

    

   ```bash
   echo %OSS_ACCESS_KEY_ID%
   echo %OSS_ACCESS_KEY_SECRET%
   ```

**配置依赖**

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.17.4</version>
</dependency>
```

**配置yml**

```yml
oss:
  endpoint: oss-cn-beijing.aliyuncs.com
  accessKeyId: yours AccessKey ID
  accessKeySecret: yours AccessKey Secret
  bucketName: yours bucket name
```



## **文件上传**

**创建类**

```
@ConfigurationProperties(prefix = "aliyun")
@Data
public class aliyunProperties {

    private String endpoint;

    private String accessKey;

    private String secretKey;

    private String bucketName;
}
```

**创建配置类**

```java
@Configuration
@ConditionalOnProperty(name = "aliyun.endpoint")
public class aliyunConfiguration {

    @Autowired
    private aliyunProperties properties;

    @Bean
    public OSS ossClient() {
        return new OSSClientBuilder().build(
            		properties.endpoint,
            		properties.accessKeyId,
            		properties.accessKeySecret)
    }
}
```

**上传逻辑**

```java
		// 创建以天为单位的文件夹名称
        String folderName = DateUtil.format(new Date(), "yyyy-MM-dd");
        // 创建新文件的名称，时间戳
        String fileNewName = DateUtil.format(new Date(),"HHmmssSSS");
        // 获取原文件的名称
        String originalFilename = file.getOriginalFilename();
        String suffix = originalFilename.substring(originalFilename.lastIndexOf("."));

        // 填写Object完整路径，完整路径中不能包含Bucket名称，例如exampledir/exampleobject.txt。
        String objectName = folderName+"/"+fileNewName+suffix;
```

```java
URL url = null;
        try {
            // 创建PutObjectRequest对象。
            PutObjectRequest putObjectRequest = new PutObjectRequest(properties.bucketName, objectName,file.getInputStream());
            // 上传字符串。
            ossClient.putObject(putObjectRequest);
            // 创建上传文件的访问路径
            url = ossClient.generatePresignedUrl(properties.bucketName, objectName, DateUtil.offsetDay(new Date(), 360 * 10));
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (ossClient != null) {
                ossClient.shutdown();
            }
        }
```


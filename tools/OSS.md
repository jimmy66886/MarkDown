# 对象存储服务

学一下怎么用

## 介绍

1. Bucket: 存储空间是用户用于存储对象(Object,就是文件)的容器,所有的对象都必须隶属于某个存储空间
2. SDK: `Software Developement Kit`的缩写,软件开发工具包,包括辅助软件开发的依赖,代码示例等,都可以叫做SDK

## 使用

### 创建bucket

在创建时,看好这几项,防止多余的服务-选本地冗余存储
读写权限选公共读,私有的好像是访问很麻烦?
![20230829093624](https://img01.zzmr.club/img/20230829093624.png)

### 获取AccessKey

直接点击右上角头像,有一个AccessKey管理
![20230829103819](https://img01.zzmr.club/img/20230829103819.png)

直接创建即可
### 编写入门案例

代码就在官网拷贝,新版里面的key和key_secret都要加入到系统环境变量里,用户环境变量还不行,必须系统的

然后就没有然后了
```java
package com.zzmr.osstest;

/**
 * @author zzmr
 * @create 2023-08-29 10:51
 */

import com.aliyun.oss.ClientException;
import com.aliyun.oss.OSS;
import com.aliyun.oss.common.auth.*;
import com.aliyun.oss.OSSClientBuilder;
import com.aliyun.oss.OSSException;
import com.aliyun.oss.model.PutObjectRequest;
import com.aliyun.oss.model.PutObjectResult;

import java.io.FileInputStream;
import java.io.InputStream;

public class Demo {

    public static void main(String[] args) throws Exception {
        // Endpoint以华东1（杭州）为例，其它Region请按实际情况填写。
        String endpoint = "https://oss-cn-beijing.aliyuncs.com";
        // 从环境变量中获取访问凭证。运行本代码示例之前，请确保已设置环境变量OSS_ACCESS_KEY_ID和OSS_ACCESS_KEY_SECRET。
        EnvironmentVariableCredentialsProvider credentialsProvider =
                CredentialsProviderFactory.newEnvironmentVariableCredentialsProvider();
        // 填写Bucket名称，例如examplebucket。
        String bucketName = "zzmr-sky-take-out";
        // 填写Object完整路径，完整路径中不能包含Bucket名称，例如exampledir/exampleobject.txt。
        String objectName = "test/test.jpg";
        // 填写本地文件的完整路径，例如D:\\localpath\\examplefile.txt。
        // 如果未指定本地路径，则默认从示例程序所属项目对应本地路径中上传文件流。
        String filePath = "C:\\Users\\zzmr\\Pictures\\临时\\微信图片_20230705135343.jpg";

        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, credentialsProvider);

        try {
            InputStream inputStream = new FileInputStream(filePath);
            // 创建PutObjectRequest对象。
            PutObjectRequest putObjectRequest = new PutObjectRequest(bucketName, objectName, inputStream);
            // 创建PutObject请求。
            PutObjectResult result = ossClient.putObject(putObjectRequest);
        } catch (OSSException oe) {
            System.out.println("Caught an OSSException, which means your request made it to OSS, "
                    + "but was rejected with an error response for some reason.");
            System.out.println("Error Message:" + oe.getErrorMessage());
            System.out.println("Error Code:" + oe.getErrorCode());
            System.out.println("Request ID:" + oe.getRequestId());
            System.out.println("Host ID:" + oe.getHostId());
        } catch (ClientException ce) {
            System.out.println("Caught an ClientException, which means the client encountered "
                    + "a serious internal problem while trying to communicate with OSS, "
                    + "such as not being able to access the network.");
            System.out.println("Error Message:" + ce.getMessage());
        } finally {
            if (ossClient != null) {
                ossClient.shutdown();
            }
        }
    }
}
```

测试下如果吧OSS当成图床怎么样
![20230830110833](https://img01.zzmr.club/img/20230830110833.png)
<!-- ![20230830111454](https://zzmr-sky-take-out.oss-cn-beijing.aliyuncs.com/img/20230830111454.png) -->
![20230830111622](https://img01.zzmr.club/img/20230830111622.png)

![0605153937](https://zzmr-sky-take-out.oss-cn-beijing.aliyuncs.com/img/0605153937.jpg)
![0605153937](https://zzmr-sky-take-out.oss-cn-beijing.aliyuncs.com/img/0605153937.jpg)
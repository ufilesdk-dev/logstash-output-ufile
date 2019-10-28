# logstash-output-ufile插件使用说明

## 前提说明
* 这个插件是建立在logstash基础上, 关于logstsh的相关介绍, 可在官网上查看
* 这个插件是在最近版本7.3.0基础上测试, 建议使用7.3.0这个版本的logstash

## 安装步骤
1. 下载[```logstash-7.3.0```](https://artifacts.elastic.co/downloads/logstash/logstash-7.3.0.tar.gz)
2. 安装[```logstash-7.3.0```](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html#installing-binary)
3. 获取插件```logstash-output-ufile-0.0.1.tar.gz```, 通过命令``` tar -xzvf logstash-output-ufile-0.0.1.tar.gz``` 解压后拷贝```logstash-output-ufile-0.0.1```到```logstash-7.3.0/vendor/bundle/jruby/2.5.0/gems/```
4. 修改文件```logstash-7.3.0/Gemfile```, 添加 ``` gem "logstash-output-ufile", :path => "vendor/bundle/jruby/2.5.0/gems/logstash-output-ufile-0.0.1" ```
5. 启动命令示例: bin/logstash -f mypipeline.conf (mypipeline.conf替换为自己的配置文件), 详细的请参考logstash官网

## 配置说明
1. 配置格式说明同[S3 output plugin](https://www.elastic.co/guide/en/logstash/6.2/plugins-outputs-s3.html), 除了几个uf开头的字段不一样以外, 其它都一样.
2. 配置格式样例如下:
```
input {
  file {
    path => ["/you_log_path"]
    start_position => "beginning"
  }
}
output {
  ufile{
    bucket => "your bucket created in ufile"
    uf_access_key_id => "public token"
    uf_secret_access_key => "secret token"
    uf_endpoint => "ufileos.com"
    uf_region => "cn-sh2 or cn-bj ...."

    codec => "line"
    rotation_strategy => "time"
    time_file => 1
    prefix => "standard_test"
    validate_credentials_on_root_bucket => false
    signature_version => 'v4'
  }
}
```
3. 配置结构解释:
  * 这个插件主要是针对配置output->ufile插件, 可以随意搭配input的其它标准插件, 例如上面的file插件.
4. ufile插件参数解释:
  * uf_access_key_id和uf_secret_access_key分别表示公钥和私钥, 可以登陆ucloud官网后在对象存储ufile的[令牌管理](https://console.ucloud.cn/ufile/token)中申请
  * uf_endpoint表示域名, 一般情况下都是 ```ufileos.com```
  * uf_region表示地区, 根据申请的bucket地域不同, 如果在上海二区申请那么就是ch-sh2, 同理ch-bj对应北京的bucket
  * 其它参数同 [S3 output plugin](https://www.elastic.co/guide/en/logstash/6.2/plugins-outputs-s3.html)
  * 可以根据自己的需要自行添加、删除、修改相关参数


## 常见问题说明
* 创建令牌的时候要授权配置的bucekt
* 创建令牌时候要勾选上传权限
* bucket配置不用加上后缀域名, 如```mybucket.cn-bj.ufileos.com``` 应该去掉```.cn-bj.ufileos.com```, 填写```mybucket```就好
* 此插件在配置中的名字是ufile, 不要填写成s3等其它了

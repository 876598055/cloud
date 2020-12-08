### Aldi南对接代理功能介绍
- 接收来在客户的对接请求，格式于prismart完全相同
- 接收客户数据后进行json格式验证，数据类型验证，数据格式验证
    - 必填信息空值验证
    - 字符串类型长度验证
    - 整数类型长度验证
    - 浮点类型位数验证
    - 时间类型转换验证
    - 单个对接批次对接商品数量验证，不大于2000
- 进行以上验证不通过，返回给客户端异常信息，验证通过则将请求转发至prisamrt
### 功能实现
1. 使用springboot注解@Validated验证数据格式 
2. 使用springboot注解@RequestBody验证json格式
3. 使用springboot的BindingResult bindingResult接收验证失败异常返回给客户端
4. 手动验证批次商品数据量
5. 将数据转发给prismart
```$java
private String integration(@Validated @RequestBody CdiInstructionsBatch args,BindingResult bindingResult){}
```
```$xslt
if(batch.getItems()!=null&&batch.getItems().size()>2000){}
```
```java
shopWebSender.processBatchRecord(JSON.toJSONString(batch), batch.getStoreCode(), prismart.getUrl(), batch.getBatchNo());
```
*****************************************************************
#### 20201208Aldi南对接代理需求变更
> 变更原因:\
> 使用@RequestBody CdiInstructionsBatch直接接收客户数据，无法判断客户传送的值为null还是没有传送某个值
> 导致客户进行对接时无法将prismart某个值置为空。
>
**变更实现**
1. 将controller接口层的参数接收格式改为String字符串接收。
2. 将json格式验证过程和数据格式验证过程拿到Service层处理。
3. json格式验证可使用alibaba fastjson。
4. 数据格式验证可使用javax @Valid
5. 验证失败返回客户端异常信息，验证通过，直接将接受的String转发给prisamrt，保证发送到prismart的数据为客户原始数据
```$java
private String integration(String args){}
```

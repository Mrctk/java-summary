# MyBatis功能架构

>MyBatis功能架构主要包括API接口层，数据处理层，基础支撑层，如下图所示
>
>![Mybatis功能框架](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL015QmF0aXMlRTYlQTElODYlRTYlOUUlQjYlRTYlODAlQkIlRTclQkIlOTMvTXlCYXRpcyVFNSU4QSU5RiVFOCU4MyVCRCVFNiU5RSVCNiVFNiU5RSU4NC5wbmc?x-oss-process=image/format,png)

我们把Mybatis的功能架构分为三层：

- API接口层：提供给外部使用的接口API，开发人员通过这些本地API来操纵数据库。接口层一接收到调用请求就会调用数据处理层来完成具体的数据处理。
- 数据处理层：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等。它主要的目的是根据调用的请求完成一次数据库操作。
- 基础支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的东西，将他们抽取出来作为最基础的组件。为上层的数据处理层提供最基础的支撑。
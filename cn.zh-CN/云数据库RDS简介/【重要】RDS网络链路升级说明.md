# 【重要】RDS网络链路升级说明 {#concept_vyz_wf2_wfb .concept}

为提供更出色的稳定性和性能，阿里云将对部分RDS实例进行网络连接模式升级，即从高安全模式（数据库代理）升级到高性能模式（标准模式）。

## 不升级的风险 {#section_v4k_gsj_ngb .section}

当前的高安全模式在某种场景下会出现资源稳定性的抖动，有可能给您的业务造成影响。为保证业务的正常稳定运行，请尽快完成实例的升级。

## 升级后的优势 {#section_gwv_mkr_mgb .section}

-   【稳定性】升级后网络链路少一次跳转，极大提高了稳定性。
-   【性能】升级后网络链路少一次跳转， 响应时间平均减少20%，性能明显提升。

## 升级范围 {#section_hzy_lkr_mgb .section}

处于高安全模式（数据库代理模式）且未开通读写分离的RDS for MySQL、PostgreSQL、PPAS实例和HybridDB for PostgreSQL实例（不涉及RDS for SQL Server实例）。具体判定方式如下：

1.  登录[RDS管理控制台](https://rds.console.aliyun.com/)。
2.  在页面左上角，选择实例所在地域。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64586/155600754337659_zh-CN.png)

3.  找到目标实例，单击实例ID。
4.  在左侧导航栏中单击**数据库连接**，查看**数据库代理状态（原高安全模式）**。
    -   如果**未开通**，则该实例无需升级。
    -   如果**已开通**，则该实例需进行升级。

        **说明：** 

        -   如果实例（MySQL）已开通读写分离，也无需升级，后续会针对已开通读写分离的实例提供升级方案。
        -   如果实例下挂载了只读实例，只需升级主实例，相应的只读实例会自动连带升级。

## 升级的影响 {#section_gdm_pkr_mgb .section}

-   在升级的过程中，会有约30秒的连接闪断（您可以指定升级的时间点，参见[方法三](#section_cd2_nkj_ngb)），请确保业务具备自动重连机制。
-   由于代理模式下，协议层默认开启了多语句 \(multi-statement\) ，所以切换后应用层如果没有开启多语句并且使用了多语句，会出现SQL语句报错。请提前检查并添加连接参数。例如，在JDBC中添加allowMultiQueries参数：

    ``` {#codeblock_uo1_5xr_gl6}
    dbc:mysql:///test?allowMultiQueries=true
    ```


## 升级方法一 {#section_cdd_rkr_mgb .section}

1.  在**数据库连接**页面，单击**切换访问模式**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64586/155600754337661_zh-CN.png)

2.  在弹出的对话框中，单击**确认**，以关闭数据库代理。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64586/155600754337662_zh-CN.png)

3.  确认业务运行正常。

    **说明：** 请务必进行确认。


## 升级方法二 {#section_gdh_ztr_mgb .section}

**说明：** 本方法仅适用于部分实例。

1.  在**数据库代理**页面，单击**已开通**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64586/155600754337721_zh-CN.png)

2.  在弹出的对话框中，单击**确认**，以关闭数据库代理。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64586/155600754337662_zh-CN.png)

3.  确认业务运行正常。

    **说明：** 请务必进行确认。


## 升级方法三 {#section_cd2_nkj_ngb .section}

1.  收到短信或邮件通知后，登录[RDS管理控制台](https://rds.console.aliyun.com/)。
2.  单击**待处理事件**。
3.  选中实例，点击**自定义操作时间**，修改升级执行的时间，即**计划切换时间**。**计划切换时间**不能晚于**最晚操作时间**。

    **说明：** 

    -   如果**开始时间**和**计划切换时间**为空，表示需要您主动设置时间，请务必点击**自定义操作时间**进行设置。
    -   如果不修改，则按照默认的**计划切换时间**（默认是在实例的可运维时间内）进行升级。
    -   不同的实例可以设置不同的**计划切换时间**。
     ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64586/155600754637740_zh-CN.png)

4.  确认**数据库代理状态（原高安全模式）**为**未开通**。
5.  确认业务运行正常。

    **说明：** 请务必进行确认。


## 常见问题 {#section_kgg_ppj_ngb .section}

1.  如何确认实例是否需要升级？

    答：请参见[升级范围](#section_hzy_lkr_mgb)。

2.  为什么无法升级？

    答：开通了读写分离功能的RDS实例目前无法直接升级。后续会针对开通了读写分离的实例提供升级方案。

3.  升级后业务需要做什么修改吗？

    答：升级过程中会有闪断，请确保业务有自动重连机制。如果没有自动重连机制，可能需要手动重启业务。升级后实例的域名（连接地址）、IP地址等都保持不变，应用程序中无需做相关修改。

4.  以后还可以再切换到高安全模式（数据库代理）吗？

    答：不需要切换。高安全模式主要是为了支持多网络并存（公私网并存），而当前的高性能模式（标准模式）已经支持了该功能。

5.  如果实例下挂载了只读实例，每个只读实例都要进行升级操作吗？

    答：不需要升级只读实例，只需升级主实例，相应的只读实例会自动连带升级。



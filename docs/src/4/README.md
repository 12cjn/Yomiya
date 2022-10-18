!> 这是个所有文章的归档处

<div style="position: relative; padding: 30% 45%;">
<iframe style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;" src="https://www.yuque.com/docs/share/31a963ea-af8c-4ef6-acd6-642a919158b6?#" frameborder="1" scrolling="yes" width="320" height="240" allowfullscreen
</iframe>
</div>

sequenceDiagram
  View->>IService: 发送请求
  IService->>ServiceImpl: 服务层处理
  ServiceImpl->>logic: 逻辑层处理
  logic->>Dao: 持久层处理
  Dao->>Mapping.xml: 调用知道的sql语句
  note right of Mapping.xml: 数据库交互
  Mapping.xml-->>Dao: 与数据库交互完毕返回
  Dao-->>logic: 持久层交互完毕返回
  logic-->>ServiceImpl: 逻辑层交互完毕返回
  ServiceImpl-->>IService: 服务交互完毕返回
  IService-->>View: 请求处理完毕，返回

gantt
dateFormat  YYYY-MM-DD
title 在mermaid代码里新增甘特图功能示例
section A部分
    已经完成的任务 :done,des1,2018-11-06,2018-11-08
    正在活动的任务 :active,des2, 2018-11-09, 3d
    (正在活动的任务结束后开始)未来的任务 :des3, after des2, 5d
    (未来的任务结束后开始)未来的任务2 : des4, after des3, 5d
section 关键任务
已经完成的关键任务 :crit, done, 2018-11-06,24h
    (A部分已经完成的任务结束后开始)解析器和jison实现 :crit, done, after des1, 2d
    解析器测试工作 :crit, active, 3d
    未来完成的关键任务 :crit, 5d
    渲染测试工作 :2d
    添加到mermaid :1d
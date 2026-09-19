## 追问 1：什么情况下RESTful不太适用？

非简单CRUD时如"下单"、"审核通过"、"批量导入"，用RPC风格action endpoint: `POST /orders/{id}/submit`或`POST /tickets/batch-import`。AI对话接口`POST /chat/completions`也偏RPC风格(SSE流式响应不像简单资源操作)。

## 追问 2：接口版本怎么管理？

三种: URL路径(/v1/tickets)、请求头(Accept: application/vnd.api+v1+json)、请求参数(?version=1)。URL方式最简单最常用。升大版本: 删字段、改字段类型或含义、返回结构大改。新加字段不算破坏性变更不用升版本。

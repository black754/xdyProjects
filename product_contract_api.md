# 产品管理 & 合同管理 OpenAI 风格 API 文档

> 本文档基于 `product_contract.sql` 中的表结构，并结合功能清单（产品版本、功能模块、合同签署）整理生成。

## 基础信息

- Base URL: `https://api.example.com/v1`
- 认证方式: `Authorization: Bearer <token>`
- 响应格式: `application/json`

## 通用规范

### 分页

- 请求参数: `page`（从 1 开始）, `page_size`
- 响应字段: `total`, `page`, `page_size`, `items`

### 错误格式

```json
{
  "error": {
    "code": "INVALID_ARGUMENT",
    "message": "字段验证失败",
    "details": [
      {"field": "name", "issue": "必填"}
    ]
  }
}
```

---

## 数据模型

### ProductVersion

映射表: `xdyai_productversion`

```json
{
  "id": "int64",
  "version_name": "string",
  "module_ids": "string",
  "version_order": "int64",
  "version_status": "string",
  "description": "string",
  "default_account_num": "int64",
  "one_account_price": "int64",
  "account_max_num": "int64",
  "default_p_c_user_num": "int64",
  "pc_user_price_under_ten": "int64",
  "pc_user_price_on_ten": "int64",
  "p_c_user_max": "int64",
  "default_move_user_num": "int64",
  "move_user_price_under_ten": "int64",
  "move_user_price_on_ten": "int64",
  "move_user_max": "int64",
  "year_max": "int64"
}
```

### FunctionalModule

映射表: `xdyai_functionalmodule`

```json
{
  "id": "int64",
  "name": "string",
  "depend_module": "string",
  "exclusion_module": "string",
  "parent_module": "int64",
  "order": "int64",
  "status": "string",
  "description": "string"
}
```

### ProductFunctionRelationship

映射表: `xdyai_productfunctionrelationship`

```json
{
  "id": "int64",
  "module_id": "int64",
  "version_id": "int64",
  "price": "int64",
  "unit": "string",
  "defined_num": "bool",
  "start_num": "int64",
  "step_num": "int64",
  "step_price": "int64",
  "max_num": "int64",
  "describe": "string"
}
```

### ProductInfo

映射表: `xdyai_productinfo`

```json
{
  "id": "int64",
  "code": "string",
  "name": "string",
  "shorthand": "string",
  "specification": "string",
  "value_type": "string",
  "inventory_class": "string",
  "inventory_class_name": "string",
  "tas_rate": "string",
  "is_new": "bool",
  "disabled": "bool",
  "unit_group": "string",
  "main_unit": "string",
  "change_type": "string",
  "unit1": "string",
  "change_rate1": "decimal",
  "unit2": "string",
  "change_rate2": "decimal",
  "inventory_descript": "string",
  "inv_s_cost": "decimal",
  "remarks": "string"
}
```

### Contract

映射表: `xdyai_contract`

```json
{
  "id": "int64",
  "name": "string",
  "initiator": "string",
  "customer": "int64",
  "status": "string",
  "initiation_time": "datetime",
  "deadline": "datetime",
  "cus_people": "string",
  "cus_phone": "string",
  "order_type": "string",
  "cus_name": "string",
  "cus_address": "string",
  "cus_postal": "string",
  "cus_email": "string",
  "num": "string",
  "signdate": "date",
  "ordernum": "string",
  "type": "string",
  "originalprice": "decimal",
  "discount": "decimal",
  "pay_type": "string",
  "bank_account": "string",
  "bankofdeposit": "string",
  "total_amount": "decimal",
  "big_amount": "string",
  "version": "string",
  "application_module": "string",
  "sign_order": "int64",
  "document_preview": "string"
}
```

### ContractBillingTerm

映射表: `xdyai_contract_billing_term`

```json
{
  "id": "int64",
  "contract_id": "int64",
  "term_name": "string",
  "trigger_event": "int64",
  "percentage": "decimal",
  "amount": "decimal",
  "due_date": "date",
  "status": "int64",
  "sequence": "int32",
  "remark": "string"
}
```

### Quote

映射表: `xdyai_quote`

```json
{
  "id": "int64",
  "customer_id": "int64",
  "opportunity_id": "int64",
  "quote_no": "string",
  "amount": "decimal",
  "status": "int64",
  "remark": "string"
}
```

### QuoteItem

映射表: `xdyai_quote_item`

```json
{
  "id": "int64",
  "quote_id": "int64",
  "product_id": "int64",
  "quantity": "decimal",
  "unit_price": "decimal",
  "amount": "decimal",
  "remark": "string"
}
```

---

## 产品管理 API

### 新增产品版本

`POST /product-versions`

```json
{
  "version_name": "基础版",
  "module_ids": "1,2,3",
  "version_order": 1,
  "version_status": "1",
  "description": "基础功能版本",
  "default_account_num": 1,
  "one_account_price": 1000,
  "account_max_num": 10,
  "default_p_c_user_num": 10,
  "pc_user_price_under_ten": 200,
  "pc_user_price_on_ten": 150,
  "p_c_user_max": 200,
  "default_move_user_num": 20,
  "move_user_price_under_ten": 100,
  "move_user_price_on_ten": 80,
  "move_user_max": 500,
  "year_max": 3
}
```

### 编辑产品版本

`PATCH /product-versions/{id}`

### 删除产品版本

`DELETE /product-versions/{id}`

### 版本设置（关联功能模块/价格规则）

`PUT /product-versions/{id}/modules`

```json
{
  "modules": [
    {
      "module_id": 1,
      "price": 10000,
      "unit": "年",
      "defined_num": false,
      "start_num": 0,
      "step_num": 0,
      "step_price": 0,
      "max_num": 0,
      "describe": "基础模块"
    }
  ]
}
```

### 搜索产品版本

`GET /product-versions?keyword=基础&page=1&page_size=20`

---

### 新增功能模块

`POST /functional-modules`

```json
{
  "name": "采购管理",
  "depend_module": "",
  "exclusion_module": "",
  "parent_module": 0,
  "order": 1,
  "status": "1",
  "description": "采购相关功能"
}
```

### 编辑功能模块

`PATCH /functional-modules/{id}`

### 删除功能模块

`DELETE /functional-modules/{id}`

### 搜索功能模块

`GET /functional-modules?keyword=采购&page=1&page_size=20`

---

## 合同管理 API（合同签署）

### 新增合同（依赖报价单）

`POST /contracts`

```json
{
  "name": "星蝶云采购系统合同",
  "initiator": "北京星蝶云科技有限公司",
  "customer": 10001,
  "status": "1",
  "initiation_time": "2024-01-01T10:00:00Z",
  "deadline": "2024-01-10T10:00:00Z",
  "cus_people": "张三",
  "cus_phone": "13800000000",
  "order_type": "新购",
  "cus_name": "XX科技有限公司",
  "cus_address": "北京市朝阳区...",
  "cus_postal": "100000",
  "cus_email": "test@example.com",
  "num": "CT-2024-0001",
  "signdate": "2024-01-02",
  "ordernum": "PO-2024-0001",
  "type": "软件订阅",
  "originalprice": 120000.00,
  "discount": 20000.00,
  "pay_type": "银行转账",
  "bank_account": "6222...",
  "bankofdeposit": "中国银行",
  "total_amount": 100000.00,
  "big_amount": "壹拾万元整",
  "version": "基础版",
  "application_module": "采购管理,库存管理",
  "sign_order": 1,
  "document_preview": "https://.../preview/ct-2024-0001"
}
```

### 编辑合同

`PATCH /contracts/{id}`

### 查看合同

`GET /contracts/{id}`

### 查询合同

`GET /contracts?keyword=CT-2024&page=1&page_size=20`

### 删除合同

`DELETE /contracts/{id}`

---

## 相关引用说明

- 合同创建依赖报价单（`xdyai_quote`, `xdyai_quote_item`）。
- 版本设置依赖功能模块与版本关系（`xdyai_functionalmodule`, `xdyai_productfunctionrelationship`）。
- 若需要扩展到开票/回款/付款流程，可在当前模型基础上补充 `xdyai_invoice_notice`, `xdyai_invoice`, `xdyai_receipt`, `xdyai_receipt_exception`, `xdyai_payment` 相关 API。

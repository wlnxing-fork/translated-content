---
title: "Permissions-Policy: ambient-light-sensor"
slug: Web/HTTP/Reference/Headers/Permissions-Policy/ambient-light-sensor
l10n:
  sourceCommit: 0880a90f3811475d78bc4b2c344eb4146f25f66c
---

{{SeeCompatTable}}

HTTP {{HTTPHeader('Permissions-Policy')}} 标头的 `ambient-light-sensor` 指令控制当前文档是否被允许通过 {{domxref('AmbientLightSensor')}} 接口收集设备周围环境中的光亮信息。

具体来说，如果定义的策略阻止使用此特性，则 {{domxref("AmbientLightSensor.AmbientLightSensor", "AmbientLightSensor()")}} 构造函数调用将抛出类型为 `SecurityError` 的 {{domxref("DOMException")}}。

## 语法

```http
Permissions-Policy: ambient-light-sensor=<allowlist>;
```

- `<allowlist>`
  - : 允许使用该特性的来源列表。请参阅 [`Permissions-Policy` > 语法](/zh-CN/docs/Web/HTTP/Reference/Headers/Permissions-Policy#语法)了解更多详细信息。

## 默认策略

`ambient-light-sensor` 的默认允许列表为：`self`。

## 规范

{{Specifications}}

## 浏览器兼容性

{{Compat}}

## 参见

- {{HTTPHeader('Permissions-Policy')}} 标头
- [权限策略](/zh-CN/docs/Web/HTTP/Guides/Permissions_Policy)

---
title: API Reference

toc_footers:
  - <div id="language-selector">
    <a href="index.html">English</a> |
    <a href="index_zh.html">简体中文</a>
   </div>
includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the 1BitPay API
---

# 简介

欢迎使用1BitPay API，您可以使用我们的API管理交易，自动化签名，管理资产等等，更多的功能马上到来。

# Quick Start

## 创建API Key

请登录商户后台, 左侧导航打开 "ApiKey"，点击新增，创建API Key ，API Key创建成功后，可以使用API Key访问 1BitPay API。

## IP白名单

如果创建 API Key 时设置了白名单，则在调用 1BitPay API 时，只允许从您设置的 IP 白名单地址发起请求。


## 鉴权规则 *

## 基本信息

### Base URL *

  https://api.1bitpay.io

### 公共参数 *
公共请求参数是每个接口都需要使用到的请求参数，每次请求均需要携带这些参数, 才能正常发起请求。公共请求参数的首字母均为`大写`，以此区分于普通接口请求参数

参数名 | 类型 | 描述
--------- | ----------- | -----------
Nonce | Int  | 随机6位字符或数字组合

### 沙盒环境 

  https://sandbox.1bitpay.io

# C2C  *



# MPC Co-Signer

## 概述
  MPC 全称为 Security Multi-Party Computation（[安全多方计算](https://zh.wikipedia.org/wiki/安全多方计算)）。通过 MPC 可以做到从生成、使用、存储整个生命周期中，私钥可用不可见。1BitPay 使用 MPC 技术将传统的单私钥变成了分布式的私钥分片，可以有效避免单私钥带来的单点风险，实现团队多人共同管理资金，并支持社交恢复。详细的私钥管理方案可参考 [私钥管理方案](https://support.1bitpay.io/v/zh/mpcwalletcn/key-management-cn).

  MPC Co-Signer 可以允许通过API自动实现交易批准和签署交易，取代使用移动设备手动审批，非常适合频繁交易的场景，实现自动化审批及签名。

## 下载私钥分片

  根据1BitPay[私钥管理方案](https://support.1bitpay.io/v/zh/mpcwalletcn/key-management-cn)，用户创建钱包后，私钥分为三部分，分片1保存在用户设备，分片2备份到iCloud 或 Google Drive，分片3保存在1BitPay SGX可信服务器，当您需要使用 MPC Co-Signer功能时，需要在商户后台 -> ApiKey模块，申请下载私钥分片，APP确认审批后，允许下载加密后的存储在用户设备上的私钥分片1。

  <aside class="warning">
  私钥分片虽然不是完整的私钥，但是也是资产管理重要的一部分，建议专人操作，并部署在安全环境。
  </aside>

## 签名算法 *

## 获得待签名列表 *
 
## 签名 *


# MPC 钱包

MPC SaaS 钱包，马上到来！


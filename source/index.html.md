---
title: API Reference

toc_footers:
  - <div id="language-selector">
    <a href="index.html">English</a> |
    <a href="index_zh.html">简体中文</a>
   </div>
  - <br>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the 1BitPay API
---

# Introduction

Welcome to 1BitPay API, you can use our API to manage transactions, automate signatures, manage assets, etc., and more functions will come soon.

# Quick Start

## Create an API Key

Please log in to the merchant system, open "ApiKey" in the left navigation, click Add, and create an API Key. After the API Key is successfully created, you can use the API Key to access the 1BitPay API.

## IP whitelist

If you set a whitelist when creating the API Key, when calling the 1BitPay API, only requests from the IP whitelist addresses you set are allowed.

## API Authentication *

## General Info

### Base URL *

  https://api.1bitpay.io

### Public Parameters *

The public request parameters are the request parameters that each interface needs to use. Each request needs to carry these parameters in order to initiate the request normally. The initial letters of public request parameters are all `uppercase` to distinguish them from ordinary interface request parameters

Parameter | Type | Description
--------- | ----------- | -----------
Nonce | int  | Random combination of 6 characters or numbers

### Sandbox  

  https://sandbox.1bitpay.io

# C2C  *



# MPC Co-Signer

## Overview

  The full name of MPC is [Secure Multi-Party Computation](https://en.wikipedia.org/wiki/Secure_multi-party_computation). Through MPC, the private key is available and invisible throughout the entire life cycle from generation, use, and storage. 1BitPay uses MPC technology to transform the traditional single private key into distributed private key sharding, which can effectively avoid the single-point risk brought by a single private key, realize the joint management of funds by multiple people in the team, and support social recovery. For detailed private key management scheme, please refer to [Private key management](https://support.1bitpay.io/mpcwallet/key-management).

  MPC Co-Signer can allow automatic transaction approval and signing transactions through API, replacing manual approval with mobile devices. It is very suitable for frequent transaction scenarios and realizes automatic approval and signature.

## Download the piece

  According to 1BitPay [Private key management](https://support.1bitpay.io/mpcwallet/key-management), after the user creates a wallet, the private key is divided into three parts, and the fragment 1 is stored in the user Device, piece 2 is backed up to iCloud or Google Drive, piece 3 is saved in 1BitPay SGX trusted server, when you need to use the MPC Co-Signer function, you need to apply for downloading the private key piece in the merchant background -> ApiKey module, After the APP confirms the approval, it is allowed to download the encrypted private key fragment 1 stored on the user device.


  <aside class="warning">
  Although private key sharding is not a complete private key, it is also an important part of asset management. It is recommended to be operated by a dedicated person and deployed in a secure environment.
  </aside>

## Signature Algorithm *

## Get list of pending signatures *
 
## Sign *


# MPC WaaS

MPC WaaS, coming soon！

---
description: >-
  安全断言标记语言（英语：Security Assertion Markup
  Language，简称SAML，发音sam-el）是一个基于XML的开源标准数据格式，它在当事方之间交换身份验证和授权数据，尤其是在身份提供者和服务提供者之间交换。SAML
  2.0 可以实现基于网络跨域的单点登录（SSO）， 以便于减少向一个用户分发多个身份验证令牌的管理开销。
---

# 接入 SAML

## SAML 2.0 协议简介

### SAML 主体

在 SAML 协议中，涉及两个主体

Service Provider 服务提供方，简称 SP

Identity Provider 身份提供方，简称 IdP

这两个主体通过用户的浏览器进行信息交换。方式上，比如 SP 可以返回带参数的重定向，让用户立刻将信息发给 IdP。而 IdP 会返回一个  表单，同时还有一段立即提交表单的 JS 代码，从而让用户立刻将信息发给 SP。

### SAML Request

当用户的身份无法鉴定时，SP 会向 IdP 发送 SAML Request 信息（通过浏览器发送），请求 IdP 来鉴定用户身份。

SAML Request 信息形式

```text
pVLBjtMwED2DxD9EvieOkzgkVlupbIWotLDVpnDggiaOs7Xk2CHjAPv3uGlX2j2wFy4+zMx7894brxAGM4rt7E/2Xv2cFfp3b9/8GYxFsbTWZJ6scIAahYVBofBSNNvPtyJLUjFOzjvpDHkBeh0DiGry2lkS7Xdr8oOVZZ/lbRvzLOvj4n2bxZWELs4hh5rVqQSekeibmjBg1iRQBCDirPYWPVgfSimr45THaXFkqeC5KPKEVdV3Eu2CH23BL8iT96Og1DgJ5uTQC855SR0E7/Qsm+pupFzKsiqy8HSc17xo87bgGetpo+2DUY1+sHe2UdMvLRWJDlf/H7TtQv914+1lCMWn4/EQH+6aI4m2T2HcOIvzoKYr9df724tgDIpxZMlZZQAn0j5XjFfBlaw4ayWDgresLWtQHQWJZBPusjpPiiWxafN/lIPy0IGHFX3O+bRjFF+C5/3u4IyWj9FHNw3g/x0JS9hS0V3cL6NCDaDNtusmhRiiMcb9vpkUeLUmPRhUhIZdl90vv+zmLw==
```

 base64 decode + inflate 解码后

```markup
<samlp:AuthnRequest
	xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
	xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_166f23bb-522f-47b2-8cad-3a3a9190ca52" Version="2.0" IssueInstant="2019-05-04T10:53:43.188Z" Destination="https://idp1.authing.cn/oauth/saml/idp/5cc6842c68d55954b3b4521f/SingleSignOnService" ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" AssertionConsumerServiceURL="https://sp1.authing.cn/oauth/saml/sp/5cc688c851bc1a45b1b69aed/acs">
	<saml:Issuer>https://sp1.authing.cn/oauth/saml/sp/5cc688c851bc1a45b1b69aed/metadata</saml:Issuer>
	<samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" AllowCreate="false"/>
</samlp:AuthnRequest>
```

### SAML Response

IdP 收到 SAML Request 后，会弹出登录框对用户身份进行认证，将用户身份以 Assertion 的形式，发送给 SP（通过浏览器发送）。

SAML Response 信息形式

```text
PHNhbWxwOlJlc3BvbnNlIHhtbG5zOnNhbWxwPSJ1cm46b2FzaXM6bmFtZXM6dGM6U0FNTDoyLjA6cHJvdG9jb2wiIHhtbG5zOnNhbWw9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDphc3NlcnRpb24iIElEPSJhNGVhODE4OTItZTFjZC00YmJmLWJkYmQtYTYzMGM4YmQ1MmYwIiBWZXJzaW9uPSIyLjAiIElzc3VlSW5zdGFudD0iMjAxOS0wNS0wNFQxMTo1NTo1MS42NzNaIiBEZXN0aW5hdGlvbj0iaHR0cDovL2xvY2FsaG9zdDo1NTU2L29hdXRoL3NhbWwvc3AvNWNjNjg4Yzg1MWJjMWE0NWIxYjY5YWVkL2FjcyI+PHNhbWw6SXNzdWVyPmh0dHA6Ly9sb2NhbGhvc3Q6NTU1Ni9vYXV0aC9zYW1sL2lkcC81Y2M2ODQyYzY4ZDU1OTU0YjNiNDUyMWYvbWV0YWRhdGE8L3NhbWw6SXNzdWVyPjxzYW1scDpTdGF0dXM+PHNhbWxwOlN0YXR1c0NvZGUgVmFsdWU9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDpzdGF0dXM6U3VjY2VzcyIvPjwvc2FtbHA6U3RhdHVzPjxzYW1sOkFzc2VydGlvbiBJRD0iYV8yODk4NmM2My05MjExLTQyMDItOTNjMi0wZjA2MTAzYzExMGUiIFZlcnNpb249IjIuMCIgSXNzdWVJbnN0YW50PSIyMDE5LTA1LTA0VDExOjU1OjUxLjY3M1oiIHhtbG5zOnNhbWw9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDphc3NlcnRpb24iPjxzYW1sOklzc3Vlcj5odHRwOi8vbG9jYWxob3N0OjU1NTYvb2F1dGgvc2FtbC9pZHAvNWNjNjg0MmM2OGQ1NTk1NGIzYjQ1MjFmL21ldGFkYXRhPC9zYW1sOklzc3Vlcj48ZHM6U2lnbmF0dXJlIHhtbG5zOmRzPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwLzA5L3htbGRzaWcjIj48ZHM6U2lnbmVkSW5mbz48ZHM6Q2Fub25pY2FsaXphdGlvbk1ldGhvZCBBbGdvcml0aG09Imh0dHA6Ly93d3cudzMub3JnLzIwMDEvMTAveG1sLWV4Yy1jMTRuIyIvPjxkczpTaWduYXR1cmVNZXRob2QgQWxnb3JpdGhtPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxLzA0L3htbGRzaWctbW9yZSNyc2Etc2hhMjU2Ii8+PGRzOlJlZmVyZW5jZSBVUkk9IiNhXzI4OTg2YzYzLTkyMTEtNDIwMi05M2MyLTBmMDYxMDNjMTEwZSI+PGRzOlRyYW5zZm9ybXM+PGRzOlRyYW5zZm9ybSBBbGdvcml0aG09Imh0dHA6Ly93d3cudzMub3JnLzIwMDEvMTAveG1sLWV4Yy1jMTRuIyIvPjwvZHM6VHJhbnNmb3Jtcz48ZHM6RGlnZXN0TWV0aG9kIEFsZ29yaXRobT0iaHR0cDovL3d3dy53My5vcmcvMjAwMS8wNC94bWxlbmMjc2hhMjU2Ii8+PGRzOkRpZ2VzdFZhbHVlPmxobHhjeTZuVEc2eVl6NDkrbWFkejMrcEtZejhIOHlueGhHc2c2Skg2Yjg9PC9kczpEaWdlc3RWYWx1ZT48L2RzOlJlZmVyZW5jZT48L2RzOlNpZ25lZEluZm8+PGRzOlNpZ25hdHVyZVZhbHVlPlFFZEticldiNm9QR3d0UHU4cUFiNythOXFoVnlIT2JkckNxQXZUT2krOTUvaXlwbngydEdtWHVxLzNuM1dzRjNHaGYrVXIvcHFndzQwRnJlV1g2NUpncGUrWC8raFFiR0lkcUJLRTVaWkVVL3N5UU1RQWdEeWhQSGFqc1E1Y1lWaHE5WUkxMmR5MHVuZ3VoOG14ZjVYcW9qYURpNmN2cmh3R2Y1clBiTHZvWT08L2RzOlNpZ25hdHVyZVZhbHVlPjxkczpLZXlJbmZvPjxkczpYNTA5RGF0YT48ZHM6WDUwOUNlcnRpZmljYXRlPk1JSUM2RENDQWxHZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBRENCa0RFTE1Ba0dBMVVFQmhNQ1kyNHhFREFPQmdOVkJBZ01CMVJwWVc1cWFXNHhEakFNQmdOVkJBb01CV2hsWW5WME1SY3dGUVlEVlFRRERBNXNiMk5oYkdodmMzUTZOVFUxTmpFVE1CRUdBMVVFQnd3S2JYbHNiMk5oYkdsMGVURU5NQXNHQTFVRUN3d0VZV2xqY3pFaU1DQUdDU3FHU0liM0RRRUpBUllUZVdWc1pYaHBia0JvYjNSdFlXbHNMbU52YlRBZUZ3MHhPVEEwTWprd09ETTFOREphRncweU1EQTBNamd3T0RNMU5ESmFNSUdRTVFzd0NRWURWUVFHRXdKamJqRVFNQTRHQTFVRUNBd0hWR2xoYm1wcGJqRU9NQXdHQTFVRUNnd0ZhR1ZpZFhReEZ6QVZCZ05WQkFNTURteHZZMkZzYUc5emREbzFOVFUyTVJNd0VRWURWUVFIREFwdGVXeHZZMkZzYVhSNU1RMHdDd1lEVlFRTERBUmhhV056TVNJd0lBWUpLb1pJaHZjTkFRa0JGaE41Wld4bGVHbHVRR2h2ZEcxaGFXd3VZMjl0TUlHZk1BMEdDU3FHU0liM0RRRUJBUVVBQTRHTkFEQ0JpUUtCZ1FDeVo5ZHg4ejNmZkNibk0rdE5Pd2tKbDA5c3hLK0RGTDJvK0I0TUhqZTc5eEQwbjExdzAwcVlDdkVySGJUMWlYbVhKNWJrUGNjT1ArajExZ3FQWnkzdUVvSk40bUNzQzIxL3BQeC82QUwwWm1LM3k5OGszNmdZbzVCNCt1aEltM3BGSUdPVnhJdVNBaHhid0tsZXB0U0hqS0dNYzNsY3lHZlpBd1Y4RVQ4SGdRSURBUUFCbzFBd1RqQWRCZ05WSFE0RUZnUVVsMnRBNlRpRGFyQzBrRVNuUGJyeGNXbEhhWXd3SHdZRFZSMGpCQmd3Rm9BVWwydEE2VGlEYXJDMGtFU25QYnJ4Y1dsSGFZd3dEQVlEVlIwVEJBVXdBd0VCL3pBTkJna3Foa2lHOXcwQkFRc0ZBQU9CZ1FBSmphZWR5bzNTRnhQdlQyMVF0WGZ0cmUzOTlteVlrbStDc3RpNktSUWVSUldSMVlEOE5wK0EwWmFMeGRTaDJ6VnZiSGlma0NORXl1RWwzdklvVFJrUFBZa0xJT2E0b05VWnMzdDg5QXZoM254b1dBNGJiY1ZqOFVvMVpqc0o3VFJsTmdLN1NaNnpBZ1VaQ3Q5S3FZZ1k2V1ladFd1Z25pZktDTVo1T3lCQ3pBPT08L2RzOlg1MDlDZXJ0aWZpY2F0ZT48L2RzOlg1MDlEYXRhPjwvZHM6S2V5SW5mbz48L2RzOlNpZ25hdHVyZT48c2FtbDpTdWJqZWN0PjxzYW1sOk5hbWVJRCBGb3JtYXQ9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDpuYW1laWQtZm9ybWF0OnBlcnNpc3RlbnQiPmF1dGhpbmd8NWNjMzJiMjhkNmViYWUyZjI0MGE5YmZkPC9zYW1sOk5hbWVJRD48c2FtbDpTdWJqZWN0Q29uZmlybWF0aW9uIE1ldGhvZD0idXJuOm9hc2lzOm5hbWVzOnRjOlNBTUw6Mi4wOmNtOmJlYXJlciI+PHNhbWw6U3ViamVjdENvbmZpcm1hdGlvbkRhdGEgTm90T25PckFmdGVyPSIyMDE5LTA1LTA0VDEyOjAwOjUxLjY3M1oiIFJlY2lwaWVudD0iaHR0cDovL2xvY2FsaG9zdDo1NTU2L29hdXRoL3NhbWwvc3AvNWNjNjg4Yzg1MWJjMWE0NWIxYjY5YWVkL2FjcyIvPjwvc2FtbDpTdWJqZWN0Q29uZmlybWF0aW9uPjwvc2FtbDpTdWJqZWN0PjxzYW1sOkNvbmRpdGlvbnMgTm90QmVmb3JlPSIyMDE5LTA1LTA0VDExOjU1OjUxLjY3M1oiIE5vdE9uT3JBZnRlcj0iMjAxOS0wNS0wNFQxMjowMDo1MS42NzNaIj48c2FtbDpBdWRpZW5jZVJlc3RyaWN0aW9uPjxzYW1sOkF1ZGllbmNlPmh0dHA6Ly9sb2NhbGhvc3Q6NTU1Ni9vYXV0aC9zYW1sL3NwLzVjYzY4OGM4NTFiYzFhNDViMWI2OWFlZC9tZXRhZGF0YTwvc2FtbDpBdWRpZW5jZT48L3NhbWw6QXVkaWVuY2VSZXN0cmljdGlvbj48L3NhbWw6Q29uZGl0aW9ucz48c2FtbDpBdHRyaWJ1dGVTdGF0ZW1lbnQ+PHNhbWw6QXR0cmlidXRlIE5hbWU9ImVtYWlsIiBOYW1lRm9ybWF0PSJ1cm46b2FzaXM6bmFtZXM6dGM6U0FNTDoyLjA6YXR0cm5hbWUtZm9ybWF0OmJhc2ljIj48c2FtbDpBdHRyaWJ1dGVWYWx1ZSB4bWxuczp4cz0iaHR0cDovL3d3dy53My5vcmcvMjAwMS9YTUxTY2hlbWEiIHhtbG5zOnhzaT0iaHR0cDovL3d3dy53My5vcmcvMjAwMS9YTUxTY2hlbWEtaW5zdGFuY2UiIHhzaTp0eXBlPSJ4czpzdHJpbmciPnRlc3QxQDEyMy5jb208L3NhbWw6QXR0cmlidXRlVmFsdWU+PC9zYW1sOkF0dHJpYnV0ZT48c2FtbDpBdHRyaWJ1dGUgTmFtZT0icGhvdG8iIE5hbWVGb3JtYXQ9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDphdHRybmFtZS1mb3JtYXQ6YmFzaWMiPjxzYW1sOkF0dHJpYnV0ZVZhbHVlIHhtbG5zOnhzPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYSIgeG1sbnM6eHNpPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYS1pbnN0YW5jZSIgeHNpOnR5cGU9InhzOnN0cmluZyI+aHR0cHM6Ly91c2VyY29udGVudHMuYXV0aGluZy5jbi9hdXRoaW5nLWF2YXRhci5wbmc8L3NhbWw6QXR0cmlidXRlVmFsdWU+PC9zYW1sOkF0dHJpYnV0ZT48c2FtbDpBdHRyaWJ1dGUgTmFtZT0idXNlcm5hbWUiIE5hbWVGb3JtYXQ9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDphdHRybmFtZS1mb3JtYXQ6YmFzaWMiPjxzYW1sOkF0dHJpYnV0ZVZhbHVlIHhtbG5zOnhzPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYSIgeG1sbnM6eHNpPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYS1pbnN0YW5jZSIgeHNpOnR5cGU9InhzOnN0cmluZyI+dGVzdDE8L3NhbWw6QXR0cmlidXRlVmFsdWU+PC9zYW1sOkF0dHJpYnV0ZT48c2FtbDpBdHRyaWJ1dGUgTmFtZT0ibmlja25hbWUiIE5hbWVGb3JtYXQ9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDphdHRybmFtZS1mb3JtYXQ6YmFzaWMiPjxzYW1sOkF0dHJpYnV0ZVZhbHVlIHhtbG5zOnhzPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYSIgeG1sbnM6eHNpPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYS1pbnN0YW5jZSIgeHNpOnR5cGU9InhzOnN0cmluZyIvPjwvc2FtbDpBdHRyaWJ1dGU+PHNhbWw6QXR0cmlidXRlIE5hbWU9ImlkZW50aWZpZXIiIE5hbWVGb3JtYXQ9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDphdHRybmFtZS1mb3JtYXQ6YmFzaWMiPjxzYW1sOkF0dHJpYnV0ZVZhbHVlIHhtbG5zOnhzPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYSIgeG1sbnM6eHNpPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYS1pbnN0YW5jZSIgeHNpOnR5cGU9InhzOnN0cmluZyI+YXV0aGluZ3w1Y2MzMmIyOGQ2ZWJhZTJmMjQwYTliZmQ8L3NhbWw6QXR0cmlidXRlVmFsdWU+PC9zYW1sOkF0dHJpYnV0ZT48c2FtbDpBdHRyaWJ1dGUgTmFtZT0icHJvdmlkZXIiIE5hbWVGb3JtYXQ9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDphdHRybmFtZS1mb3JtYXQ6YmFzaWMiPjxzYW1sOkF0dHJpYnV0ZVZhbHVlIHhtbG5zOnhzPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYSIgeG1sbnM6eHNpPSJodHRwOi8vd3d3LnczLm9yZy8yMDAxL1hNTFNjaGVtYS1pbnN0YW5jZSIgeHNpOnR5cGU9InhzOnN0cmluZyI+YXV0aGluZzwvc2FtbDpBdHRyaWJ1dGVWYWx1ZT48L3NhbWw6QXR0cmlidXRlPjwvc2FtbDpBdHRyaWJ1dGVTdGF0ZW1lbnQ+PC9zYW1sOkFzc2VydGlvbj48L3NhbWxwOlJlc3BvbnNlPg==
```

base64 decode + inflate 解码后

```markup
<samlp:Response xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
  xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="a4ea81892-e1cd-4bbf-bdbd-a630c8bd52f0" Version="2.0" IssueInstant="2019-05-04T11:55:51.673Z" Destination="https://sp1.authing.cn/oauth/saml/sp/5cc688c851bc1a45b1b69aed/acs">
  <saml:Issuer>https://idp1.authing.cn/oauth/saml/idp/5cc6842c68d55954b3b4521f/metadata</saml:Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
  </samlp:Status>
  <saml:Assertion ID="a_28986c63-9211-4202-93c2-0f06103c110e" Version="2.0" IssueInstant="2019-05-04T11:55:51.673Z" 
    xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion">
    <saml:Issuer>https://idp1.authing.cn/oauth/saml/idp/5cc6842c68d55954b3b4521f/metadata</saml:Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
        <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256"/>
        <ds:Reference URI="#a_28986c63-9211-4202-93c2-0f06103c110e">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256"/>
          <ds:DigestValue>lhlxcy6nTG6yYz49+madz3+pKYz8H8ynxhGsg6JH6b8=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>QEdKbrWb6oPGwtPu8qAb7+a9qhVyHObdrCqAvTOi+95/iypnx2tGmXuq/3n3WsF3Ghf+Ur/pqgw40FreWX65Jgpe+X/+hQbGIdqBKE5ZZEU/syQMQAgDyhPHajsQ5cYVhq9YI12dy0unguh8mxf5XqojaDi6cvrhwGf5rPbLvoY=</ds:SignatureValue>
      <ds:KeyInfo>
        <ds:X509Data>
          <ds:X509Certificate>MIIC6DCCAlGgAwIBAgIBADANBgkqhkiG9w0BAQsFADCBkDELMAkGA1UEBhMCY24xEDAOBgNVBAgMB1RpYW5qaW4xDjAMBgNVBAoMBWhlYnV0MRcwFQYDVQQDDA5sb2NhbGhvc3Q6NTU1NjETMBEGA1UEBwwKbXlsb2NhbGl0eTENMAsGA1UECwwEYWljczEiMCAGCSqGSIb3DQEJARYTeWVsZXhpbkBob3RtYWlsLmNvbTAeFw0xOTA0MjkwODM1NDJaFw0yMDA0MjgwODM1NDJaMIGQMQswCQYDVQQGEwJjbjEQMA4GA1UECAwHVGlhbmppbjEOMAwGA1UECgwFaGVidXQxFzAVBgNVBAMMDmxvY2FsaG9zdDo1NTU2MRMwEQYDVQQHDApteWxvY2FsaXR5MQ0wCwYDVQQLDARhaWNzMSIwIAYJKoZIhvcNAQkBFhN5ZWxleGluQGhvdG1haWwuY29tMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCyZ9dx8z3ffCbnM+tNOwkJl09sxK+DFL2o+B4MHje79xD0n11w00qYCvErHbT1iXmXJ5bkPccOP+j11gqPZy3uEoJN4mCsC21/pPx/6AL0ZmK3y98k36gYo5B4+uhIm3pFIGOVxIuSAhxbwKleptSHjKGMc3lcyGfZAwV8ET8HgQIDAQABo1AwTjAdBgNVHQ4EFgQUl2tA6TiDarC0kESnPbrxcWlHaYwwHwYDVR0jBBgwFoAUl2tA6TiDarC0kESnPbrxcWlHaYwwDAYDVR0TBAUwAwEB/zANBgkqhkiG9w0BAQsFAAOBgQAJjaedyo3SFxPvT21QtXftre399myYkm+Csti6KRQeRRWR1YD8Np+A0ZaLxdSh2zVvbHifkCNEyuEl3vIoTRkPPYkLIOa4oNUZs3t89Avh3nxoWA4bbcVj8Uo1ZjsJ7TRlNgK7SZ6zAgUZCt9KqYgY6WYZtWugnifKCMZ5OyBCzA==</ds:X509Certificate>
        </ds:X509Data>
      </ds:KeyInfo>
    </ds:Signature>
    <saml:Subject>
      <saml:NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">authing|5cc32b28d6ebae2f240a9bfd</saml:NameID>
      <saml:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <saml:SubjectConfirmationData NotOnOrAfter="2019-05-04T12:00:51.673Z" Recipient="https://sp1.authing.cn/oauth/saml/sp/5cc688c851bc1a45b1b69aed/acs"/>
      </saml:SubjectConfirmation>
    </saml:Subject>
    <saml:Conditions NotBefore="2019-05-04T11:55:51.673Z" NotOnOrAfter="2019-05-04T12:00:51.673Z">
      <saml:AudienceRestriction>
        <saml:Audience>https://sp1.authing.cn/oauth/saml/sp/5cc688c851bc1a45b1b69aed/metadata</saml:Audience>
      </saml:AudienceRestriction>
    </saml:Conditions>
    <saml:AttributeStatement>
      <saml:Attribute Name="email" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
        <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">test1@123.com				
        </saml:AttributeValue>
      </saml:Attribute>
      <saml:Attribute Name="photo" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
        <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">https://usercontents.authing.cn/authing-avatar.png
        </saml:AttributeValue>
      </saml:Attribute>
      <saml:Attribute Name="username" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
        <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">test1	
        </saml:AttributeValue>
      </saml:Attribute>
      <saml:Attribute Name="nickname" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
        <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string"/>
      </saml:Attribute>
      <saml:Attribute Name="identifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
        <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">authing|5cc32b28d6ebae2f240a9bfd
        </saml:AttributeValue>
      </saml:Attribute>
      <saml:Attribute Name="provider" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:basic">
        <saml:AttributeValue xmlns:xs="http://www.w3.org/2001/XMLSchema" 
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">authing			
        </saml:AttributeValue>
      </saml:Attribute>
    </saml:AttributeStatement>
  </saml:Assertion>
</samlp:Response>
```

读到这里，你可能会对 SP、IdP 如何处理这些冗长的 xml 信息感到困惑。Authing 会解决这些繁琐的处理，而你只需关注 Authing 返回的 JWT Token。以及使用 Authing SDK 检验一个 Authing JWT Token 的有效性。


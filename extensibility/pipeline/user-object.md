---
description: user 对象中保存了当前用户的各种数据，以及用于添加自定义字段、自定义 token 字段的方法。
---

# user 对象

{% hint style="warning" %}
Pre-Register（注册前） Pipeline 中没有 user  对象。
{% endhint %}

## 属性 <a id="variables"></a>

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x5C5E;&#x6027;&#x540D;</th>
      <th style="text-align:left">&#x503C;&#x7C7B;&#x578B;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">_id</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x7528;&#x6237; ID</td>
    </tr>
    <tr>
      <td style="text-align:left">username</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x7528;&#x6237;&#x540D;</td>
    </tr>
    <tr>
      <td style="text-align:left">email</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x90AE;&#x7BB1;</td>
    </tr>
    <tr>
      <td style="text-align:left">emailVerified</td>
      <td style="text-align:left">boolean</td>
      <td style="text-align:left">&#x90AE;&#x7BB1;&#x662F;&#x5426;&#x5DF2;&#x9A8C;&#x8BC1;</td>
    </tr>
    <tr>
      <td style="text-align:left">phone</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x624B;&#x673A;&#x53F7;</td>
    </tr>
    <tr>
      <td style="text-align:left">phoneVerified</td>
      <td style="text-align:left">boolean</td>
      <td style="text-align:left">&#x624B;&#x673A;&#x53F7;&#x662F;&#x5426;&#x5DF2;&#x9A8C;&#x8BC1;</td>
    </tr>
    <tr>
      <td style="text-align:left">photo</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5934;&#x50CF;&#x94FE;&#x63A5;</td>
    </tr>
    <tr>
      <td style="text-align:left">nickname</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x6635;&#x79F0;</td>
    </tr>
    <tr>
      <td style="text-align:left">gender</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x6027;&#x522B;</td>
    </tr>
    <tr>
      <td style="text-align:left">signedUp</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x6CE8;&#x518C;&#x65F6;&#x95F4;&#xFF0C;&#x683C;&#x5F0F;&#x4E3A;</p>
        <p><code>2020-02-07T04:29:40.877Z</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">lastLogin</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x4E0A;&#x6B21;&#x767B;&#x5F55;&#x65F6;&#x95F4;&#xFF0C;&#x683C;&#x5F0F;&#x4E3A;</p>
        <p><code>2020-02-07T04:29:40.877Z</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">oauth</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x793E;&#x4F1A;&#x5316;&#x767B;&#x5F55;&#x4FE1;&#x606F;</td>
    </tr>
    <tr>
      <td style="text-align:left">registerMethod</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x6CE8;&#x518C;&#x65B9;&#x5F0F;</td>
    </tr>
    <tr>
      <td style="text-align:left">blocked</td>
      <td style="text-align:left">boolean</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x88AB; block</td>
    </tr>
    <tr>
      <td style="text-align:left">company</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x516C;&#x53F8;&#x540D;</td>
    </tr>
    <tr>
      <td style="text-align:left">browser</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x8BBF;&#x95EE;&#x6D4F;&#x89C8;&#x5668;</td>
    </tr>
    <tr>
      <td style="text-align:left">device</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x8BBF;&#x95EE;&#x8BBE;&#x5907;</td>
    </tr>
    <tr>
      <td style="text-align:left">country</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x56FD;&#x5BB6;</td>
    </tr>
    <tr>
      <td style="text-align:left">region</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5730;&#x533A;</td>
    </tr>
    <tr>
      <td style="text-align:left">address</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5730;&#x5740;</td>
    </tr>
  </tbody>
</table>## 方法 <a id="methods"></a>

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x65B9;&#x6CD5;&#x540D;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
      <th style="text-align:left">&#x793A;&#x4F8B;&#x4EE3;&#x7801;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">addMetaData</td>
      <td style="text-align:left">&#x6DFB;&#x52A0;&#x81EA;&#x5B9A;&#x4E49; metadata &#x5230; user &#x5BF9;&#x8C61;&#x3002;&#x4E0D;&#x4F1A;&#x6301;&#x4E45;&#x6027;&#x4FDD;&#x5B58;&#x81F3;&#x6570;&#x636E;&#x5E93;&#xFF0C;<b>&#x53EA;&#x5BF9;&#x5F53;&#x524D;&#x8BF7;&#x6C42;&#x6709;&#x6548;</b>&#x3002;&#x5F00;&#x53D1;&#x8005;&#x53EF;&#x7528;&#x4E8E;&#x6DFB;&#x52A0;&#x4E00;&#x6B21;&#x6027;&#x7684;&#x6570;&#x636E;&#x3002;&#x6709;&#x5173;&#x5982;&#x4F55;&#x83B7;&#x53D6;&#x3001;&#x7BA1;&#x7406;&#x7528;&#x6237;
        metadata&#xFF0C;&#x8BF7;&#x89C1; <a href="../../scan-qrcode/app-qrcode/">&#x7BA1;&#x7406;&#x7528;&#x6237; MetaData</a>&#x3002;</td>
      <td
      style="text-align:left">user.addMetaData(&quot;KEY&quot;, &quot;VALUE&quot;)</td>
    </tr>
    <tr>
      <td style="text-align:left">addMetaDataAndPersist</td>
      <td style="text-align:left">&#x6DFB;&#x52A0;&#x81EA;&#x5B9A;&#x4E49; metadata &#x5230; user &#x5BF9;&#x8C61;&#xFF0C;<b>&#x540C;&#x65F6;&#x6301;&#x4E45;&#x5316;&#x4FDD;&#x5B58;&#x81F3;&#x6570;&#x636E;&#x5E93;</b>&#x3002;VALUE
        &#x957F;&#x5EA6;&#x4E0D;&#x80FD;&#x8D85;&#x8FC7; 1000 &#x5B57;&#x7B26;&#xFF0C;&#x5426;&#x5219;&#x4F1A;&#x88AB;&#x4E22;&#x5F03;&#x3002;&#x6709;&#x5173;&#x5982;&#x4F55;&#x83B7;&#x53D6;&#x3001;&#x7BA1;&#x7406;&#x7528;&#x6237;
        metadata&#xFF0C;&#x8BF7;&#x89C1; <a href="../../scan-qrcode/app-qrcode/">&#x7BA1;&#x7406;&#x7528;&#x6237; MetaData</a>&#x3002;</td>
      <td
      style="text-align:left">user.addMetaDataAndPersist(&quot;KEY&quot;, &quot;VALUE&quot;)</td>
    </tr>
    <tr>
      <td style="text-align:left">setTokenField</td>
      <td style="text-align:left">
        <p>&#x5F80;&#x7528;&#x6237;&#x7684; token &#x4E2D;&#x52A0;&#x5165;&#x81EA;&#x5B9A;&#x4E49;&#x5B57;&#x6BB5;&#x3002;VALUE
          &#x957F;&#x5EA6;&#x4E0D;&#x80FD;&#x8D85;&#x8FC7; 100 &#x5B57;&#x7B26;&#xFF0C;&#x5426;&#x5219;&#x4F1A;&#x5BFC;&#x81F4;
          token &#x8FC7;&#x957F;&#x3002;&#x4E86;&#x89E3;&#x5982;&#x4F55;&#x68C0;&#x9A8C;&#x3001;&#x89E3;&#x5BC6;
          token&#xFF0C;&#x8BF7;&#x89C1;<a href="../../advanced/verify-jwt-token.md">&#x9A8C;&#x8BC1; Token</a>&#x3002;<b>&#x7531;&#x4E8E; graphql &#x9650;&#x5236;&#xFF0C;&#x5982;&#x679C;&#x4F60;&#x7684; token &#x4E2D;&#x52A0;&#x5165;&#x4E86;&#x81EA;&#x5B9A;&#x4E49;&#x5B57;&#x6BB5;&#xFF0C;&#x8BF7;&#x52A1;&#x5FC5;&#x4F7F;&#x7528; </b>
          <a
          href="https://users.authing.cn/authing/token"><b>https://users.authing.cn/authing/token</b>
            </a><b> &#x63A5;&#x53E3;&#xFF0C;&#x5426;&#x5219;&#x65E0;&#x6CD5;&#x83B7;&#x53D6;&#x5230;&#x81EA;&#x5B9A;&#x4E49;&#x5B57;&#x6BB5;&#x3002;</b>
        </p>
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p><b>&#x6B64;&#x63A5;&#x53E3;&#x4EC5;&#x5728; POST_AUTHENTICATION&#xFF08;&#x767B;&#x5F55;&#x540E;&#xFF09; Pipeline &#x4E2D;&#x53EF;&#x7528;&#x3002;</b>
        </p>
      </td>
      <td style="text-align:left">user.setTokenField(&apos;KEY&apos;, &apos;VALUE&apos;)</td>
    </tr>
    <tr>
      <td style="text-align:left">removeTokenField</td>
      <td style="text-align:left">&#x5220;&#x9664;&#x7528;&#x6237; token &#x4E2D;&#x7684;&#x5B57;&#x6BB5;&#x3002;<b>&#x8BF7;&#x52FF;&#x5220;&#x9664; id &#x548C; clientId &#x5B57;&#x6BB5;&#xFF0C;&#x5426;&#x5219; token &#x5C06;&#x65E0;&#x6CD5;&#x88AB;&#x6210;&#x529F;&#x9A8C;&#x8BC1;&#xFF01;</b>
      </td>
      <td style="text-align:left">user.removeTokenField(&apos;KEY&apos;)</td>
    </tr>
  </tbody>
</table>


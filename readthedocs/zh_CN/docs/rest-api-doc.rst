WeIdentity RestService API 说明文档
=====================================

1. 总体介绍
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

每个API的入参都是满足以下格式的json字符串：

.. code-block:: java

    {
        "functionArg": 随调用SDK方法而变的入参json字符串 {
            ...
        },
        "transactionArg": 交易参数json字符串 {
            "invokerWeId": 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥（非必须）
        }
        "functionName": 调用SDK方法名
        "v": API版本号
    }

参数说明：

* functionArg是随不同的SDK调用方法而变的，具体的参数可以查看SDK接口文档；后文会为每个所提及的接口给出对应的链接
* transactionArg仅包括一个变量invokerWeId，由传入方决定使用在服务器端托管的具体哪个WeIdentity DID所对应的私钥
    * 非必需，只有在那些需要通过私钥写交易签名的方法才会需要；后文中详细说明
* functionName是调用的SDK方法名，用于决定具体调用什么功能
* v是调用的API方法版本

每个API的接口返回都是满足以下格式的json字符串：

.. code-block:: java

    {
        "respBody": 随调用SDK方法而变的输出值json字符串 {
        }
        "ErrorCode": 错误码
        "ErrorMessage": 错误信息，成功时为"success"
    }


其中具体的输出值result亦是随不同的SDK调用方法而变的。

在后文中，我们将会逐一说明目前所提供的功能及其使用方式。

2. 代理创建WeIdentity DID
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - createWeIdForAuthority
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.publicKey
     - 创建的WeIdentity DID对应的公钥，会以此创建WeID
     - Y
   * - functionArg.orgId
     - 需注册的机构名
     - Y
   * - transactionArg
     - 
     - Y
   * - transactionArg.invokerWeId
     - 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥，如admin。
     - Y
   * - v
     - 版本号
     - Y

.. note::
  本接口的公钥会被记录在链上，具体由invokerWeId（一般为admin）的私钥帮忙创建此WeID上链。

接口入参示例：

.. code-block:: java

    {
        "functionArg": {
            "publicKey": "712679236821355231513532168231727831978932132185632517152735621683128"
        },
        "transactionArg": {
            "invokerWeId": "admin"
        },
        "functionName": "createWeIdForAuthority",
        "v": "1.0.0"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - WeIdentity DID

返回示例：

.. code-block:: java

    {
        "ErrorCode": 0,
        "ErrorMessage": "success",
        "respBody": "did:weid:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
    }

3. 根据WeID获取WeIdentity DID Document
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - getWeIdDocument
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.weId
     - WeIdentity DID，与SDK直接调用的方式入参要求一致，下同
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参示例：

.. code-block:: java

    {
        "functionArg": {
            "weId": "did:weid:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
        },
        "transactionArg": {
        },
        "functionName": "getWeIdDocument",
        "v": "1.0.0"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - WeIdentity DID Document

返回示例：

.. code-block:: java

    {
        "respBody": {
            "@context" : "https://w3id.org/did/v1",
            "id" : "did:weid:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
            "created" : 1553224394993,
            "updated" : 1553224394993,
            "publicKey" : [ ],
            "authentication" : [ ],
            "service" : [ ]
        },
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }

4. 更新WeIdentity DID公钥
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - updateWeIdPublicKey
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.publicKey
     - 挂载新的公钥给WeID上
     - Y
   * - functionArg.weId
     - 需要被挂载的WeID
     - Y
   * - transactionArg
     - 
     - Y
   * - v
     - 版本号
     - Y

.. note::
  本接口会为WeID挂载新的公钥

接口入参示例：

.. code-block:: java

    {
        "functionArg": {
            "weId": "did:weid:0xef53ad212a2de77fee518b4914b8579a40c604be",
            "publicKey": "632679236821355231517342168231727831978932132185632517152735621685128"
        },
        "transactionArg": {
        },
        "functionName": "updateWeIdPublicKey",
        "v": "1.0.0"
    }


接口返回: application/json


.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - WeIdentity DID

返回示例：

.. code-block:: java

    {
        "ErrorCode": 0,
        "ErrorMessage": "success",
        "respBody": "did:weid:0xef53ad212a2de77fee518b4914b8579a40c604be"
    }

5. 创建AuthorityIssuer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json


接口入参：


.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - registerAuthorityIssuer
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.weId
     - WeIdentity DID，与SDK直接调用的方式入参一致，下同
     - Y
   * - functionArg.name
     - 机构名
     - Y
   * - transactionArg
     - 
     - Y
   * - transactionArg.invokerWeId
     - 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥。注意：如果在这里填入了预先定义在application.properties里的暗语，则可确保有足够的权限。
     - Y
   * - v
     - 版本号
     - Y

接口调用示例：

.. code-block:: java

    {
        "functionArg": {
            "weid": "did:weid:0x1Ae5b88d37327830307ab8da0ec5D8E8692A35D3",
            "name": "Sample College"
        },
        "transactionArg": {
            "invokerWeId": "did:weid:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
        },
        "functionName": "registerAuthorityIssuer",
        "v": "1.0.0"
    }


接口返回: application/json


.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - True/False

返回示例：

.. code-block:: java

    {
        "ErrorCode": 0,
        "ErrorMessage": "success",
        "respBody": True
    }

6. 查询AuthorityIssuer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - queryAuthorityIssuer
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.weId
     - WeIdentity DID，与SDK直接调用的方式入参一致，下同
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参示例：

.. code-block:: java

    {
        "functionArg": {
            "weId": "did:weid:0x1ae5b88d37327830307ab8da0ec5d8e8692a35d3"
        },
        "transactionArg": {
        },
        "functionName": "queryAuthorityIssuer",
        "v": "1.0.0"
    }

接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - 完整的Authority Issuer信息


.. code-block:: java

    {
        "respBody": {
            "accValue": ,
            "created": 16845611984115,
            "name": "Sample College",
            "weid": "did:weid:0x1ae5b88d37327830307ab8da0ec5d8e8692a35d3"
        }
        "ErrorCode": 0
        "ErrorMessage": "success"
    }


7. 根据Authority Issuer机构名获取WeIdentity DID Document
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - getWeIdDocument
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.orgId
     - 机构名，与Authority Issuer的name一致
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参示例：

.. code-block:: java

    {
        "functionArg": {
            "orgId": "Sample College"
        },
        "transactionArg": {
        },
        "functionName": "getWeIdDocument",
        "v": "1.0.0"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - WeIdentity DID Document

返回示例：

.. code-block:: java

    {
        "respBody": {
            "@context" : "https://w3id.org/did/v1",
            "id" : "did:weid:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
            "created" : 1553224394993,
            "updated" : 1553224394993,
            "publicKey" : [ ],
            "authentication" : [ ],
            "service" : [ ]
        },
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }

8. 创建CredentialPojo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json

接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - createCredentialPojo
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.claim
     - claim Json结构体，与SDK直接调用的方式入参一致，下同     
     - Y
   * - functionArg.cptId
     - CPT ID
     - Y
   * - functionArg.issuer
     - issuer WeIdentity DID
     - Y
   * - functionArg.expirationDate
     - 过期时间（使用UTC格式）
     - Y
   * - transactionArg
     - 
     - Y
   * - transactionArg.invokerWeId
     - 用于索引私钥的WeIdentity DID，服务器端会凭此找到所托管的私钥
     - Y
   * - v
     - 版本号
     - Y

接口入参：Json，以signature代替私钥

.. code-block:: java

    {
        "functionArg": {
            "cptId": 10,
            "issuer": "did:weid:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
            "expirationDate": "2019-04-18T21:12:33Z",
            "claim": {
                "name": "zhang san",
                "gender": "F",
                "age": 18
            },
        },
        "transactionArg": {
            "invokerWeId": "did:weid:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
        },
        "functionName": "createCredentialPojo",
        "v": "1.0.0"
    }

接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - 完整的CredentialPojo信息


接口返回示例:

.. code-block:: java

    {
      "respBody": {
          "cptId": 2000156,
          "issuanceDate": 1580996777,
          "context": "https://github.com/WeBankFinTech/WeIdentity/blob/master/context/v1",
          "claim": {
              "content": "b1016358-cf72-42be-9f4b-a18fca610fca",
              "receiver": "did:weid:101:0x7ed16eca3b0737227bc986dd0f2851f644cf4754",
              "weid": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
          },
          "id": "21d10ab1-75fe-4733-9f1d-f0bad71b5922",
          "proof": {
              "created": 1580996777,
              "creator": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa#keys-0",
              "salt": {
                  "content": "ncZ5F",
                  "receiver": "L0c40",
                  "weid": "I4aop"
              },
              "signatureValue": "HEugP13uDVBg2G0kmmwbTkQXobsrWNqtGQJW6BoHU2Q2VQpwVhK382dArRMFN6BDq7ogozYBRC15QR8ueX5G3t8=",
              "type": "Secp256k1"
          },
          "type": [
              "VerifiableCredential",
              "hashTree"
          ],
          "issuer": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
          "expirationDate": 4111737153
      },
      "errorCode": 0,
      "errorMessage": "success"
    }

9. 验证CredentialPojo
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json


接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - verifyCredentialPojo
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.claim
     - claim Json 结构体，与SDK直接调用的方式入参要求一致，下同
     - Y
   * - functionArg.cptId
     - CPT ID
     - Y
   * - functionArg.context
     - context值
     - Y
   * - functionArg.uuid
     - CredentialPojo的UUID
     - Y
   * - functionArg.issuer
     - issuer WeIdentity DID
     - Y
   * - functionArg.issuranceDate
     - 颁发时间
     - Y
   * - functionArg.expirationDate
     - 过期时间
     - Y
   * - functionArg.proof
     - Credential签名值
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

接口入参：

.. code-block:: java

    {
        "functionArg": {
          "cptId": 2000156,
          "issuanceDate": 1580996777,
          "context": "https://github.com/WeBankFinTech/WeIdentity/blob/master/context/v1",
          "claim": {
              "content": "b1016358-cf72-42be-9f4b-a18fca610fca",
              "receiver": "did:weid:101:0x7ed16eca3b0737227bc986dd0f2851f644cf4754",
              "weid": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa"
          },
          "id": "21d10ab1-75fe-4733-9f1d-f0bad71b5922",
          "proof": {
              "created": 1580996777,
              "creator": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa#keys-0",
              "salt": {
                  "content": "ncZ5F",
                  "receiver": "L0c40",
                  "weid": "I4aop"
              },
              "signatureValue": "HEugP13uDVBg2G0kmmwbTkQXobsrWNqtGQJW6BoHU2Q2VQpwVhK382dArRMFN6BDq7ogozYBRC15QR8ueX5G3t8=",
              "type": "Secp256k1"
          },
          "type": [
              "VerifiableCredential",
              "hashTree"
          ],
          "issuer": "did:weid:101:0xfd28ad212a2de77fee518b4914b8579a40c601fa",
          "expirationDate": 4111737153
        },
        "transactionArg": {
        },
        "functionName": "verifyCredentialPojo"
        "v": "1.0.0"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - True/False


接口返回：

.. code-block:: java

    {
        "respBody": true,
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }

10. 验证LiteCredential
^^^^^^^^^^^^^^^^^^^^^^^^^^^

调用接口：

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - 标题
     - 描述
   * - 接口名
     - weid/api/invoke
   * - Method
     - POST
   * - Content-Type
     - application/json


接口入参：

.. list-table::
   :header-rows: 1
   :widths: 30 60 20

   * - Key
     - Value
     - Required
   * - functionName
     - verifyLiteCredential
     - Y
   * - functionArg
     - 
     - Y
   * - functionArg.id
     - 凭证ID
     - Y
   * - transactionArg
     - 
     - N，传空
   * - v
     - 版本号
     - Y

.. note::
    传入Credential Id（不传credential原文），获取链上存证的接口。存证中包括签名。

调用示例：

.. code-block:: java

    {
        "functionArg": {
            "id": "21d10ab1-75fe-4733-9f1d-f0bad71b5922"
        },
        "transactionArg": {
        },
        "functionName": "verifyLiteCredential"
        "v": "1.0.0"
    }


接口返回: application/json

.. list-table::
   :header-rows: 1
   :widths: 30 50

   * - Key
     - Value
   * - ErrorCode
     - 错误码，0表示成功
   * - ErrorMessage
     - 错误信息
   * - respBody
     - True/False


接口返回：

.. code-block:: java

    {
        "respBody": "HJPbDmoi39xgZBGi/aj1zB6VQL5QLyt4qTV6GOvQwzfgUJEZTazKZXe1dRg5aCt8Q44GwNF2k+l1rfhpY1hc/ls=",
        "ErrorCode": 0,
        "ErrorMessage": "success"
    }
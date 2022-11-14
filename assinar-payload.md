# Como Assinar o Payload

No contexto da API Payment Initiation, os payloads de mensagem de consentimento e de pagamento que trafegam tanto por parte da instituição iniciadora de transação de pagamento (ITP) quanto por parte da instituição detentora de conta devem estar assinados. Abaixo temos as orientações para assinatura das mensagens JWS.

JWS é o acrônimo de JSON Web Signature, um JWS é formado a partir de um JWT (JSON Web Token) assinado. 

Informações complementares de segurança podem ser consultadas em [https://github.com/OpenBanking-Brasil/specs-seguranca/blob/main/open-banking-brasil-financial-api-1_ID3-ptbr.md](https://github.com/OpenBanking-Brasil/specs-seguranca/blob/main/open-banking-brasil-financial-api-1_ID3-ptbr.md).

## **Passo 1** - Identifique a chave privada e o certificado de assinatura correspondente a serem usados para assinatura:

As assinaturas devem ser realizadas com uso do certificado digital de assinatura especificado no [Padrão de Certificados Open Finance Brasil](https://github.com/OpenBanking-Brasil/specs-seguranca/blob/main/open-banking-brasil-certificate-standards-1_ID1-ptbr.md#certificado-de-assinatura-certificadoassinatura "https://github.com/OpenBanking-Brasil/specs-seguranca/blob/main/open-banking-brasil-certificate-standards-1_ID1-ptbr.md#certificado-de-assinatura-certificadoassinatura").

O certificado de assinatura deve ser válido no momento da criação do JWS.

## **Passo 2** - Geração do JOSE Header

O JOSE Header deve conter os seguintes campos:

---

| Nome | Tipo   | Obrigatório | Descrição                                                                                               |
| ---- | ------ | ----------- | ------------------------------------------------------------------------------------------------------- |
| alg  | string | true        | O algoritmo que será usado para assinar o JWS. Deve ser preenchido com o valor&nbsp;PS256.              |
| kid  | string | true        | Deve ser obrigatoriamente preenchido com o valor do identificador da chave utilizado para a assinatura. |
| typ  | string | true        | É o tipo de conteúdo usado para trafegar mensagens na API. Deve ser preenchido com o valor&nbsp;JWT.    |

---

## **Passo 3** - Montando a mensagem JWS

Para garantir a integridade e o não-repúdio das informações tramitadas em *API´s sensíveis e que indicam essa necessidade na sua documentação*, deve ser adotado a estrutura no padrão JWS definida na \[[RFC7515](https://datatracker.ietf.org/doc/html/rfc7515 "https://datatracker.ietf.org/doc/html/rfc7515")\] e que inclui:

1. **Cabeçalho** (_JSON Object Signing and Encryption – JOSE Header_), onde se define o algoritmo utilizado e inclui informações sobre a chave pública ou certificado que podem ser utilizadas para validar a assinatura;
2. **Payload** (_JWS Payload_): conteúdo propriamente dito e detalhado na especificação da API além de informações sobre `claims` JWT;
3. **Assinatura digital** (_JWS Signature_): assinatura digital, realizada conforme parâmetros do cabeçalho.

O payload das mensagens (requisição e resposta *JWT*) assinadas devem incluir as seguintes `claims` presentes na [RFC7519](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1 "https://datatracker.ietf.org/doc/html/rfc7519#section-4.1"):

---

| Nome | Tipo   | Obrigatório | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ---- | ------ | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| aud  | string | true        | (requisição JWT): o Provedor do Recurso (p. ex. a instituição Detentora da Conta) deverá validar se o valor do campo&nbsp;aud&nbsp;coincide com o endpoint sendo acionado.(resposta JWT): o cliente da API (p. ex. instituição Iniciadora) deverá validar se o valor do campo&nbsp;aud&nbsp;coincide com o seu próprio&nbsp;organisationId&nbsp;listado no diretório.                                                                                                                                                                    |
| iss  | string | true        | (requisição JWT&nbsp;e&nbsp;resposta JWT): o receptor da mensagem deverá validar se o valor do campo&nbsp;iss&nbsp;coincide com o&nbsp;organisationId&nbsp;da outra parte listado no diretório.                                                                                                                                                                                                                                                                                                                                          |
| jti  | string | true        | (***requisição JWT*** e ***resposta JWT***): o valor do campo **jti** deverá ser preenchido com o UUID definido pela instituição de acordo com a [RFC 4122](https://datatracker.ietf.org/doc/html/rfc4122 "https://datatracker.ietf.org/doc/html/rfc4122") usando o versão 4.                                                                                                                                                                                                                                                            |
| iat  | string | true        | (***requisição JWT*** e ***resposta JWT***): o valor do campo **iat** deverá ser preenchido com o horário da geração da mensagem e de acordo com o padrão estabelecido na [RFC7519](https://datatracker.ietf.org/doc/html/rfc7519#section-2 "https://datatracker.ietf.org/doc/html/rfc7519#section-2") para o formato *NumericDate*. A claim iat deverá ser gerada no Unix Time GMT +0 e sua verificação deverá possuir uma tolerância de +/- 60 segundos para cobrir pequenas diferenças nos relógios dos servidores dos participantes. |

---

### Importante

* **A claim do “jti” deve ser única para um clientId dentro de um intervalo de tempo de 86.400 segundos, não podendo ser reutilizada neste período. Em caso de reutilização, deverá ser retornado o código de erro HTTP 403.**
* **As validações referentes as claims do JWT devem preceder a validação de idempotência (e.g. “jti”, “iat” e “iss”)**

Cada elemento acima deve ser codificado utilizando o padrão Base64url \[[RFC4648](https://datatracker.ietf.org/doc/html/rfc4648#section-5 "https://datatracker.ietf.org/doc/html/rfc4648#section-5")\] e, feito isso, os elementos devem ser concatenados com “.” (método JWS Compact Serialization, conforme definido na \[[RFC7515](https://datatracker.ietf.org/doc/html/rfc7515#section-3.1 "https://datatracker.ietf.org/doc/html/rfc7515#section-3.1")\]).

Formato da mensagem JWS:

**payload** = Base64url(**JOSEHeader**) + "." + Base64url(**payload JWT**) + "." + Base64url(**digital signature**)

Veja abaixo o exemplo de mensagem JWS assinada e codificada e um exemplo de mensagem JWS decodificada.

**Exemplo de requisição - JWS assinada e codificada**

`eyJhbGciOiJQUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IlBXQWk1cnVRY0hmelB6cTJKRmRwWTduQVVoNkx6VFRRdERCVXBPTTM3SlEifQ.eyJhdWQiOiIzNDQ5YmYyMS0wYjA3LTQ4ZTYtYjZmYy0xM2ViMTYxYTk5MDEiLCJpc3MiOiJjYTFiOThlMS05N2EyLTQzZGItOTQ3Zi04YTA4MDU0YzM0MmUiLCJqdGkiOiJhMmZkMzkzYy0xMDdhLTRhZDQtYmUyMi0zY2ZlZWVhOTBlZmUiLCJpYXQiOiIxNjI4MjU3NzM3IiwiZGF0YSI6eyJjb25zZW50SWQiOiJ1cm46YmFuY29leDpDMUREMzMxMjMiLCJjcmVhdGlvbkRhdGVUaW1lIjoiMjAyMS0wNS0yMVQwODozMDowMFoiLCJleHBpcmF0aW9uRGF0ZVRpbWUiOiIyMDIxLTA1LTIxVDA4OjMwOjAwWiIsInN0YXR1c1VwZGF0ZURhdGVUaW1lIjoiMjAyMS0wNS0yMVQwODozMDowMFoiLCJzdGF0dXMiOiJBV0FJVElOR19BVVRIT1JJU0FUSU9OIiwibG9nZ2VkVXNlciI6eyJkb2N1bWVudCI6eyJpZGVudGlmaWNhdGlvbiI6IjExMTExMTExMTExIiwicmVsIjoiQ1BGIn19fSwiYnVzaW5lc3NFbnRpdHkiOnsiZG9jdW1lbnQiOnsiaWRlbnRpZmljYXRpb24iOiIxMTExMTExMTExMTExMSIsInJlbCI6IkNOUEoifX0sImNyZWRpdG9yIjp7InBlcnNvblR5cGUiOiJQRVNTT0FfTkFUVVJBTCIsImNwZkNucGoiOiI1ODc2NDc4OTAwMDEzNyIsIm5hbWUiOiJNYXJjbyBBbnRvbmlvIGRlIEJyaXRvIn0sInBheW1lbnQiOnsidHlwZSI6IlBJWCIsImRhdGUiOiIyMDIxLTAxLTAxIiwiY3VycmVuY3kiOiJCUkwiLCJhbW91bnQiOiIxMDAwMDAuMTIiLCJpYmdlVG93bkNvZGUiOiI1MzAwMTA4IiwiZGV0YWlscyI6eyJsb2NhbEluc3RydW1lbnQiOiJESUNUIiwicXJDb2RlIjoiMDAwMjAxMDQxNDEyMzQ1Njc4OTAxMjM0MjY2NjAwMTRCUi5HT1YuQkNCLlBJWDAxNDQ2Njc1NkM2MTZFNkYzMjMwMzEzOTQwNjU3ODYxNkQ3MDZDNjUyRTYzNkY2RDI3MzAwMDEyXG5CUi5DT00uT1VUUk8wMTEwMDEyMzQ1Njc4OTUyMDQwMDAwNTMwMzk4NjU0MDYxMjMuNDU1ODAyQlI1OTE1Tk9NRURPUkVDRUJFRE9SNjAwOEJSQVNJTElBNjEwODcwMDc0OTAwNjJcbjUzMDUxNVJQMTIzNDU2NzgtMjAxOTUwMzAwMDE3QlIuR09WLkJDQi5CUkNPREUwMTA1MS4wLjA4MDQ1MDAxNEJSLkdPVi5CQ0IuUElYMDEyM1BBRFJBTy5VUkwuUElYLzAxMjNBQlxuQ0Q4MTM5MDAxMkJSLkNPTS5PVVRSTzAxMTkwMTIzLkFCQ0QuMzQ1Ni5XWFlaNjMwNEVCNzZcbiIsInByb3h5IjoiMTIzNDU2Nzg5MDEiLCJjcmVkaXRvckFjY291bnQiOnsiaXNwYiI6IjEyMzQ1Njc4IiwiaXNzdWVyIjoiMTc3NCIsIm51bWJlciI6IjEyMzQ1Njc4OTAiLCJhY2NvdW50VHlwZSI6IkNBQ0MifX19LCJkZWJ0b3JBY2NvdW50Ijp7ImlzcGIiOiIxMjM0NTY3OCIsImlzc3VlciI6IjE3NzQiLCJudW1iZXIiOiIxMjM0NTY3ODkwIiwiYWNjb3VudFR5cGUiOiJDQUNDIn0sImxpbmtzIjp7InNlbGYiOiJodHRwczovL2FwaS5iYW5jby5jb20uYnIvb3Blbi1iYW5raW5nL3BheW1lbnRzL3YxL2NvbnNlbnRzIn0sIm1ldGEiOnsidG90YWxSZWNvcmRzIjoxLCJ0b3RhbFBhZ2VzIjoxLCJyZXF1ZXN0RGF0ZVRpbWUiOiIyMDIxLTA1LTIxVDA4OjMwOjAwWiJ9fQ.FH8722_85hWbYQS7g9C_b0_vjYvzaByq7ovFIVXWUsreEyb1KvhXFm1-GttBXIQORv9DpSsimmqo1MsLQE8Qc_J3fMkb-vNfMAd5D4JJUSJq33X8ydAvrXdOyCfjeEcUC-6g_O3nexT6clK6RkH0Umf6X3hev3JNaObk2IvjKeMdygQ3UXpGl2CTCCWvmcfcGeRA-sNdLheLQsVIPoopZ-FHBmr5MboLRod06SCO-BKkLdKg8eVAf6LrkiZrFLVlDMwNTFolUK7JlNnPZjPdSBmR7pPjPzT1blgd9fTWl7pbOh93mejgVFB0B3aKHz-2Y83KBCTH8C32rnnxK4YYmA `

**Exemplo de requisição - JWS decodificada**

```json
{
  "alg": "PS256",
  "typ": "JWT",
  "kid": "PWAi5ruQcHfzPzq2JFdpY7nAUh6LzTTQtDBUpOM37JQ"
}
{
  "aud": "https://api.banco.com.br/openbanking/payments/v1/consents",
  "iss": "5647fe90-f6bc-11eb-9a03-0242ac130003",
  "jti": "7960577c-662c-456e-8cf5-e630828af635",
  "iat": "1628257484",
  "data": {
    "loggedUser": {
      "document": {
        "identification": "11111111111",
        "rel": "CPF"
      }
    },
    "businessEntity": {
      "document": {
        "identification": "11111111111111",
        "rel": "CNPJ"
      }
    },
    "creditor": {
      "personType": "PESSOA_NATURAL",
      "cpfCnpj": "58764789000137",
      "name": "Marco Antonio de Brito"
    },
    "payment": {
      "type": "PIX",
      "date": "2021-01-01",
      "currency": "BRL",
      "amount": "100000.12",
      "ibgeTownCode": "5300108",
      "details": {
        "localInstrument": "DICT",
        "qrCode": "00020104141234567890123426660014BR.GOV.BCB.PIX014466756C616E6F32303139406578616D706C652E636F6D27300012\nBR.COM.OUTRO011001234567895204000053039865406123.455802BR5915NOMEDORECEBEDOR6008BRASILIA61087007490062\n530515RP12345678-201950300017BR.GOV.BCB.BRCODE01051.0.080450014BR.GOV.BCB.PIX0123PADRAO.URL.PIX/0123AB\nCD81390012BR.COM.OUTRO01190123.ABCD.3456.WXYZ6304EB76\n",
        "proxy": "12345678901",
        "creditorAccount": {
          "ispb": "12345678",
          "issuer": "1774",
          "number": "1234567890",
          "accountType": "CACC"
        }
      }
    },
    "debtorAccount": {
      "ispb": "12345678",
      "issuer": "1774",
      "number": "1234567890",
      "accountType": "CACC"
    }
  }
}

* "Assinatura omitida por questões de brevidade"
```

**Exemplo de resposta - JWS assinada e codificada**

`eyJhbGciOiJQUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IlBXQWk1cnVRY0hmelB6cTJKRmRwWTduQVVoNkx6VFRRdERCVXBPTTM3SlEifQ.eyJhdWQiOiIzNDQ5YmYyMS0wYjA3LTQ4ZTYtYjZmYy0xM2ViMTYxYTk5MDEiLCJpc3MiOiJjYTFiOThlMS05N2EyLTQzZGItOTQ3Zi04YTA4MDU0YzM0MmUiLCJqdGkiOiJhMmZkMzkzYy0xMDdhLTRhZDQtYmUyMi0zY2ZlZWVhOTBlZmUiLCJpYXQiOiIxNjI4MjU3NzM3IiwiZGF0YSI6eyJjb25zZW50SWQiOiJ1cm46YmFuY29leDpDMUREMzMxMjMiLCJjcmVhdGlvbkRhdGVUaW1lIjoiMjAyMS0wNS0yMVQwODozMDowMFoiLCJleHBpcmF0aW9uRGF0ZVRpbWUiOiIyMDIxLTA1LTIxVDA4OjMwOjAwWiIsInN0YXR1c1VwZGF0ZURhdGVUaW1lIjoiMjAyMS0wNS0yMVQwODozMDowMFoiLCJzdGF0dXMiOiJBV0FJVElOR19BVVRIT1JJU0FUSU9OIiwibG9nZ2VkVXNlciI6eyJkb2N1bWVudCI6eyJpZGVudGlmaWNhdGlvbiI6IjExMTExMTExMTExIiwicmVsIjoiQ1BGIn19fSwiYnVzaW5lc3NFbnRpdHkiOnsiZG9jdW1lbnQiOnsiaWRlbnRpZmljYXRpb24iOiIxMTExMTExMTExMTExMSIsInJlbCI6IkNOUEoifX0sImNyZWRpdG9yIjp7InBlcnNvblR5cGUiOiJQRVNTT0FfTkFUVVJBTCIsImNwZkNucGoiOiI1ODc2NDc4OTAwMDEzNyIsIm5hbWUiOiJNYXJjbyBBbnRvbmlvIGRlIEJyaXRvIn0sInBheW1lbnQiOnsidHlwZSI6IlBJWCIsImRhdGUiOiIyMDIxLTAxLTAxIiwiY3VycmVuY3kiOiJCUkwiLCJhbW91bnQiOiIxMDAwMDAuMTIiLCJpYmdlVG93bkNvZGUiOiI1MzAwMTA4IiwiZGV0YWlscyI6eyJsb2NhbEluc3RydW1lbnQiOiJESUNUIiwicXJDb2RlIjoiMDAwMjAxMDQxNDEyMzQ1Njc4OTAxMjM0MjY2NjAwMTRCUi5HT1YuQkNCLlBJWDAxNDQ2Njc1NkM2MTZFNkYzMjMwMzEzOTQwNjU3ODYxNkQ3MDZDNjUyRTYzNkY2RDI3MzAwMDEyXG5CUi5DT00uT1VUUk8wMTEwMDEyMzQ1Njc4OTUyMDQwMDAwNTMwMzk4NjU0MDYxMjMuNDU1ODAyQlI1OTE1Tk9NRURPUkVDRUJFRE9SNjAwOEJSQVNJTElBNjEwODcwMDc0OTAwNjJcbjUzMDUxNVJQMTIzNDU2NzgtMjAxOTUwMzAwMDE3QlIuR09WLkJDQi5CUkNPREUwMTA1MS4wLjA4MDQ1MDAxNEJSLkdPVi5CQ0IuUElYMDEyM1BBRFJBTy5VUkwuUElYLzAxMjNBQlxuQ0Q4MTM5MDAxMkJSLkNPTS5PVVRSTzAxMTkwMTIzLkFCQ0QuMzQ1Ni5XWFlaNjMwNEVCNzZcbiIsInByb3h5IjoiMTIzNDU2Nzg5MDEiLCJjcmVkaXRvckFjY291bnQiOnsiaXNwYiI6IjEyMzQ1Njc4IiwiaXNzdWVyIjoiMTc3NCIsIm51bWJlciI6IjEyMzQ1Njc4OTAiLCJhY2NvdW50VHlwZSI6IkNBQ0MifX19LCJkZWJ0b3JBY2NvdW50Ijp7ImlzcGIiOiIxMjM0NTY3OCIsImlzc3VlciI6IjE3NzQiLCJudW1iZXIiOiIxMjM0NTY3ODkwIiwiYWNjb3VudFR5cGUiOiJDQUNDIn0sImxpbmtzIjp7InNlbGYiOiJodHRwczovL2FwaS5iYW5jby5jb20uYnIvb3Blbi1iYW5raW5nL3BheW1lbnRzL3YxL2NvbnNlbnRzIn0sIm1ldGEiOnsidG90YWxSZWNvcmRzIjoxLCJ0b3RhbFBhZ2VzIjoxLCJyZXF1ZXN0RGF0ZVRpbWUiOiIyMDIxLTA1LTIxVDA4OjMwOjAwWiJ9fQ.FH8722_85hWbYQS7g9C_b0_vjYvzaByq7ovFIVXWUsreEyb1KvhXFm1-GttBXIQORv9DpSsimmqo1MsLQE8Qc_J3fMkb-vNfMAd5D4JJUSJq33X8ydAvrXdOyCfjeEcUC-6g_O3nexT6clK6RkH0Umf6X3hev3JNaObk2IvjKeMdygQ3UXpGl2CTCCWvmcfcGeRA-sNdLheLQsVIPoopZ-FHBmr5MboLRod06SCO-BKkLdKg8eVAf6LrkiZrFLVlDMwNTFolUK7JlNnPZjPdSBmR7pPjPzT1blgd9fTWl7pbOh93mejgVFB0B3aKHz-2Y83KBCTH8C32rnnxK4YYmA `

**Exemplo de resposta - JWS decodificada**

```json
{
  "alg": "PS256",
  "typ": "JWT",
  "kid": "PWAi5ruQcHfzPzq2JFdpY7nAUh6LzTTQtDBUpOM37JQ"
}
{
  "aud": "3449bf21-0b07-48e6-b6fc-13eb161a9901",
  "iss": "ca1b98e1-97a2-43db-947f-8a08054c342e",
  "jti": "a2fd393c-107a-4ad4-be22-3cfeeea90efe",
  "iat": "1628257737",
  "data": {
    "consentId": "urn:bancoex:C1DD33123",
    "creationDateTime": "2021-05-21T08:30:00Z",
    "expirationDateTime": "2021-05-21T08:30:00Z",
    "statusUpdateDateTime": "2021-05-21T08:30:00Z",
    "status": "AWAITING_AUTHORISATION",
    "loggedUser": {
      "document": {
        "identification": "11111111111",
        "rel": "CPF"
      }
    },
    "businessEntity": {
      "document": {
        "identification": "11111111111111",
        "rel": "CNPJ"
      }
    },
    "creditor": {
      "personType": "PESSOA_NATURAL",
      "cpfCnpj": "58764789000137",
      "name": "Marco Antonio de Brito"
    },
    "payment": {
      "type": "PIX",
      "date": "2021-01-01",
      "currency": "BRL",
      "amount": "100000.12",
      "ibgeTownCode": "5300108",
      "details": {
        "localInstrument": "DICT",
        "qrCode": "00020104141234567890123426660014BR.GOV.BCB.PIX014466756C616E6F32303139406578616D706C652E636F6D27300012\nBR.COM.OUTRO011001234567895204000053039865406123.455802BR5915NOMEDORECEBEDOR6008BRASILIA61087007490062\n530515RP12345678-201950300017BR.GOV.BCB.BRCODE01051.0.080450014BR.GOV.BCB.PIX0123PADRAO.URL.PIX/0123AB\nCD81390012BR.COM.OUTRO01190123.ABCD.3456.WXYZ6304EB76\n",
        "proxy": "12345678901",
        "creditorAccount": {
          "ispb": "12345678",
          "issuer": "1774",
          "number": "1234567890",
          "accountType": "CACC"
        }
      }
    },
    "debtorAccount": {
      "ispb": "12345678",
      "issuer": "1774",
      "number": "1234567890",
      "accountType": "CACC"
    }
  },
  "links": {
    "self": "https://api.banco.com.br/open-banking/payments/v1/consents"
  },
  "meta": {
    "totalRecords": 1,
    "totalPages": 1,
    "requestDateTime": "2021-05-21T08:30:00Z"
  }
}

* "Assinatura omitida por questões de brevidade"

```

## Em caso de erro

* Na validação da assinatura pelo Provedor do Recurso a API deve retornar mensagem de erro `HTTP` com status code `400` e a resposta deve incluir na propriedade code do objeto de resposta de erro especificado na API (_ResponseError_) a indicação da falha com o conteúdo `BAD_SIGNATURE`.
* Erros na validação da mensagem recebida pela aplicação cliente (p. ex. iniciador de pagamento) devem ser registrados e o `Provedor do Recurso` (p. ex. instituição detentora de conta) deve ser notificado.

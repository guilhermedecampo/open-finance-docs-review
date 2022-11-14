# Visão geral

A API tem como objetivo coletar o consentimento e realizar a iniciação de pagamento entre bancos, instituições financeiras e é acessível também à estabelecimentos comerciais participantes do Open Finance Brasil.

Os recursos estão disponíveis para pagadores que possuem registro em uma instituição detentora de conta participante do Open Finance, independentemente de serem pessoa física ou jurídica.

## Criar consentimento para iniciação de pagamento : (POST /payments/v2/consents)

Método para a criação do consentimento para iniciação de pagamento.

### Dicionário de dados

Campos de resposta do endpoint de /consents

[Fazer download do dicionário de dados](https://openbanking-brasil.github.io/openapi/dictionary/paymentsPostConsents_v2.csv "https://openbanking-brasil.github.io/openapi/dictionary/paymentsPostConsents_v2.csv")
## Consultar consentimento para iniciação de pagamento : (GET /payments/v2/consents/{consentId})

Método para consultar o consentimento para iniciação de pagamento.

### Dicionário de dados

Campos de resposta do endpoint de /consents

[Fazer download do dicionário de dados](https://openbanking-brasil.github.io/openapi/dictionary/paymentsGetConsentsConsentId_v2.csv "https://openbanking-brasil.github.io/openapi/dictionary/paymentsGetConsentsConsentId_v2.csv")

## Pix - Criar iniciação de pagamento : (POST /payments/v2/pix/payments)

Método para a criação de uma iniciação de pagamento.

### Dicionário de dados

Campos de resposta do endpoint de /pix/payments

[Fazer download do dicionário de dados](https://openbanking-brasil.github.io/openapi/dictionary/paymentsPostPixPayments_v2.csv "https://openbanking-brasil.github.io/openapi/dictionary/paymentsPostPixPayments_v2.csv")

## Pix - Consultar iniciação de pagamento : (GET /payments/v2/pix/payments/{paymentId})

Método para consultar uma iniciação de pagamento.

### Dicionário de dados

Campos de resposta do endpoint de /pix/payments/{paymentId}.

[Fazer download do dicionário de dados](https://openbanking-brasil.github.io/openapi/dictionary/paymentsGetPixPaymentsPaymentId_v2.csv "https://openbanking-brasil.github.io/openapi/dictionary/paymentsGetPixPaymentsPaymentId_v2.csv")

## Pix - Cancelar iniciação de pagamento : (PATCH /payments/v2/pix/payments/{paymentId})

Método para cancelar uma iniciação de pagamento.

### Dicionário de dados

Campos de resposta do endpoint de /pix/payments/{paymentId}.

[Fazer download do dicionário de dados](https://openbanking-brasil.github.io/openapi/dictionary/paymentsPatchPixPaymentsPaymentId_v2.csv "https://openbanking-brasil.github.io/openapi/dictionary/paymentsPatchPixPaymentsPaymentId_v2.csv")

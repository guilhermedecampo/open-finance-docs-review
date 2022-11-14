# Validação de informações no DICT

A dententora de conta poderá optar em qual momento e se realizará validações no DICT.

## Opção 1: Criação do Pagamento - POST /pix/payments (síncrona)

Neste cenário o pagamento não é criado, porém o consentimento é consumido e está com o status CONSUMED.

**Retorno da API POST /pix/payments:**

- Caso seja um erro por dados inválidos, o retorno deve ser um erro HTTP 422, com código "**DETALHE_PGTO_INVALIDO**", título “**Detalhe do pagamento inválido.**” e descrição “**Parâmetro \[nome_campo\] diverge do cadastrado no DICT.**”;
- Caso seja um erro de suspeita de fraude o retorno deve ser um erro HTTP 422, com código "**NAO_INFORMADO**", título "**Não informado.**" e descrição “**Não reportado/identificado pela instituição detentora de conta.**"

## Opção 2: Processamento do pagamento – POST /pix/payments (assíncrona)

Neste cenário o pagamento é criado com sucesso e o consentimento é consumido, porém, as validações contra o DICT só ocorrerão de forma assíncrona e em caso de negativa será percebido pela iniciadora na consulta do pagamento.

**Primeira requisição: Retorno da API POST /pix/payments:**

- Sucesso HTTP 201 e status de pagamento PDNG ou PATC.

**Segunda requisição: Retorno da API GET /pix/payments/{paymentId}**

- Caso seja um erro por dados inválidos o retorno deve ser um sucesso HTTP 200, com status de pagamento RJCT e motivo "**DETALHE_PGTO_INVALIDO**";
- Caso seja um erro por suspeita de fraude o retorno deve ser um erro HTTP 200, com status de pagamento RJCT e motivo "**NAO_INFORMADO**".

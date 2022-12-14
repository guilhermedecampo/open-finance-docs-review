# Descrição do Diagrama de Sequência – APIs Fase 3

![](/images/diagram.jpeg)

## Detalhamento da iniciação de pagamento:

1.  Debtor (Usuário) inicia o processo de pagamento na iniciadora.
    
2.  Na iniciadora, o debtor seleciona a detentora e os dados de pagamentos: **Observação**: não serão ofertados, no primeiro momento, **Pix Saque e Pix troco**. Também não será possível agendamentos para **Pix QR Codes Dinâmico com vencimento**. [Aqui referências a regulamentação relacionada ao Pix](https://www.bcb.gov.br/estabilidadefinanceira/pix?modalAberto=regulamentacao_pix "https://www.bcb.gov.br/estabilidadefinanceira/pix?modalAberto=regulamentacao_pix").
    
    1.  Se transação por Chave Pix ou QR Code Estático:
        
        1.  É realizada consulta ao DICT (diretório de contas).  
            **Observação**: se a Iniciadora for um participante direto, detentora ou não de conta, no ecossistema do Pix, ele fará a consulta de forma direta ao DICT. Se a iniciadora for um participante indireto, será necessário consulta por meio de uma instituição com acesso direto com a qual a iniciadora possua relacionamento.
            
        2.  A iniciadora recebe as informações consultadas:
            
            1.  Dados de chave
                
            2.  Nome do creditor
                
            3.  Instituição detentora da conta do creditor
                
            4.  CPF / CPNJ do creditor
                
    2.  Se transação por QR Code Dinâmico:
        
        1.  É realizada consulta dos dados do QR code do creditor:
            
            1.  CNPJ / CPF
                
            2.  Data de vencimento
                
            3.  Nome Instituição
                
            4.  Endereço (logradouro, cidade, UF e CEP)
                
            5.  Identificador
                
            6.  Chave Pix
                
            7.  Valor Original
                
            8.  Valor Final
                
            9.  Vencimento
                
            10.  Expiração
                
    3.  Se transação por dados manuais (agência e conta):
        
        1.  Insere-se dados:
            
            1.  Instituição financeira
                
            2.  Agência
                
            3.  Conta
                
            4.  Nome
                
            5.  CPF / CNPJ
                
        2.  **Observação**: não é realizada consulta no creditor ou no DICT.
            
3.  Após consultas, a iniciadora segue para o fluxo de autorização e consentimento.
    

#### Estabelece TLS

Toda comunicação máquina-a-máquina (m2m) usará mTLS, conforme RFC rfc8705 e detalhado na especificação de segurança: [Open Finance Brasil Financial-grade API Security Profile 1.0 Implementers Draft 3](https://openbanking-brasil.github.io/specs-seguranca/open-banking-brasil-financial-api-1_ID3.html "https://openbanking-brasil.github.io/specs-seguranca/open-banking-brasil-financial-api-1_ID3.html").

#### POST /tokens - Pedido de access\_token e scope: payments, openid

Antes de começar o fluxo de iniciação de pagamento, a Instituição Iniciadora deverá ter se cadastrado como _client_ na Instituição Detentora da Conta, em acordo com o especificado para o Registro Dinâmico de Clientes (Dynamic Client Registration). Os detalhes dessa etapa podem ser encontrados na especificação de segurança:  [Open Finance Brasil Financial-grade API Dynamic Client Registration 1.0 Implementers Draft 2](https://openbanking-brasil.github.io/specs-seguranca/open-banking-brasil-dynamic-client-registration-1_ID2.html "https://openbanking-brasil.github.io/specs-seguranca/open-banking-brasil-dynamic-client-registration-1_ID2.html"). Uma vez cadastrada, a Instituição Iniciadora deverá obter o token de acesso (_access\_token_) pelo fluxo de _client credentials_, conforme especificado pela RFC 6749 (rfc6749), com os escopos _payments_ e _openid_.

#### Valida certificado SSL e scopes

Ao receber a requisição da Iniciadora, o Servidor de Autorização da Instituição Detentora da Conta deverá validar o certificado SSL e os escopos, se esses estão de acordo com a especificação: _payments_ e _openid_.

#### Gera access\_token

Em caso de sucesso da validação, o Servidor de Autorização da Instituição Detentora da Conta deverá gerar o _access\_token_, que será utilizado para a criação de consentimento.

#### Access\_token (scope: payments, openid)

O Servidor de Autorização da Instituição Detentora da Conta deverá responder à requisição com o _access\_token_ conforme padrões a serem definidos pelo GT de Segurança.

#### POST /payments/consents

Para a criação de consentimento, considerando o requerido para FAPI - Loding Intent ([Financial\_API\_Lodging\_Intent.md](https://bitbucket.org/openid/fapi/src/master/Financial_API_Lodging_Intent.md "https://bitbucket.org/openid/fapi/src/master/Financial_API_Lodging_Intent.md")), após a obtenção do token de acesso, a Instituição Iniciadora deverá usar esse token de acesso para fazer a requisição POST de consentimento. A criação do consentimento encontra-se detalhada na seção das APIs para Pagamentos (Open Finance Brasil).

#### 201 Created

A API de Consentimento deverá responder o Http Status 201 e Payload contendo _**consentId**_, e status inicial do consentimento em **AWAITING\_AUTHORISATION** conforme especificado na documentação Open Finance Brasil.

#### Redirecionamento

No caso do consentimento ter sido criado com sucesso, a Instituição Iniciadora deverá fazer o redirecionamento para a Instituição Detentora da Conta. Esse fluxo de redirecionamento deve considerar todos os requisitos definidos para o objeto de requisição OpenID Connect (Seção 4.3 da especificação de segurança - [Third Party Provider End To End User Guide](https://openbanking-brasil.github.io/specs-seguranca/tpp-user-guide.html "https://openbanking-brasil.github.io/specs-seguranca/tpp-user-guide.html")). Esse redirecionamento é o passo que permitirá o início da autenticação do usuário na Instituição Detentora da Conta.

#### Validações de negócios (Detentora)

Ao receber o POST /payments/v1/pix/payments é importante observar que a Detentora deverá validar as informações passadas pela Iniciadora nos campos do payload de envio do consentimento e do pagamento (como, por exemplo, valores e dados do creditado), além de ser necessário decodificar o código para os casos de pagamentos iniciados a partir de um Pix QRCode, a fim de que a Detentora carregue as informações complementares (como, por exemplo, o TxID) na mensageria do Pix (atenção para a PACS008 e as [regulamentações do Pix](https://www.bcb.gov.br/estabilidadefinanceira/pix?modalAberto=regulamentacao_pix "https://www.bcb.gov.br/estabilidadefinanceira/pix?modalAberto=regulamentacao_pix")). A Detentora deve validar as informações de detalhes do payload com os dados de detalhes do consentimento, de acordo com a forma de pagamento. Caso os dados do detalhe informados no consentimento sejam diferente dos dados enviados na iniciação de pagamento, a Detentora de retornar erro HTTP 422 Unprocessable Entity, com o code BENEFICIARIO\_INCOMPATIVEL.

#### Efetivação do pagamento<<Async>>

A Detentora de Conta efetua a transação de pagamento entre o Debtor e Creditor através da forma de pagamento escolhida pelo Debtor. A efetivação da transação acontece de maneira assíncrona ao fluxo do Open Finance, seguindo as regras e interfaces do arranjo utilizado (apenas PIX disponível nesse momento).

#### Loop (Polling)

A Iniciadora deverá consultar periodicamente a Instituição Detentora de Conta para verificar o status da transação de iniciação pagamento. Os possíveis status de uma transação de iniciação de pagamento estão detalhados na documentação (Open Finance Brasil). Como sugestão, é indicado que a Instituição Iniciadora do pagamento implemente um retry exponencial e respeite o “rate limit” descriminado na documentação. A recomendação para uso do polling encontra-se detalhada na seção de “Recomendação uso de polling” ([Open Finance Brasil](/wiki/spaces/DraftOF/pages/10223651 "/wiki/spaces/DraftOF/pages/10223651")).

#### GET pix/payments/{paymentId}

Durante o período de polling a Iniciadora deverá consultar o status da transação através da rota “Get pix/payments/{paymentId}” informado o respectivo paymentId da transação. A consulta encontra-se detalhada na seção das APIs para Pagamentos (Open Finance Brasil).

#### Exibe comprovante de iniciação de pagamento

Caso a Iniciadora identifique que a transação de pagamento foi aprovada pela Detentora de Conta (status “ACCC”), poderá ser exibido o comprovante da **efetivação** da Transação de Pagamento. Caso o status do pagamento seja diferente de “ACCC” e/ou “RJCT”, deverá ser apresentada a efetivação da **solicitação** de Iniciação de Pagamento, apresentando as informações ([segundo Guia de Usuário TPP](https://openfinancebrasil.atlassian.net/wiki/spaces/DraftOF/pages/7997165/Guia+do+Usu+rio "https://openfinancebrasil.atlassian.net/wiki/spaces/DraftOF/pages/7997165/Guia+do+Usu+rio") – “Etapa 6: Efetivação da Solicitação):

*   Forma de pagamento (de acordo com os arranjos de pagamento vigentes e Circular 4.015);
    
*   Valor da transação de pagamento (opcional para transações sucessivas);
    
*   Informações referentes ao Recebedor da Transação de Pagamento;

Os possíveis status de uma transação de iniciação de pagamento estão detalhados na documentação (Open Finance Brasil).
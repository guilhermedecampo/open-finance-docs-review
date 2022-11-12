# Iniciação de pagamentos

Na fase 3 do Open Finance Brasil será oferecida aos clientes a possibilidade de movimentação financeira a partir de aplicativos e plataformas externas ao ambiente no qual mantém sua conta.

Na prática o que teremos é a oferta de pagamentos, transferências e outras operações executadas a partir de aplicativos de terceiros, sempre com a prévia coleta do consentimento do cliente para a iniciação destas transações.

No âmbito da [Resolução conjunta nº 1](https://www.in.gov.br/en/web/dou/-/resolucao-conjunta-n-1-de-4-de-maio-de-2020-255165055 "https://www.in.gov.br/en/web/dou/-/resolucao-conjunta-n-1-de-4-de-maio-de-2020-255165055"), de 04 de maio de 2020 o Open Finance Brasil passa a contar com os atores e operações ali definidos, reproduzidos a seguir.

## Instituição detentora de conta

É a instituição participante do Open Finance que possui a capacidade de ofertar quaisquer dos tipos de conta a seguir: conta de depósitos à vista (conta-corrente), conta de poupança, conta-salário e conta de pagamento pré-paga, guardando similaridade com o conceito de [ASPSP](https://www.openbanking.org.uk/account-providers/ "https://www.openbanking.org.uk/account-providers/") - Account Servicing Payment Service Provider do modelo britânico.

No contexto do Open Finance as instituições detentoras de conta deverão observar critérios de segurança e conformidade previamente definidos.

Consulte neste [link](https://openbanking-brasil.github.io/specs-seguranca/aspsp-user-guide.html "https://openbanking-brasil.github.io/specs-seguranca/aspsp-user-guide.html") as especificações de segurança aplicáveis.

## Instituição iniciadora de transação de pagamento

É a instituição participante que presta serviço de iniciação de transação de pagamento sem deter em momento algum os fundos transferidos na prestação do serviço.

De forma análoga ao caso das detentoras de conta, as iniciadoras mantém certo grau de similaridade com o conceito de [TPP]( https://www.openbanking.org.uk/fintechs/ " https://www.openbanking.org.uk/fintechs/") - Third Party Provider do modelo britânico, devendo também observar critérios específicos de segurança, conforme detalhado neste [link]( https://openbanking-brasil.github.io/specs-seguranca/tpp-user-guide.html " https://openbanking-brasil.github.io/specs-seguranca/tpp-user-guide.html").

## Serviço de iniciação de transação de pagamento

É o serviço que possibilita a iniciação da instrução de uma transação de pagamento, ordenado pelo cliente, relativamente a uma conta de depósitos à vista (conta-corrente), conta-salário, conta de poupança ou conta de pagamento pré-paga.

Inicialmente o Open Finance estará disponibilizando a iniciação de Pix com execução na data corrente.

Futuramente com a evolução do ecossistema novas modalidades de operações serão agregadas, assim como a possibilidade de agendamentos.

## Iniciação pelo Recebedor

Iniciação pelo recebedor ocorre quando a prestação de serviço pela iniciadora é para, e somente para, o recebedor. Nestes casos somente será permitido como beneficiário do pagamento o próprio recebedor. Algumas Instituições Detentoras de conta precisarão ajustar seus sistemas de forma a apropriar o campo em sua estrutura ao invés de gerá-lo nos casos pertinentes.

## Informações gerais

O QR Code estático deve ser validado nos dados que o compõe. Em particular, não existindo valor financeiro, a transação assume o valor do consentimento, que será objeto de autorização posterior pelo cliente.
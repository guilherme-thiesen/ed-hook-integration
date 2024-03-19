# Integração com o Hook da Encontro Digital

Este repositório tem como objetivo apresentar os formatos de retorno disponibilizados pelo Hook da Encontro Digital.

Para utilizar estes recursos, entre em contato com a Encontro Digital.
Você pode optar por enviar cada tipo de retorno para um endpoint diferente, ou todos para o mesmo endpoint.
Os envios são realizados apenas 1 vez por ação.

## Integração de cadastros, vendas, retirada de cortesias e transferência de ingressos:
| Tipo de ação              | Descrição                                                      |Ação |
|---------------------|----------------------------------------------------------------|-------------|
| `Cadastro`           | Finalização do processo de cadastro                                              | register         |
| `Venda`           | Atividade de venda, seja criação ou alteração de status                                              | sale         |
 | `Transferência de ingresso`           | Transferência de um participante para outro, de ingresso pago.                                              | transfer-ticket         |
  | `Retirada de ingresso cortesia`           | Retirada de uma cortesia por um participante.                                              | courtesy-ticket         |
  | `Conciliação bancária`           | Lote semanal enviado pelo sistema com as conciliações realizadas até o momento                                              | bank-reconciliation |

# JSON PADRÃO:
```json
{
  "platform": {"type": "string", "description": "Identificador da plataforma."},
  "project": {"type": "string", "description": "Identificador do projeto."},
  "user_hash": {"type": "string", "description": "Hash único do usuário para identificação."},
  "name": {"type": "string", "description": "Nome do usuário."},
  "email": {"type": "string", "description": "Endereço de e-mail do usuário."},
  "phone": {"type": "string", "description": "Número de telefone do usuário."},
  "document_type": {"type": "string", "description": "Tipo de documento do usuário."},
  "document_number": {"type": "string", "description": "Número do documento do usuário."},
  "company": {"type": "string", "description": "Empresa do usuário."},
  "know_about": {"type": "string", "description": "Como o usuário conheceu a plataforma."},
  "action": {"type": "string", "description": "Ação realizada pelo usuário (e.g., registro, venda)."},
  "payment_status": {"type": "string", "description": "Status de pagamento da transação."},
  "payment_method": {"type": "string", "description": "Método de pagamento utilizado."},
  "cart": {"type": "array", "description": "Lista de produtos adquiridos."},
  "sale_hash": {"type": "string", "description": "Código único da venda."},
  "boleto_url": {"type": "string", "description": "URL do boleto gerado."},
  "pix_code": {"type": "string", "description": "Código PIX para pagamento."},
  "installments": {"type": "number", "description": "Número de parcelas do pagamento."},
  "total": {"type": "number", "description": "Valor total da transação."},
  "tax": {"type": "number", "description": "Taxas da transação."},
  "fees": {"type": "number", "description": "Juros sobre parcelamento."},
  "administration_tax": {"type": "number", "description": "Taxa de administração cobrada."},
  "net_value": {"type": "number", "description": "Valor líquido da venda após deduções."},
  "created_at": {"type": "Date", "description": "Data de criação do registro."}
}
```

# Descritivo:
| Campo               | Descrição                                                      | Obrigatório |
|---------------------|----------------------------------------------------------------|-------------|
| `platform`           | Identificador da plataforma                                           | Sim         |
| `project`           | Código do projeto                                              | Sim         |
| `user_hash`         | Código do usuário                                              | Sim         |
| `name`              | Nome do usuário                                                | Sim         |
| `email`             | E-mail do usuário                                              | Sim         |
| `phone`             | Telefone do usuário                                            | Não         |
| `document_type`     | Tipo de documento: `cpf`, `other`, `passport`                  | Sim         |
| `document_number`   | Número do documento                                            | Sim         |
| `company`           | Empresa                                                        | Não         |
| `know_about`        | Como conheceu a plataforma                                     | Não         |
| `action`            | Ação realizada:`register`, `sale`, `courtesy-ticket`, `transfer-ticket`       | Sim         |
| `payment_status`    | Status de pagamento: `waiting`, `paid`, `cancelled`, `expired` | Somente para vendas |
| `payment_method`    | Forma de pagamento: `boleto`, `pix`, `credit_card`, `courtesy` | Somente para vendas |
| `cart`              | Lista de produtos adquiridos no formato Array                  | Somente para vendas |
| `sale_hash`         | Identificador da venda                                         | Somente para vendas |
| `boleto_url`        | Código do boleto                                               | Somente para vendas |
| `pix_code`          | Código do PIX                                                  | Somente para vendas |
| `installments`      | Número de parcelas                                             | Somente para vendas com financeiro |
| `total`             | Valor pago pelo cliente em centavos                            | Somente para vendas com financeiro |
| `tax`               | Valor das taxas em centavos                                    | Somente para vendas com financeiro |
| `administration_tax`| Valor das taxas em centavos                                    | Somente para vendas com financeiro |
| `fees`              | Juros sobre parcelamento em centavos                           | Somente para vendas com financeiro |
| `net_value`         | Valor líquido em centavos                                      | Somente para vendas com financeiro |
| `created_at`        | Data da ação                                                   | Sim         |


# Descritivo de produtos adquiridos no campo cart:
| Campo            | Descrição                                                      | Obrigatório |
|------------------|----------------------------------------------------------------|-------------|
| `sale_hash`      | Identificador da venda                                         | Sim         |
| `ticket_hash`    | Identificador do ingresso                                      | Sim         |
| `product`        | Nome do produto                                                | Sim         |
| `quantity`       | Quantidade                                                     | Sim         |
| `price`          | Preço unitário                                                 | Sim         |

# Exemplos

# Cadastro: action: register
```json
{
    "platform": "Plataforma de ingressos A",
    "project": "evento_1",
    "user_hash": "b81cbc79-ec15-4094-b243-e7363e07dead",
    "name": "João da Silva",
    "email": "joao@encontrodigital.com.br",
    "phone": "51999999999",
    "document_type": "cpf",
    "document_number": "99999999999",
    "company": "Encontro Digital",
    "know_about": "Whatsapp",
    "action": "register",
    "created_at": "2024-03-19T13:16:37.330+00:00",
  }
```
# Venda: action: sale (sem financeiro)
Observação: As comunicações de sale sempre se repetem, alterando apenas o status de
waiting: Aguardando pagamento
paid: Pago
cancelled: Cancelado / estornado
expired: Boleto ou pix expirado
```json
{
    "platform": "Plataforma de ingressos A",
    "project": "evento_1",
    "sale_hash": "89e8bb8a-c4bc-41a8-a839-8e72a42c476d",
    "user_hash": "b81cbc79-ec15-4094-b243-e7363e07dead",
    "name": "João da Silva",
    "email": "joao@encontrodigital.com.br",
    "phone": "51999999999",
    "document_type": "cpf",
    "document_number": "99999999999",
    "company": "Encontro Digital",
    "know_about": "Whatsapp",
    "action": "sale",
    "payment_status": "waiting",
    "payment_method": "credit_card",
    "cart": [
      {
       "sale_hash" : "89e8bb8a-c4bc-41a8-a839-8e72a42c476d",
       "ticket_hash" : "d29568cf-2d92-4e72-ad2c-2ab1807831ca",
       "product" : "Ingresso A",
       "quantity": 1,
       "price": 10000,
      }
    ],
 
    "created_at": "2024-03-19T13:16:37.330+00:00",
  }
```
# Venda: action: sale (com financeiro ativado)
Observação: As comunicações de sale sempre se repetem, alterando apenas o status de
waiting: Aguardando pagamento
paid: Pago
cancelled: Cancelado / estornado
expired: Boleto ou pix expirado
```json
{
    "platform": "Plataforma de ingressos A",
    "project": "evento_1",
    "sale_hash": "89e8bb8a-c4bc-41a8-a839-8e72a42c476d",
    "user_hash": "b81cbc79-ec15-4094-b243-e7363e07dead",
    "name": "João da Silva",
    "email": "joao@encontrodigital.com.br",
    "phone": "51999999999",
    "document_type": "cpf",
    "document_number": "99999999999",
    "company": "Encontro Digital",
    "know_about": "Whatsapp",
    "action": "sale",
    "payment_status": "waiting",
    "payment_method": "credit_card",
    "cart": [
      {
       "sale_hash" : "89e8bb8a-c4bc-41a8-a839-8e72a42c476d",
       "ticket_hash" : "d29568cf-2d92-4e72-ad2c-2ab1807831ca",
       "product" : "Ingresso A",
       "quantity": 1,
       "price": 10000,
      }
    ],
    "boleto_url": "",
    "pix_code": "",
    "installments": "1",
    "total": 100000,
    "tax": 10000,
    "fees": 0,
    "administration_tax": 0,
    "net_value": 90000,
    "created_at": "2024-03-19T13:16:37.330+00:00",
  }
```

# Transferência de ingresso: action: transfer-ticket
Esta ação confirma a transferência de um ingresso para outro. 
O pagador continua o mesmo, porém a titularidade do ingresso apontado no cart é alterada.
```json
{
    "platform": "Plataforma de ingressos A",
    "project": "evento_1",
    "sale_hash": "89e8bb8a-c4bc-41a8-a839-8e72a42c476d",
    "user_hash": "751a8406-8c1b-4d49-a1c0-555212c873c4",
    "name": "Pedro da Silva",
    "email": "joao@encontrodigital.com.br",
    "phone": "51999999999",
    "document_type": "cpf",
    "document_number": "99999999999",
    "company": "Encontro Digital",
    "know_about": "Facebook",
    "action": "transfer-ticket",
    "payment_status": "waiting",
    "payment_method": "credit_card",
    "cart": [
      {
       "sale_hash" : "89e8bb8a-c4bc-41a8-a839-8e72a42c476d",
       "ticket_hash" : "d29568cf-2d92-4e72-ad2c-2ab1807831ca",
       "product" : "Ingresso A",
       "quantity": 1,
       "price": 10000,
      }
    ],
    "created_at": "2024-03-19T13:16:37.330+00:00",
  }
```

# Retirada de cortesia: action: courtesy-ticket
Esta ação confirma a retirada de uma cortesia. 
Um ingresso de cortesia sempre está atribuido a uma venda com forma de pagamento courtesy.
```json
{
    "platform": "Plataforma de ingressos A",
    "project": "evento_1",
    "sale_hash": "89e8bb8a-c4bc-41a8-a839-8e72a42c476d",
    "user_hash": "751a8406-8c1b-4d49-a1c0-555212c873c4",
    "name": "Pedro da Silva",
    "email": "joao@encontrodigital.com.br",
    "phone": "51999999999",
    "document_type": "cpf",
    "document_number": "99999999999",
    "company": "Encontro Digital",
    "know_about": "Facebook",
    "action": "courtesy-ticket",
    "payment_status": "paid",
    "payment_method": "courtesy",
    "cart": [
      {
       "sale_hash" : "da030bb2-7873-4da7-beee-e8c344617cc0",
       "ticket_hash" : "d29568cf-2d92-4e72-ad2c-2ab1807831ca",
       "product" : "Ingresso A",
       "quantity": 1,
       "price": 0,
      }
    ],
    "created_at": "2024-03-19T13:16:37.330+00:00",
  }
```

# Conciliação bancária: action: bank-reconcilliation
Semanalmente, a plataforma encaminha o lote de vendas conciliadas com saques disponíveis.
Normalmente o relatório ocorre no mesmo dia do saque.

# Descritivo do lote
| Campo               | Descrição                                                      | Exemplo |
|---------------------|----------------------------------------------------------------|-------------|
| `platform`           | `Identificador da plataforma`                                           | Plataforma A        |
| `project`           | `Código do projeto`                                              | evento_1         |
| `action`            | `Ação realizada:`bank-reconcilliation`      | bank-reconcilliation         |
| `values`            | `Lançamentos: Descritos na tabela abaixo`      | []      |
| `created_at`        | `Data da ação`                                                   | 2024-03-19T13:16:37.330+00:00       |



# Descritivo de Lançamento (campo values)

| Campo                | Descrição                                           | Exemplo                         |
|----------------------|-----------------------------------------------------|---------------------------------|
| `transaction_id`     | ID da transação                                     | 19057852                        |
| `sale_hash`          | Hash da venda                                       | 89e8bb8a-c4bc-41a8-a839-8e72a42c476d |
| `user_hash`          | Hash do usuário                                     | b81cbc79-ec15-4094-b243-e7363e07dead |
| `name`               | Nome do usuário                                     | João da Silva                   |
| `email`              | E-mail do usuário                                   | joao@encontrodigital.com.br     |
| `phone`              | Telefone do usuário                                 | 51999999999                     |
| `document_type`      | Tipo de documento                                   | cpf                             |
| `document_number`    | Número do documento                                 | 99999999999                     |
| `company`            | Empresa                                             | Encontro Digital                |
| `know_about`         | Como conheceu a plataforma                         | Whatsapp                        |
| `payment_status`     | Status do pagamento                                 | paid                            |
| `payment_method`     | Método de pagamento                                 | credit_card                     |
| `installments`       | Número de parcelas                                  | 1                               |
| `type`               | Tipo de transação (crédito, estorno retirada)               | credit /  debit  / withdraw        |
| `total`              | Valor total da transação                            | 100000 / 0                      |
| `tax`                | Taxas da transação                           | 10000 / 0                       |
| `fees`               | Juros de parcelamento                                    | 0                               |
| `administration_tax` | Taxa de administração                               | 0                               |
| `antecipation_gap`   | Gap de antecipação                                  | 0                               |
| `net_value`          | Valor líquido da transação                          | 90000 / 0                       |
| `cash_inflow`        | Entrada de caixa                                    | 90000 / 0                       |
| `cash_outflow`       | Saída de caixa                                      | 0 / 90000                       |
| `balance`            | Saldo após a transação                              | 90000 / 0                       |
| `created_at`         | Data da conciliação                       | 2024-03-19T13:16:37.330+00:00   |

Nota: Os valores em "Exemplo" correspondem aos primeiros registros do array `values` demonstrados abaixo.

# Exemplo em JSON
```json
{
    "platform": "Plataforma de ingressos A",
    "project": "evento_1",
    "action": "bank-reconcilliation",
    "values": [
{
    "transaction_id": "19057852",
    "sale_hash": "89e8bb8a-c4bc-41a8-a839-8e72a42c476d",
    "user_hash": "b81cbc79-ec15-4094-b243-e7363e07dead",
    "name": "João da Silva",
    "email": "joao@encontrodigital.com.br",
    "phone": "51999999999",
    "document_type": "cpf",
    "document_number": "99999999999",
    "company": "Encontro Digital",
    "know_about": "Whatsapp",
    "payment_status": "paid",
    "payment_method": "credit_card",
    "installments": "1",
    "type": "credit",
    "total": 100000,
    "tax": 10000,
    "fees": 0,
    "administration_tax": 0,
    "antecipation_gap": 0,
    "net_value": 90000,
    "cash_inflow": 90000,
    "cash_outflow": 0,
    "balance": 90000,
    "created_at": "2024-03-19T13:16:37.330+00:00",
  },
{
    "transaction_id": "19057853",
    "sale_hash": "",
    "user_hash": "",
    "name": "",
    "email": "",
    "phone": "",
    "document_type": "",
    "document_number": "",
    "company": "",
    "know_about": "",
    "payment_status": "",
    "payment_method": "",
    "installments": "",
    "type": "withdraw",
    "total": 0,
    "tax": 0,
    "fees": 0,
    "administration_tax": 0,
    "antecipation_gap": 0,
    "withdraw_tax": 367,
    "net_value": 0,
    "cash_inflow": 0,
    "cash_outflow": 90000,
    "balance": 0,
    "created_at": "2024-03-20T13:16:37.330+00:00",
  },
 
]
   
    "created_at": "2024-03-19T13:16:37.330+00:00",
  }
```






# Em caso de dúvidas, entre em contato com comercial@encontrodigital.com.br

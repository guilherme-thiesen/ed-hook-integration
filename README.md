# ed-hook-integration

Este repositório tem como objetivo apresentar os formatos de retorno disponibilizados pelo Hook da Encontro Digital.

Para utilizar estes recursos, entre em contato com a Encontro Digital.
Você pode optar por enviar cada tipo de retorno para um endpoint diferente, ou todos para o mesmo endpoint.
Os envios são realizados apenas 1 vez por ação.

## Integração de cadastros, vendas, retirada de cortesias e transferência de ingressos:

Cadastro : Finalização do processo de cadastro : register 
Venda : Atividade de venda, seja criação ou alteração de status : sale

# JSON PADRÃO:
{
    "project": "string",
    "user_hash": "string",
    "name": "string",
    "email": "string",
    "phone": "string",
    "document_type": "string",
    "document_number": "string",
    "company": "string",
    "know_about": "string",
    "action": "string",
    "payment_status": "string",
    "payment_method: "string",
    "cart": [],
    "sale_hash": "string",
    "boleto_url": "string",
    "pix_code": "string",
    "installments": "number",
    "total": "number",
    "tax": "number",
    "fees": "number",
    "administration_tax": "number",
    "net_value": "number",
    "created_at": "Date",
  }

# Descritivo:
| Campo               | Descrição                                                      | Obrigatório |
|---------------------|----------------------------------------------------------------|-------------|
| `project`           | Código do projeto                                              | Sim         |
| `user_hash`         | Código do usuário                                              | Sim         |
| `name`              | Nome do usuário                                                | Sim         |
| `email`             | E-mail do usuário                                              | Sim         |
| `phone`             | Telefone do usuário                                            | Não         |
| `document_type`     | Tipo de documento: `cpf`, `other`, `passport`                  | Sim         |
| `document_number`   | Número do documento                                            | Sim         |
| `company`           | Empresa                                                        | Não         |
| `know_about`        | Como conheceu a plataforma                                     | Não         |
| `action`            | Ação realizada:
                        `register`, `sale`, 'courtesy-ticket', 'transfer-ticket'       | Sim         |
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
{
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
    "created_at": "Date",
  }

# Venda: action: sale (sem financeiro)
Observação: As comunicações de sale sempre se repetem, alterando apenas o status de
waiting: Aguardando pagamento
paid: Pago
cancelled: Cancelado / estornado
expired: Boleto ou pix expirado

{
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
    "payment_method: "credit_card",
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

# Venda: action: sale (com financeiro ativado)
Observação: As comunicações de sale sempre se repetem, alterando apenas o status de
waiting: Aguardando pagamento
paid: Pago
cancelled: Cancelado / estornado
expired: Boleto ou pix expirado

{
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
    "payment_method: "credit_card",
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


# Transferência de ingresso: action: transfer-ticket
Esta ação confirma a transferência de um ingresso para outro. 
O pagador continua o mesmo, porém a titularidade do ingresso apontado no cart é alterada.

{
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
    "payment_method: "credit_card",
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


# Retirada de cortesia: action: courtesy-ticket
Esta ação confirma a retirada de uma cortesia. 
Um ingresso de cortesia sempre está atribuido a uma venda com forma de pagamento courtesy.

{
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
    "payment_method: "courtesy",
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


# Em caso de dúvidas, entre em contato com comercial@encontrodigital.com.br

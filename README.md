# dcpushinpay
Documentação da PushinPay
Introdução

Bem vindo a API da PushinPay, aqui vocẽ entrará a documentação completa de Pix cashin e Pix cashout Autenticação

Nossas apis estão protegidas por token de autenticação e também por IP's de acesso confiáveis, ou seja, para cada token gerado você poderá determinar quais IP's poderão acessar nossos endpoints a partir do mesmo

Authorization: Bearer YOUR_TOKEN

URL BASE PRODUÇÃO: https://api.pushinpay.com.br/api

URL BASE SANDBOX: https://api-sandbox.pushinpay.com.br/api Você pode alterar YOUR_TOKEN para seu token gerado em nosso portal e se autenticar usando o header Authorization. Transações PIX Criar QrCode PIX

Request

curl -X POST "/api/pix/cashIn"
-H "Authorization: YOUR_TOKEN"
-H "Content-Type: application/json"
-d '{ "value": 35, "webhook_url": "http://teste.com" }'

Esse endpoint cria um código PIX para pagamento Body Name Required Description value sim O valor a ser gerado em cima do qrcode. O valor precisa ser do tipo INT, ou seja, para R$ 1,00 real, deve ser enviado o valor 100. webhook_url não Esse parametro te como funcionalidade apontar para qual endpoint será enviado o webhook quando o status do pagamento for alterado.

Ao realizar uma chamada com sucesso, o endpoint irá retornar a seguinte estrutura:

{ "id": "9c29870c-9f69-4bb6-90d3-2dce9453bb45", "qr_code": "00020101021226770014BR.GOV.BCB.PIX2555api...", "status": "created", "value": 35, "webhook_url": "http://teste.com", "qr_code_base64": "data:image/png;base64,iVBORw0KGgoAA....." }

HTTP Request

POST /api/pix/cashIn Webhook de retorno

Ao adicionar o campo webhook_url na criação do qrcode pix, quando o status for alterados e caso falhe a tentativa nós tentaremos enviar 3x, e caso as 3x falhe, via painel administrativo será possível retomar os envios do mesmo. Também é possível adicionar um header customizado que iremos enviar para você em todos os webhooks, essa configuração está disponível em seu menu de configurações de nosso painel

Exemplo de webhook a ser retornado:

{ "id":"9c29870c-9f69-4bb6-90d3-2dce9453bb45", "value":"35", "status":"paid" }

Request com SPLIT

curl -X POST "/api/pix/cashIn"
-H "Authorization: YOUR_TOKEN"
-H "Content-Type: application/json"
-d '{ "value": 35, "webhook_url": "http://teste.com", "split_rules": [ { "value": 50, "account_id": "9C3AD98C-F00B-4729-BEAC-0A4B70B3A043" } ] }'

Não recomendamos a pratica de scrap, por isso atente-se a usar os nossos webhooks para receber alterações de status Consultar status de QrCode PIX

Request

curl -X GET "/api/transactions/{ID}"
-H "Authorization: YOUR_TOKEN"
-H "Content-Type: application/json"

Esse endpoint você pode consultar o status de um qrcode e também obter dados do mesmo

Exemplo de resposta:

{ "id": "9C17B975-903F-44CB-BB70-E838F85DC228", "status": "created", "value": "72", "description": null, "payment_type": "pix", "created_at": "2024-05-21T02:04:56.703000Z", "updated_at": "2024-05-21T02:04:56.703000Z", "webhook_url": "https://seu-tite.com", "pix_details": { "id": "9C17B975-8F08-445D-9BBB-C40572C1E446", "expiration_date": "2024-05-21 02:09:56.000", "emv": "00020101021226770014BR.GOV.BCB.PIX2555api.itau/pix/qr/v2/01dd8aba-a56c-4623-aa90-3ade0aea1ebe5204000053039865802BR5910PUSHIN PAY6009SAO PAULO62070503***63047D83", "created_at": "2024-05-21T02:04:56.703000Z", "updated_at": "2024-05-21T02:04:56.703000Z" } }

Não recomendamos a pratica de scrap, por isso atente-se a usar os nossos webhooks para receber alterações de status Fazer uma transferência PIX

Request

curl -X POST "/api/pix/cashOut"
-H "Authorization: YOUR_TOKEN"
-H "Content-Type: application/json"
-d '{ "value": 20, "pix_key_type": "evp", "pix_key": "e7df7636-6186-408e-a49a-a43efa3ababc", "webhook_url": "https://seu-tite.com" }'

HTTP Request

POST /api/pix/cashOut Body Name Required Description value sim O valor a ser gerado para a transferência. O valor precisa ser do tipo INT, ou seja, para R$ 1,00 real, deve ser enviado o valor 100. pix_key_type sim Tipo de chave pix a ser enviado o valor, elaas são: evp,national_registration,phone,email. pix_key sim O valor da chave. webhook_url não Esse parametro te como funcionalidade apontar para qual endpoint será enviado o webhook quando o status da transferência for alterado.

Ao realizar uma chamada com sucesso, o endpoint irá retornar a seguinte estrutura:

{ "id": "9c33c0b5-4af2-42c8-958e-0c9af0ad0e1a", "endToEndId": null, "receiver_name": null, "webhook_url": "https://seu-tite.com", "value": 22, "status": "created", "receiver_national_registration": null, "pix_key_type": "evp", "pix_key": "4dffe51c-003a..." }

Webhook de retorno

Ao adicionar o campo webhook_url na criação da transferência, quando o status for alterado e caso falhe, nós tentaremos enviar 3x, e caso as 3x falhe, via painel administrativo será possível retomar os envios do mesmo. Também é possível adicionar um header customizado que iremos enviar para você em todos os webhooks, essa configuração está disponível em seu menu de configurações de nosso painel

Exemplo de webhook a ser retornado:

{ "id":"fb88a89d012f45eea86cff9a86ea81e6", "value": 22, "status":"paid", "description":"Transação PIX enviada para ...." }

Consultar status de uma transferência

Request

curl -X GET "/api/transfers/{ID}"
-H "Authorization: YOUR_TOKEN"
-H "Content-Type: application/json"

Esse endpoint você pode consultar o status de uma traansferência e também obter dados do mesmo

Exemplo de resposta:

{ "id": "9C33B34B-C962-47B1-BD9C-314D3CAE463C", "status": "paid", "value": "20", "description": "Transação PIX enviada para...", "receiver_national_registration": "43...", "receiver_name": "....", "receiver_type": "individual", "end_to_end_id": "E19468242202406032...", "pix_key_type": "evp", "pix_key": "4dffe51....", "created_at": "2024-06-03T23:50:53.537000Z", "updated_at": "2024-06-03T23:50:57.207000Z", "webhook_url": "https://your-site.com" }

Realizar estorno de transação

Request

curl -X POST "/api/transactions/{transaction_id}/refund"
-H "Authorization: YOUR_TOKEN"
-H "Content-Type: application/json"

Esse endpoint permite realizar o estorno de uma transação

Obs: O prazo máximo para a realização do estorno é de 30 dias. Após isso não será mais possível realizar o estorno. Status code Body Description 200 [] Transação sendo processada 400 {'error': ''} As mensagens de erro podem ser de: Prazo de estorno excedido, Você não possui saldo para realizar o estorno, Não foi possível concluir o estorno. Não recomendamos a pratica de scrap, por isso atente-se a usar os nossos webhooks para receber alterações de status Boletos Criar BolePIX

Request

curl -X POST "/api/transactions/bankslip"
-H "Authorization: YOUR_TOKEN"
-H "Content-Type: application/json"
-d '{ "our_number": 2, "document_number": "DOC4561237", "amount": 8526, "expiration": "2025-01-01", "bank_teller_instructions": "Confirm payment", "protest_data": {"days_to_protest": 7}, "bankruptcy_protest_data": {"days_to_bankruptcy_protest": 14}, "max_payment_days": 45, "fine_data": {"fine_type": "absolute", "fine_amount": 10000, "days_to_fine": 10}, "interest_data": { "interest_type": "workdays_daily_amount", "interest_amount": 1000, "days_to_interest": 2 }, "write_off_data": {"days_to_write_off": 365}, "rebate_amount": 2000, "discounts_data": [ { "discount_amount": 5000, "discount_number": 1, "discount_type": "absolute", "discount_limit_date": "2024-12-01" } ], "payer_data": { "name": "Global Tech", "contact": { "email": "finance@globaltech.com", "phone": {"area_code": "11", "number": "987654321", "country_code" : "055"} }, "address": { "street": "101 High St.", "neighborhood": "Tech Park", "number": "202", "postal_code": "01001000", "city": "Innovation City", "state": "SP", "complement": "Building A" }, "document_number": "12345678000195", "person_type": "legal" }, "guarantor_data": { "name": "Jane Doe", "contact": { "email": "jane.doe@pushinpay.com.br", "phone": {"country_code": "055", "area_code": "11", "number": "999999999"} }, "address": { "street": "202 Elm St.", "neighborhood": "Quiet Neighborhood", "number": "303", "postal_code": "01001000", "city": "Peaceful Town", "state": "RJ", "complement": "House 1" }, "document_number": "23456789012", "person_type": "natural" }, "webhook_url": "https://seu-site/api/teste-pushin" }'

Esse endpoint cria um boleto Campo Tipo Descrição Caracteres our_number * integer Número único de identificação do boleto junto à carteira. Pode ser enviado pelo cliente e, caso não seja, a PushinPay irá gerar um (OBSERVAÇÃO: Em staging esse número vai sr ignorado, sempre um aleatório vai ser gerado) 11 document_number string Número de identificação do boleto. Pode ser o número da nota fiscal eletrônica 10 amount * integer Valor base do boleto - expiration * string Data de vencimento 10 bank_teller_instructions string Observações ao pagador do boleto 320 rebate_amount float Valor de abatimento do boleto, que será aplicado em cima do valor base - max_payment_days * integer Máximo de dias corridos que o boleto ficará disponível para pagamento, após o vencimento (pode ser no máximo 365) - write_off_data object Configuração de baixa Objeto write_off_data protest_data object Configuração de protesto Objeto protest_data bankruptcy_protest_data object Configuração de protesto falimentar Objeto bankruptcy_protest_data fine_data object Configuração de multa Objeto fine_data interest_data object Configuração de juros Objeto interest_data discounts_data object array Descontos Objeto discount payer_data * object Dados do pagador Objeto payer_data guarantor_data object Dados do sacador avalista Objeto guarantor_data Objeto write_off_data Campo Tipo Descrição Caracteres days_to_write_off * integer Dias, após o vencimento, para que o boleto seja baixado automaticamente - Objeto protest_data Campo Tipo Descrição Caracteres days_to_protest * integer Dias, após o vencimento, para que o boleto seja protestado automaticamente - Objeto bankruptcy_protest_data Campo Tipo Descrição Caracteres days_to_bankruptcy_protest * integer Dias, após o vencimento, para que o boleto seja protestado automaticamente - Objeto fine_data Opção 1: multa em valor absoluto (fine_type=absolute) Campo Tipo Descrição Caracteres fine_type * string Tipo da multa Enumeradores fine_type fine_amount * float Valor absoluto da multa - days_to_fine * integer Dias, após o vencimento, para que a multa seja cobrada - Opção 2: multa em valor percentual (fine_type=percentage) Campo Tipo Descrição Caracteres fine_type * string Tipo da multa Enumeradores fine_type fine_percentage * integer Valor percentual da multa, de 1 a 100 - days_to_fine * integer Dias, após o vencimento, para que a multa seja cobrada - Enumeradores fine_type Enumerador Descrição absolute valor absoluto percentage valor percentual Objeto interest_data Opção 1: juros utilizando valores absolutos (interest_type=calendar_days_daily_amount ou interest_type=workdays_daily_amount) Campo Tipo Descrição Caracteres interest_type * string Tipo de juros Enumeradores interest_type interest_amount * float Valor a ser cobrado por unidade de tempo determinada (dias úteis ou corridos) - days_to_interest * integer Dias, após o vencimento, para que comece a cobrar os juros - Opção 2: juros utilizando valores percentuais (interest_type=calendar_days_monthly_percentage) Campo Tipo Descrição Caracteres interest_type * string Tipo de juros Enumeradores interest_type interest_percentage * integer Porcentagem a ser cobrada por unidade de tempo determinada (dias úteis ou corridos) - days_to_interest * integer Dias, após o vencimento, para que comece a cobrar os juros - Enumeradores interest_type Enumerador Descrição calendar_days_daily_amount Valor diário sobre dias corridos workdays_daily_amount Valor diário sobre dias úteis calendar_days_monthly_percentage Porcentagem de juros cobrados mensalmente, com base em dias corridos Objeto discount Opção 1: descontos utilizando valores absolutos (discount_type in ["absolute", "anticipation_calendar_days_daily_amount", "anticipation_workdays_daily_amount"]) Campo Tipo Descrição Caracteres discount_amount * float Valor absoluto de desconto por unidade de tempo - discount_number * integer Número do desconto - discount_type * string Configuração do desconto em valores absolutos Enumerador discount_type discount_limit_date * string Data limite para aplicação do desconto 10 Opção 2: descontos utilizando valores percentuais (discount_type in ["percentage", "anticipation_calendar_days_daily_percentage", "anticipation_workdays_daily_percentage"]) Campo Tipo Descrição Caracteres discount_percentage * float Valor percentual de desconto por unidade de tempo - discount_number * integer Número do desconto - discount_type * string Configuração do desconto em valores percentuais Enumerador discount_type discount_limit_date * string Data limite para aplicação do desconto 10

Atenção!
O boleto pode ter até três descontos, sendo que os descontos devem ser todos do mesmo tipo, isto é, devem ter o mesmo discount_type. Os descontos devem ser numerados de 1 a 3, de maneira crescente e começando necessariamente em 1. Ou seja, caso sejam enviados dois descontos na requisição, devem necessariamente ser numerados com 1 e 2.

Enumeradores discount_type Enumerador Descrição absolute Valor fixo anticipation_calendar_days_daily_amount Valor diário de desconto de antecipação, sobre dias corridos anticipation_workdays_daily_amount Valor diário de desconto de antecipação, sobre dias úteis percentage Porcentagem fixa anticipation_calendar_days_daily_percentage Porcentagem mensal de desconto de antecipação, com base em dias corridos anticipation_workdays_daily_percentage Porcentagem anual de desconto de antecipação, com base em dias úteis Objeto payer_data e guarantor_data Campo Tipo Descrição Caracteres name * string Nome completo 100 document_number * string Número do documento (CPF/CNPJ) 11 ou 14 person_type * string Tipo da pessoa (física ou jurídica) Enumeradores person_type contact object Informações de contato Objeto contact address object Endereço Objeto address Enumeradores person_type Enumerador Descrição natural pessoa física legal pessoa jurídica Objeto contact Campo Tipo Descrição Caracteres email string E-mail de contato 320 phone object Telefone de contato Objeto phone Objeto phone Campo Tipo Descrição Caracteres country_code * string Código DDI (Discagem Direta Internacional) 3 area_code * string Código DDD (Discagem Direta à Distância) 2 number * string Número de telefone 9 Objeto address Campo Tipo Descrição Caracteres street * string Logradouro 500 number * string Número 6 complement string Complemento 500 neighborhood * string Bairro 100 postal_code * string CEP 8 city * string Cidade 100 state * string Estado (UF) Enumerador state Enumeradores state Enumerador Descrição AC Acre AL Alagoas AM Amazonas AP Amapá BA Bahia CE Ceará DF Distrito Federal ES Espírito Santo GO Goiás MA Maranhão MG Minas Gerais MS Mato Grosso do Sul MT Mato Grosso PA Pará PB Paraíba PE Pernambuco PI Piauí PR Paraná RJ Rio de Janeiro RN Rio Grande do Norte RO Rondônia RR Roraima RS Rio Grande do Sul SC Santa Catarina SE Sergipe SP São Paulo TO Tocantins EX Exceção Enviar Instrução de Boleto

Para solicitar uma instrução de boleto, basta fazer a requisição de emissão com o occurrence_type conforme abaixo: Valor Descrição bank_slip_edit Editar informações do pagador de um boleto existente. extension Prorrogação da data de vencimento de um boleto existente. write_off Baixa sem financeiro de um boleto. rebate Abatimento do pagamento. cancel_rebate Cancelar abatimento do pagamento. bank_slip_edit Edição de um boleto existente (Desconto, Endereço, Multa/juros). protest_request Protestar boleto. bankruptcy_protest_request Protesto Falimentar. protest_remove_request Cancelar Protesto. protest_cancel_request Sustar Protesto sem Baixa. protest_cancel_and_write_off_request Sustar Protesto com Baixa. Solicitar Prorrogação de Data de Boleto

Este endpoint altera a data de vencimento de um boleto. Para solicitar essa instrução, o boleto precisa estar registrado e deve estar pagável. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/extension'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--header 'Content-Type: application/json'
--data-raw '{ "expiration": "2024-08-30" }'

Parâmetros Requeridos Campo Tipo Descrição Caracteres expiration * string Nova data de vencimento do boleto 10 Solicitar Baixa de Boleto

Este endpoint solicita a baixa de um boleto. Para solicitar essa instrução, o boleto precisa estar registrado. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/write_off'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Solicitar Abatimento de Boleto

Este endpoint solicita um abatimento no valor de um boleto. Para solicitar essa instrução, o boleto não pode estar vencido. Requisição

curl --location --request PUT '/api/transactions/bankslip/{}/rebate'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--header 'Content-Type: application/json'
--data-raw '{ "rebate_amount": 10 }'

Campo Tipo Descrição Caracteres rebate_amount * integer Valor do abatimento a ser aplicado - Cancelar Abatimento de Boleto

Este endpoint cancela um abatimento aplicado a um boleto. Para solicitar essa instrução, o boleto deve conter um abatimento ativo e não pode estar vencido. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/cancel_rebate'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Solicitar Desconto em Boleto

Este endpoint aplica um desconto ao valor de um boleto. Para solicitar essa instrução, o boleto não pode estar vencido ou baixado. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/discount'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--header 'Content-Type: application/json'
--data-raw '{ "discounts": [ { "discount_number": 1, "discount_limit_date": "2024-08-30", "discount_value": 50 }
] }'

Campo Tipo Descrição Caracteres discounts * array Lista de objetos de desconto - discount_number * integer Número do desconto - discount_limit_date * string Data limite para aplicação do desconto 10 discount_value * integer Valor do desconto a ser aplicado - Adicionar/Editar Endereço de Boleto

Este endpoint adiciona ou edita o endereço associado a um boleto. Para solicitar essa instrução, o boleto deve estar registrado e ser pagável. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/address'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--header 'Content-Type: application/json'
--data-raw '{ "payer_address": "Rua dos Alfeneiros, 4, Little Whinging - Surrey, City, SP", "payer_postal_code_root": "17057", "payer_postal_code_suffix": "770" }'

Campo Tipo Descrição Caracteres payer_address * string Endereço completo do pagador 500 payer_postal_code_root * string Parte principal do CEP 5 payer_postal_code_suffix * string Parte complementar do CEP 3 Editar Multa/Juros de Boleto

Este endpoint edita as configurações de multa e juros de um boleto. Para solicitar essa instrução, o boleto deve estar registrado e ser pagável. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/fine'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--header 'Content-Type: application/json'
--data-raw '{ "fine_percentage": 1, "interest_daily_value": 0.33 }'

Campo Tipo Descrição Caracteres fine_percentage integer Percentual da multa a ser aplicada - interest_daily_value float Valor dos juros diários a ser aplicado - Solicitar Protesto de Boleto

Este endpoint solicita o protesto de um boleto. Para solicitar essa instrução, o boleto precisa estar vencido e deve ter os dados de endereço do pagador. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/protest_request'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Solicitar Protesto Falimentar de Boleto

Este endpoint solicita o protesto falimentar de um boleto. Para solicitar essa instrução, o boleto precisa estar vencido e deve ter os dados de endereço do pagador. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/bankruptcy_protest_request'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Cancelamento de Protesto de Boleto

Este endpoint solicita o cancelamento do protesto de um boleto. Para solicitar essa instrução, o boleto precisa estar protestado. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/protest_remove_request'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Cancelar Protesto Automático de Boleto

Este endpoint solicita o cancelamento automático do protesto de um boleto. Para solicitar essa instrução, o boleto deve estar registrado com a opção de protesto automático ativa. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/protest_remove_request_automatic'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Sustar Protesto sem Baixa de Boleto

Este endpoint solicita a sustentação do protesto de um boleto sem realizar a baixa. Para solicitar essa instrução, o boleto precisa estar protestado. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/protest_cancel_request'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Cancelar Protesto Automático de Boleto

Este endpoint solicita o cancelamento automático do protesto de um boleto. Para solicitar essa instrução, o boleto precisa estar registrado com a opção de protesto automático ativada. Requisição

curl --location --request PUT '/api/transactions/bankslip/{id}/protest_remove_request_automatic'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Exemplo de Resposta

A resposta pode variar conforme o tipo de instrução, refletindo mudanças nos campos de occurrence_stats e retornando erros no campo semantic_errors. Exemplo de Resposta

{ "file_info": { "beneficiary_code": null, "beneficiary_name": null, "file_sequence_id": null, "file_type_identifier": null, "file_type_literal": null, "service_code": null, "service_literal": null, "wrote_at": null }, "occurrence_stats": { "bank_slip_edit": 0, "bankruptcy_protest_request": 0, "cancel_rebate": 0, "extension": 0, "notary_office_entry": 0, "notary_office_exit": 0, "notary_office_payment": 0, "notification": 0, "payment": 0, "payment_notice": 0, "payment_write_off": 0, "protest_cancel_and_write_off_request": 0, "protest_cancel_request": 0, "protest_remove_request": 0, "protest_request": 0, "rebate": 0, "registration": 0, "write_off": 1 }, "semantic_errors": [ { "0": { "errors": [ { "created_at": "2019-03-12T12:59:32", "reason_code": "CEP Inválido", "translation_en_us": "Invalid Postal Code", "translation_pt_br": "CEP Inválido" } ], "our_number": 1000000000, "participant_control_number": null } } ] }

Recuperar um Boleto

Para recuperar as informações de um boleto, você pode fazer uma requisição GET para o seguinte endpoint. Endpoint

GET /transactions/bankslip/{bankslip_id} Exemplo de Requisição

curl --location --request GET '{{url}}/transactions/bankslip/9cdea5f2-a3a0-466a-9d18-41461e22a0e8'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'

Exemplo de resposta

{ "amount": 85.26, "asset_type": null, "automatic_bankruptcy_protest": true, "automatic_protest": true, "automatic_write_off": true, "bank_slip_file": [], "bank_slip_key": "9ee5e534-37ad-459e-b8dd-3114fde86608", "bank_slip_status": { "created_at": "2019-02-14T17:30:39", "enumerator": "registered", "translation_path": "bank_slip.BankSlipStatus.registered" }, "bank_teller_instructions": "Confirm payment", "bankruptcy_protest_data": { "days_to_bankruptcy_protest": 14 }, "barcode": "32992994800000085260001090000000007648307600", "beneficiary_account_branch": null, "beneficiary_account_digit": null, "beneficiary_account_key": "0ef8870f-631f-482b-8da4-6069236c334c", "beneficiary_account_number": null, "beneficiary_document_number": "31232970000146", "beneficiary_key": "d89075da-9856-4e87-9c2b-23a7fb691c3b", "beneficiary_name": "Lorival Guerreiro Filho Ltda", "billing_account_key": null, "business_date_expiration": "2025-01-02", "created_at": "2024-08-27T23:20:09", "days_before_fine": null, "days_before_interest": null, "days_to_bankruptcy_protest": 14, "days_to_protest": 7, "days_to_write_off": 365, "digitable_line": "32990001039000000000176483076006299480000008526", "discount_limit_date": null, "discount_value": "0", "discounts": [], "discounts_data": [ { "discount_amount": 50, "discount_limit_date": "2024-12-01", "discount_number": 1, "discount_type": "absolute" } ], "document_number": "DOC4561237", "expenses": [], "expiration": "2025-01-01", "fine_data": { "days_to_fine": 10, "fine_amount": 100, "fine_type": "absolute" }, "fine_percentage": null, "guarantor_address": null, "guarantor_city": null, "guarantor_data": { "address": { "city": "Peaceful Town", "complement": "House 1", "neighborhood": "Quiet Neighborhood", "number": "303", "postal_code": "01001000", "state": "RJ", "street": "202 Elm St." }, "contact": { "email": "jane.doe@qitech.com.br", "phone": { "area_code": "11", "country_code": "055", "number": "999999999" } }, "document_number": "23456789012", "name": "Jane Doe", "person_type": "natural" }, "guarantor_document": null, "guarantor_name": null, "guarantor_person_type": null, "guarantor_postal_code": null, "guarantor_state": null, "has_protest_pending_feedback": false, "historical_our_number": null, "institution_registration_date": "2024-08-27", "interest_daily_value": null, "interest_data": { "days_to_interest": 2, "interest_amount": 10, "interest_type": "workdays_daily_amount" }, "lock_origin_type": null, "max_payment_days": 45, "nfe_key": null, "nfe_url": null, "notary_office_number": null, "notary_office_protocol": null, "notification": [], "occurrences": [ { "agent_type": "system", "automatic_bankruptcy_protest": null, "automatic_protest": null, "automatic_write_off": null, "created_at": "2024-08-27T23:20:09", "discount_amount": null, "discounts": [], "discounts_data": [ { "discount_amount": 50, "discount_limit_date": "2024-12-01", "discount_number": 1, "discount_type": "absolute" } ], "fine_data": { "days_to_fine": 10, "fine_amount": 100, "fine_type": "absolute" }, "fine_percentage": null, "interest_daily_value": null, "interest_data": { "days_to_interest": 2, "interest_amount": 10, "interest_type": "workdays_daily_amount" }, "iof_amount": null, "new_bank_slip_status": { "created_at": "2019-02-14T17:30:39", "enumerator": "registered", "translation_path": "bank_slip.BankSlipStatus.registered" }, "new_due_date": "2025-01-01", "new_protest_status": { "created_at": "2019-02-14T17:30:40", "enumerator": "not_protested", "translation_path": "bank_slip.ProtestStatus.not_protested" }, "notary_office_number": null, "notary_office_protocol": null, "notification": [], "occurrence_expenses": null, "occurrence_feedback": { "created_at": "2019-02-14T17:30:46", "enumerator": "confirmed", "translation_path": "bank_slip.OccurrenceFeedback.confirmed" }, "occurrence_key": "976d4850-2f9e-4602-a081-65d11ceb723a", "occurrence_reasons": [], "occurrence_type": { "created_at": "2019-02-14T17:30:45", "enumerator": "registration", "translation_path": "bank_slip.OccurrenceType.registration" }, "old_bank_slip_status": { "created_at": "2019-02-14T17:30:39", "enumerator": "accepted", "translation_path": "bank_slip.BankSlipStatus.accepted" }, "old_due_date": null, "old_protest_status": null, "paid_amount": null, "paid_fine_amount": null, "paid_interest_amount": null, "payer_address": null, "payer_postal_code": null, "payment_bank": null, "payment_branch": null, "payment_credit_date": null, "payment_method": null, "payment_origin": null, "protest_confirmation": null, "protest_distribution_cost": null, "protest_electronic_cost": null, "protest_emolument": null, "protest_expenses": null, "protest_other_expenses": null, "protocol_date": null, "protocol_region": null, "rebate_amount": 20, "registration_institution_occurrence_date": "2024-08-27", "registration_institution_occurrence_event": [], "registration_institution_occurrence_status": { "created_at": "2019-02-14T17:30:47", "enumerator": "confirmed", "translation_path": "bank_slip.RegistrationInstitutionOccurrenceStatus.confirmed" }, "request_control_key": "9cdea5f2-a3a0-466a-9d18-41461e22a0e8", "requester_occurrence_event": [], "requester_occurrence_status": null, "selected_user_agent": null } ], "original_expiration": "2025-01-01", "our_number": 76, "paid_amount": null, "paid_fine_amount": null, "paid_interest_amount": null, "participant_control_number": null, "payer_account_digit": null, "payer_account_number": null, "payer_account_type": null, "payer_address": "101 High St., 202, Building A - Tech Park", "payer_bank": null, "payer_branch_digit": null, "payer_branch_number": null, "payer_data": { "address": { "city": "Innovation City", "complement": "Building A", "neighborhood": "Tech Park", "number": "202", "postal_code": "01001000", "state": "SP", "street": "101 High St." }, "contact": { "email": "finance@globaltech.com", "phone": { "area_code": "11", "country_code": "055", "number": "987654321" } }, "document_number": "12345678000195", "name": "Global Tech", "person_type": "legal" }, "payer_document": "12345678000195", "payer_name": "Global Tech", "payer_person_type": { "created_at": "2019-02-14T17:30:41", "enumerator": "legal", "translation_path": "bank_slip.PersonType.legal" }, "payer_postal_code": "01001000", "payment_data": null, "payment_date": null, "printing_policy": null, "protest_data": { "days_to_protest": 7 }, "protest_status": { "created_at": "2019-02-14T17:30:40", "enumerator": "not_protested", "translation_path": "bank_slip.ProtestStatus.not_protested" }, "protocol_date": null, "protocol_region": null, "qr_code": { "image": "/9j/4AAQSkZJRgABAQAA...", "pix_key": "7a857687-5160-440e-ae1e-5abd8b5db382", "qr_code_key": "01ae691a-949a-48a4-898b-132d262b6f88", "qr_code_url": "00020126930014br.gov.bcb.pix2571qrcode-h.app/bacen/cobv/01ae691a949a48a4898b132d262b6f885204000053039865802BR5925LorivalGuerreiroFilhoLtda6008SaoPaulo61080145200062070503***63046951", "receiver_conciliation_id": "01ae691a949a48a4898b132d262b6f88" }, "rebate_amount": 20, "reference_requester_profile_code": null, "registration_institution": { "created_at": "2020-03-24T20:36:24", "enumerator": "qi_scd", "febraban_code": "329", "remittance_sequence": 461, "settlement_resource_account_key": "3e46d266-4fdb-4fd2-b87a-3e3de366afd4" }, "request_control_key": "9cdea5f2-a3a0-466a-9d18-41461e22a0e8", "requester_profile": 9, "requester_profile_code": "329-09-0001-4830760", "requester_registration_date": "2024-08-27", "settlement_account_key": "0ef8870f-631f-482b-8da4-6069236c334c", "settlements": [], "tags": null, "write_off_data": { "days_to_write_off": 365 } }

Testar Aprovação de Boleto

Para testar a aprovação de um boleto, você pode fazer uma requisição POST para o seguinte endpoint. Endpoint

POST /api/sandbox/transactions/bankslip/{id}/approve Exemplo de Requisição

curl --location --request POST '/api/sandbox/transactions/bankslip/{id}/approve'
--header 'Authorization: Bearer YOUR_TOKEN'
--header 'Accept: application/json'
--data-raw ''

Errors This error section is stored in a separate file in includes/_errors.md. Slate allows you to optionally separate out your docs into many files...just save them to the includes folder and add them to the top of your index.md's frontmatter. Files are included in the order listed.

The Kittn API uses the following error codes: Error Code Meaning 400 Bad Request -- Your request is invalid. 401 Unauthorized -- Your API key is wrong. 403 Forbidden -- The kitten requested is hidden for administrators only. 404 Not Found -- The specified kitten could not be found. 405 Method Not Allowed -- You tried to access a kitten with an invalid method. 406 Not Acceptable -- You requested a format that isn't json. 410 Gone -- The kitten requested has been removed from our servers. 418 I'm a teapot. 429 Too Many Requests -- You're requesting too many kittens! Slow down! 500 Internal Server Error -- We had a problem with our server. Try again later. 503 Service Unavailable -- We're temporarily offline for maintenance. Please try again later.

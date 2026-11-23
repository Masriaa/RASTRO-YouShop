# RASTRO - YouShop

Este repositório contém um workflow do [n8n](https://n8n.io/) para triagem automática de mensagens de atendimento da YouShop. O projeto foi preparado como uma demonstração com dados fictícios e não está configurado para produção.

O workflow recebe uma mensagem, mascara informações pessoais, cria um identificador pseudonimizado, aplica regras de prioridade e usa o Google Gemini para classificar e estruturar o caso. Casos incertos ou de maior urgência são encaminhados para revisão humana.

## Arquivo principal

- [`Workflow Rastro.json`](./Workflow%20Rastro.json): exportação completa do workflow n8n.

## Como funciona

1. Um formulário coleta `mensagem`, `tipo_usuario` e `email`.
2. Dados pessoais presentes na mensagem são mascarados utilizando Regex, e não IA.
3. O e-mail é transformado em um identificador pseudonimizado por HMAC-SHA256.
4. Regras locais atribuem uma cor de urgência:
   - Verde: rotina;
   - Amarelo: urgente;
   - Vermelho: emergência.
5. O Gemini classifica o caso em categorias como compra, entrega, pagamento, acesso, plataforma ou segurança.
6. O workflow decide se pode consultar logs simulados e montar o ticket.
7. A IA nunca reduz uma urgência definida pelas regras locais.
8. Casos incertos ou não verdes seguem para uma etapa de revisão humana.

## Importação no n8n

### Requisitos

- Uma instância do n8n;
- Credencial da API do Google Gemini;
- Opcionalmente, uma credencial de envio de e-mail.

### Passos

1. Abra o n8n e selecione **Import from File**.
2. Importe [`Workflow Rastro.json`](./Workflow%20Rastro.json).
3. Ative ou substitua as credenciais nos nós do Gemini.
4. Configure o nó **Enviar confirmação ao cliente** antes de ativar o envio.
5. Substitua os nós `No Operation` de revisão e ticket por integrações reais, como Slack, Sheets ou outro sistema de atendimento.
6. Teste o workflow com dados fictícios e mantenha-o inicialmente desativado ou em ambiente de teste.

## Configuração necessária

Antes de usar em um ambiente real:

- Configure sua própria credencial do Google Gemini.
- Remova os identificadores de credencial incorporados no arquivo exportado e vincule credenciais da sua instância.
- Gere um novo segredo de HMAC-SHA256 seguro e não o mantenha diretamente no workflow. Use uma variável de ambiente ou credencial do n8n.
- Configure um serviço de e-mail confiável.
- Substitua a base de logs simulada por uma consulta autorizada ao sistema de registros.
- Defina retenção, controle de acesso e auditoria para os dados processados.
- Revise a documentação e a política de privacidade aplicáveis ao tratamento dos dados.

## Privacidade e segurança

O arquivo demonstra práticas de minimização de dados:

- mascaramento de CPF, e-mail e telefone na mensagem;
- pseudonimização do e-mail;
- remoção do e-mail antes do envio de dados à IA;
- consulta a logs limitada à categoria de plataforma e à confiança mínima;
- possibilidade de revisão humana;
- busca de logs implementada apenas com dados fictícios.

Mesmo assim, o workflow não pode ser considerado pronto para dados reais sem revisão técnica, jurídica e de segurança.

## Dados fictícios de teste

Os exemplos já presentes na simulação incluem, entre outros:

- `cliente1@exemplo.com`
- `creator2@exemplo.com`
- `produtor3@exemplo.com`
- `cliente7@exemplo.com`
- `creator5@exemplo.com`

Use somente informações fictícias durante testes.

## Limitações

- O nó de e-mail está desativado.
- A revisão humana é representada por um nó `No Operation`.
- O registro do ticket é representado por um nó `No Operation`.
- A base de logs é estática e simulada.
- O modelo pode gerar respostas incorretas ou ambíguas.
- As regras de prazo são ilustrativas e não representam políticas reais de atendimento.

## Detalhe de segurança:
O secret presente no arquivo .json é aleatório e não está sendo usado em nenhum outro ambiente, será usado apenas para teste.

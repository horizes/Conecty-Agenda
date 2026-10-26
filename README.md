# Conecty Agenda

Projeto independente do dashboard financeiro, com publicação, páginas e sessão de acesso próprios.

- Administração: `index.html`.
- Agendamento público: `agenda.html?agenda=ID`, obtido no botão Copiar link público.
- Retorno do Google: `agenda-google-callback.html`.
- O repositório público de hospedagem recebe somente `dist/`. Nunca enviar `backend/`, credenciais, dados ou arquivos `.env`.

## Desenvolvimento

`npm ci`, `npm test` e `npm run build`.

As dependências de build/testes estão fixadas no lockfile. A função de servidor usa versões fixas do SDK Supabase e Nodemailer. O código de agenda foi adaptado do projeto existente sem importar registros, identidades ou conexões da outra empresa.

## Servidor

Função exclusiva `conecty-agenda`, com validação própria da sessão administrativa. O agendamento público consulta apenas disponibilidade. Contatos e credenciais Google ficam cifrados; as tabelas e RPCs internas não podem ser lidas diretamente por visitantes ou usuários autenticados.

Instalar `backend/schema.sql` e `backend/scheduler.sql`, publicar `backend/conecty-agenda.ts` e desativar apenas a verificação JWT do gateway desta nova função, pois ela também atende à disponibilidade pública. A gestão continua exigindo sessão ativa verificada pelo servidor.

A infraestrutura de autenticação da equipe Conecty é reutilizada; o navegador guarda uma sessão específica da agenda, sem compartilhar o login local do dashboard. Proprietários administram as agendas da equipe; gestores e operadores usam sua agenda; contas de cliente e perfis de consulta não administram a agenda.

`CONECTY_AGENDA_SITE_URL` configura o endereço separado, padrão `https://horizes.github.io/Conecty-Agenda/`. A chave de proteção da agenda é derivada com HKDF da chave já existente, usando domínio próprio; as cifras não se confundem com as credenciais Omie. Pode-se usar uma chave dedicada em `CONECTY_AGENDA_DATA_KEY`.

Google Calendar/Meet exige uma credencial OAuth própria da Conecty, importada pela equipe em Configurações. O responsável por cada agenda autoriza sua própria conta Google. Não reutilizar credenciais de outro projeto.

Confirmações de reuniões exigem um provedor configurado no servidor: Resend com remetente autorizado ou SMTP TLS na porta 465, nas variáveis `CONECTY_AGENDA_SMTP_HOST`, `CONECTY_AGENDA_SMTP_PORT`, `CONECTY_AGENDA_SMTP_USER`, `CONECTY_AGENDA_SMTP_PASS` e `CONECTY_AGENDA_SMTP_FROM`. O SMTP do Auth não é automaticamente disponibilizado às funções. As credenciais devem ser cadastradas pessoalmente pelo responsável no serviço, sem incluí-las no código.

A rotina de processamento usa o agendador privado já existente, somente quando há trabalhos pendentes, sem disparar consultas financeiras. Uma falha de envio incerta fica em conferência em vez de reenviar às cegas.

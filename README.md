# ASTOLFO HOST

Arquitetura:
- `frontend/`: site estático, compatível com GitHub Pages.
- `backend/`: API Node.js + Express + Sequelize + PostgreSQL.
- `backend/.env.example`: segredos e configurações; nunca publique o `.env`.
- `backend/src/`: autenticação, planos, Coins, webhook, auditoria e proteção anti-abuso.

## Importante

GitHub Pages executa somente o frontend estático. O backend precisa rodar em um servidor/VPS/container com PostgreSQL.

Não coloque senhas, tokens OAuth, segredo de webhook ou credenciais de gateway no GitHub.

A conta Master é criada na inicialização usando `MASTER_EMAIL` e `MASTER_PASSWORD` do ambiente. A senha fornecida ao assistente não é gravada no código-fonte.

## Backend

```bash
cd backend
cp .env.example .env
npm install
npm run dev
```

Antes de iniciar, configure PostgreSQL e os valores do `.env`.

Em produção:
```bash
npm start
```

## Frontend / GitHub Pages

Edite `frontend/config.js` e coloque a URL pública da API:

```js
window.ASTOLFO_API_URL = "https://api.seudominio.com.br";
```

Depois envie a pasta `frontend/` para um repositório GitHub e ative:
Settings -> Pages -> Deploy from branch -> main -> /frontend (se sua configuração de Pages permitir pasta) ou publique uma branch dedicada contendo os arquivos do frontend.

Uma alternativa simples é manter `frontend/` como raiz de um repositório exclusivo para o site.

## Domínio

O domínio `.com.br` pode continuar no Registro.br e apontar para o GitHub Pages. A API deve usar outro host, por exemplo `api.seudominio.com.br`, apontando para seu servidor.

## Segurança

- JWT em cookie HttpOnly é preferível ao localStorage para sessões.
- HTTPS obrigatório em produção.
- Segredos somente em variáveis de ambiente.
- Webhook usa HMAC SHA-256 e comparação em tempo constante.
- Webhook é idempotente pelo `eventId`.
- Alterações de saldo usam transação e ledger.
- Conta suspensa fica impedida de executar bots.
- A conta Master não deve depender apenas do e-mail: o papel `MASTER` também é verificado.
- Não existe "RAM infinita": limites de infraestrutura são impostos pelo ambiente de hospedagem.

## OAuth / OTP

Os adaptadores de Google/Facebook/WhatsApp/SMS neste projeto são pontos de integração seguros. As credenciais e URLs dos provedores ficam no `.env`. Para produção, implemente/verifique o fluxo oficial de cada provedor e nunca aceite um `userId` vindo do cliente como prova de identidade.



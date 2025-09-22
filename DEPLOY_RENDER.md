Guia rápido: colocar no ar no Render (leigo-friendly)

Resumo: você vai criar o Banco (Postgres), publicar a API (pasta `server/`) e publicar o site estático (raiz do projeto). Depois só troca uma URL no `index.html`.

1) Banco de dados (PostgreSQL)
- Acesse o painel do Render: https://render.com
- Clique em: New → PostgreSQL → Create Database
- Entre no banco criado → aba Connections → copie a “External Database URL”.
- Se precisar, clique em Reveal/Regenerate Password para ver/trocar a senha.

2) API (Web Service)
- Render → New → Web Service → selecione o seu repositório do GitHub
- Root Directory: `server`
- Health Check Path: `/api/v1/health`
- Environment (adicione variáveis):
  - `DATABASE_PROVIDER=postgresql`
  - `DATABASE_URL=postgresql://USUARIO:SENHA@HOST:5432/NOME_DB?sslmode=require&connection_limit=5`
    - Dica: pegue a “External Database URL” e acrescente `:5432` e `?sslmode=require&connection_limit=5` no fim
  - `JWT_SECRET` = crie uma frase longa e aleatória
  - `CLIENT_ORIGIN=*` (provisório; vamos apertar depois)
  - (opcional) `RATE_LIMIT_MAX=500`, `RATE_LIMIT_LOGIN_MAX=20`
- Post-deploy command: `npx prisma migrate deploy`
- Clique em Create Web Service. No final, anote a URL pública (ex.: `https://sua-api.onrender.com`).

3) Apontar o front para a API
- Edite o arquivo `index.html` (já deixei pronto):
  - Procure pelo bloco “Configuração de ambiente” perto do final
  - Troque `https://SUA-API.onrender.com/api/v1` pela URL da sua API
- Faça commit/push no GitHub (o Render vai reconstruir o site estático depois do próximo passo).

4) Site estático
- Render → New → Static Site → selecione o mesmo repositório
- Root Directory: deixe em branco (raiz)
- Build Command: deixe vazio
- Publish Directory: `.` (ponto)
- Crie o Static Site e anote a URL (ex.: `https://seu-site.onrender.com`).

5) Apertar CORS da API
- Vá no serviço da API → Settings → Environment
- Troque `CLIENT_ORIGIN=*` por `CLIENT_ORIGIN=https://seu-site.onrender.com`
- Salve e redeploy.

6) Teste final
- Acesse `https://seu-site.onrender.com`
- Faça login admin, cadastre 1–2 clientes, registre uma operação (+2%)
- Faça login como um cliente e confirme o saldo/impactos atualizados

Resolução de problemas
- Health Check: `https://SUA-API.onrender.com/api/v1/health` deve responder `ok`
- Se der erro de CORS, confira `CLIENT_ORIGIN` na API
- Senha de banco exposta? No DB do Render → Connections → Regenerate Password → atualize a `DATABASE_URL` na API


# Administra — sistema administrativo inicial

Sistema web administrativo em Python/FastAPI + PostgreSQL, com:
- Login individual e papéis `admin` / `usuario`
- Cadastro, consulta, edição e inativação de clientes (pessoa física/jurídica)
- Lançamentos financeiros: receitas e despesas
- Contas a pagar/receber com status pendente/pago
- Dashboard e relatório mensal
- API REST versionada em `/api/v1`
- PostgreSQL persistente via volume Docker

> Esta é uma base funcional para desenvolvimento inicial. Antes de usar com dados reais em produção, configure HTTPS, segredos fortes, backups testados, política de privacidade/LGPD, monitoramento e revisão de segurança.

## Requisitos no Windows
- Windows 10/11
- Docker Desktop com WSL 2 habilitado
- Git opcional

## Executar localmente no Windows
1. Instale e abra o Docker Desktop.
2. Extraia este ZIP para uma pasta, por exemplo `C:\administra`.
3. No Explorador de Arquivos, abra a pasta extraída.
4. Copie `.env.example` para `.env`.
5. Edite `.env` e troque `SECRET_KEY`, `ADMIN_PASSWORD` e `POSTGRES_PASSWORD` por senhas fortes.
6. Abra PowerShell nessa pasta e execute:

```powershell
docker compose up --build -d
docker compose logs -f web
```

7. Acesse `http://localhost:8000`.
8. Entre com o usuário e senha definidos em `.env` (`ADMIN_USERNAME` e `ADMIN_PASSWORD`).

Para parar:
```powershell
docker compose down
```
Para parar sem apagar o banco, use `docker compose down` (não use `-v`).
**Não execute `docker compose down -v` se quiser preservar os dados**, pois isso remove o volume do banco.

## Publicar on-line
Para publicar com acesso pela internet, use um servidor/VPS ou plataforma compatível com Docker:
1. Crie uma VPS Linux e instale Docker + Docker Compose.
2. Envie os arquivos do projeto para o servidor.
3. Configure `.env` com segredos exclusivos e fortes.
4. Configure domínio e HTTPS (por exemplo, Caddy ou Nginx + certificado TLS).
5. Restrinja o acesso à porta do PostgreSQL; não exponha a porta 5432 à internet.
6. Execute `docker compose up --build -d`.
7. Configure backups automáticos e teste a restauração antes de cadastrar dados importantes.

O `docker-compose.yml` incluído é adequado para desenvolvimento e primeira implantação controlada. Para produção pública, recomenda-se colocar um proxy reverso com HTTPS na frente do app, restringir portas, configurar logs/alertas e revisar a política de usuários e permissões.

## Banco de dados e persistência
Os dados ficam no volume Docker `postgres_data`, portanto permanecem após reiniciar ou recriar o container. Não remova esse volume sem backup. O app cria as tabelas ao iniciar; para evolução contínua, adote migrações Alembic antes de mudanças relevantes no modelo.

## API futura / integração com outro site
A API está em `/api/v1`:
- `GET /api/v1/health`
- `GET /api/v1/customers`
- `POST /api/v1/customers`
- `GET /api/v1/transactions`
- `POST /api/v1/transactions`

A documentação interativa fica em `/docs`. As rotas de dados exigem login por sessão nesta primeira versão. Para integrar um segundo site, o próximo passo recomendado é adicionar autenticação de integração por OAuth2/client credentials ou tokens rotativos, escopos/permissões, rate limiting e logs de auditoria. Não compartilhe a senha de um funcionário com outro sistema.

## Usuários
Entre como administrador e abra **Usuários** para criar acessos individuais. O primeiro usuário administrador é criado a partir das variáveis `ADMIN_USERNAME` e `ADMIN_PASSWORD` no primeiro início. O sistema não oferece recuperação automática de senha nesta versão; o administrador pode alterar a senha de um usuário pela tela de usuários.

## Cuidados importantes
- Use nomes de usuário individuais; nunca compartilhe logins.
- Cadastre apenas dados necessários e autorizados.
- Mantenha o sistema atualizado e backups protegidos.
- A versão inicial não integra bancos, emissão fiscal, meios de pagamento ou contabilidade oficial.

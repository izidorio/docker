# Projeto Docker PostgreSQL

Este projeto fornece uma configuração Docker simples para executar um servidor PostgreSQL 18.1 usando Docker Compose.

## 📋 Pré-requisitos

- [Docker](https://www.docker.com/get-started) instalado
- [Docker Compose](https://docs.docker.com/compose/install/) instalado

## 🚀 Como usar

### 1. Configuração inicial

Copie o arquivo de exemplo de variáveis de ambiente:

```bash
cp .env.example .env
```

### 2. Personalizar variáveis (opcional)

Edite o arquivo `.env` para personalizar as configurações:

```env
POSTGRES_DB=pergamo          # Nome do banco de dados
POSTGRES_USER=docker         # Usuário do PostgreSQL
POSTGRES_PASSWORD=docker    # Senha do PostgreSQL
POSTGRES_PORT=5432          # Porta do PostgreSQL (host)
```

### 3. Iniciar o servidor

Execute o seguinte comando para iniciar o container PostgreSQL:

```bash
docker-compose up -d
```

O flag `-d` executa o container em modo detached (em segundo plano).

### 4. Verificar status

Para verificar se o container está rodando:

```bash
docker-compose ps
```

### 5. Conectar ao banco de dados

Você pode conectar ao PostgreSQL usando qualquer cliente compatível:

**Usando psql (linha de comando):**
```bash
docker exec -it postgres-db psql -U docker -d pergamo
```

**Usando variáveis do .env:**
```bash
docker exec -it postgres-db psql -U ${POSTGRES_USER} -d ${POSTGRES_DB}
```

**String de conexão:**
```
Host: localhost
Port: 5432 (ou o valor definido em POSTGRES_PORT)
Database: pergamo
Username: docker
Password: docker
```

### 6. Parar o servidor

Para parar o container:

```bash
docker-compose down
```

Para parar e remover os volumes (⚠️ **ATENÇÃO**: Isso apagará todos os dados):

```bash
docker-compose down -v
```

## 📁 Estrutura do projeto

```
.
├── docker-compose.yml    # Configuração do Docker Compose
├── .env.example         # Exemplo de variáveis de ambiente
├── .env                 # Variáveis de ambiente (não versionado)
├── .gitignore          # Arquivos ignorados pelo Git
├── README.md           # Este arquivo
└── data/               # Diretório de dados do PostgreSQL (criado automaticamente)
```

## 🔧 Configurações

### Versão do PostgreSQL

O projeto está configurado para usar **PostgreSQL 18.1**.

### Persistência de dados

Os dados do PostgreSQL são persistidos no diretório `./data` na raiz do projeto. Este diretório é criado automaticamente quando o container é iniciado pela primeira vez.

**Importante:** A partir do PostgreSQL 18+, o volume é montado em `/var/lib/postgresql` (ao invés de `/var/lib/postgresql/data`) para permitir que o PostgreSQL crie diretórios específicos por versão major, facilitando upgrades futuros.

### Portas

Por padrão, o PostgreSQL está mapeado para a porta `5432` do host. Você pode alterar isso através da variável `POSTGRES_PORT` no arquivo `.env`.

## 🛠️ Comandos úteis

### Ver logs do container

```bash
docker-compose logs -f db
```

### Reiniciar o container

```bash
docker-compose restart db
```

### Executar comandos SQL

```bash
docker exec -it postgres-db psql -U docker -d pergamo -c "SELECT version();"
```

### Backup do banco de dados

```bash
docker exec postgres-db pg_dump -U docker pergamo > backup.sql
```

### Restaurar backup

```bash
docker exec -i postgres-db psql -U docker pergamo < backup.sql
```

## ⚠️ Notas importantes

- O arquivo `.env` está no `.gitignore` e não deve ser versionado por questões de segurança
- Os dados são persistidos localmente no diretório `data/`
- Certifique-se de que a porta configurada não está em uso por outro serviço
- Para produção, altere as credenciais padrão no arquivo `.env`

## 🔄 Migração de versões antigas

Se você estava usando uma versão anterior do PostgreSQL (como 13) e está atualizando para 18.1, você pode encontrar um erro ao iniciar o container devido a dados antigos no formato anterior.

**Opção 1: Remover dados antigos (⚠️ PERDA DE DADOS)**

Se você não precisa dos dados antigos, simplesmente remova o diretório `data/`:

```bash
docker-compose down
rm -rf data/
docker-compose up -d
```

**Opção 2: Migrar dados usando pg_upgrade**

Se você precisa preservar os dados, será necessário fazer um upgrade usando `pg_upgrade`. Isso requer ter ambas as versões disponíveis temporariamente. Consulte a [documentação oficial do PostgreSQL](https://www.postgresql.org/docs/current/pgupgrade.html) para mais detalhes sobre o processo de migração.

## 📚 Recursos

- [Documentação do PostgreSQL](https://www.postgresql.org/docs/)
- [Documentação do Docker Compose](https://docs.docker.com/compose/)
- [PostgreSQL Docker Hub](https://hub.docker.com/_/postgres)

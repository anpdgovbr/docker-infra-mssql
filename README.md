# 🗄️ Infraestrutura Docker para Microsoft SQL Server (ANPD)

Este repositório fornece uma configuração de ambiente de desenvolvimento local utilizando Docker e Docker Compose. Ele é composto por um container do Microsoft SQL Server 2022 configurado com collation e senha segura para o usuário `sa`.

O ambiente foi projetado para ser simples, robusto e facilmente adaptável para futuras implantações em ambientes de produção, como o Kubernetes.

---

## 📋 Pré-requisitos

Antes de começar, garanta que você tenha as seguintes ferramentas instaladas em sua máquina:

- **[Docker](https://docs.docker.com/get-docker/)**
- **[Docker Compose](https://docs.docker.com/compose/install/)**

---

## ⚙️ Configuração

O projeto utiliza variáveis de ambiente para configurar o serviço. Para personalizar suas configurações, siga os passos abaixo:

1. **Crie um arquivo de ambiente:**
   Copie o arquivo de exemplo `.env.example` para um novo arquivo chamado `.env`. Este arquivo conterá suas configurações locais.

   ```bash
   cp .env.example .env
   ```

2. **Ajuste as variáveis:**
   Abra o arquivo `.env` e modifique as variáveis conforme necessário:

   - `MSSQL_SA_PASSWORD`: Senha do usuário administrador (`sa`).
   - `MSSQL_PID`: Tipo de licença (`Developer`, `Express`, `Evaluation`, etc.).
   - `MSSQL_TCP_PORT`: Porta TCP para acesso ao banco (default: `1433`).
   - `MSSQL_COLLATION`: Collation padrão do servidor (ex: `Latin1_General_CI_AI`).

---

## 🚀 Como Executar o Ambiente

Com o Docker em execução, suba os containers em modo detached (background):

```bash
docker compose up -d
```

Este comando irá:

- Baixar a imagem oficial do SQL Server 2022.
- Criar e iniciar o container `mssql-dev`.
- Criar um volume chamado `mssqldata` para persistir os dados.
- Criar uma network chamada `mssql-net` para isolar o serviço.

Para parar os serviços, execute:

```bash
docker compose down
```

---

## 🛠️ Serviços Disponíveis

| Serviço        | Nome do Container | Porta Exposta (Host) | Porta Interna | Volume de Dados |
| -------------- | ----------------- | -------------------- | ------------- | --------------- |
| **SQL Server** | `mssql-dev`       | `1433`               | `1433`        | `mssqldata`     |

---

## 🐘 Conectando ao Banco de Dados

Você pode se conectar ao SQL Server das seguintes formas:

- **De outros containers na mesma rede Docker:**

  - **Host:** `mssql`
  - **Porta:** `1433`

- **Da sua máquina local (Host):**
  - **Host:** `localhost`
  - **Porta:** `1433` (ou a que você definiu no `.env`)

Utilize as credenciais:

- **Usuário:** `sa`
- **Senha:** valor da variável `MSSQL_SA_PASSWORD` no seu `.env`.

---

## 💾 Backups e Gerenciamento de Dados

Você pode executar comandos diretamente no container para realizar backups ou operações administrativas:

### Acessar o prompt `sqlcmd` dentro do container:

```bash
docker exec -it mssql-dev /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P 'SUA_SENHA'
```

> Substitua `'SUA_SENHA'` pela senha definida no `.env`.

### Backup manual:

Para realizar um backup, você precisará montar um volume compartilhado com o host ou executar via scripts dentro do banco. (ver exemplos em docs oficiais da Microsoft).

---

## 🔄 Atualizando a Imagem

Para garantir que você está usando a versão mais recente do SQL Server:

```bash
docker compose pull
docker compose up -d
```

---

## 🧪 Troubleshooting

- **Erro de porta em uso (`port is already allocated`):**
  Verifique se a porta `1433` já está em uso. Altere no `.env` ou libere-a.

- **Falha na senha do `sa`:**
  A senha deve cumprir os requisitos: mínimo 8 caracteres, incluindo letras maiúsculas, minúsculas, números e símbolos.

- **Logs do container:**
  Verifique com:

  ```bash
  docker logs mssql-dev
  ```

---

## ⚠️ Segurança

- **Não use senhas fracas ou padrão em ambientes de produção.**
- O `.env` **não deve ser versionado**.
- Esta configuração é voltada para **desenvolvimento local**. Para produção, recomenda-se hardening da imagem, autenticação externa e SSL/TLS.

---

🛡️ **ANPD | Divisão de Desenvolvimento e Sustentação de Sistemas**

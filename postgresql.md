Ubuntu/Debian

### Instalar o PostgreSQL
```bash
sudo apt update 
sudo apt upgrade -y
sudo apt install postgresql postgresql-contrib -y
```

### Verificar o status do serviço
```
sudo systemctl status postgresql
```
Se o PostgreSQL não estiver ativo, inicie-o com:
```bash
sudo systemctl start postgresql
sudo systemctl enable postgresql  # Para iniciar automaticamente
```



# Passo 2: Configurar o PostgreSQL
**1. Acessar o usuário `postgres`**
```bash
sudo -i -u postgres
psql  # Entrar no console do PostgreSQL
```
**2. Criar um novo usuário e banco de dados**

-- Criar um novo usuário (substitua 'seu_usuario' e 'senha_segura')
```sql
CREATE USER <seu_usuario> WITH PASSWORD 'senha_segura';
```
ex: CREATE USER root WITH PASSWORD ‘5001’;

-- Criar um banco de dados
```sql
CREATE DATABASE <meu_banco> OWNER <seu_usuario>;
```
ex: CREATE DATABASE teste OWNER root;

-- Dar permissões
```sql
GRANT ALL PRIVILEGES ON DATABASE <meu_banco> TO <seu_usuario>;
```
ex: GRANT ALL PRIVILEGES ON DATABASE teste TO root;
-- Sair do psql
```sql
\q
```
Perfeito! Vamos criar um usuário no PostgreSQL. Siga o passo a passo:


## 1. Acessar o PostgreSQL
No terminal, execute:
```bash
sudo -u postgres psql
```

➡️ Isso abre o prompt do PostgreSQL como usuário administrador.

✅ 2. Criar um novo usuário
No prompt do PostgreSQL, execute:
```sql
CREATE USER nome_do_usuario WITH PASSWORD 'sua_senha';
```
➡️ Exemplo: 
CREATE USER rbb WITH PASSWORD '123456';


✅ 3. (Opcional) Dar privilégios ao usuário
Se quiser que o usuário crie bancos de dados:
```sql
ALTER USER nome_do_usuario CREATEDB;
```

Se quiser que seja superusuário (não recomendado, mas útil para testes):
```sql
ALTER USER nome_do_usuario WITH SUPERUSER;
```

➡️ Exemplo:
ALTER USER rbb CREATEDB;
ou
ALTER USER rbb WITH SUPERUSER;


✅ 4. (Opcional) Criar um banco de dados para esse usuário
Se quiser que ele tenha um banco próprio:
```sql
CREATE DATABASE nome_do_banco OWNER nome_do_usuario;
```

➡️ Exemplo:
CREATE DATABASE bancoteste OWNER rbb;


✅ 5. Sair do PostgreSQL
```sql
\q
```


✅ 6. Testar a conexão
No terminal:
```bash
psql -U nome_do_usuario -d nome_do_banco
```

➡️ Exemplo:
psql -U rbb -d bancoteste

Se pedir senha → digite a senha que criou.




3. Configurar acesso remoto (opcional)
Edite o arquivo de configuração:
```bash
sudo nano /etc/postgresql/<versão>/main/postgresql.conf
```
- Descomente/modifique:
  ```ini
  listen_addresses = '*'  # Permite conexões externas
  ```
- Em seguida, edite `pg_hba.conf`:
  ```bash
  sudo nano /etc/postgresql/<versão>/main/pg_hba.conf
  ```
- Adicione:
  ```ini
  host    all             all             0.0.0.0/0               md5
  ```
- Reinicie o PostgreSQL:
  ```bash
  sudo systemctl restart postgresql


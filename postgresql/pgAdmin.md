# Instalar uma Interface Web (Tipo phpMyAdmin)
Temos algumas opções de interface para o postgresql, a seguir sera apresentada 3 opções!

###Opção 1: pgAdmin (Recomendado)
**1. Instalar o pgAdmin (via Docker)**
```bash
docker run -p 5050:80 \
    -e "PGADMIN_DEFAULT_EMAIL=admin@example.com" \
    -e "PGADMIN_DEFAULT_PASSWORD=senha_admin" \
    -d dpage/pgadmin4
```
**2. Acessar no navegador**
```
http://seu-ip:5050
```
- **Login**: `admin@example.com`
- **Senha**: `senha_admin`

**3. Adicionar um servidor PostgreSQL**
1. Clique em **Add New Server**.
2. Em **Connection**, preencha:
   - **Host**: `IP-do-seu-servidor-postgres` (ou `localhost` se estiver na mesma máquina)
   - **Port**: `5432`
   - **Database**: `nome-do-banco`
   - **Username**: `usuario-postgres`
   - **Password**: `senha-postgres`

---

###Opção 2: phpPgAdmin (Similar ao phpMyAdmin)
1. Instalar o phpPgAdmin (Ubuntu/Debian)
```bash
sudo mkdir -p /srv/phppgadmin
cd /srv

sudo apt install phppgadmin apache2 -y
ou
sudo apt install phppgadmin
ou
sudo git clone https://github.com/phppgadmin/phppgadmin.git
```

2. Permissões:
```bash
sudo chown -R www-data:www-data /srv/phppgadmin
sudo chmod -R 755 /srv/phppgadmin
```

3. Configurar acesso
Edite o arquivo de configuração:
```bash
$ sudo nano /etc/phppgadmin/config.inc.php
```
- Altere:
  ```php
  $ conf['extra_login_security'] = false;  // Permite logar com qualquer usuário
  ```



Você quer manter o phpMyAdmin no diretório /var/www/html e colocar o phpPgAdmin em outro local, para não misturar os dois.
Sem problema! Você pode instalar o phpPgAdmin em qualquer pasta fora de /var/www/html, como por exemplo em:

/opt/phppgadmin
/srv/phppgadmin

Ou mesmo criando um diretório separado:
/var/www/phppgadmin

mas vamos la que aqui estamos instalando primeiro o pgadmin


Depois disso, cria-se um Alias ou um VirtualHost para acessá-lo normalmente via navegador.

### ✅ Como instalar o phpPgAdmin em outro diretório com Apache
✅ 1. Escolha o diretório
Exemplo: /srv/phppgadmin
sudo mkdir -p /srv/phppgadmin

✅ 2. Baixar o phpPgAdmin
cd /srv
sudo git clone https://github.com/phppgadmin/phppgadmin.git

Agora vai ter:
/srv/phppgadmin


✅ 3. Ajustar permissões
sudo chown -R www-data:www-data /srv/phppgadmin
sudo chmod -R 755 /srv/phppgadmin


✅ 4. Configurar o Apache
Agora criamos um Alias para acessar o phpPgAdmin, por exemplo, como http://localhost/phppgadmin.
Crie um arquivo de configuração:
sudo nano /etc/apache2/conf-available/phppgadmin.conf

Coloque:
Alias /phppgadmin /srv/phppgadmin

<Directory /srv/phppgadmin>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>

Ative a configuração:
sudo a2enconf phppgadmin
Reinicie o Apache:
sudo systemctl reload apache2


✅ 5. Configurar o phpPgAdmin
Entre no diretório:
cd /srv/phppgadmin/conf
Se não tiver o arquivo config.inc.php:
sudo cp config.inc.php-dist config.inc.php
Edite:
sudo nano config.inc.php
Configurações principais:
$conf['servers'][0]['desc'] = 'PostgreSQL';
$conf['servers'][0]['host'] = 'localhost';
$conf['extra_login_security'] = false;


✅ 6. Ajuste do PostgreSQL
⚠️ Se ainda não ajustou, lembre-se de configurar:
pg_hba.conf → aceitar conexões locais via md5.
postgresql.conf → listen_addresses = 'localhost'.


E reinicie:
sudo systemctl restart postgresql


✅ 7. Acessar pelo navegador
Agora, mesmo com o phpMyAdmin ocupando /var/www/html, você acessa normalmente:
http://localhost/phppgadmin

E o phpMyAdmin continua em:
http://localhost/phpmyadmin


✅ Resumo:
✅ phpPgAdmin instalado fora do /var/www/html.
✅ Apache configurado com um Alias para redirecionar.
✅ Sem interferir no phpMyAdmin.
✅ Ambas aplicações acessíveis via navegador.
Opção 3: Adminer (Leve e Simples)
**1. Instalar o Adminer (via Docker)**
```bash
docker run -p 8080:8080 -d adminer
```
**2. Acessar no navegador**
```
http://seu-ip:8080
```
- Selecione **PostgreSQL** e preencha:
  - **Servidor**: `IP-do-postgres` (ou `localhost`)
  - **Usuário**: `usuario-postgres`
  - **Senha**: `senha-postgres`
  - **Banco de dados**: `nome-do-banco`

---

Resumo das Opções
| **Ferramenta** | **Como Acessar** | **Recomendação** |
|-       ---------------|----------          --------|------------           ------|
| **pgAdmin** | `http://ip:5050` | Melhor para administração avançada |
| **phpPgAdmin** | `http://ip/phppgadmin` | Similar ao phpMyAdmin |
| **Adminer** | `http://ip:8080` | Leve e simples |

---

**🔹 Dicas Importantes**
1. **Libere as portas no firewall**:
   ```bash
   sudo ufw allow 5432/tcp  # PostgreSQL
   sudo ufw allow 80/tcp    # Apache (phpPgAdmin)
   sudo ufw allow 5050/tcp  # pgAdmin
   sudo ufw allow 8080/tcp  # Adminer
   ```
2. **Se estiver usando um servidor remoto**, substitua `localhost` pelo **IP do servidor PostgreSQL**.
3. **Problemas de conexão?** Verifique:
   - O PostgreSQL está ouvindo conexões externas? (`listen_addresses = '*'` em `postgresql.conf`)
   - O usuário tem permissão? (`GRANT ALL PRIVILEGES ON DATABASE...`)

O phpPgAdmin é uma ferramenta web semelhante ao phpMyAdmin, mas para bancos de dados PostgreSQL. Abaixo está um guia passo a passo para instalar e configurar o phpPgAdmin em um sistema baseado em Ubuntu ou Debian.

✅ Passos para instalar o phpPgAdmin no Ubuntu/Debian
1. Instalar PostgreSQL (se ainda não tiver)
sudo apt update
sudo apt install postgresql postgresql-contrib
Verifique se o serviço está ativo:
sudo systemctl status postgresql


2. Instalar Apache e PHP
Se ainda não tiver:
sudo apt install apache2 php php-pgsql libapache2-mod-php


3. Instalar phpPgAdmin
No Ubuntu 20.04 e Debian Buster, o phpPgAdmin pode não estar nos repositórios padrão. Então, você tem duas opções:
➡️ Opção A - Instalar via repositório
Em distribuições antigas (Ubuntu 18.04, Debian Stretch):
sudo apt install phppgadmin

O phpPgAdmin será instalado em /usr/share/phppgadmin.
Mas, nas versões mais recentes (Ubuntu 20.04+, Debian Bullseye+), o pacote foi removido dos repositórios.

4. Configurar o phpPgAdmin
Edite o arquivo de configuração:
sudo nano /var/www/html/phppgadmin/conf/config.inc.php
Ajuste conforme sua necessidade, por exemplo:
$conf['servers'][0]['desc'] = 'PostgreSQL';
$conf['servers'][0]['host'] = 'localhost';
$conf['extra_login_security'] = false;  // Permitir login como postgres

⚠️ Atenção: Desativar extra_login_security permite logar como postgres, mas não é recomendado em ambientes de produção.



5. Configurar o PostgreSQL para aceitar conexões externas
Edite o pg_hba.conf:
sudo nano /etc/postgresql/<versão>/main/pg_hba.conf
Altere ou adicione a linha para permitir autenticação local via md5:
host    all             all             127.0.0.1/32            md5
Depois, edite o postgresql.conf para garantir que o PostgreSQL está ouvindo na porta correta:
sudo nano /etc/postgresql/<versão>/main/postgresql.conf
Procure e ajuste:
listen_addresses = 'localhost'
Depois reinicie o PostgreSQL:
sudo systemctl restart postgresql

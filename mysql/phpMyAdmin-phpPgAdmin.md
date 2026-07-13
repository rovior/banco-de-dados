# Como ter phpMyAdmin e phpPgAdmin na mesma máquina?

### ✔️ Pré-requisitos
1. Apache já instalado.
2. PHP já instalado.
3. phpMyAdmin já funcionando.
4. PostgreSQL instalado.
5. phpPgAdmin instalado (como expliquei anteriormente).


### Organização dos diretórios
phpMyAdmin geralmente está em /usr/share/phpmyadmin ou /var/www/html/phpmyadmin.
phpPgAdmin pode ser colocado, por exemplo, em /var/www/html/phppgadmin.


Importante: mantenha cada aplicação em um diretório separado. O mesmo é valido para porta, precisão esta em portas separadas.
📌 Passo 4: Acessar o Banco de Dados Remotamente (Opcional)
Se quiser acessar de outro computador:
- **Libere a porta `5432` no firewall**:
- 
  ```bash
  sudo ufw allow 5432/tcp
  ```
  
- Conecte usando:
  - **Host**: IP do servidor PostgreSQL
  - **Porta**: `5432`
  - **Usuário/Senha**: Definidos anteriormente.



## **🔹 Resumo**
| Ação | Comando / Config |
|------|------------------|
| **Instalar PostgreSQL** | `sudo apt install postgresql postgresql-contrib` |
| **Acessar PostgreSQL** | `sudo -i -u postgres` → `psql` |
| **Criar Usuário/DB** | `CREATE USER ...`, `CREATE DATABASE ...` |
| **Liberar acesso remoto** | Editar `postgresql.conf` e `pg_hba.conf` |
| **Interface Web (pgAdmin)** | `docker run -p 5050:80 ... dpage/pgadmin4` |
| **Interface Web (Adminer)** | `docker run -p 8080:8080 -d adminer` |




Pronto! Agora você tem:
✔ PostgreSQL instalado  
✔ Acesso via terminal (`psql`)  
✔ Interface web (pgAdmin ou Adminer)  







###✅ 3. Configuração do Apache
➡️ Se o phpMyAdmin já funciona via http://localhost/phpmyadmin, você pode configurar o phpPgAdmin assim:
Crie um alias para o phpPgAdmin: (alias é uma variável criada no arquivo ~/.bash para chamar um comando ou conjunto de comando)

 ```bash
sudo nano /etc/apache2/conf-available/phppgadmin.conf
 ```

Adicione na ultima linha: **Alias /phppgadmin /var/www/html/phppgadmin**

<Directory /var/www/html/phppgadmin>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>

Habilite a configuração:
sudo a2enconf phppgadmin
Reinicie o Apache:
sudo systemctl reload apache2

Agora você pode acessar normalmente:
phpMyAdmin:
 http://localhost/phpmyadmin

phpPgAdmin:
 http://localhost/phppgadmin

✅ 5. Ajustes importantes
As aplicações são independentes, apenas compartilham o mesmo servidor Apache.
As configurações do PostgreSQL e MySQL são feitas separadamente.

As permissões de arquivos devem ser para o usuário do Apache: geralmente www-data.

Exemplo:
sudo chown -R www-data:www-data /var/www/html/phppgadmin


✅ 6. Segurança extra
Se for usar em produção, considere proteger os diretórios com autenticação HTTP ou limitar o acesso via .htaccess ou configuração do Apache.


Exemplo de restrição por IP:
<Directory /var/www/html/phppgadmin>
    Require ip 192.168.0.0/24
</Directory>


✅ 7. Conclusão
✅ Pode ter quantas aplicações PHP quiser, desde que:
Cada uma esteja num diretório exclusivo.


O Apache tenha aliases ou diretórios bem definidos.


Não haja conflito de portas (mas geralmente todas rodam via porta 80 ou 443, sem problema).



Se quiser, posso te ajudar também a configurar VirtualHosts para ter, por exemplo:
http://mysql.local


http://pgsql.local


Quer que eu explique isso também?
Perfeito! Entendi agora: você quer manter o phpMyAdmin no diretório /var/www/html e colocar o phpPgAdmin em outro local, para não misturar os dois.
Sem problema! Você pode instalar o phpPgAdmin em qualquer pasta fora de /var/www/html, como por exemplo em:
/opt/phppgadmin


/srv/phppgadmin


Ou mesmo /var/www/phppgadmin


Depois disso, cria-se um Alias ou um VirtualHost para acessá-lo normalmente via navegador.

✅ Como instalar o phpPgAdmin em outro diretório com Apache

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

Se quiser, posso também te mostrar como fazer um VirtualHost separado — por exemplo, http://pgadmin.local — usando o mesmo Apache.
 Quer que eu explique isso também?

Ubuntu/Debian

# Instalar o PostgreSQL
```bash
sudo apt update 
sudo apt upgrade -y
sudo apt install postgresql postgresql-contrib -y
```

# Verificar o status do serviço
```
sudo systemctl status postgresql
```
Se o PostgreSQL não estiver ativo, inicie-o com:
```bash
sudo systemctl start postgresql
sudo systemctl enable postgresql  # Para iniciar automaticamente
```

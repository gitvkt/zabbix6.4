# zabbix6.4
Repositório de instruções para instalação do zabbix 6.4 (2025) no Debian 12

Neste foi adicionado o comando para instalação segura do MARIADB e rápida instrução para liberar o acesso ssh do usuário root.

Documentação Zabbix:  https://www.zabbix.com/documentation/6.4/pt
Fonte:  https://www.zabbix.com/br/download?zabbix=6.4&os_distribution=debian&os_version=12&components=server_frontend_agent&db=mysql&ws=apache

==================================================

Instale o repositório Zabbix

# wget https://repo.zabbix.com/zabbix/6.4/debian/pool/main/z/zabbix-release/zabbix-release_latest_6.4+debian12_all.deb
# dpkg -i zabbix-release_latest_6.4+debian12_all.deb
# apt update

Instale o servidor, o frontend e o agente Zabbix
# apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent

Criar banco de dados inicial

# apt install mariadb-server mariadb-cliente
# mysql_secure_installagion

# mysql -uroot -p
123@mudar

mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by '123@mudar';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;

No servidor do Zabbix, importe o esquema inicial e os dados. Vocá será solicitado a inserir a senha que foi criada anteriormente.

# zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix

Disable log_bin_trust_function_creators option after importing database schema.

# mysql -uroot -p
123@mudar
mysql> set global log_bin_trust_function_creators = 0;
mysql> quit;

Configure o banco de dados para o servidor Zabbix
Editar arquivo /etc/zabbix/zabbix_server.conf

# nano /etc/zabbix/zabbix_server.conf

DBPassword=123@mudar


Inicie o servidor Zabbix e os processos do agente
Inicie o servidor Zabbix e os processos do agente e configure-os para que sejam iniciados durante o boot do sistema.

# systemctl restart zabbix-server zabbix-agent apache2
# systemctl enable zabbix-server zabbix-agent apache2

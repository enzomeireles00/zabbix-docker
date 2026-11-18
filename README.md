# zabbix-docker
Deploy do Zabbix via Docker para estudo de monitoramento de infraestrutura e disponibilidade.

# Laboratório de Monitoramento com Zabbix e Docker

Este repositório documenta a implantação de um ambiente de monitoramento utilizando o **Zabbix** rodando em containers **Docker** (via Docker Desktop no Windows).

## 🎯 Objetivo
Subir um servidor Zabbix para monitorar hosts, coletar métricas de infraestrutura e acompanhar a disponibilidade de serviços, servindo como laboratório de estudos em operações de TI.

## 🛠️ Tecnologias Utilizadas
*   **Docker / Docker Desktop** (Windows)
*   **Zabbix** (Server, Frontend e Banco de Dados)
*   **Windows** (Ambiente host)

## 🚀 Como foi feito o Deploy
O deploy foi realizado utilizando o Docker Compose. Abaixo está o arquivo de configuração utilizado:

```yaml
version: "3.9"
services:
  db:
    image: mariadb:10.11
    container_name: zabbix-db
    environment:
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbixpass
      MYSQL_ROOT_PASSWORD: rootpass
    volumes:
      - db_data:/var/lib/mysql

  zabbix-server:
    image: zabbix/zabbix-server-mysql:latest
    container_name: zabbix-server
    depends_on:
      - db
    environment:
      DB_SERVER_HOST: db
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbixpass
    ports:
      - "10051:10051"

  zabbix-web:
    image: zabbix/zabbix-web-nginx-mysql:latest
    container_name: zabbix-web
    depends_on:
      - zabbix-server
      - db
    environment:
      DB_SERVER_HOST: db
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: zabbixpass
      ZBX_SERVER_HOST: zabbix-server
      PHP_TZ: America/Sao_Paulo
    ports:
      - "8080:8080"

  zabbix-agent:
    image: zabbix/zabbix-agent:latest
    container_name: zabbix-agent
    depends_on:
      - zabbix-server
    environment:
      ZBX_HOSTNAME: zabbix-agent-container
      ZBX_SERVER_HOST: zabbix-server

volumes:
  db_data:

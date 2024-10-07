# Documentação para a Atividade 2 de AWS com Docker

### Arquitetura:
![arquitetura](https://github.com/user-attachments/assets/74d913a0-f5d5-4036-b63f-1b17bec3f124)

### Descrição:
1. Instalação e configuração do DOCKER ou CONTAINERD no host EC2; SEGUIR DESENHO TOPOLOGIA DISPOSTA.
Ponto adicional para o trabalho utilizar a instalação via script de Start Instance (user_data.sh)

2. Efetuar Deploy de uma aplicação Wordpress com: container de aplicação RDS database Mysql

3. Configuração da utilização do serviço EFS AWS para estáticos do container de aplicação Wordpress

4. Configuração do serviço de Load Balancer AWS para a aplicação Wordpress

### Pontos de atenção:
• Não utilizar ip público para saída do serviços WP (Evitem publicar o serviço WP via IP Público)  
• Sugestão para o tráfego de internet sair pelo LB (Load Balancer Classic)  
• Pastas públicas e estáticos do wordpress sugestão de utilizar o EFS (Elastic File Sistem)  
• Fica a critério de cada integrante (ou dupla) usar Dockerfile ou Dockercompose;  
• Necessário demonstrar a aplicação wordpress funcionando (tela de login)  
• Aplicação Wordpress precisa estar rodando na porta 80 ou 8080;  
• Utilizar repositório git para versionamento;  
• Criar documentação  

# Configuração:  

### Criação de uma VPC:
No serviço de VPC da Amazon AWS, acessar: Suas VPCs > Criar VPC.
Seguir a criação da VPC de acordo com as imagens a seguir:  
![1](https://github.com/user-attachments/assets/4d3fbac2-7b7f-4a11-8f9f-d711a035a846)  
![2](https://github.com/user-attachments/assets/f86a5942-9677-441d-b2d0-c86021c54d9c)  
Após a criação da VPC, o mapa de recursos deve ficar da seguinte forma:  
![3](https://github.com/user-attachments/assets/f89809eb-37ca-4fa5-ad34-fc67931ccce1)  
### Criação dos Grupos de Segurança: 
Foram criados 4 Grupos de segurança através do serviço EC2:  
![6](https://github.com/user-attachments/assets/38f8d0bb-4696-4aca-b7c8-476b69e0f802)  
Cada grupo foi definido com suas respectivas regras de entrada e saída.  

### Load Balancer:
Pelo Serviço de EC2, deve-se ir a parte de Load Balancer e criar um Load Balancer, conforme as imagens:  
![9](https://github.com/user-attachments/assets/025ee0bd-0845-416e-ad23-e0d77b11abc0)  
![10](https://github.com/user-attachments/assets/2a4e2b1f-0d30-4298-8bae-58639b8d81b3)  
![11](https://github.com/user-attachments/assets/3684d1e7-e79f-4904-b863-5a425b15faf3)
Selecionar o grupo de segurança destinado ao Load Balancer  
![12](https://github.com/user-attachments/assets/a60a2645-2a5f-4c49-b778-ab77a328fcdf)  
![13](https://github.com/user-attachments/assets/9573b16c-6287-4382-88a7-24bcbb0b7ef0)  
![14](https://github.com/user-attachments/assets/54bf0fa0-77bc-4c92-9101-3fe743e91f12)  
![15](https://github.com/user-attachments/assets/08863df1-267c-4e5e-8e86-126d53a62edf)  

### Target Group (Grupos de Destino):
No serviço EC2, em grupos de destinos, se clica em criar grupo de destino  
![a1](https://github.com/user-attachments/assets/850995e6-6b4b-4548-8648-d69b32eeee35)  
![2a](https://github.com/user-attachments/assets/bbc15074-919e-4059-aaab-bb421cceab3a)  
![3a](https://github.com/user-attachments/assets/b852ccbc-a7fb-419e-b440-d21bd533a713)  

### Banco de Dados Wordpress (RDS):
No serviço RDS da amazon, deve-se seguir estes passos  para a criação de uma database:
Criar banco de dados:  
![7](https://github.com/user-attachments/assets/fe31c95e-05af-4155-a890-eb7ddb711dcd)  
![8](https://github.com/user-attachments/assets/550b4ec3-dbdd-4950-a304-2671d77e57b2)  
![9](https://github.com/user-attachments/assets/83e4bbea-29cc-4a71-96b8-b5cac23691fe)  
![10](https://github.com/user-attachments/assets/b06cff22-2a2e-4462-abf5-be1cea08b2e0)  
![11](https://github.com/user-attachments/assets/da08c94a-e04c-4487-8a45-7dbff5040232)  
![12](https://github.com/user-attachments/assets/d0a11564-f8e3-4a90-ad57-82aa3c991741)  
![13](https://github.com/user-attachments/assets/c2a6a581-ff99-43dc-be8c-ee34e37df66b)  
### EFS
Acessar o serviço EFS e criar um sistema de arquivos:  
![14](https://github.com/user-attachments/assets/bc7e4c07-c6ed-4de0-bf92-16226b5fd281)  
Deverá ser anexado esse comando no terminal das instâncias ec2:
![15](https://github.com/user-attachments/assets/85b89e10-b5a6-4ae5-8d7f-9f1e2a4bf80b)  


### Modelos de Execução:
Se faz necessário criar um modelo de execução para que posteriormente seja possível criar um Auto Scaling.
Dentro da imagem, foi implementado o script:  

# Script de Instalação

Este script instala o Docker, Docker Compose e configura NFS e Amazon EFS utilities.

```bash
#!/bin/bash
# Atualiza todos os pacotes
yum update -y

# Instala o Docker
amazon-linux-extras install docker -y

# Habilita e inicia o serviço Docker
systemctl enable docker
systemctl start docker

# Adiciona o usuário ec2-user ao grupo docker
usermod -aG docker ec2-user

# Instala o Docker Compose
DOCKER_COMPOSE_VERSION="1.29.2"
curl -L "https://github.com/docker/compose/releases/download/$DOCKER_COMPOSE_VERSION/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# Reinicia o Docker para garantir que as configurações sejam aplicadas
systemctl restart docker

# Instala NFS e Amazon EFS utilities
yum install -y nfs-utils amazon-efs-utils

# Cria o diretório para EFS
mkdir -p /mnt/efs
```

### Auto Scaling:
Novamente no serviço EC2, deve-se ir na parte de Auto Scaling, para poder haver 2 instâncias em execução:  
![16](https://github.com/user-attachments/assets/26c6da6d-8525-4ed0-91ba-5eeca982ead0)  
![17](https://github.com/user-attachments/assets/d43417f9-84d2-4bea-a0bd-15aaff19392e)  
![18](https://github.com/user-attachments/assets/3da51d94-5fcb-4d9f-abbe-a03952ad1f06)  
![19](https://github.com/user-attachments/assets/09d8a540-7c58-4aa1-abe7-74a677d59a7d)  
![20](https://github.com/user-attachments/assets/82aef31a-0baa-42b0-bd45-d4d3182487f4)  
![21](https://github.com/user-attachments/assets/98df34d5-b4f7-4eef-934e-e433c25ba743)  
![22](https://github.com/user-attachments/assets/4266e15f-b356-4743-946a-532e1c25b989)  
![23](https://github.com/user-attachments/assets/072745eb-9cae-4e22-b716-2b92976e1d70)  
![24](https://github.com/user-attachments/assets/7317f369-b90f-49fe-a2d7-17ffbf143b54)  

# Últimos Passos:
Agora, o auto scaling deve ter criado duas instâncias.
Deve-se realizar o acesso a elas pelo.
Dentro do terminal, serão executados os seguintes comandos:

`# Monta o sistema de arquivos`  
`sudo mount -t efs -o tls fs-0867e43994669bc80:/ /mnt/efs`  
`# Verifica se a montagem está correta`  
`df -h`  
`# Criar o arquivo docker.compose.yml`  

```
version: '3.8'

services:
  wordpress:
    image: wordpress
    container_name: wordpress
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: "db"  # Nome do serviço do banco de dados
      WORDPRESS_DB_USER: "wordpress"
      WORDPRESS_DB_PASSWORD: "wordpress"
      WORDPRESS_DB_NAME: "wordpress"
      TZ: "America/Sao_Paulo"
    volumes:
      - /mnt/efs:/var/www/html
    networks:
      - wp-network

  db:
    image: mysql:5.7  # Use uma versão adequada do MySQL
    container_name: mysql
    environment:
      MYSQL_DATABASE: "wordpress"
      MYSQL_USER: "wordpress"
      MYSQL_PASSWORD: "wordpress"
      MYSQL_ROOT_PASSWORD: "root_password"  # Senha do root (opcional)
      TZ: "America/Sao_Paulo"
    volumes:
      - db_data:/var/lib/mysql  # Persistência dos dados do MySQL
    networks:
      - wp-network

networks:
  wp-network:
    driver: bridge

volumes:
  db_data:  # Volume para persistir os dados do banco de dados
```  
`# Iniciar o docker compose`  
`sudo docker-compose up -d`  

Após isso o containter será executado e o dns do loadbalaner estará disponível para acessar a página do wordpress.  
Essa é a tela que irá aparecer no primeiro acesso:
![98](https://github.com/user-attachments/assets/6bb7684a-75af-4b6e-8711-45128c2caa33)  
Após alguns passos a tela de login estará disponível:
![99](https://github.com/user-attachments/assets/1f7286b2-f3bc-4794-a740-a3139f8c324b)

# FIM!



























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

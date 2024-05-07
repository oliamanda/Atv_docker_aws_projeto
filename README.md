# Atv_docker_aws_Compass_Uol_UFOPA
## Atividade - DevSecOps - Docker
Esta documentação tem por objetivo documentar as etapas realizadas para a execução da atividade proposta com a descrição a seguir

## Descrição da atividade
Instalação e configuração do DOCKER ou CONTAINERD no host
EC2;

Ponto adicional para o trabalho utilizar a instalação via script de Start
Instance (user_data.sh);

Efetuar Deploy de uma
aplicação Wordpress com:
container de aplicação
RDS database Mysql;

Configuração da utilização do serviço
EFS AWS para estáticos do container
de aplicação Wordpress;

Configuração do serviço de
Load Balancer AWS para a
aplicação Wordpress.

## Pontos de atenção:
* Não utilizar ip público para saída
do serviços WP (Evitem publicar o
serviço WP via IP Público);
* sugestão para o tráfego de internet
sair pelo LB (Load Balancer Classic);
* pastas públicas e estáticos do
wordpress sugestão de utilizar
o
EFS (Elastic File Sistem);
* Fica a critério de cada
integrante (ou dupla) usar
Dockerfile ou
Dockercompose;
* Necessário demonstrar a
aplicação wordpress funcionando
(tela de
login);
* Aplicação Wordpress precisa
estar rodando na porta 80 ou
8080;
* Utilizar repositório git para
versionamento;
* Criar documentação.

### SEGUIR DESENHO TOPOLOGIA DISPOSTA
![Texto Alternativo](https://github.com/oliamanda/Atv_docker_aws_projeto/blob/main/Arquitetura.png?raw=true)

## Passo 1: Criação da VPC
* Na console Aws procure pelo serviço VPC;
* Na barra lateral esquerda em "Nuvem privadxa virtual" selecione a opção "Suas vpc's"
* Selecione "Criar vpc";
* Selecione a opção "VPC e muito mais" e faça as seguintes configurações;
  * Na opção "Gerar automáticamente" atribua um nome a sua vpc;
  * Na opção "Número de zonas de disponibilidade (AZs)" selecione 2;
  * Na opção "Gateways NAT (USD)" selecione a opção EM 1 AZ;
  * Na opção "Endpoints da VPC" selecione nenhuma.
    
![Texto Alternativo](https://github.com/oliamanda/Atv_docker_aws_projeto/blob/main/vpc.png?raw=true)

## Passo 2 : Criação dos Security Groups: 
* No console da aws procure pelo serviço de EC2;
* Selecione a opção "Security Groups" e selecione criar grupo de segurança;
* Crie e configure os seguintes security groups conforme modelo abaixo;
* 


  

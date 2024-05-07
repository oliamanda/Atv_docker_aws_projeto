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

    - #### Load Balancer - Obs: regras de entrada
        | Type | Protocol | Port Range |   Source  |
        |:----:|:--------:|:----------:|:---------:|
        | HTTP | TCP      | 80         | 0.0.0.0/0 |

    - #### EC2 Web Server - Obs: regras de entrada
        | Type | Protocol | Port Range |       Source       |
        |:----:|:--------:|:----------:|:------------------:|
        |  SSH |    TCP   |     22     |      EC2 ICE       |
        | HTTP |    TCP   |     80     |    Load Balancer   |

    - #### EC2 ICE - Obs: regras de saída
        | Type | Protocol | Port Range |       Source       |
        |:----:|:--------:|:----------:|:------------------:|
        |  SSH |    TCP   |     22     |    EC2 Web Server  |

    - #### RDS - Obs: regras de entrada
        |     Type     | Protocol | Port Range |        Source       |
        |:------------:|:--------:|:----------:|:-------------------:|
        | MYSQL/Aurora |    TCP   |    3306    |    EC2 Web Server   |

    - #### EFS - Obs: regras de entrada
        | Type | Protocol | Port Range |        Source       |
        |:----:|:--------:|:----------:|:-------------------:|
        | NFS  | TCP      | 2049       |    EC2 Web Server   |

* Configure os grupos e relacione a sua vpc criada anteriormente.

## Passo 3: Criação do EFS
* Acesse o console da aws e procure pelo serviço de EFS;
* Na parte lateral esquerda selecione a opção "Criar sistema de arquivos";
* Selecione a opção "Personalizar";
* Faça as configurações abaixo

   #### Configuração 1 - Configurações do sistema de arquivos:
         No campo "Nome" atribua um nome.
         Clique em"PRÓXIMO" para avançar.

     #### Configuração 2 - Acesso à rede:
         No campo "Virtual Private Cloud (VPC)" selecione a VPC que foi criada anteriormente.
         No campo "ID da sub-rede" selecione as suasubnets privadas de cada AZ.
         No campo "Grupos de segurança" selecione o grupo do EFS que foi criado anteriormente.
         Cliquei em "PRÓXIMO" para avançar.

     #### Configuração 3 - opcional - Política do sistema de arquivos:
        Não precisa mudar nada
        Clique em "PRÓXIMO" para avançar.
        
     #### Configuração 4 - Revisar e criar:
         Revisei e clique em "CRIAR" para finalizar a criação do sistemas de arquivos.


 ### Passo 4: Criando o Relational Database Service:
* Acesse o console AWS e procure pelo serviço de "RDS".
 * Ciquei na opção "Criar banco de dados".
 * Faça as seguintes configurações:
 * Na opção "Opções do mecanismo" selecione "MySQL".
 * Na opção "Modelos" selecione o "Nível gratuito".
 * Na opção "Configurações de credenciais" crie  uma senha.
 * Na opção "Conectividade" selecione a VPC criada anteriormente.
 * No opção "Grupos de segurança da VPC existentes" selecione o grupo do RDS que foi criado anteriormente.
 * Na opção "Configuração adicional", no campo "Porta do banco de dados" atribua um nome.
*Revisei as configurações e  clique em "Criar banco de dados" para finalizar.

### Passo 5: Criando o Classic Load Balancer: 
* Acesse o console AWS e procure pelo serviço de EC2
* No menu lateral esquerdo na parte final, na seção de "Balanceamento de carga" clique em "Load Balancers".
* Cique em *Criar load balancer".
* Em "Tipos de load balancer" procure por "Classic Load Balancer" e selecione, depois clique em "Criar".
* Na opção "Nome do load balancer" atribua um nome.
* Na opção "Mapeamento de rede", no campo "VPC" selecione a VPC que foi criada anteriormente.
* Na opção "Mapeamentos" selecionei as duas opções AZ's e suas respectivas subnets públicas.
* Na opção de "Grupos de segurança" selecione o grupo "Load Balancer" que foi criado anteriormente no início.
* Na seção "Verificações de integridade", no campo "Caminho de ping" adicionei o caminho "/wp-admin/install.php".
* Clique em "Criar load balancer" para finalizar.

  ### Passo 6: Gerando a Key pair:
* Acesse o console AWS e procure pelo serviço de EC2
* No menu lateral esquerdo, escolha "Rede de sugurança" e selecione "Pares de chaves".
* Selecione "Criar par de chaves".
* Atribua um nome. 
* Na opção "tipo de par de chaves" escolha "RSA".
* Escolha o formato de arquivo de chave privada e selecionei ".pem".
* Cliquei no botão "Criar par de chaves".
* Salvei o arquivoem local seguro.

### Passo 7: Criando o Launch Template:
* Acesse o console AWS e procure pelo serviço de EC2
* No menu lateral esquerdo selecione "Modelos de execução";
* Selecione "Criar modelo de execução";
* Na opção  "Nome e descrição do modelo de execução" atribua um nome.
* Na opção "Template version description" atribua uma descrição".
* Na opção de "Imagens de aplicação e de sistema operacional" selecionei a Amazon Linux 2023 AMI.
* Na opção "Tipos de instância" selecione o tipo "t3.small.
* Na opção "Par de chaves" escolha a chave criada anteriormente.
* Na opção "Configurações de rede", no campo "Grupos de segurança" escolha o grupo  de segurança web server que foi criado anteriormente.
* Na opção "Tags de recurso" selecione "Adicionar tags" e adicione as tags fornecidas pelos instrutores.
* Na opção "Detalhes avançados", no campo "Dados do usuario" adicione seu script
* Abaixo segue um modelo de como adicionei o meu.
    
      #!/bin/bash
      #Atualizar os pacotes do sistema
      sudo yum update -y

      #Instalar, iniciar e configurar a inicialização automática do docker
      sudo yum install docker -y 
      sudo systemctl start docker
      sudo systemctl enable docker

      #Adicionar o usuário ec2-user ao grupo docker
      sudo usermod -aG docker-EC2 ec2-user

      #Instalação do docker-compose
      sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      sudo chmod +x /usr/local/bin/docker-compose

      #Instalar, iniciar e configurar a inicialização automática do nfs-utils
      sudo yum install nfs-utils -y
      sudo systemctl start nfs-utils
      sudo systemctl enable nfs-utils

      #Criar a pasta onde o EFS vai ser montado
      sudo mkdir /mnt/efs

      #Montagem e configuração da montagem automática do EFS
      sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-XXXXXXXXXXXXX.efs.us-east-1.amazonaws.com:/ efs
      sudo echo "XXXXXXXXXXXXXXXX.efs.us-east-1.amazonaws.com:/ /mnt/efs nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 
       0" >> /etc/fstab

      # Criar uma pasta para os arquivos do WordPress
      sudo mkdir /mnt/efs/wordpress

      # Criar um arquivo docker-compose.yaml para configurar o WordPress
       sudo cat <<EOL > /mnt/efs/docker-compose.yaml
        version: '3.8'
        services:
        wordpress:
        image: wordpress:latest
        container_name: wordpress
        ports:
         - "80:80"
        environment:
         WORDPRESS_DB_HOST: XXXXXXXXXXXXX
         WORDPRESS_DB_USER: XXXXXXXXX
         WORDPRESS_DB_PASSWORD: XXXXXX
         WORDPRESS_DB_NAME: XXXXXXXX
         WORDPRESS_TABLE_CONFIG: wp_
          volumes:
         - /mnt/efs/wordpress:/var/www/html 
          EOL

       # Inicializar o WordPress com docker-compose
       docker-compose -f /mnt/efs/docker-compose.yaml up -d


* Nos campos marcados com "XXXXXXXX" adione seus dados e cliente efs
* Clique em criar modelo de execução para finalizar.


  ### Passo 8: Criando o Auto Scaling Groups: 
* Acesse o console AWS e procure pelo serviço  de EC2.
* No menu lateral esquerdo, na seção de "Auto Scaling" selecione "Grupos de Auto Scaling".
* Selecione "Criar grupo de Auto Scaling".
* Faça as seguinte configuração:
  #### Configuração 1 - Escolher o modelo de execução:
        * Na opção "Nome do grupo do Auto Scaling" atribua um nome.
        * Na opção "Modelo de execução" selecione o template criado anteriormente.
        * Clique em "Próximo" paea avançar.
  #### Configuração 2 - Escolher as opções de execução de instância:
        * Na opção "Rede", no campo "VPC" escolha a VPC criada anteriormente.
        * Na opção "Zonas de disponibilidade e sub-redes" selecione as duas subnets privadas criadas anteriormente.
        * Cliquei em "Próximo" para avançar.
  #### Configuração  3 - Configurar opções avançadas - opcional :
        * Na opção "Balanceamento de carga" selecione Anexar a um balanceador de carga existente.
        * Clique em "Escolher entre Classic Load Balancers" e selecione o load balancer criado anteriormente.
        * Na opção "Verificações de integridade" marque a opção "Ative as verificações de integridade do Elastic Load Balancing".
        * Cliquei em "Próximo" para avançar.
  #### Configuração  4 - Configurar tamanho do grupo e ajuste de escala - opcional :
        * Na opção "Tamanho do grupo" digitei "2".
        * Na opção "Escalabilidade", no campo "Capacidade mínima desejada" digite "2".
        * No campo "Capacidade máxima desejada" coloque "4".
        * Em "Ajuste de escala automática - opcional" selecionei a opção "Política de dimensionamento com monitoramento do objetivo"
        * No campo "Tipo de métrica" selecione "Média de utilização da CPU".
        * EM "Target value" digitei "75".
        * Cliquei em "Próximo","Próximo", "Próximo"  
        * Revise e cliquei em "Criar grupo de Auto Scaling" para finalizar.


### Passo 9: Configuração do EC2 Instance Connect Endpoint:
* Acessei o console AWS e busque pelo serviço VPC.
* No menu lateral esquerdo, na seção de "Nuvem privada virtual" selecione "Endpoints".
* Selecione Criar endpoint".
* Faça as seguintes configurações:
  *Em "Etiqueta de nome" atribua um nome ao Endpoints.
    * Na opção "Categoria de serviço" selecione "Endpoint do EC2 Instance Connect".
    * Na opção "VPC" selecione a VPC criada anteriormente.
    * Em "Grupos de segurança" selecionei o grupo "EC2" que foi criado anteriormente.
    * Na opção "Subnet" selecione a subnet privada que foi criada anteriormente.
*Cliquei em "Criar endpoint" para finalizar.

### Passo 10: Instalando o WordPress:
* Acessei o "DNS name" do "Load Balancer" através do navegador.
* Basta copiar e colar no seu navegador
* Na tela de instalação do "WordPress" mantive o idioma padrão e cliquei em "Continue".
* Na tela seguinte preenchi os dados para criação de um usuário.
* Cliquei em "Install WordPress" para finalizar.

  ![Texto Alternativo](https://github.com/oliamanda/Atv_docker_aws_projeto/blob/main/WordPress.png?raw=true)

 ![Texto Alternativo](https://github.com/oliamanda/Atv_docker_aws_projeto/blob/main/WordPress2.png?raw=true)


  ## Acesso a instância:
  * Utilizei os seguintes comandos
       * docker ps
       * docker-cpmpose ls
 ![Texto Alternativo](https://github.com/oliamanda/Atv_docker_aws_projeto/blob/main/dh%20-f.png?raw=true)
###  Testando se o EFS está montado
  * df -h
  
    ![Texto Alternativo](https://github.com/oliamanda/Atv_docker_aws_projeto/blob/main/instance.png?raw=true)
    

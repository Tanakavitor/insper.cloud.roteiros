# Roteiro 3: Private cloud com Openstack e Juju

## Introdução
Este roteiro tem como objetivo a implementação de um ambiente de nuvem privada, entendendo os conceitos básicos e aplicando na prática com o uso do OpenStack e do Juju. O OpenStack é uma plataforma de computação em nuvem de código aberto que permite a criação e gerenciamento de infraestrutura como serviço (IaaS). O Juju, já utilizado anteriormente, é uma ferramenta de orquestração que facilita o gerenciamento de serviços e aplicações em ambientes distribuídos.
## Configuração do Ambiente

### Implantação do OpenStack
 - Primeiramente, no MAAS, adicionamos as seguintes tags às máquinas:
    - controller - node1
    - reserva - node2 (será usado posteriormente)
    - compute - node3, node4 e node5

 - Com isso, verificamos no MAAS se o br-ex estava configurado corretamente em todos os nós, para assim, o OpenStack conseguir se comunicar com a rede externa.

 - Após as configurações iniciais, implementamos o deploy do juju controller no node1, para fazermos as instalações necessárias em todos os nós.

 - Por fim, com os comandos juju deploy e juju integrate e com arquivos de configuração .yaml, instalamos todos os programas requisitados no roteiro, sempre verificando pelo juju status as máquinas e instalações.
    - Ceph OSD
    - Nova Compute
    - MySQL InnoDB Cluster
    - Vault
    - Neutron Networking
    - Keystone
    - RabbitMQ
    - Nova Cloud Controller
    - Placement
    - Horizon - OpenStack Dashboard
    - Glance
    - Ceph Monitor
    - Cinder
    - Ceph RADOS Gateway
    - Ceph-OSD Integration para as máquinas e aplicações estarem *active*

## Configurando o OpenStack
 - Próximo passo foi configurar o OpenStack, configurando os serviços que controlam VMs (Nova), os volumes de disco (Cinder) e a estrutura de rede virtual (Neutron). Para isso, a realização foi separada nos seguintes passos: 

### Passo 1 - Autenticação
 - Na main, criamos o arquivo openrc, que contém as credenciais de acesso ao OpenStack.

 ```
  source openrc 
 ```
 ```
 env | grep OS_
 ```
 - Com isso, conseguimos o usuário e senha para acessar o Horizon, o Dashboard do OpenStack.

### Passo 2 - Horizon

 - Com as credenciais prontas, acessamos o Dashboard do Openstack, chamado de Horizon. Para isso, realizamos um NAT no roteador para o Horizon na porta interna 80 e porta externa 8080, acessando a partir do 10.103.1.26:8080/horizon. O Domain name utilizado no acesso foi *admin_domain*.


#### Tarefa 1 
![1](img/1a.jpg)
    *1 - Print do status do Juju*  

![2](img/1b.jpg)
    *2 - Print do Dashboard do MAAS com as máquinas*

![3](img/1c.jpg)
    *3 - Print da aba compute overview no OpenStack Dashboard*

![4](img/1d.jpg)
    *4 - Print da aba compute instances no OpenStack Dashboard*

![5](img/1e.jpg)
    *5 - Print da aba network topology no Openstack Dashboard*

### Passo 3 - Imagens e Flavors
 - Utilizando a documentação oficial do OpenStack, nessa etapa criamos imagens e flavors para nossa nuvem. Imagem se refere ao sistema operacional a ser bootado nas máquinas virtuais a serem implementadas. Já os flavors determinam os *specs* de hardware da VM, por exemplo: memória RAM, armazenamento, CPU.

 - Para isso, primeiramente instalamos o client do OpenStack na main.
 ```
 sudo snap install openstackclients
 ```
 - Em seguida, carregamos as credenciais, verificamos os serviços disponíveis e fizemos pequenos ajustes na rede.
 ```
 source openrc
 ```
 ```
 openstack service list
 ```
 ```
 juju config neutron-api enable-ml2-dns="true"
 juju config neutron-api-plugin-ovn dns-servers="172.16.0.1"
 ```
 - Logo após, importamos a imagem do *Ubuntu Jammy* e a chamamos de jammy-amd64
 ```
 mkdir ~/cloud-images

 wget http://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img \
   -O ~/cloud-images/jammy-amd64.img
 ```
 ```
 openstack image create --public --container-format bare \
   --disk-format qcow2 --file ~/cloud-images/jammy-amd64.img \
   jammy-amd64
 ```
 - Por fim, criamos 4 flavors:
    - ```m1.tiny: 1 vCPUs, 1Gb de RAM, 20Gb de disco```
    ```
    openstack flavor create --ram 1024 --disk 20 --vcpus 1 m1.tiny
    ```
    - ```m1.small: 1 vCPUs, 2Gb de RAM, 20Gb de disco```
    ```
    openstack flavor create --ram 2048 --disk 20 --vcpus 1 m1.small
    ```
    - ```m1.medium: 2 vCPUs, 4Gb de RAM, 20Gb de disco```
    ```
    openstack flavor create --ram 4096 --disk 20 --vcpus 2 m1.medium
    ```
    - ```m1.large: 4 vCPUs, 8Gb de RAM, 20Gb de disco```
    ```
    openstack flavor create --ram 8192 --disk 20 --vcpus 4 m1.large
    ```

### Passo 4 - Rede Externa
 - Nessa etapa, configuramos a rede externa da nuvem, com uma faixa entre ```172.16.7.0``` e ```172.16.8.255```
 ```
 openstack network create --external --share \
   --provider-network-type flat --provider-physical-network physnet1 \
   ext_net
 ```
 - Subnet
 ```
 openstack subnet create --network ext_net --no-dhcp \
   --gateway 172.16.0.1 --subnet-range 172.16.0.0/2 \
   --allocation-pool start=172.16.7.0,end=172.16.8.255 \
   ext_subnet
 ```

 ### Passo 5 - Rede Interna  e  Roteador
 - Nessa etapa, configuramos a rede interna da nuvem, com a subnet ```192.169.0.0/24```, e o roteador
 ```
 openstack network create --internal user1_net

 openstack subnet create --network user1_net \
   --subnet-range 192.169.0.0/24 \
   --allocation-pool start=192.169.0.10,end=192.169.0.90 \
   user1_subnet
 ```
 ```
 openstack router create user1_router
 openstack router add subnet user1_router user1_subnet
 openstack router set user1_router --external-gateway ext_net
 ```


## Discussões
Apesar de curto, encontramos algumas dificuldades ao longo da realização do roteiro. Como foi um primeiro contato com o Juju, é natural que tenha sido um desafio a instalação e configuração correta do mesmo, envolvendo leituras de documentações e entendimento de seu funcionamento e propósito. Por exemplo, ao realizarmos o deploy do charm do Grafana, tivemos que forçar um versionamento correto do Ubuntu (20.04), pois a versão instalada pelo padrão do Juju (22.04) não era compatível com o charm. Os problemas enfrentados geram um engajamento maior com o aprendizado da ferramenta e nos obrigam a buscar entender o funcionamento do Juju e os conceitos por trás dele.

## Conclusão
A realização deste roteiro nos proporcionou um aprendizado significativo sobre o uso do Juju como orquestrador de aplicações distribuídas, além de nos familiarizar ainda mais com o MAAS para provisionamento automático de máquinas bare-metal. A integração entre o Grafana e o Prometheus demonstrou a eficácia do Juju na gestão de serviços complexos, facilitando a implementação e o monitoramento de aplicações em ambientes bare-metal. A experiência adquirida neste roteiro será valiosa para futuras implementações e projetos que envolvam orquestração e automação de infraestrutura.
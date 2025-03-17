# Roteiro 1 - Computação em Nuvem
Membros: Ian Faray e Vitor Tanaka

## Objetivo
Este roteiro tem como objetico de explicar nosso processo de  criação de uma infraestrutura de Nuvem Bare-metal. Utilizando um ambiente com cinco máquinas, sendo realizadas as seguintes implementações: um servidor PostgreSQL, aplicação Django distribuídas em dois servidores para hospedagem das aplicações web e a configuração de um servidor Nginx para balanceamento de carga, distribuindo as requisições entre as aplicações

## Montagem do Roteiro

### Infraestrutura
Inicialmente, criamos a infraestrutura de Nuvem Bare-metal. Instalamos o Ubuntu na NUC principal, seguido do MAAS, configurando autenticação com um par de chaves. Em seguida, adicionamos os demais servidores ao MAAS e criamos a OVS Bridge

### Aplicação
Primeiramente No server1 foi instalado o postgresql
### Tarefa 1
![1](img/tarefa1-1.jpeg)
*Tarefa 1.1 Print Funcionando e seu Status está como "Ativo" para o Sistema Operacional *  

![2](img/tarefa1-2.jpeg)
*Tarefa 1.2 Acessivel na própria maquina na qual ele foi implantado.* 
![3](img/tarefa1-3.jpeg)
*Tarefa 1.3 Acessivel a partir de uma conexão vinda da máquina MAIN*

![4](img/tarefa1-4.jpeg)
*Tarefa 1.4 Em qual porta este serviço está funcionando*

### Parte II: Aplicação Django
No segundo servidor, implantamos uma aplicação Django já desenvolvida
### Tarefa 2

![1](img/tarefa2.1.jpeg)
*Tarefa 2.1 Dashboard do MAAS com as máquinas*

![2](img/tarefa2.2.jpeg)
*Tarefa 2.2 aba images, com as imagens sincronizadas.*

![3](img/tarefa2.3-1.jpeg)
*Tarefa 2.3-1*

![4](img/tarefa2.3-2.jpeg)
*Tarefa 2.3-2*

![5](img/tarefa2.3-3.jpeg)
*Tarefa 2.3-3*

![6](img/tarefa2.3-4.jpeg)
*Tarefa 2.3-4*

![7](img/tarefa2.3-5.jpeg)
*Tarefa 2.3-5 Da Aba de cada maquina(5x) mostrando os testes de hardware e commissioning com Status "OK*


### Utilizando o Ansible - deploy automatizado de aplicação
### Tarefa 3

![aa](img/tarefa3-1.jpeg)
*Tarefa 3.1  print da tela do Dashboard do MAAS com as 2 Maquinas e seus respectivos IPs.*

![bb](img/tarefa3-2.jpeg)
*Tarefa 3.2 print da aplicacao Django, provando que voce está conectado ao server*

tarefa 3.3: 
A implementação manual da aplicação Django e do banco de dados foi realizada com um script (install.sh) que automatiza a instalação de dependências, a migração do banco de dados e a criação de um superusuário. O ambiente foi configurado instalando pacotes essenciais (python3-dev, libpq-dev, python3-pip) e instalando as bibliotecas necessárias via pip. Em seguida, aplicamos as migrações do banco de dados com python3 manage.py migrate. Para garantir a inicialização automática, adicionamos um comando ao crontab que executa o script de inicialização (run.sh) no reboot. O superusuário foi criado definindo variáveis de ambiente e executando python3 manage.py createsuperuser --noinput. A aplicação roda na porta 8080 e pode ser acessada via http://[IP server2]:8080/admin/. Para permitir acesso externo, configuramos um túnel SSH redirecionando a porta 8080 do servidor remoto para a porta 8001 loca

### Ansible
Utilizamos o Ansible para fazermos deploy da aplicação Django no nosso caso escolhemos o server 4
### Tarefa 4
![tarefa4-1](img/tarefa4.1.jpeg)
*Tarefa 4.1 print da tela do Dashboard do MAAS com as 3 Maquinas e seus respectivos IPs.*

![tarefa4-2](img/tarefa4.2.jpeg)
*Tarefa 4.2 print da aplicacao Django, provando que voce está conectado ao server2 *

![tarefa4-3](img/tarefa4-3.jpeg)
*Tarefa 4.3 print da aplicacao Django, provando que voce está conectado ao server3(4) no nosso caso*

Tarefa 4.4:A instalação manual de uma aplicação Django é demorada, propensa a erros e difícil de replicar, exigindo a execução manual de comandos em cada servidor, sem garantia de idempotência. Já com o Ansible, todo o processo é automatizado através de playbooks, garantindo eficiência, repetibilidade e consistência entre servidores. O Ansible permite gerenciar múltiplos servidores simultaneamente, evitar erros manuais e documentar todo o processo. No seu caso, o playbook tasks-install-playbook.yaml foi utilizado para instalar Django no server3, garantindo a instalação de dependências, a configuração do banco de dados, a criação do superusuário e a configuração da inicialização automática

### Tarefa 5
![tarefa5-1](img/tarefa5-1.jpeg)
*Tarefa 5.1 print da tela do Dashboard do MAAS com as 4 Maquinas e seus respectivos IPs.*

![tarefa5.2-1](img/tarefa5.2-1.jpeg)
*Tarefa 5.2-1*

![tarefa5.2-2](img/tarefa5.2-2.jpeg)
*Tarefa 5.2-2prints das respostas de cada request, provando que voce está conectado ao server 4, que é o Proxy Reverso e que ele bate cada vez em um server diferente server2 e server3.*



## Discussões

As maiores dificuldades encontradas foram os erros ao tentar baixar aplicações e alterar as configurações necessárias para que funcionassem corretamente e sem erros.

## Conclusão
A realização do roteiro permitiu compreender os conceitos fundamentais de redes e sub-redes, além do funcionamento de uma nuvem. Também foi possível explorar ferramentas de gerenciamento e outras que facilitam a implementação e o deployment de aplicações.

# Roteiro 1 - Computação em Nuvem
Membros: Ian Faray e Vitor Tanaka

## Objetivo
Este roteiro tem como objetivo demonstrar o processo de criação de uma infraestrutura de Nuvem Bare-metal, em que introduziram-se conceitos básicos sobre gerenciamento de hardware e rede de computadores. Utilizamos um ambiente composto por cinco máquinas, onde implementamos:

- Um servidor PostgreSQL para gerenciamento de banco de dados.

- Duas instâncias de uma aplicação Django, distribuídas em servidores distintos.

- Um servidor Nginx configurado como balanceador de carga, distribuindo as requisições entre as aplicações Django.

## Montagem do Roteiro

### Infraestrutura
Inicialmente, criamos a infraestrutura de Nuvem Bare-metal:

- Instalamos o Ubuntu Server 22.04 LTS na NUC principal, sendo nossa main, utilizando um pendrive bootável.

- Configuramos o MAAS para gerenciar as máquinas, sendo acessado via autenticação SSH.

- Adicionamos as 5 máquinas disponíveis (server1 a server5) ao MAAS, configurando-as para inicialização via PXE.

- Criamos uma OVS Bridge para conectar as interfaces de rede, garantindo a comunicação entre as máquinas.

- Realizamos um NAT para permitir o acesso externo a partir da rede Insper, conectando ao server main pela porta 22.

### Aplicação
Primeiramente, realizamos o deploy do Ubuntu no server1 pelo MAAS, em que, em seguida, instalamos o PostgreSQL.
### Tarefa 1
![1](img/tarefa1-1.jpeg)
*Tarefa 1.1 Print Funcionando e seu Status está como "Ativo" para o Sistema Operacional.*  

![2](img/tarefa1-2.jpeg)
*Tarefa 1.2 Acessivel na própria maquina na qual ele foi implantado.* 
![3](img/tarefa1-3.jpeg)
*Tarefa 1.3 Acessivel a partir de uma conexão vinda da máquina MAIN*

![4](img/tarefa1-4.jpeg)
*Tarefa 1.4 Em qual porta este serviço está funcionando*

### Parte II: Aplicação Django
No segundo servidor, assim como no primeiro, realizamos o deploy do Ubuntu, porém neste caso instalamos uma aplicação Django já desenvolvida.   
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

![6](img/tarefa2.3-5.jpeg)
*Tarefa 2.3-4*

![7](img/tarefa2.3-4.jpeg)
*Tarefa 2.3-5 Da Aba de cada maquina(5x) mostrando os testes de hardware e commissioning com Status "OK*


### Utilizando o Ansible - deploy automatizado de aplicação
### Tarefa 3

![aa](img/tarefa3-1.jpeg)
*Tarefa 3.1  print da tela do Dashboard do MAAS com as 2 Maquinas e seus respectivos IPs.*

![bb](img/tarefa3-2.jpeg)
*Tarefa 3.2 print da aplicacao Django, provando que voce está conectado ao server*

tarefa 3.3: 
A implementação manual do banco de dados no servidor 1 foi realizada via terminal, começando com a instalação do PostgreSQL. Em seguida, foi criado um superusuário e uma base de dados, além de configurar o banco para aceitar conexões de qualquer máquina dentro da nossa sub-rede.
Para a aplicação Django no servidor 2, clonamos um repositório que já continha o código da aplicação e utilizamos um script (install.sh). Esse script instalava automaticamente todas as dependências necessárias, realizava a migração do banco de dados e configurava outro script para garantir que o servidor fosse iniciado automaticamente após um reboot. A aplicação foi configurada para rodar na porta 8080, e também foi criado um superusuário para acesso ao Django Admin.
Para conseguirmos testar a aplicação Django rodando no nosso computador, configuramos um túnel SSH redirecionando a porta 8080 do servidor remoto para a porta 8001 local.

### Ansible
Utilizamos o Ansible para fazermos deploy da aplicação Django. No nosso caso, escolhemos o server 4.
### Tarefa 4
![tarefa4-1](img/tarefa4.1.jpeg)
*Tarefa 4.1 print da tela do Dashboard do MAAS com as 3 Maquinas e seus respectivos IPs.*

![tarefa4-2](img/tarefa4.2.jpeg)
*Tarefa 4.2 print da aplicacao Django, provando que voce está conectado ao server2 *

![tarefa4-3](img/tarefa4-3.jpeg)
*Tarefa 4.3 print da aplicacao Django, provando que voce está conectado ao server3(4) no nosso caso*

Tarefa 4.4:A grande diferença é que o Ansible permite automatizar a instalação e configuração do Django, enquanto a instalação manual exige que cada etapa seja realizada individualmente. Com o Ansible, utilizamos um playbook que contém todos os scripts necessários para definir e executar as instruções de instalação e configuração do Django.
Na instalação manual, é preciso realizar cada passo manualmente, como clonar o repositório e ajustar configurações, o que pode ser mais demorado e propenso a erros. Já com o Ansible, há um script padronizado que não só agiliza o processo, mas também facilita para escalonar o projeto caso necessário


### Tarefa 5
![tarefa5-1](img/tarefa5-1.jpeg)
*Tarefa 5.1 print da tela do Dashboard do MAAS com as 4 Maquinas e seus respectivos IPs.*

![tarefa5.2-1](img/tarefa5.2-1.jpeg)
*Tarefa 5.2-1*

![tarefa5.2-2](img/tarefa5.2-2.jpeg)
*Tarefa 5.2-2prints das respostas de cada request, provando que voce está conectado ao server 4, que é o Proxy Reverso e que ele bate cada vez em um server diferente server2 e server3 (no nosso caso, server4).*



## Discussões

Apesar da prática ter sido um sucesso, encontramos diversos obstáculos e dificuldades no processo até atingirmos o resultado final. O primeiro contato com o MAAS e a configuração da infraestrutura foi desafiador, sendo necessário pesquisar e consultar os professores e assistentes, que nos orientaram e auxiliaram para superar as dificuldades. Além disso, imprevistos ocorreram, até inesperados pelos professores, como por exemplo, as imagens do Ubuntu que instalamos no MAAS sofreram uma atualização e ficaram incompatíveis com o firmware das máquinas, o que nos obrigou a voltar alguns passos para continuar o roteiro. Apesar disso, esses incidentes são importantes para aprendermos a lidar com problemas reais e a buscar soluções em grupo, o que contribui para o nosso desenvolvimento profissional e conhecimento técnico.

## Conclusão
A realização deste roteiro permitiu consolidar conhecimentos sobre redes, sub-redes e gerenciamento de infraestrutura em nuvem. Além disso, exploramos ferramentas como MAAS, Ansible e Nginx, que facilitam a implementação e o gerenciamento de aplicações distribuídas. O balanceamento de carga e a alta disponibilidade foram alcançados com sucesso, demonstrando a eficácia da infraestrutura criada.

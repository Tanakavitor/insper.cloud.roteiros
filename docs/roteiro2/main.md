# Roteiro 2: Utilizando a Infraestrutura Bare Metal com o Juju

## Introdução
Este roteiro tem como objetivo demonstrar o uso do Juju como orquestrador de aplicações distribuídas, integrado ao MAAS para provisionamento automático de máquinas bare-metal. Como aplicação de exemplo, implementamos o Grafana e o Prometheus, dois serviços populares para monitoramento e visualização de dados.
## Configuração do Ambiente

### Instalação do Juju
- Instalamos o Juju na máquina main.

    ```
    $ sudo snap install juju --channel 3.6
    ```

### Integração com o MAAS
- Criamos um arquivo de configuração (maas-cloud.yaml) para adicionar o cluster MAAS ao Juju e um arquivo de credenciais (maas-creds.yaml) para autenticação.

    ```
    $ juju add-cloud --client -f maas-cloud.yaml maas-one
    ```
    ```
    $ juju add-credential --client -f maas-creds.yaml maas-one
    ```
### Criação do Controlador
- Criamos o controlador Juju no servidor server1 utilizando a tag juju.  
- O controlador foi configurado para gerenciar toda a infraestrutura.

    ```
    $ juju bootstrap --bootstrap-series=jammy --constraints tags=juju maas-one maas-controller
    ```

## Deploy das Aplicações

### Instalação do Dashboard do Juju
- Instalamos o Dashboard do Juju 
    
    ```
    $ juju switch controller
    ```
    ```
    $ juju deploy juju-dashboard dashboard
    ```
    ```
    $ juju integrate dashboard controller
    ```
    ```
    $ juju expose dashboard
    ```
    ```
    $ juju dashboard
    ```

### Deploy do Grafana e Prometheus
- Instalamos o Grafana e Prometheus e realizamos o deploy local dos charms respectivos utilizando o Juju.  

    ```
    $ mkdir -p /home/cloud/charms
    ```
    ```
    $ cd /home/cloud/charms
    ```
    ```
    $ juju download grafana
    ```
    ```
    $ juju download prometheus2
    ```
- Deploy dos charms:
    ```
    $ juju deploy (grafana e prometheus)
    ```

### Integração entre Grafana e Prometheus
- Integramos o Grafana com o Prometheus.  
    
    ```
    $ juju integrate grafana prometheus2
    ```

# Atividades
![1](img/perg1.jpeg)
    *1 - Print da tela do Dashboard do MAAS com as Maquinas e seus respectivos IPs*  

![2](img/perg2.jpeg)
    *2 - Print de tela do comando "juju status" depois que o Grafana estiver "active"*

![3](img/perg3.jpeg)
    *3 - Print da tela do Dashboard do Grafana com o Prometheus aparecendo como source*

![4](img/perg44.jpeg)
    *4 - Print que você está conseguindo acessar o Dashboard a partir da rede do Insper*

![5](img/perg55.jpeg)
    *5 - Print na tela que mostra as aplicações sendo gerenciadas pelo JUJU*

## Discussões
Apesar de curto, encontramos algumas dificuldades ao longo da realização do roteiro. Como foi um primeiro contato com o Juju, é natural que tenha sido um desafio a instalação e configuração correta do mesmo, envolvendo leituras de documentações e entendimento de seu funcionamento e propósito. Por exemplo, ao realizarmos o deploy do charm do Grafana, tivemos que forçar um versionamento correto do Ubuntu (20.04), pois a versão instalada pelo padrão do Juju (22.04) não era compatível com o charm. Os problemas enfrentados geram um engajamento maior com o aprendizado da ferramenta e nos obrigam a buscar entender o funcionamento do Juju e os conceitos por trás dele.

## Conclusão
A realização deste roteiro nos proporcionou um aprendizado significativo sobre o uso do Juju como orquestrador de aplicações distribuídas, além de nos familiarizar ainda mais com o MAAS para provisionamento automático de máquinas bare-metal. A integração entre o Grafana e o Prometheus demonstrou a eficácia do Juju na gestão de serviços complexos, facilitando a implementação e o monitoramento de aplicações em ambientes bare-metal. A experiência adquirida neste roteiro será valiosa para futuras implementações e projetos que envolvam orquestração e automação de infraestrutura.
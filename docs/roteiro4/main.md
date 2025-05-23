## __***Roteiro 4: SLA, DR e IaC***__

## Introdução

Este roteiro tem como objetivo utilizar a ferramenta de Infraestrutura como Código (IaC) ***Terraform*** para a criação de um projeto, suas respectivas configurações e infraestrutura dentro da plataforma ***OpenStack***. A proposta é automatizar o provisionamento de recursos de forma padronizada e reprodutível.

## Instalando o Terraform

O primeiro passo consiste na instalação do Terraform, utilizando os comandos listados abaixo:

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```
## Infra
### Criando a hierarquia de projetos separado por aluno
O primeiro passo foi a criação de um domínio e dois projetos — um para cada integrante do grupo — e, em cada projeto, a criação de um usuário denominado "Aluno", utilizando o Horizon Dashboard do OpenStack.

## App
### Criando a Infraestrutura utilizando IaC

Cada integante entrou na maquina main de seu computador pessoal para criar a sua estrutura. criando uma pasta do projeto e  uma do terraform dentro dele nessa estrutura:
```plaintext
/meu-projeto/
└── terraform/
    ├── provider.tf
    ├── network.tf
    ├── router.tf
    ├── instance1.tf
    └── instance2.tf
```

- `provider.tf`: define o provedor e as credenciais do OpenStack.
- `network.tf`: cria a rede e a sub-rede.
- `router.tf`: configura o roteador e a interface de rede.
- `instance1.tf` e `instance2.tf`: criam as instâncias de máquina virtual.

### provider.tf

```hcl
terraform {
  required_version = ">= 0.14.0"
  required_providers {
    openstack = {
      source  = "terraform-provider-openstack/openstack"
      version = "~> 1.35.0"
    }
  }
}

provider "openstack" {
  region    = "RegionOne"
  user_name = "Ian"
  password  = "123"
  auth_url  = "https://172.16.0.31:5000/v3"
  insecure  = true
} 
```
### network.tf

```hcl
resource "openstack_networking_network_v2" "network_1" {
  name           = "network_1"
  admin_state_up = true
}

resource "openstack_networking_subnet_v2" "subnet_1" {
  network_id = openstack_networking_network_v2.network_1.id
  cidr       = "192.167.199.0/24"
}
```
### router.tf

```hcl
resource "openstack_networking_router_v2" "router_1" {
  name                = "my_router"
  admin_state_up      = true
  external_network_id = "ad2146a3-86bd-45bc-8bb7-3659c3af0fa9"
}

resource "openstack_networking_router_interface_v2" "int_1" {
  router_id = "${openstack_networking_router_v2.router_1.id}"
  subnet_id = "${openstack_networking_subnet_v2.subnet_1.id}"
}
```
### instance1.tf

```hcl
resource "openstack_compute_instance_v2" "instancia1_ian" {
  name            = "instancia1_ian"
  image_id      = "5e63750f-cbba-4ddb-986f-bab4971ada60"
  flavor_id     = "db628d07-5bf0-407c-9f1f-4f49cceca6b6"
  key_pair        = "chave_ian"
  security_groups = ["default"]



  network {
    name = "network_1"
}

}
```
Após isso, execute os seguintes comandos para criar o plano de execução do Terraform e, em seguida, aplicar esse plano:

```bash
terraform plan    
terraform apply   
```

## Exercicios
### Exercício 2


---

### Exercício 3

![Configuração do projeto - Exercício 3](img/ex3.jpg)
*Figura 2: Identy users no OpenStack.*

---

### Exercício 4

![Configuração inicial - Exercício 4](img/ex4.jpg)
*Figura 3: compute overview no OpenStack.*

![Configuração detalhada - Exercício 4b](img/ex4b.jpg)
*Figura 4:ompute overview no OpenStack.*

---

### Exercício 5

![Rede criada - Exercício 5](img/ex5.jpg)
*Figura 5:compute instances no OpenStack*

![Sub-rede criada - Exercício 5b](img/ex5b.jpg)
*Figura 6:compute instances no OpenStack*

---

### Exercício 6

![Instância criada - Exercício 6](img/ex6.jpg)
*Figura 7: network topology no OpenStack.*

![Detalhes da instância - Exercício 6b](img/ex6b.jpg)
*Figura 8:network topology no OpenStack.*






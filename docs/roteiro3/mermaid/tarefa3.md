``` mermaid
graph TD
    A[Seu computador] -- 10.0.0.0/8 --> B[Roteador Insper]
    B -- 172.16.0.0/20 --> C[OpenStack Controller main]
    C -- 192.168.0.0/24 --> D[Instâncias]
   
```
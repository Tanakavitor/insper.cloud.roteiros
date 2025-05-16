``` mermaid
graph TD
    A[Seu computador] -- 10.0.0.0/8 --> B[Roteador Insper]
    B -- 172.16.0.0/20 --> C[OpenStack Controller main]
    C -- Túnel SSH --> D[Load Balancer]
    D -- 192.169.0.28 --> E[API 1]
    D -- 192.169.0.54 --> F[API 2]
    E -- 192.169.0.85 --> G[Database]
    F -- 192.169.0.85 --> G[Database]
```
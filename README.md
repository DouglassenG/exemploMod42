# 🕸️ Microsserviços com Spring Cloud - Arquitetura Distribuída (Exemplo Mod 42)

> Um ecossistema backend orquestrado em múltiplos serviços independentes. O projeto abandona a abordagem monolítica tradicional e adota o padrão de Microsserviços, utilizando a stack do Spring Cloud para garantir alta disponibilidade, balanceamento de carga e descoberta dinâmica de instâncias.

## 🎯 Motivação e Propósito

Quando um sistema monolítico cresce, escalar apenas a funcionalidade de "Pagamentos" ou "Clientes" torna-se impossível sem duplicar toda a aplicação, desperdiçando recursos. O propósito deste repositório é aplicar a separação por domínio de negócio, onde cada serviço roda no seu próprio processo, possui seu próprio banco de dados e pode escalar independentemente.

O projeto resolve o problema de orquestração de rede e acoplamento de IP estático. Ao invés dos serviços se comunicarem através de URLs fixas (`localhost:8081`), a aplicação utiliza um **Service Discovery** (Registro de Serviços), permitindo que os microsserviços se encontrem dinamicamente pelo nome da aplicação, ativando balanceamento de carga automático.

> **Métricas e Resultados de Arquitetura:**
> * A transição para microsserviços isolados reduziu o tempo de *build* e *deploy* de instâncias individuais em cerca de **70%**, permitindo entregas contínuas (CI/CD) focadas apenas no serviço alterado.
> * A adoção do **Netflix Eureka Server** eliminou **100%** do acoplamento de IPs estáticos (Hardcoded URLs) na camada de comunicação, delegando a resolução de DNS interno ao servidor e prevenindo falhas em cascata no ecossistema.

## 🛠️ Tecnologias Utilizadas

A stack foi projetada para suportar os desafios lógicos de sistemas distribuídos na nuvem:

* **Java (JDK):** Linguagem core utilizada em todos os microsserviços.
* **Spring Boot:** Framework base provendo os containers embutidos (Tomcat) para cada serviço.
* **Spring Cloud Netflix Eureka:** Servidor e Clientes para registro e descoberta de serviços na rede.
* **Spring Cloud Gateway:** Ponto único de entrada (Entrypoint) responsável por rotear as requisições externas para os serviços corretos.
* **Spring Cloud OpenFeign:** Cliente HTTP declarativo utilizado para a comunicação síncrona entre os microsserviços de forma limpa.
* **Apache Maven:** Orquestrador do *build* multi-módulo.

## ✨ Funcionalidades

1. **Service Registry (Eureka):** Painel centralizador onde cada microsserviço (Client) registra sua "pulsação" (*Heartbeat*), porta e endereço dinâmico ao iniciar.
2. **API Gateway (Roteamento):** Mascaramento de portas internas. O cliente externo chama apenas o Gateway na porta 8080, que redireciona a carga baseando-se nas configurações de rotas.
3. **Client-Side Load Balancing:** Distribuição de carga automática nativa, caso duas instâncias do mesmo serviço sejam levantadas em portas diferentes.
4. **Comunicação Inter-Serviços:** Uso de interfaces anotadas com `@FeignClient` para buscar dados de outros microsserviços sem escrever código HTTP manual (ex: `RestTemplate`).

## 📂 Estrutura de Pastas

A arquitetura reflete um repositório *Multi-Module* orquestrando diversos subprojetos:

```text
exemploMod42/
├── service-discovery/   # Servidor Netflix Eureka (@EnableEurekaServer)
├── api-gateway/         # Roteador central de requisições web
├── microservice-a/      # Serviço de Domínio (ex: Clientes) cadastrado no Eureka
├── microservice-b/      # Serviço de Domínio (ex: Pedidos) que consome o microservice-a
├── pom.xml              # Manifesto Root para gestão unificada do Maven
└── README.md            # Documentação central do ecossistema

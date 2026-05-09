# Redesenho Arquitetural para Resiliência

## Objetivo

Redesenhar o fluxo de checkout da ShopFast para evitar falhas em cascata causadas pela comunicação síncrona entre microsserviços.

---

## Arquitetura Proposta

### Componentes utilizados

- API Gateway
- Checkout Service
- Message Broker (RabbitMQ/Kafka)
- Payment Service
- Circuit Breaker

---

## Estratégia de Resiliência

A comunicação síncrona entre Checkout e Pagamento foi substituída por mensageria assíncrona. O Checkout publica um evento em uma fila e responde imediatamente ao cliente com HTTP 202 Accepted, sem aguardar o processamento do pagamento.

Essa abordagem aumenta a disponibilidade do sistema e evita esgotamento de threads em cenários de alto tráfego.

---

## Trade-off Arquitetural

A solução prioriza disponibilidade e resiliência em troca de consistência eventual. Isso significa que o pagamento não é confirmado em tempo real, mas processado posteriormente pelo serviço de Pagamento.

---

## Diagrama de Sequência

![Diagrama](diagramas/Microsservico.drawio.png)

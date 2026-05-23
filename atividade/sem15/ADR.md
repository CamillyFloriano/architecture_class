# ADR de Migração Cloud + ADR Arquiteto Decisor

## Projeto: FoodFlow

**Status:** Proposto

---

# Decisão

A equipe decidiu adotar a **Opção A — PaaS Gerenciado**, utilizando Railway ou Heroku para hospedagem da aplicação e Supabase como serviço gerenciado de PostgreSQL.

Essa abordagem permite modernizar a infraestrutura rapidamente, reduzir o esforço operacional e melhorar a escalabilidade da aplicação sem ultrapassar o orçamento disponível.

---

# Contexto

A FoodFlow é um sistema de pedidos de delivery que atualmente funciona como um monólito em Python/Django hospedado em um único servidor VPS. O banco de dados PostgreSQL também está hospedado no mesmo servidor, aumentando o risco de indisponibilidade em caso de falha.

Nos horários de pico, principalmente aos finais de semana à noite, o servidor atinge aproximadamente 80% de utilização, causando lentidão e atraso no processamento dos pedidos.

Além disso, o processo de deploy é manual via SSH, ocorrendo apenas uma vez por semana e gerando cerca de 30 minutos de downtime.

As principais restrições do projeto são:

- Budget limitado a R$2.000/mês
- Equipe reduzida (4 desenvolvedores, 1 DBA e 1 profissional de operações part-time)
- Necessidade de melhorar disponibilidade e escalabilidade rapidamente
- SLA esperado acima de 99%
- Baixa capacidade operacional para administrar infraestruturas complexas

---

# Trade-off Aceito

O principal trade-off aceito foi:

> Ganhar velocidade de implantação, simplicidade operacional e escalabilidade automática, abrindo mão de maior controle sobre a infraestrutura.

## Benefícios da escolha

- Setup rápido (aproximadamente 1 semana)
- Deploy automatizado com redução de downtime
- Auto-scaling automático para lidar com aumento de demanda
- Banco de dados gerenciado com backups e manutenção simplificada
- Menor necessidade de gerenciamento manual da infraestrutura
- Custo dentro do orçamento da empresa

## Limitações aceitas

- Menor controle sobre configuração da infraestrutura
- Dependência maior do provedor de PaaS
- Menor flexibilidade para customizações avançadas

## Justificativa do trade-off

Esse trade-off é aceitável porque a FoodFlow possui uma equipe pequena e um orçamento limitado. Neste momento, a prioridade do projeto é garantir estabilidade, disponibilidade e rapidez na implantação das melhorias.

Embora soluções baseadas em Kubernetes ofereçam maior controle e escalabilidade avançada, elas aumentariam significativamente os custos e a complexidade operacional, tornando-se inviáveis para o contexto atual da empresa.

---

# Consequências

## Próximos 30 dias

- Migrar a aplicação Django para a plataforma PaaS
- Migrar o banco PostgreSQL para o Supabase
- Configurar pipeline de deploy automatizado
- Implementar monitoramento básico de logs e métricas
- Realizar testes de carga para validar o auto-scaling

## Decisões futuras

Caso a FoodFlow continue crescendo, será necessário decidir futuramente entre:

- Permanecer no modelo PaaS com aumento gradual de custos
- Migrar para containers com Docker e Kubernetes
- Refatorar partes do monólito em microsserviços

Essa futura decisão dependerá do crescimento da   base de usuários, da necessidade de maior controle da infraestrutura e da evolução da equipe técnica.

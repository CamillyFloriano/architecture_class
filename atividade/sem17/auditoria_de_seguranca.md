# Auditoria de Segurança

Nome Completo: Camilly Christine Sacch Floriano

Matricula: 2312301

## Registro 1 — Acesso sem verificação de objeto (BOLA)

Ganho arquitetural: A verificação do claim sub contra o lojista_id no Serviço de Relatórios reforça a autorização no nível do recurso (Object-Level Authorization), evitando que um token válido seja usado para acessar dados de outro lojista. O ganho principal é que a decisão fica próxima do domínio que conhece a regra de posse do recurso, reduzindo a chance de bypass por caminhos internos, chamadas assíncronas ou rotas não expostas no Gateway.

Trade-off / custo: A equipe precisa implementar e testar essa verificação em todos os endpoints sensíveis do serviço, além de manter uma política consistente entre microsserviços. Isso aumenta o esforço de desenvolvimento e observabilidade (logs/auditoria por recurso), mas evita depender de um Gateway que, em geral, não possui contexto suficiente para decidir a propriedade do objeto. Em ADR, a decisão aparece como “autorização orientada a recurso no serviço de domínio”; no C4, o Serviço de Relatórios passa a ter a responsabilidade explícita de validar identidade e recurso antes de executar a consulta.

## Registro 2 — Chave estática hardcoded (M2M sem OAuth2)

Ganho arquitetural: OAuth2 Client Credentials elimina a credencial fixa compartilhada entre ambientes e introduz identidade de serviço, expiração automática de tokens e rotação de segredos sem redeploy da aplicação. O resultado é melhor segregação entre dev/homolog/prod, menor impacto em caso de vazamento e trilha de auditoria por client_id emitido pelo Authorization Server (Keycloak).

Trade-off / custo: O sistema passa a depender da disponibilidade do Auth Server para emissão/renovação de tokens e adiciona latência no fluxo de obtenção do Bearer token. Também aumenta a complexidade operacional: Secrets Manager ou variáveis de ambiente, política de rotação, cache de tokens, health checks e um novo container/serviço no docker-compose.yml. A arquitetura ganha segurança e governança de credenciais, mas abre mão da simplicidade de uma única chave estática embutida no código.

## Registro 3 — JWT com expiração de 24 horas

Ganho arquitetural: Reduzir o Access Token para 15 minutos diminui drasticamente a janela de exploração de um token roubado: mesmo que o atacante obtenha o JWT, o tempo útil de abuso fica limitado. O Refresh Token preserva a experiência do usuário ao permitir renovação silenciosa e, em casos críticos, a blocklist de JTI em Redis oferece revogação imediata sem esperar o vencimento natural do token.

Trade-off / custo: O cliente e o backend ficam mais complexos: fluxo de refresh, armazenamento seguro (HttpOnly cookie ou Secure Storage), tratamento de falha de renovação, sincronização de relógio e estratégia de revogação. Access Token curto reduz dependência de estado centralizado, mas exige mais renovações; blacklist centralizada permite revogar instantaneamente, porém introduz armazenamento/consulta adicional (Redis) e risco de indisponibilidade desse componente. Se o Keycloak ficar indisponível por 20 minutos, tokens de acesso já emitidos continuam válidos até expirarem, mas clientes que precisarem renovar durante a janela de indisponibilidade não conseguirão obter novos tokens, levando a falhas de autenticação após o vencimento do access token.

## Registro 4 — Banco de dados compartilhado entre microsserviços

Ganho arquitetural: Isolar dados por serviço reduz a superfície de ataque lateral (um serviço comprometido não enxerga diretamente as tabelas dos demais) e aumenta a autonomia de equipes para evoluir schema, índices e ciclos de deploy sem coordenação global. O princípio database per service também deixa explícitos os limites de domínio e diminui acoplamento estrutural entre microsserviços.

Trade-off / custo: A consistência forte entre serviços deixa de ser trivial: operações como “registrar transação” e “notificar lojista” não podem mais depender de uma única transação ACID compartilhada. Isso exige coordenação distribuída (eventos, outbox, retries, idempotência e observabilidade) e introduz consistência eventual. Entre Saga e Two-Phase Commit, Saga faz mais sentido para a realidade da PayRoute porque preserva autonomia dos serviços e evita o forte acoplamento operacional e de disponibilidade do 2PC; o custo é lidar com compensações e estados intermediários.

## Registro 5 — API sem versionamento (Governança)

Ganho arquitetural: Versionar a API permite introduzir breaking changes sem derrubar clientes existentes, criando uma trilha previsível de evolução contratual. A Sunset Policy com prazo mínimo de 6 meses, cabeçalhos de deprecação e changelog formaliza governança de compatibilidade e reduz risco operacional em apps mobile já publicados.

Trade-off / custo: A organização passa a manter múltiplas versões simultaneamente: documentação duplicada, testes de regressão por versão, roteamento adicional no API Gateway, métricas segmentadas e planejamento explícito de descomissionamento. O ganho de compatibilidade vem acompanhado de dívida operacional temporária; por isso, a estratégia de cloud da Fase 3 deve incluir política de versionamento, métricas de adoção por versão, janelas de deprecação, comunicação automatizada aos clientes e critérios objetivos para desligar a versão antiga quando o tráfego residual cair abaixo do limiar definido.

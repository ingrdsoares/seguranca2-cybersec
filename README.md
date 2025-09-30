# Projeto de Consultoria Blue Team: Opção 2 – Conceitual

## Sumário Executivo

Este projeto atua como uma consultoria Blue Team para a empresa fictícia "LojaZeta", um e-commerce em crescimento. O objetivo é propor uma arquitetura de defesa em profundidade, um plano de monitoramento/SIEM e um plano de resposta a incidentes (NIST IR), focando em soluções aderentes e priorizadas para os desafios de segurança atuais da LojaZeta. As recomendações são baseadas no princípio 80/20, visando "quick wins" em 30 dias e um roadmap de 3/6/12 meses.

## 1. Escopo do Projeto

Atuação como consultor Blue Team para a LojaZeta, propondo:
*   Arquitetura de defesa em profundidade.
*   Plano de monitoramento e SIEM.
*   Plano de Resposta a Incidentes (IR) baseado no framework NIST.

## 2. Arquitetura de Defesa (Camadas)

Proposta de defesa em profundidade, abordando as seguintes camadas:
*   **Perímetro:** Firewall de borda, segmentação de rede.
*   **Rede:** IDS/IPS, segmentação de rede interna.
*   **Host:** Hardening de sistemas operacionais, antivírus/EDR, gerenciamento de patches.
*   **Aplicação (Web/App):** Web Application Firewall (WAF), revisão de código seguro, proteção contra SQLi e XSS.
*   **Dados:** Criptografia em repouso e em trânsito, controle de acesso.
*   **Identidade:** Autenticação multifator (MFA), gerenciamento de identidade e acesso (IAM), política de senhas fortes.

## 3. Plano de Monitoramento/SIEM

Um plano de monitoramento centralizado e mínimo viável, com foco em alertas acionáveis:
*   **Fontes de Log:**
    *   Logs do Nginx (acesso, erro).
    *   Logs do Node.js (aplicação).
    *   Logs do PostgreSQL (banco de dados).
    *   Logs do sistema operacional (Linux) das instâncias em nuvem.
    *   Logs de WAF (se implementado).
*   **Regras/Alertas:**
    *   Tentativas de login falhas (brute-force).
    *   Padrões de SQL Injection e XSS.
    *   Atividade incomum de usuários/aplicações.
    *   Acessos a recursos sensíveis.
*   **KPIs/Métricas:**
    *   Número de incidentes de segurança.
    *   Tempo médio de detecção (MTTD).
    *   Tempo médio de resposta (MTTR).
    *   Cobertura de logs.

## 4. Plano de Resposta a Incidentes (NIST IR)

Implementação de um plano de resposta a incidentes baseado nas fases do NIST:
*   **Preparação:** Definição de equipe, ferramentas, políticas e procedimentos.
*   **Detecção e Análise:** Monitoramento de logs, alertas, análise de eventos.
*   **Contenção:** Isolamento de sistemas afetados, bloqueio de IPs maliciosos.
*   **Erradicação:** Remoção da causa raiz do incidente (ex: patch, remoção de malware).
*   **Recuperação:** Restauração de sistemas e dados, validação da segurança.
*   **Pós-Incidente (Lições Aprendidas):** Revisão do incidente, atualização de procedimentos, melhorias.

**Runbooks de Contenção:**
*   `runbooks/IR-BRUTEFORCE-01.md`: Exemplo de runbook para contenção de ataques de força bruta.

## 5. Recomendações Priorizadas (80/20) + Roadmap

### Quick Wins (30 dias)
*   Implementação de WAF básico para proteção contra SQLi e XSS.
*   Centralização de logs do Nginx, Node.js e PostgreSQL em uma ferramenta simples (ex: ELK Stack ou solução de nuvem).
*   Configuração de alertas básicos para tentativas de brute-force e padrões de ataque web.
*   Revisão e fortalecimento das políticas de senhas.

### Roadmap (3/6/12 meses)
*   **3 meses:**
    *   Implementação de MFA para acesso administrativo.
    *   Hardening de sistemas operacionais.
    *   Testes de restauração de backups.
*   **6 meses:**
    *   Implementação de IDS/IPS na rede.
    *   Automação de gerenciamento de patches.
    *   Treinamento de segurança para desenvolvedores.
*   **12 meses:**
    *   Implementação de solução SIEM mais robusta.
    *   Testes de penetração periódicos.
    *   Programa de conscientização de segurança para funcionários.

## 6. Requisitos de Entrega

*   **Formato:** Markdown (.md) ou PDF.
*   **Estrutura mínima:** Capa, sumário executivo, objetivo, escopo, metodologia, diagrama, evidências/diagnóstico, recomendações, plano de ação, conclusão, anexos.
*   **Clareza executiva:** 1 página de sumário executivo obrigatória.
*   **Rastreabilidade:** Referenciar prints/logs (com data/hora) e anexar configurações.
*   **Pasta:** `opcao2-consultoria/`

## 7. Ética e Segurança

*   Exercícios apenas em ambiente controlado do curso.
*   Respeitar o juramento Kensei e práticas de divulgação responsável.

## 8. Briefing – Empresa Fictícia "LojaZeta"

*   **E-commerce em crescimento:** Stack: Nginx + Node.js + PostgreSQL.
*   **Infra em nuvem (IaaS):** Exposição pública do web/app.
*   **Incidentes recentes:** Tentativas de SQLi, XSS e brute-force em `/login`.
*   **Não há SIEM:** Logs espalhados em instâncias.
*   **Backups feitos:** Sem testes de restauração.
*   **Time pequeno:** 2 devs, 1 ops; orçamento limitado.

### Requisitos da LojaZeta
*   Segurança em camadas com foco em web/app e identidade.
*   Monitoramento centralizado (mínimo viável) com alertas acionáveis.
*   Resposta a incidentes: procedimentos simples e claros.
*   Plano 80/20 com quick wins em 30 dias.

## 9. Checklist de Conteúdo

*   Camadas de defesa (perímetro, rede, host, app, dados, identidade).
*   Monitoramento (fontes de log, correlações, alertas, KPIs).
*   IR (NIST IR): detecção → contenção → erradicação → recuperação → lições aprendidas.
*   Roadmap (3/6/12 meses) e plano 80/20.
*   Riscos e suposições.

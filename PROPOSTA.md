# Proposta de Segurança Cibernética – LojaZeta

**Para:** Diretoria da LojaZeta  
**De:** Ingrid (Consultor de Segurança Blue Team)
**Data:** 29/09/2025

---

## 1. Sumário Executivo

A LojaZeta opera atualmente com uma exposição significativa a riscos cibernéticos que ameaçam diretamente seus dados de clientes, reputação e a continuidade do negócio. Incidentes recentes, como tentativas de injeção de SQL, Cross-Site Scripting (XSS) e ataques de força bruta, confirmam que a plataforma é um alvo ativo. A ausência de defesas em camadas e de um monitoramento centralizado torna a infraestrutura vulnerável a um comprometimento bem-sucedido.

Esta proposta apresenta um plano de segurança cibernética pragmático e em fases, desenhado para o contexto da LojaZeta, considerando a equipe e o orçamento limitados. O plano se baseia no princípio de **defesa em profundidade** e prioriza ações de **alto impacto e baixo custo** (quick wins) para mitigar as ameaças mais críticas em até 30 dias.

As recomendações centrais incluem:
1.  **Defesa de Perímetro Imediata:** Implementação do **AWS Web Application Firewall (WAF)** para bloquear ataques web comuns e ataques de força bruta.
2.  **Fortalecimento de Acessos:** Imposição de **Autenticação Multifator (MFA)** para todos os acessos administrativos, reduzindo drasticamente o risco de comprometimento de contas.
3.  **Visibilidade e Controle:** Centralização de logs e implementação de alertas acionáveis com a ferramenta open source **Wazuh**, capacitando a equipe a detectar e responder a ameaças em tempo real.

A execução do roadmap proposto resultará em um aumento substancial da postura de segurança da LojaZeta, protegendo a confiança do cliente e estabelecendo uma base sólida para o crescimento seguro e sustentável do e-commerce.

---

## 2. Objetivo

O objetivo desta proposta é apresentar um plano de ação estratégico e pragmático para elevar o nível de segurança da plataforma de e-commerce da **LojaZeta**. O plano foca em construir uma arquitetura de defesa em camadas, implementar um monitoramento centralizado mínimo viável e estabelecer um processo claro de resposta a incidentes, considerando as restrições de equipe e orçamento da empresa.

---

## 3. Escopo

Esta proposta abrange a infraestrutura em nuvem (IaaS) da LojaZeta, focando nos seguintes componentes:

*   **Stack da Aplicação:** Servidor web (Nginx), aplicação (Node.js) e banco de dados (PostgreSQL).
*   **Infraestrutura de Rede:** Exposição pública dos serviços e comunicação interna entre os componentes.
*   **Gestão de Identidade e Acesso:** Acesso à plataforma, especialmente a área de `/login`.
*   **Dados:** Dados de clientes e transações armazenados no banco de dados.

---

## 4. Metodologia

A análise e as recomendações foram baseadas nos seguintes frameworks e princípios:

*   **Defesa em Profundidade:** Estruturação da segurança em múltiplas camadas para que a falha de um único controle não resulte em um comprometimento total.
*   **NIST Cybersecurity Framework (CSF):** Como guia para as funções de Identificar, Proteger, Detectar, Responder e Recuperar.
*   **NIST SP 800-61:** Para a criação de um plano de resposta a incidentes simplificado e eficaz.
*   **Princípio 80/20 (Princípio de Pareto):** Para priorizar as ações que trarão o maior impacto na segurança com o menor esforço/custo relativo.

---

## 5. Diagrama da Arquitetura de Defesa Proposta

*(Um diagrama em Mermaid será desenvolvido no arquivo `diagrama.mmd` e inserido aqui para visualizar as camadas de defesa propostas para a LojaZeta.)*

---

## 6. Diagnóstico e Recomendações

### 6.1. Arquitetura de Defesa em Camadas

*(Análise dos riscos atuais, como tentativas de SQLi, XSS e brute-force, e proposta de controles para cada camada.)*

*   **Camada de Perímetro/Rede:**
    *   **Diagnóstico:** A aplicação está diretamente exposta à internet, sendo alvo de ataques automatizados (SQLi, XSS, brute-force). A falta de filtros na borda permite que tráfego malicioso chegue diretamente à aplicação.
    *   **Recomendações (Quick Wins):**
        *   **Implementar AWS WAF:** Ativar o Web Application Firewall da AWS no Load Balancer. Iniciar com o conjunto de regras gerenciadas `AWSManagedRulesCommonRuleSet` para bloquear instantaneamente as tentativas mais comuns de SQLi e XSS, que foram observadas nos incidentes recentes.
        *   **Configurar Rate Limiting para `/login`:** Criar uma regra específica no AWS WAF para limitar a quantidade de requisições por minuto de um mesmo endereço de IP para a rota de login. Isso mitigará de forma eficaz os ataques de força bruta.
        *   **Revisar Security Groups:** Aplicar o princípio do menor privilégio, garantindo que:
            *   Apenas tráfego HTTPS (porta 443) da internet seja permitido para o Nginx.
            *   O Nginx só possa se comunicar com a aplicação Node.js na porta específica da aplicação.
            *   A aplicação Node.js só possa se comunicar com o banco de dados PostgreSQL na sua porta específica.
            *   O banco de dados não tenha nenhum acesso direto da internet.
*   **Camada de Host/Infraestrutura:**
    *   **Diagnóstico:** Atualmente, não há um processo automatizado para aplicar patches de segurança ou verificar a integridade dos sistemas. Uma única vulnerabilidade não corrigida no sistema operacional ou em um pacote instalado pode permitir que um atacante ganhe acesso ao servidor.
    *   **Recomendações:**
        *   **Gestão de Vulnerabilidades (Patching):** Utilizar o **AWS Systems Manager Patch Manager** para automatizar a verificação e aplicação de patches de segurança nas instâncias. Isso garante a correção contínua de vulnerabilidades conhecidas com baixo esforço operacional.
        *   **Proteção de Endpoint (Anti-Malware):** Instalar e configurar o **ClamAV** (solução open source) para realizar varreduras diárias nos sistemas de arquivos. Isso ajuda a detectar artefatos maliciosos que possam ter superado as outras camadas de defesa.
        *   **Monitoramento de Integridade de Arquivos (FIM):** Implementar o **AIDE (Advanced Intrusion Detection Environment)**. Configurar uma tarefa agendada para que o AIDE verifique diariamente se houve alterações em arquivos críticos de configuração do sistema, alertando a equipe sobre atividades suspeitas.
*   **Camada de Aplicação:**
    *   **Diagnóstico:** As tentativas de SQLi e XSS indicam que há falhas de segurança diretamente no código-fonte da aplicação ou em suas dependências. O desenvolvimento atual não conta com ferramentas para identificar essas falhas antes da produção.
    *   **Recomendações:**
        *   **Análise de Composição de Software (SCA):** Integrar o **Snyk Open Source** (plano gratuito) ou o **OWASP Dependency-Check** ao pipeline de CI/CD. A ferramenta deve ser configurada para falhar o build caso uma vulnerabilidade crítica seja encontrada em uma biblioteca de terceiros, forçando a correção antes do deploy.
        *   **Análise Estática de Segurança (SAST):** Adotar o **SonarScanner** (open source) para realizar análises estáticas no código Node.js. Isso ajuda a identificar padrões de código inseguros que podem levar a vulnerabilidades como SQL Injection, XSS e outras.
        *   **Revisão de Código e Boas Práticas:** Instituir um processo de *code review* focado em segurança. Documentar e disseminar para os desenvolvedores a obrigatoriedade do uso de ORM (Object-Relational Mapping) ou *Prepared Statements* para todas as interações com o banco de dados e a utilização de *frameworks* de frontend que já fazem *output encoding* por padrão para prevenir XSS.
*   **Camada de Dados:**
    *   **Diagnóstico:** Os dados de clientes e transações são os ativos mais críticos da LojaZeta. Atualmente, não há garantia de que estejam criptografados em repouso, e os backups não são testados.
    *   **Recomendações:**
        *   **Criptografia em Repouso:** Habilitar a criptografia nativa do serviço de banco de dados PostgreSQL na AWS (ex: AWS RDS Encryption). Isso protege os dados contra acesso não autorizado direto no armazenamento físico.
        *   **Criptografia em Trânsito:** Garantir que toda a comunicação entre os serviços (cliente-servidor, Nginx-Node, Node-PostgreSQL) seja feita utilizando TLS (HTTPS para o cliente, TLS para comunicação interna).
        *   **Testes de Restauração de Backup:** Instituir um processo trimestral para testar a restauração dos backups. Um backup que nunca foi testado não é confiável.

*   **Camada de Identidade e Acesso:**
    *   **Diagnóstico:** O ataque de *brute-force* evidencia a falta de controles robustos de autenticação. Além disso, o acesso à infraestrutura pode não estar seguindo o princípio do menor privilégio.
    *   **Recomendações:**
        *   **MFA Obrigatório:** Implementar Multi-Factor Authentication (MFA) como política obrigatória para acesso à console da AWS e a qualquer painel administrativo da LojaZeta. Este é um dos controles mais eficazes contra o comprometimento de credenciais.
        *   **Política de Senhas Fortes:** Aplicar na plataforma uma política de senhas que exija complexidade mínima (tamanho, caracteres especiais, etc.) para clientes e administradores.
        *   **Revisão de Permissões (IAM):** Realizar uma revisão completa das roles e policies de IAM na AWS. Assegurar que as credenciais usadas pela aplicação Node.js tenham permissão apenas para acessar o banco de dados, e nada mais. O acesso da equipe de operações também deve ser restrito ao necessário.

### 6.2. Plano de Monitoramento Centralizado (SIEM Mínimo Viável)

*   **Diagnóstico:** Os logs do ambiente estão descentralizados, o que torna a detecção e a análise de incidentes reativa e extremamente difícil. Não há correlação de eventos entre as diferentes partes da infraestrutura.
*   **Recomendações:**
    *   **Ferramenta Proposta (Custo-Benefício):** Implementar o **Wazuh**, uma plataforma de segurança open source que atua como SIEM e solução de proteção de endpoints. Sua implementação em uma instância dedicada na AWS oferece uma visão centralizada da segurança sem custos de licença.
    *   **Fontes de Log a serem Coletadas:**
        *   **AWS:** Logs do WAF (tentativas de ataque), VPC Flow Logs (tráfego de rede) e CloudTrail (ações na API da AWS).
        *   **Instâncias:** Logs de acesso e erro do Nginx, logs de eventos da aplicação Node.js (incluindo autenticação), logs de autenticação do sistema operacional (ex: `/var/log/auth.log`) e logs de auditoria do PostgreSQL.
    *   **Regras de Correlação e Alertas Essenciais:** Configurar o Wazuh para gerar alertas acionáveis (via e-mail ou integração com Slack) para os seguintes eventos:
        *   **Detecção de Ataque Web:** Múltiplos erros 4xx de um mesmo IP, bloqueio de SQLi/XSS pelo WAF.
        *   **Tentativa de Brute-Force:** 5 falhas de login para o mesmo usuário em menos de 1 minuto.
        *   **Atividade Suspeita no Host:** Login de usuário não usual, alteração em arquivo crítico (integrado com AIDE), ou detecção de malware (integrado com ClamAV).
        *   **Atividade Suspeita na Nuvem:** Login na Console AWS de IP desconhecido ou criação de usuário IAM fora do horário comercial.
    *   **KPIs de Segurança:**
        *   **Número de incidentes de segurança críticos por mês.**
        *   **Tempo Médio para Detecção (MTTD):** Tempo entre o início de um incidente e seu alerta.
        *   **Tempo Médio para Resposta (MTTR):** Tempo entre o alerta e a contenção do incidente.

### 6.3. Plano de Resposta a Incidentes (NIST IR Simplificado)

*   **Diagnóstico:** A empresa não possui um processo formal para responder a incidentes de segurança, o que pode levar a ações desordenadas, perda de evidências e um tempo de recuperação maior.
*   **Recomendações:** Adotar um ciclo de vida de resposta a incidentes simplificado, baseado no framework NIST.
    *   **1. Preparação:**
        *   **Ferramentas:** Garantir que o Wazuh esteja implementado e que os alertas estejam sendo enviados para um canal central (e-mail ou Slack).
        *   **Conhecimento:** Manter a documentação da arquitetura de rede atualizada.
        *   **Treinamento:** Realizar um walkthrough trimestral com a equipe sobre este plano de resposta.
        *   **Backups:** Assegurar que os backups automáticos estejam funcionando e que o processo de restauração seja testado (conforme Seção 6.1).
    *   **2. Detecção & Análise:**
        *   **Fonte:** O principal gatilho será um alerta do Wazuh.
        *   **Análise Inicial:** O membro da equipe que receber o alerta deve acessar o painel do Wazuh para entender o escopo: qual o alerta, qual o IP de origem, qual o sistema afetado?
        *   **Validação:** É um ataque real ou um falso positivo? (Ex: um desenvolvedor rodando um teste que disparou um alerta).
        *   **Classificação:** Classificar a severidade (baixa, média, alta) com base no impacto potencial. Um ataque de brute-force contido é médio; um acesso não autorizado bem-sucedido é alto.
    *   **3. Contenção, Erradicação & Recuperação:**
        *   **Contenção (Curto Prazo):** Isolar o problema imediatamente. Ex: Bloquear o IP do atacante no AWS WAF; desabilitar uma conta de usuário comprometida.
        *   **Erradicação (Médio Prazo):** Encontrar e remover a causa raiz do incidente. Ex: Aplicar um patch em uma vulnerabilidade; corrigir uma falha de SQL Injection no código.
        *   **Recuperação (Longo Prazo):** Restaurar os sistemas para o estado normal de operação. Ex: Restaurar dados de um backup seguro; reabilitar o serviço para os usuários.
    *   **4. Pós-Incidente (Lições Aprendidas):**
        *   **Relatório:** Dentro de uma semana após o incidente, criar um breve relatório respondendo: O que aconteceu? Qual foi o impacto? O que fizemos bem? O que podemos melhorar?
        *   **Melhoria Contínua:** Usar as respostas para fortalecer as defesas. Ex: Criar uma nova regra no WAF; melhorar a validação de entrada no código; adicionar um novo alerta no Wazuh.

---

## 7. Roadmap de Implementação (Plano 80/20)

Este roadmap prioriza as ações com o maior retorno sobre o investimento em segurança (princípio 80/20), focando em mitigar os riscos mais imediatos com o menor esforço relativo.

| Prazo | Ações Principais | Impacto Esperado |
|---|---|---|
| **Quick Wins (30 dias)** | 1. **Implementar AWS WAF** com regras gerenciadas e rate limiting na rota `/login`.<br>2. **Revisar e restringir Security Groups** para aplicar o princípio do menor privilégio na rede.<br>3. **Habilitar MFA obrigatório** para acesso à Console AWS e ao painel admin da LojaZeta.<br>4. **Ativar criptografia em repouso** no banco de dados PostgreSQL. | **Altíssimo**<br>Mitiga diretamente os ataques de SQLi, XSS e brute-force observados. Reduz drasticamente o risco de comprometimento de contas e vazamento de dados. |
| **Médio Prazo (3-6 meses)** | 1. **Implantar o Wazuh** em uma instância dedicada para centralizar o monitoramento.<br>2. **Automatizar o patching de S.O.** com o AWS Systems Manager.<br>3. **Integrar ferramentas de SCA (Snyk) e SAST (SonarScanner)** no pipeline de CI/CD. | **Alto**<br>Cria capacidade de detecção e resposta. Reduz a janela de exposição a vulnerabilidades conhecidas no S.O. e em dependências de software. |
| **Longo Prazo (6-12 meses)** | 1. **Implementar FIM (AIDE) e Anti-Malware (ClamAV)** nas instâncias.<br>2. **Instituir rotina trimestral de testes de restauração de backup**.<br>3. **Conduzir um workshop de desenvolvimento seguro** para a equipe. | **Médio**<br>Aumenta a capacidade de detecção de ameaças no nível do host. Garante a resiliência do negócio. Eleva a maturidade de segurança da equipe. |

---

## 8. Riscos e Suposições

*   **Suposição:** A equipe interna (2 devs, 1 ops) terá disponibilidade para apoiar a implementação das ferramentas e processos.
*   **Risco:** O orçamento limitado pode impactar a escolha de ferramentas, exigindo maior dependência de soluções open source que demandam mais tempo de configuração.

---

## 9. Conclusão

A implementação das recomendações contidas nesta proposta transformará a postura de segurança da LojaZeta de reativa para proativa. Ao estabelecer defesas em camadas, visibilidade através de monitoramento centralizado e um plano claro de resposta a incidentes, a empresa não apenas mitiga os riscos imediatos de ataques web, mas também constrói uma fundação de segurança resiliente. Este investimento é fundamental para proteger a reputação da marca, a confiança dos clientes e para garantir a continuidade e o crescimento seguro do negócio.

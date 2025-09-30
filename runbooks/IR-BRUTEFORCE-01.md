# Runbook de Resposta a Incidentes: Tentativa de Brute-Force

**ID do Runbook:** IR-BRUTEFORCE-01  
**Severidade Padrão:** Média

---

## 1. Detecção e Validação

*   **Fonte do Alerta:** Wazuh - Regra "Múltiplas falhas de autenticação para o mesmo usuário".
*   **Ação Imediata:**
    1.  Acesse o painel do Wazuh e localize o alerta.
    2.  **Valide:**
        *   O IP de origem é conhecido? (Pode ser um teste interno?)
        *   O nome de usuário alvo existe?
        *   A atividade ainda está em andamento?
    3.  Se a atividade for suspeita e contínua, prossiga para a contenção. Se for um falso positivo, documente e feche o alerta.

---

## 2. Contenção

*   **Objetivo:** Bloquear o ataque para prevenir um acesso não autorizado.
*   **Passo a Passo:**
    1.  Copie o endereço de IP de origem do alerta no Wazuh.
    2.  Acesse a console da AWS e navegue até o serviço **WAF & Shield**.
    3.  Selecione a Web ACL associada ao Load Balancer da LojaZeta.
    4.  Vá para a seção de "IP sets" e adicione o IP malicioso a uma lista de bloqueio (ex: `manual-block-list`).
    5.  **Verifique:** Monitore os logs do WAF para confirmar que as requisições do IP bloqueado estão sendo negadas com o código de erro `403 Forbidden`.

---

## 3. Erradicação e Recuperação

*   **Objetivo:** Garantir que não houve acesso e que o sistema está seguro.
*   **Passo a Passo:**
    1.  **Análise de Acesso:** Verifique nos logs da aplicação Node.js se o ataque resultou em algum login bem-sucedido a partir do IP do atacante ou para o usuário-alvo.
    2.  **Se o acesso NÃO ocorreu:** Nenhuma ação de erradicação é necessária. O sistema já está recuperado.
    3.  **Se o acesso OCORREU:**
        *   **Erradicação:**
            *   Force o logout da sessão do usuário comprometido.
            *   Force a redefinição da senha do usuário comprometido.
            *   Analise os logs para entender quais ações o atacante realizou.
        *   **Recuperação:**
            *   Comunique o usuário sobre o incidente e a necessidade de redefinir a senha.
            *   Restaure dados de um backup se necessário (caso extremo).

---

## 4. Pós-Incidente

*   **Documentação:** Crie um breve resumo do incidente:
    *   Data e hora do alerta.
    *   IP de origem e usuário-alvo.
    *   Ações de contenção tomadas.
    *   Resultado da análise (acesso bem-sucedido ou não).
*   **Melhoria Contínua:**
    *   Considere diminuir o limiar da regra de brute-force no WAF (se o ataque não foi bloqueado automaticamente) ou no Wazuh (se o alerta demorou).
    *   Se o usuário-alvo tinha uma senha fraca, reforce a comunicação sobre a política de senhas fortes.

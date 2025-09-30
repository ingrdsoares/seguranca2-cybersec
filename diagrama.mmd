graph TD
    subgraph "Internet"
        Usuario[Usuário]
    end

    subgraph "AWS Cloud"
        subgraph "Camada de Perímetro"
            WAF[AWS WAF]
            LB[Application Load Balancer]
        end

        subgraph "Camada de Rede (VPC)"
            subgraph "Sub-rede Pública"
                SG_Web[SG: Permitir 443 de 0.0.0.0/0]
                Nginx[Instância Nginx]
                SG_Web --> Nginx
            end
            
            subgraph "Sub-rede Privada de Aplicação"
                SG_App[SG: Permitir tráfego do SG_Web]
                Node[Instância Node.js]
                SG_App --> Node
            end

            subgraph "Sub-rede Privada de Dados"
                SG_DB[SG: Permitir tráfego do SG_App]
                Postgres[Banco de Dados PostgreSQL RDS<br>Criptografia em Repouso Ativada]
                SG_DB --> Postgres
            end
        end

        subgraph "Camada de Monitoramento e Resposta"
            Wazuh[SIEM - Wazuh]
            Alertas[Alertas (E-mail/Slack)]
            CloudTrail[AWS CloudTrail]
            
            Wazuh --> Alertas
        end
    end

    Usuario -- HTTPS --> WAF
    WAF --> LB
    LB --> Nginx
    Nginx --> Node
    Node --> Postgres

    Nginx -- Logs --> Wazuh
    Node -- Logs --> Wazuh
    Postgres -- Logs --> Wazuh
    WAF -- Logs --> Wazuh
    CloudTrail -- Logs --> Wazuh

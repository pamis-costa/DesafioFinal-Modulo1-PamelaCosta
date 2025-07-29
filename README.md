# RELATÓRIO TÉCNICO - LAB SEGMENTAÇÃO DE REDE

**Aluno(a):** Pâmela Daniele Pereira Costa
**Data:** 28/07/2025
**Versão:** 1.0

---

## OBJETIVO

Analisar a rede simulada para identificar exposição de ativos, falhas na segmentação de rede e riscos operacionais que possam comprometer a segurança da organização.

---

## ESCOPO

O escopo da análise compreende o ambiente Docker simulado com seus múltiplos hosts e as seguintes redes segmentadas: corp_net (10.10.10.0/24), infra_net (10.10.30.0/24) e guest_net (10.10.50.0/24).

---

## METODOLOGIA

* **Ferramentas:** nmap para descoberta de hosts e enumeração de serviços; arp-scan para validação de ativos.
* **Técnicas:** Descoberta de hosts ativos em cada sub-rede; Varredura completa de portas para cada host; Identificação de versões de serviços (Banners) e uso de scripts padrão do Nmap (-sC); Análise manual e documentada.

---

## DIAGRAMA DE REDE

![Diagrama da Rede](diagrama_rede.png)

---

## DIAGNÓSTICO (ACHADOS)

As ferramentas confirmaram o mesmo conjunto de hosts ativos. A seguir uma tabela do inventário de ativos descobertos:

| Rede | IP do Ativo | Nome Identificado |
| :--- | :--- | :--- |
| corp_net | 10.10.10.10 | WS_001 |
| | 10.10.10.101 | WS_002 |
| | 10.10.10.127 | WS_003 |
| | 10.10.10.222 | WS_004 |
| infra_net | 10.10.30.10 | ftp-server |
| | 10.10.30.11 | mysql-server |
| | 10.10.30.15 | samba-server |
| | 10.10.30.17 | openldap.projeto |
| | 10.10.30.117 | zabbix-server |
| | 10.10.30.227 | legacy-server |
| guest_net | 10.10.50.2 | laptop-luiz |
| | 10.10.50.3 | laptop-vastro |
| | 10.10.50.4 | macbook-aline |
| | 10.10.50.6 | notebook-carlos |

## DIAGNÓSTICO (ACHADOS)

1.  **[Geral] - Falha Crítica de Segmentação de Rede:** Comunicação irrestrita entre as redes de visitantes, corporativa e de infraestrutura.
2.  **[10.10.30.17] - LDAP - Porta 389:** Servidor de autenticação com versão severamente desatualizada (OpenLDAP 2.2.X).
3.  **[10.10.30.11] - MySQL - Porta 3306:** Exposição direta do serviço de banco de dados para redes não autorizadas.
4.  **[10.10.30.10] - FTP - Porta 21:** Uso de protocolo inseguro que transmite credenciais e dados em texto claro.
5.  **[10.10.30.15] - Samba - Portas 139/445:** Serviço de compartilhamento de arquivos com assinatura de mensagens não obrigatória, permitindo ataques Man-in-the-Middle.

## RECOMENDAÇÕES

1.  **Implementar segmentação de rede efetiva:** Criar regras de firewall (ACLs) para bloquear todo o tráfego entre as sub-redes por padrão, permitindo apenas as comunicações estritamente necessárias e autorizadas.
2.  **Atualizar o servidor OpenLDAP:** Dada a idade crítica do software, o servidor openldap deve ser atualizado para uma versão recente e com suporte ou ser substituído. O acesso ao serviço deve ser restrito.
3.  **Restringir acesso ao banco de dados:** O acesso ao mysql-server na porta 3306 deve ser limitado apenas aos IPs dos servidores de aplicação que necessitam se conectar a ele.
4.  **Desativar o servidor FTP:** O protocolo FTP deve ser desativado e substituído por uma alternativa segura, como SFTP (que opera sobre SSH na porta 22).
5.  **Fortalecer a configuração do samba:** A assinatura de mensagens (SMB signing) deve ser configurada como "obrigatória" no servidor samba-server para prevenir ataques MitM.

---

## PLANO DE AÇÃO BASEADO EM IMPACTO E FACILIDADE

A tabela a seguir mostra as seguintes recomendações:

| Ação Corretiva | Impacto na Segurança | Facilidade de Implementação | Prioridade |
| :--- | :--- | :--- | :--- |
| Isolar a infra_net com regras de firewall | Crítico | Média | Crítica |
| Atualizar ou desativar o servidor OpenLDAP | Crítico | Difícil | Crítica |
| Restringir acesso de rede ao mysql-server | Alto | Média | Alta |
| Substituir FTP por SFTP e restringir acesso | Alto | Média | Alta |
| Exigir assinatura de pacotes no Samba | Médio | Alta | Média |

---

## CONCLUSÃO

Portanto, essa análise de segurança conclui que a rede da organização possui uma postura de segurança deficiente. As falhas de segmentação e a presença de software criticamente desatualizado criam um risco para o comprometimento de dados e dos sistemas. A implementação das ações corretivas citadas anteriormente é fundamental e necessária para proteger os ativos da organização.

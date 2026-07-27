# 🌐 Repositório de Infraestrutura e Segurança de Redes

## 📝 Visão Geral do Ecossistema
Este repositório funciona como um **HUB centralizado** destinado a documentar, validar e demonstrar a aplicação prática de engenharia de tráfego, resiliência de Camada 3, alta disponibilidade e blindagem de ativos (Hardening) contra vetores de ataque reais. 

Todos os cenários foram construídos seguindo as boas práticas dos fabricantes e os padrões oficiais da indústria (como **IEEE** e **IETF**), sendo validados por meio de análises de tabelas de roteamento e testes de estresse físicos (Troubleshooting).

---

## 👨‍💻 Sobre Mim

* **Formação:** Bacharel em Ciência da Computação (Concluído) e Pós-graduando em Engenharia de Redes de Computadores (Em conclusão).
* **Certificações/Cursos:** Formação preparatória para a certificação internacional **MikroTik Certified Network Associate (MTCNA)** em conclusão; Estágios institucionais de Linux Ubuntu e Redes TCP/IP realizados no Centro de Telemática do Exército Brasileiro.
* **Background Profissional:** 8 anos de serviço ativo no Exército Brasileiro, desenvolvendo sólida disciplina operacional, conformidade com processos rígidos de segurança, liderança, gestão de incidentes sob pressão (ITSM) e resiliência.

---

## 🔬 Linha do Tempo dos Laboratórios (Sumário Técnico)

Abaixo estão indexados os projetos desenvolvidos. Cada diretório contém sua respectiva topologia, scripts CLI limpos e evidências analíticas de validação de tráfego.

| Laboratório | Foco de Engenharia | Principais Tecnologias / Protocolos |
| :--- | :--- | :--- |
| [📁 01 - Roteamento Estático](./01-roteamento-estatico) | Lógica de Encaminhamento & VLSM | IPv4, Subnetting Cirúrgico (VLSM/CIDR), Sumarização de Rotas, VLANs (802.1Q), Router-on-a-Stick |
| [📁 02 - Roteamento Dinâmico](./02-roteamento-dinamico-ospf) | Convergência e Escalabilidade | OSPFv2 (Área 0), Adjacências de Vizinhança, Máscaras Wildcard, Análise de Logs de Trânsito |
| [📁 03 - Alta Disponibilidade](./03-alta-disponibilidade-hsrp) | Tolerância a Falhas (Fault Tolerant) | Redundância FHRP/HSRP, IP Virtual Flutuante, Interface Tracking (Uplink), Preempção Automática |
| [📁 04 - Hardening OSPF](./04-seguranca-ospf-md5) | Segurança e Integridade de Borda | Blindagem de Camada 3, Autenticação Criptografada MD5 no OSPF, Mitigação de Injeção de Rotas Maliciosas |
| [📁 05 - Segurança Switching](./05-seguranca-switching) | Mitigação de Ataques na LAN (L2) | Port Security (Modo Sticky), DHCP Snooping (Trusted/Untrusted Ports), Proteção contra Rogue DHCP e MAC Flooding |

---

## 🛠️ Metodologia de Validação
O grande diferencial técnico deste portfólio não se limita à configuração dos ativos, mas à **comprovação de funcionamento**:
1. **Análise de Tabelas de Rotas:** Validação de convergência de múltiplos caminhos de custo idêntico (**ECMP - Equal-Cost Multi-Path**).
2. **Simulação de Incidentes Críticos:** Aplicação de comandos de colapso (`shutdown`) em interfaces de borda para auditar o tempo de failover liso através do fluxo ICMP (perdas mínimas de pacotes).
3. **Logs do Sistema (Syslog):** Auditoria ativa através do comando `log-adjacency-changes` e monitoramento de estados de proteção de portas (`err-disabled`).

---

## 🚀 Conexão com o Mercado Real
Cada laboratório simula uma dor real de infraestrutura. As implementações de segurança e controle de porta que desenvolvi localmente evoluem no mercado corporativo para arquiteturas dinâmicas de autenticação na porta baseadas no padrão **IEEE 802.1X** (com servidores como Cisco ISE / Aruba ClearPass), garantindo que apenas dispositivos autorizados ganhem acesso à rede física e lógica da empresa.

---

---

### 🤝 Conecte-se comigo

* **LinkedIn:** [Acessar Perfil Profissional](https://linkedin.com)

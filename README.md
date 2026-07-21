# 🌐 Repositório de Engenharia, Infraestrutura e Segurança de Redes

Bem-vindo ao meu portfólio técnico de infraestrutura de redes corporativas. Este repositório funciona como um HUB centralizado para documentar, validar e demonstrar a aplicação prática de protocolos de roteamento, resiliência de camada 3, alta disponibilidade e segurança da informação (*Hardening*).

---

## 👨‍💻 Sobre Mim
* **Formação:** Bacharel em Ciência da Computação (Concluído) e Pós-graduando em Engenharia de Redes.
* **Certificações/Cursos:** Formação preparatória MTCNA (MikroTik RouterOS) em conclusão; Estágios institucionais de Linux Ubuntu e Redes TCP/IP realizados no Centro de Telemática do Exército Brasileiro.
* **Background Profissional:** 8 anos de serviço no Exército Brasileiro, desenvolvendo sólida disciplina operacional, conformidade com processos rígidos de segurança, liderança e resiliência sob pressão.

---

## 🛠️ Ecossistema de Laboratórios Práticos (Sumário do Portfólio)

Abaixo estão indexados todos os projetos desenvolvidos em ambientes simulados (**Cisco Packet Tracer**) e emulados (**EVE-NG / Multi-Vendor**), contendo topologias, scripts CLI completos e evidências de validação (*Troubleshooting*):

### 📁 [01-roteamento-estatico](./01-roteamento-estatico/)
* **Foco:** Lógica de Encaminhamento e Arquitetura de Sub-redes.
* **Tecnologias:** Endereçamento IPv4, Subnetting cirúrgico com Máscaras de Tamanho Variável (**VLSM/CIDR**), Sumarização de Rotas e Segmentação de Camada 2 via VLANs com encapsulamento `802.1Q` (*Router-on-a-Stick*).

### 📁 [02-roteamento-dinamico-ospf](./02-roteamento-dinamico-ospf/)
* **Foco:** Convergência Automatizada de Larga Escala.
* **Tecnologias:** Protocolo de Roteamento Dinâmico **OSPF v2 (Area 0)**, formação de adjacências de vizinhança (*Neighbor Adjacency*), dimensionamento com máscaras wildcard e auditoria de logs de trânsito corporativo.

### 📁 [03-alta-disponibilidade-hsrp](./03-alta-disponibilidade-hsrp/)
* **Foco:** Resiliência Crítica e Tolerância a Falhas (*Fault Tolerant*).
* **Tecnologias:** Redundância de Gateway via **FHRP/HSRP (Grupo 1)** com IP Virtual flutuante, mecanismo inteligente de **Interface Tracking** monitorando a WAN e comando de preempção automática (*Preempt*) para failover de alta velocidade.
* **Evidência Prática:** Validação de conectividade ICMP apresentando perda de apenas 1 pacote durante queda física de link.

### 📁 [04-seguranca-ospf-md5](./04-seguranca-ospf-md5/) ⏳ *Em Breve*
* **Foco:** Hardening de Protocolos de Roteamento.
* **Tecnologias:** Blindagem da tabela de rotas do backbone corporativo através da implementação de autenticação criptografada **MD5 no OSPF**, mitigando ataques de injeção de rotas maliciosas na rede.

### 📁 [05-seguranca-switching](./05-seguranca-switching/) ⏳ *Em Breve*
* **Foco:** Mitigação de Ataques na Camada 2 (LAN).
* **Tecnologias:** Segurança de borda local através de **Port Security** (bloqueio de MACs desconhecidos) e **DHCP Snooping** (mitigação de servidores DHCP falsos na infraestrutura).

---

## 🔬 Metodologia de Validação Técnica
Todos os cenários são documentados seguindo rigor documental corporativo internacional. As validações contam com análises de tabelas de roteamento (ex: Equal-Cost Multi-Path - ECMP no switch core) e testes de estresse físicos para comprovar a estabilidade da engenharia proposta antes do deploy em produção.

---
*Alinhado com objetivos de atuação em ambientes híbridos corporativos e preparação analítica para Concursos de TI (Infraestrutura).*

# Laboratórios Práticos de Infraestrutura e Redes de Computadores 🚀

Repositório dedicado à documentação técnica, mapeamento topológico e validação de conectividade lógica em ambientes de redes de computadores de escala corporativa.

---

## 🛠️ Tecnologias e Simuladores Utilizados
* **Cisco Packet Tracer**: CLI de ativos e simulação de tráfego corporativo.
* **Protocolos e Padrões**: OSPF, Roteamento Estático, IEEE 802.1Q (dot1Q), IPv4 (VLSM/CIDR), TCP/IP.

---

## 📂 Projetos Implementados

Acesse a documentação completa, diagramas e códigos CLI de cada laboratório através dos links abaixo:

### [📁 01. Laboratório de Roteamento Estático Corporativo](./01-roteamento-estatico/)
* **Escopo Técnico**: Mapeamento manual de tabelas de roteamento com comandos `ip route`, interconexão de cookies de trânsito em blocos `/30` e segmentação local com subinterfaces.

### [📁 02. Laboratório de Roteamento Dinâmico (OSPF v2)](./02-roteamento-dinamico-ospf/)
* **Escopo Técnico**: Configuração e validação de convergência automática em Área Única (Area 0), cálculo de máscaras inversas (*wildcard*), adjacências de vizinhança e redundância lógica.

---

## 📐 Cenários de Switching e Otimização IP (Aplicados em Ambos os Projetos)

### 1. Arquitetura de Switching Inter-VLAN
* **Objetivo**: Segmentação lógica do tráfego interno por departamentos corporativos para otimização de domínios de transmissão e aumento de conformidade de segurança.
* **Escopo Técnico**: Criação de VLANs corporativas, isolamento de portas (Modo Access e Trunking) e implementação de roteamento Inter-VLAN via subinterfaces lógicas ajustadas em roteadores (*Router-on-a-Stick*) através do protocolo dot1Q (802.1Q).

### 2. Otimização de Escopo IPv4 (VLSM & CIDR)
* **Objetivo**: Planejamento analítico e dimensionamento inteligente de blocos de sub-redes para eliminação de desperdício de endereços IP de hosts.
* **Escopo Técnico**: Partição de escopo de rede IP sob medida utilizando cálculos de blocos lógicos VLSM e notações CIDR com base nas necessidades de volumetria de cada departamento (TI, Vendas, RH, Finanças, Operações e Almoxarifado).

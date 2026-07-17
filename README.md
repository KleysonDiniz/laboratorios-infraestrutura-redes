# Laboratórios Práticos de Infraestrutura e Redes de Computadores 🚀

Repositório dedicado à documentação técnica, mapeamento topológico e validação de conectividade lógica em ambientes de redes corporativas de larga escala.

---

## 🛠️ Tecnologias e Simuladores Utilizados
* **Cisco Packet Tracer**: CLI de ativos Cisco e simulação de tráfego corporativo.
* **Protocolos e Padrões**: OSPF v2, Roteamento Estático Sumarizado, IEEE 802.1Q (dot1Q), IPv4 (VLSM/CIDR), TCP/IP.

---

## 📂 Projetos Implementados (Acesse os Laboratórios)

Clique nos links abaixo para acessar a documentação detalhada, diagramas de topologia e validações de ping de cada cenário:

### [📁 01. Laboratório de Roteamento Estático Corporativo](./01-roteamento-estatico/)
* Mapeamento manual de tabelas de roteamento com técnica de Sumarização de Rotas para redução de overhead de CPU nos ativos de borda.

### [📁 02. Laboratório de Roteamento Dinâmico (OSPF v2)](./02-roteamento-dinamico-ospf/)
* Configuração e validação de convergência automatizada, trocas de pacotes Hello, gerenciamento de adjacências e cálculo de máscaras inversas.

---

## 📐 Cenários de Switching e Otimização IP (Fundamentos Aplicados)

### 1. Arquitetura de Switching Inter-VLAN
* **Objetivo**: Segmentação lógica do tráfego interno por departamentos corporativos para otimização de domínios de transmissão e aumento de conformidade de segurança.
* **Escopo Técnico**: Criação de VLANs corporativas, isolamento de portas (Modo Access e Trunking) e implementação de roteamento Inter-VLAN via subinterfaces lógicas ajustadas em roteadores (*Router-on-a-Stick*) através do protocolo dot1Q (802.1Q).

### 2. Otimização de Escopo IPv4 (VLSM & CIDR)
* **Objetivo**: Planejamento analítico e dimensionamento inteligente de blocos de sub-redes para eliminação de desperdício de endereços IP de hosts.
* **Escopo Técnico**: Partição de escopo de rede IP sob medida utilizando cálculos de blocos lógicos VLSM e notações CIDR com base nas necessidades de volumetria de cada departamento (TI, Vendas, RH, Finanças, Operações e Almoxarifado).

---

## 💻 Resumo Técnico de Configuração CLI (Amostra do Backbone)

*Clique nas abas abaixo para expandir e visualizar as amostras de comandos implementadas nos ativos do projeto (Otimizado para indexação ATS/IAs de Recrutamento):*

<details>
<summary><b>🛠️ 1. Clique para expandir os comandos de Trunking & Inter-VLAN (dot1Q)</b></summary>

```text
interface GigabitEthernet0/1
 switchport mode trunk
!
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.192
```
</details>

<details>
<summary><b>🛣️ 2. Clique para expandir os comandos de Roteamento Estático vs OSPF</b></summary>

```text
# Engenharia Estática (Sumarizada)
ip route 192.168.20.0 255.255.255.0 10.0.0.2
ip route 192.168.30.0 255.255.255.0 10.0.0.2

# Engenharia Dinâmica (OSPF Wildcard)
router ospf 1
 log-adjacency-changes
 network 192.168.40.0 0.0.0.63 area 0
```
</details>

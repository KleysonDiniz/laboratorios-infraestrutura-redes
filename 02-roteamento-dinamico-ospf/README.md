# 🚀 Laboratório de Roteamento Dinâmico (OSPF v2)

Este projeto demonstra a implementação de uma infraestrutura corporativa multi-setorial utilizando o protocolo de roteamento dinâmico **OSPF (Open Shortest Path First)** em Área Única (Area 0) para convergência automática de tabelas de rotas e alta escalabilidade.

---

## 🗺️ Topologia da Rede

![Topologia do Projeto Dinâmico](img/topologia-dinamico.png)

---

## 📈 Planejamento de Endereçamento IP (VLSM por Localidade)

O projeto utiliza três redes principais distintas, com escopos fatiados de forma cirúrgica utilizando Máscaras de Tamanho Variável (VLSM) para mitigar o desperdício de endereços:

### 🔗 Redes de Trânsito (Backbone Ponto a Ponto)
Os links que conectam os roteadores de borda entre si utilizam a máscara **`/30` (255.255.255.252)**. Esta configuração é o padrão absoluto da indústria para redes de trânsito ponto a ponto, pois libera exatamente 2 endereços IP válidos por enlace, eliminando qualquer desperdício de escopo IPv4 no backbone corporativo:
* **Link Roteador A ↔ Roteador B:** Rede `10.0.0.0/30` (IPs úteis: `.1` e `.2`)
* **Link Roteador B ↔ Roteador C:** Rede `10.0.0.4/30` (IPs úteis: `.5` e `.6`)

### Roteador "A" (esquerdo): Rede `192.168.40.0`

| Setor / VLAN | Hosts | Máscara / CIDR | Salto | Rede / Broadcast | Gateway | IPs Válidos (PCs) |
| :--- | :---: | :--- | :---: | :--- | :---: | :--- |
| **Setor Financeiro** - VLAN 10 | 50 | `255.255.255.192` (/26) | 64 | `.0` / `.63` | `.1` | `.2` até `.62` |
| **Setor Operacional** - VLAN 20 | 20 | `255.255.255.224` (/27) | 32 | `.64` / `.95` | `.65` | `.66` até `.94` |
| **Almoxarifado** - VLAN 30 | 10 | `255.255.255.240` (/28) | 16 | `.96` / `.111` | `.97` | `.98` até `.110` |

### Roteador "B" (meio): Rede `192.168.50.0`

| Setor / VLAN | Hosts | Máscara / CIDR | Salto | Rede / Broadcast | Gateway | IPs Válidos (PCs) |
| :--- | :---: | :--- | :---: | :--- | :---: | :--- |
| **Central Atend.** - VLAN 10 | 15 | `255.255.255.224` (/27) | 32 | `.0` / `.31` | `.1` | `.2` até `.30` |
| **Centro Distrib.** - VLAN 20 | 5 | `255.255.255.240` (/28) | 16 | `.32` / `.47` | `.33` | `.34` até `.46` |

### Roteador "C" (direito): Rede `192.168.60.0`

| Setor / VLAN | Hosts | Máscara / CIDR | Salto | Rede / Broadcast | Gateway | IPs Válidos (PCs) |
| :--- | :---: | :--- | :---: | :--- | :---: | :--- |
| **Setor Financeiro** - VLAN 10 | 50 | `255.255.255.192` (/26) | 64 | `.0` / `.63` | `.1` | `.2` até `.62` |
| **Setor Operacional** - VLAN 20 | 20 | `255.255.255.224` (/27) | 32 | `.64` / `.95` | `.65` | `.66` até `.94` |
| **Almoxarifado** - VLAN 30 | 10 | `255.255.255.240` (/28) | 16 | `.96` / `.111` | `.97` | `.98` até `.110` |

---

## 💻 Configurações na CLI (Processo OSPF & Máscaras Wildcard)

Diferente do roteamento estático, o OSPF calcula os caminhos dinamicamente. Para anunciar as redes locais e fechar as adjacências de vizinhança nas redes de trânsito, aplicamos o processo OSPF em Área Única associado ao cálculo da **Máscara Wildcard (Inversa)**.

### 🔌 1. Ativação do Gateway Inter-VLAN (Exemplo Roteador "A")
Comandos cruciais utilizados para ativar o entroncamento (*Router-on-a-Stick*) através do protocolo dot1Q:

```text
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.40.1 255.255.255.192

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.40.65 255.255.255.224

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.40.97 255.255.255.240
```
### 🏷️ 2. Configuração de Trunking nos Switches (IEEE 802.1Q)
Para suportar as subinterfaces lógicas configuradas nos roteadores de borda, a porta de uplink do switch foi definida em modo tronco:
```text
interface GigabitEthernet0/1
 switchport mode trunk
```

### 🔄 2. Ativação do Protocolo OSPF v2

**No Roteador "A" (esquerdo):**
```text
router ospf 1
 log-adjacency-changes
 network 10.0.0.0 0.0.0.3 area 0
 network 192.168.40.0 0.0.0.63 area 0
 network 192.168.40.64 0.0.0.31 area 0
 network 192.168.40.96 0.0.0.15 area 0
```

**No Roteador "B" (meio):**
```text
router ospf 1
 log-adjacency-changes
 network 10.0.0.0 0.0.0.3 area 0
 network 10.0.0.4 0.0.0.3 area 0
 network 192.168.50.0 0.0.0.31 area 0
 network 192.168.50.32 0.0.0.15 area 0
```

**No Roteador "C" (direito):**
```text
router ospf 1
 log-adjacency-changes
 network 10.0.0.4 0.0.0.3 area 0
 network 192.168.60.0 0.0.0.63 area 0
 network 192.168.60.64 0.0.0.31 area 0
 network 192.168.60.96 0.0.0.15 area 0
```

* **Por que usar `log-adjacency-changes`?** Garante o envio de logs informativos para o terminal CLI sempre que ocorrerem mudanças de estado de adjacência com roteadores vizinhos, otimizando o monitoramento e troubleshooting do backbone.
* **Por que as máscaras usam a notação `0.0.0.X`?** O OSPF utiliza a Máscara Wildcard, que é o inverso matemático da máscara de sub-rede comum. Isso indica ao protocolo exatamente qual escopo de rede ele deve inspecionar e anunciar na Área 0 de forma automatizada.

---

## 🧪 Validação e Testes de Conectividade

Para validar o processo de convergência automática do protocolo OSPF e garantir a alcançabilidade global das rotas de forma dinâmica, foi realizado um teste de ICMP (Ping) entre as duas extremidades da infraestrutura corporativa.

### Escopo do Cenário de Teste:
* **Origem:** PC "N" (Setor Financeiro - Roteador C) | IP: `192.168.60.10`
* **Destino:** PC "K" (Setor de RH - Roteador A) | IP: `192.168.40.100`
* **Convergência Dinâmica:** Os roteadores trocaram pacotes Hello e estabeleceram adjacência (vizinhança) em Area 0. O Roteador "C" aprendeu a rota para o bloco `192.168.40.96/28` (VLAN 30) de forma totalmente automática através dos anúncios dinâmicos do OSPF, encaminhando os pacotes com sucesso através do backbone até o destino, sem qualquer mapeamento estático manual.

Abaixo, a evidência do terminal comprovando o sucesso na comunicação ponta a ponta:

![Evidência de Ping Sucesso](img/ping-dinamico.png)

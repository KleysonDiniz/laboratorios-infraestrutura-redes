# 🌐 Laboratório de Roteamento Estático Corporativo

Este projeto demonstra a implementação de uma infraestrutura de rede corporativa multi-setorial segmentada logicamente por VLANs, com roteamento de borda configurado de forma totalmente estática e manual (`ip route`) interligando três localidades principais.

---

## 🗺️ Topologia da Rede

![Topologia do Projeto Estático](img/topologia-estatico.png)

---

## 📈 Planejamento de Endereçamento IP (VLSM por Localidade)

O projeto utiliza três redes principais distintas, com escopos fatiados de forma cirúrgica utilizando Máscaras de Tamanho Variável (VLSM) para mitigar o desperdício de endereços em cada localidade:

### Roteador "A" (esquerdo): Rede `192.168.10.0`
* **VLAN 10 - TI (50 hosts)**: 
  * Máscara: `255.255.255.192` (/26) | Salto: 64
  * Rede: `192.168.10.0` | Gateway: `192.168.10.1` | Broadcast: `192.168.10.63`
  * Intervalo Válido para PCs: `192.168.10.2` até `192.168.10.62`
* **VLAN 20 - Vendas (20 hosts)**: 
  * Máscara: `255.255.255.224` (/27) | Salto: 32
  * Rede: `192.168.10.64` | Gateway: `192.168.10.65` | Broadcast: `192.168.10.95`
  * Intervalo Válido para PCs: `192.168.10.66` até `192.168.10.94`
* **VLAN 30 - RH (10 hosts)**: 
  * Máscara: `255.255.255.240` (/28) | Salto: 16
  * Rede: `192.168.10.96` | Gateway: `192.168.10.97` | Broadcast: `192.168.10.111`
  * Intervalo Válido para PCs: `192.168.10.98` até `192.168.10.110`

### Roteador "B" (meio): Rede `192.168.20.0`
* **VLAN 10 - Central de Atendimento (15 hosts)**: 
  * Máscara: `255.255.255.224` (/27) | Salto: 32
  * Rede: `192.168.20.0` | Gateway: `192.168.20.1` | Broadcast: `192.168.20.31`
  * Intervalo Válido para PCs: `192.168.20.2` até `192.168.20.30`
* **VLAN 20 - Centro de Distribuição (5 hosts)**: 
  * Máscara: `255.255.255.240` (/28) | Salto: 16
  * Rede: `192.168.20.32` | Gateway: `192.168.20.33` | Broadcast: `192.168.20.47`
  * Intervalo Válido para PCs: `192.168.20.34` até `192.168.20.46`

### Roteador "C" (direito): Rede `192.168.30.0`
* **VLAN 10 - Setor Financeiro (50 hosts)**: 
  * Máscara: `255.255.255.192` (/26) | Salto: 64
  * Rede: `192.168.30.0` | Gateway: `192.168.30.1` | Broadcast: `192.168.30.63`
  * Intervalo Válido para PCs: `192.168.30.2` até `192.168.30.62`
* **VLAN 20 - Setor Operacional (20 hosts)**: 
  * Máscara: `255.255.255.224` (/27) | Salto: 32
  * Rede: `192.168.30.64` | Gateway: `192.168.30.65` | Broadcast: `192.168.30.95`
  * Intervalo Válido para PCs: `192.168.30.66` até `192.168.30.94`
* **VLAN 30 - Almoxarifado (10 hosts)**: 
  * Máscara: `255.255.255.240` (/28) | Salto: 16
  * Rede: `192.168.30.96` | Gateway: `192.168.30.97` | Broadcast: `192.168.30.111`
  * Intervalo Válido para PCs: `192.168.30.98` até `192.168.30.110`

---

## 💻 Configurações na CLI (Subinterfaces & Tabelas de Roteamento)

### 1. Ativação do Gateway Inter-VLAN (Exemplo Roteador "A")
Comandos cruciais utilizados para ativar o entroncamento (*Router-on-a-Stick*) através do protocolo dot1Q:

```text
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.192

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.10.65 255.255.255.224

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.10.97 255.255.255.240
```

### 🛣️ 2. Comandos de Roteamento Estático (`ip route`)
Para otimização da tabela de roteamento e redução de overhead, foi aplicada a técnica de **Sumarização de Rotas (Classless/24)**. Em vez de declarar cada sub-rede individualmente, os roteadores apontam para o bloco cheio de cada localidade através dos próximos saltos (*Next-Hop*):

**No Roteador "A" (esquerdo):**
```text
ip route 192.168.20.0 255.255.255.0 10.0.0.2
ip route 192.168.30.0 255.255.255.0 10.0.0.2
```

**No Roteador "B" (meio):**
```text
ip route 192.168.10.0 255.255.255.0 10.0.0.1
ip route 192.168.30.0 255.255.255.0 10.0.0.6
```

**No Roteador "C" (direito):**
```text
ip route 192.168.10.0 255.255.255.0 10.0.0.5
ip route 192.168.20.0 255.255.255.0 10.0.0.5
```

---

## 🧪 Validação e Testes de Conectividade

A conectividade de ponta a ponta cruzando os roteadores de trânsito através de rotas estáticas foi validada e documentada com sucesso através de testes de ICMP (Ping).

![Evidência de Ping Sucesso](img/ping-estatico.png)

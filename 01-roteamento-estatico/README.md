# 🌐 Laboratório de Roteamento Estático Corporativo

Este projeto demonstra a implementação de uma infraestrutura de rede corporativa multi-setorial segmentada logicamente por VLANs, com roteamento de borda configurado de forma totalmente estática e manual (`ip route`) interligando três localidades principais.

---

## 🗺️ Topologia da Rede
<p align="center">
  <img src="img/01-network-topology.png" alt="Topologia do Projeto Estático">
</p>

## 📁 Arquivos do Laboratório

Abaixo estão indexados a topologia executável do Cisco Packet Tracer e as configurações dos dispositivos (CLI) extraídas diretamente dos ativos:

* 💻 **Arquivo do Packet Tracer:** [Roteamento-Estatico.pkt](src/Roteamento-Estatico.pkt)
* 📄 **CLI - Roteador A (Router-A):** [Roteador-A.cfg](src/Roteador-A.cfg)
* 📄 **CLI - Roteador B (Router-B):** [Roteador-B.cfg](src/Roteador-B.cfg)
* 📄 **CLI - Roteador C (Router-C):** [Roteador-C.cfg](src/Roteador-C.cfg)

---

## 📈 Planejamento de Endereçamento IP (VLSM por Localidade)
O projeto utiliza três redes principais distintas, com escopos fatiados de forma cirúrgica utilizando Máscaras de Tamanho Variável (VLSM) para mitigar o desperdício de endereços:

### 🔗 Redes de Trânsito (Backbone Ponto a Ponto)
Os links que conectam os roteadores de borda entre si utilizam a máscara **`/30` (255.255.255.252)**. Esta configuração é o padrão absoluto da indústria para redes de trânsito ponto a ponto, pois libera exatamente 2 endereços IP válidos por enlace, eliminando qualquer desperdício de escopo IPv4 no backbone corporativo:
* **Link Roteador A ↔ Roteador B:** Rede `10.0.0.0/30` (IPs úteis: `.1` e `.2`)
* **Link Roteador B ↔ Roteador C:** Rede `10.0.0.4/30` (IPs úteis: `.5` e `.6`)

### Roteador "A" (esquerdo): Rede `192.168.10.0`

| Setor / VLAN | Hosts | Máscara / CIDR | Salto | Rede / Broadcast | Gateway | IPs Válidos (PCs) |
| :--- | :---: | :--- | :---: | :--- | :---: | :--- |
| **TI** - VLAN 10 | 50 | `255.255.255.192` (/26) | 64 | `.0` / `.63` | `.1` | `.2` até `.62` |
| **Vendas** - VLAN 20 | 20 | `255.255.255.224` (/27) | 32 | `.64` / `.95` | `.65` | `.66` até `.94` |
| **RH** - VLAN 30 | 10 | `255.255.255.240` (/28) | 16 | `.96` / `.111` | `.97` | `.98` até `.110` |

### Roteador "B" (meio): Rede `192.168.20.0`

| Setor / VLAN | Hosts | Máscara / CIDR | Salto | Rede / Broadcast | Gateway | IPs Válidos (PCs) |
| :--- | :---: | :--- | :---: | :--- | :---: | :--- |
| **Central Atend.** - VLAN 10 | 15 | `255.255.255.224` (/27) | 32 | `.0` / `.31` | `.1` | `.2` até `.30` |
| **Centro Distrib.** - VLAN 20 | 5 | `255.255.255.240` (/28) | 16 | `.32` / `.47` | `.33` | `.34` até `.46` |

### Roteador "C" (direito): Rede `192.168.30.0`

| Setor / VLAN | Hosts | Máscara / CIDR | Salto | Rede / Broadcast | Gateway | IPs Válidos (PCs) |
| :--- | :---: | :--- | :---: | :--- | :---: | :--- |
| **Setor Financeiro** - VLAN 10 | 50 | `255.255.255.192` (/26) | 64 | `.0` / `.63` | `.1` | `.2` até `.62` |
| **Setor Operacional** - VLAN 20 | 20 | `255.255.255.224` (/27) | 32 | `.64` / `.95` | `.65` | `.66` até `.94` |
| **Almoxarifado** - VLAN 30 | 10 | `255.255.255.240` (/28) | 16 | `.96` / `.111` | `.97` | `.98` até `.110` |

---

## 💻 Configurações na CLI (Subinterfaces & Tabelas de Roteamento)

### 🔌 1. Ativação do Gateway Inter-VLAN (Exemplo Roteador "A")
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

### 🎛️ 2. Configuração de Trunking nos Switches (IEEE 802.1Q)
Para suportar o tráfego de múltiplas VLANs trafegando simultaneamente pelo mesmo cabo físico até o roteador, a porta de uplink do switch principal foi configurada explicitamente em modo tronco (*Trunk*):
```text
interface GigabitEthernet0/1
 switchport mode trunk
```

### 🛣️ 3. Comandos de Roteamento Estático (`ip route`)
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

### 📋 4. Auditoria da Tabela de Roteamento (Router-A)
Abaixo está a evidência da tabela lida diretamente no equipamento via comando `show ip route`. É possível verificar as rotas corporativas mapeadas com a letra **S** (Static), confirmando que o encaminhamento manual foi inserido com sucesso na memória do dispositivo:

<!-- Imagem reduzida centralizada -->
<p align="center">
  <img src="img/02-router-a-routing-table.png" alt="Tabela de Roteamento Router-A" width="40%">
</p>

<!-- Tabela invisível original que garante a centralização e o botão com borda -->
<table align="center" style="border: none !important; background: transparent !important; border-collapse: collapse;">
  <tr style="border: none !important; background: transparent !important;">
    <td style="border: none !important; background: transparent !important; text-align: center; padding: 0;">
      <details style="display: inline-block;">
        <summary style="cursor: pointer; list-style: none;">
          <code><strong>🔍 Clique aqui para aplicar ZOOM na imagem acima</strong></code>
        </summary>
        <br><br>
        <p align="center">
          <img src="img/02-router-a-routing-table.png" alt="ZOOM Tabela de Roteamento Router-A" width="100%">
        </p>
      </details>
    </td>
  </tr>
</table>

---

## 🧪 Validação e Testes de Conectividade

Para validar a convergência das tabelas de rotas e o correto funcionamento do ecossistema, foi realizado um teste duplo de conectividade ICMP (Ping) e rastreamento de caminho lógico (Tracert) atravessando toda a infraestrutura física do laboratório.

### Escopo do Cenário de Teste:
* **Origem:** PC "A" (Departamento de TI) | IP: `192.168.10.10`
* **Destino:** PC "H" (Almoxarifado) | IP: `192.168.30.100`

### Análise do Fluxo de Tráfego (Salto por Salto):
1. O tráfego parte do PC "A" e bate no gateway local `192.168.10.1` (Router-A).
2. O Router-A checa sua tabela estática, localiza o destino na sumarização `/24` e encaminha o pacote para o próximo salto `10.0.0.2` (Router-B).
3. O Router-B repassa o pacote pelo link de trânsito até o próximo salto `10.0.0.6` (Router-C).
4. O Router-C entrega o pacote diretamente na interface de destino final na rede local.

Abaixo, a evidência do terminal combinando o **Ping** (provando 100% de sucesso e 0% de perda) e o **Tracert** (provando a precisão do caminho desenhado de ponta a ponta):

<!-- Imagem reduzida centralizada -->
<p align="center">
  <img src="img/03-connectivity-validation-tests.png" alt="Validação de Conectividade Ping e Tracert" width="40%">
</p>

<!-- Tabela invisível original que garante a centralização e o botão com borda -->
<table align="center" style="border: none !important; background: transparent !important; border-collapse: collapse;">
  <tr style="border: none !important; background: transparent !important;">
    <td style="border: none !important; background: transparent !important; text-align: center; padding: 0;">
      <details style="display: inline-block;">
        <summary style="cursor: pointer; list-style: none;">
          <code><strong>🔍 Clique aqui para aplicar ZOOM na imagem acima</strong></code>
        </summary>
        <br><br>
        <p align="center">
          <img src="img/03-connectivity-validation-tests.png" alt="ZOOM Validação de Conectividade Ping e Tracert" width="100%">
        </p>
      </details>
    </td>
  </tr>
</table>


---

## 🔄 Evolução deste Laboratório: Roteamento Dinâmico

Embora o roteamento estático com VLSM seja extremamente seguro e eficiente para redes de pequeno porte, ele se torna complexo de gerenciar manualmente à medida que a infraestrutura corporativa cresce e ganha novos caminhos redundantes.

Para analisar como automatizar a descoberta de redes e acelerar o tempo de convergência em larga escala, acesse a evolução deste projeto com o protocolo OSPF:

👉 **[Acessar Laboratório 02: Roteamento Dinâmico (OSPF)](../02-roteamento-dinamico-ospf/)**

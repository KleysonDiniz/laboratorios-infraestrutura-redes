# Projeto 05: Segurança de Ativos em Camada 2 (Port Security & DHCP Snooping)

## 📌 Descrição do Projeto
Este projeto apresenta a implementação prática de mecanismos de segurança de Camada 2 (Enlace) em switches Cisco corporativos utilizando o simulador Cisco Packet Tracer. O objetivo principal é blindar a infraestrutura de rede local contra ameaças de intrusão física e engenharia social, mitigando vetores críticos como a clonagem ou estouro de endereços MAC (MAC Spoofing/Flooding) e a introdução de servidores DHCP maliciosos (Rogue DHCP) na LAN corporativa.

O laboratório simula um cenário real de ataque onde um invasor desconecta o terminal de um funcionário homologado para tentar ganhar acesso à rede através da mesma tomada física da parede, acionando os gatilhos automatizados de defesa do Switch.

---

## 🛠️ Tecnologias e Ferramentas Utilizadas
*   **Simulador de Rede:** Cisco Packet Tracer
*   **Ativos de Rede:** Cisco Catalyst Switch 2960 (`SW-ACESSO-01`) / Cisco Router ISR 4331 (`GW-MATRIZ`)
*   **Protocolos e Recursos:** Port Security (Sticky Mode), DHCP Snooping (Trusted/Untrusted Ports), IPv4 Networking, Syslog Monitoring.

---

## 📐 Topologia e Fluxo de Segurança
Abaixo está o mapeamento visual do comportamento da infraestrutura de rede durante a execução dos testes, evidenciando o contraste entre o tráfego permitido e o bloqueio imediato da ameaça utilizando a estratégia de cabo único na interface `Fa0/2`:

![Topologia de Rede - Antes e Depois da Mitigação](01-topologia-fluxo.png)

---

## 🚀 Principais Comandos de Configuração

### 1. Configuração do Servidor DHCP Legítimo (`GW-MATRIZ`)
Criação do pool de endereçamento IPv4 corporativo para distribuição dinâmica de parâmetros de rede:
```gns3
GW-MATRIZ(config)# interface GigabitEthernet0/0/0
GW-MATRIZ(config-if)# ip address 192.168.50.1 255.255.255.0
GW-MATRIZ(config-if)# no shutdown
GW-MATRIZ(config-if)# exit
GW-MATRIZ(config)# ip dhcp pool LAN_KLEYSON
GW-MATRIZ(dhcp-config)# network 192.168.50.0 255.255.255.0
GW-MATRIZ(dhcp-config)# default-router 192.168.50.1
GW-MATRIZ(dhcp-config)# dns-server 8.8.8.8
```

### 2. Blindagem contra Servidores Rogue (`DHCP Snooping`)
Ativação global do monitoramento de pacotes DHCP e definição de portas confiáveis (Trusted) para evitar ataques de *Man-in-the-Middle*. A remoção da opção 82 foi aplicada para garantir a compatibilidade de pacotes no ambiente de simulação:
```gns3
SW-ACESSO-01(config)# ip dhcp snooping
SW-ACESSO-01(config)# ip dhcp snooping vlan 1
SW-ACESSO-01(config)# no ip dhcp snooping information option

! Definindo a interface conectada ao Roteador DHCP Real como confiável
SW-ACESSO-01(config)# interface FastEthernet0/1
SW-ACESSO-01(config-if)# ip dhcp snooping trust
```

### 3. Proteção de Portas de Acesso (`Port Security`)
Configuração restrita na interface do usuário final para memorizar dinamicamente o primeiro endereço MAC conectado (Sticky) e aplicar o desligamento imediato (Shutdown) por hardware em caso de divergência:
```gns3
SW-ACESSO-01(config)# interface FastEthernet0/2
SW-ACESSO-01(config-if)# switchport mode access
SW-ACESSO-01(config-if)# switchport port-security
SW-ACESSO-01(config-if)# switchport port-security maximum 1
SW-ACESSO-01(config-if)# switchport port-security mac-address sticky
SW-ACESSO-01(config-if)# switchport port-security violation shutdown
```

---

## 🧪 Validação dos Testes e Resultados

### Fase 1: Estado Saudável da Rede (Usuário Homologado)
Ao conectar o terminal `PC - LEGITIMO` na interface `FastEthernet0/2`, o Switch intercepta a comunicação através do DHCP Snooping, popula as tabelas dinâmicas e grava de forma persistente o endereço físico legítimo através do recurso `Sticky`.
*   **Comandos de Auditoria:** `show port-security interface FastEthernet0/2` e `show interface FastEthernet0/2`.
*   **Status Obtido:** Porta operacional em estado saudável (`Secure-up`), tráfego de dados totalmente permitido e link ativo (`connected`).

![Estado Saudável - Usuário Autorizado](02-estado-saudavel-legitimo.png)

### Fase 2: Flagrante de Ataque e Bloqueio de Intruso (`err-disabled`)
Para simular a ameaça, o cabo da interface `FastEthernet0/2` foi desconectado do usuário legítimo e inserido no terminal `PC - INVASOR`. No momento em que o intruso tentou gerar o primeiro frame de dados na rede forçando um pedido de IP via `ipconfig /renew`, os mecanismos de defesa agiram instantaneamente:
*   **Mecanismo de Defesa:** O Switch identificou que o endereço MAC de origem não correspondia ao MAC autorizado no histórico `Sticky`.
*   **Status Obtido:** A política de violação executou o congelamento imediato da porta por hardware. O status foi alterado para **`Secure-shutdown`** e a interface entrou em modo **`(err-disabled)`**, cortando totalmente a conectividade do invasor e gerando alertas automáticos nos logs do sistema (Syslog).

![Bloqueio do Invasor - Err-Disabled](03-bloqueio-invasor-errdisabled.png)

---

## 🛠️ Procedimento de Recuperação e Normalização
Em um cenário real de engenharia de redes, após a remoção física da ameaça e contenção do incidente pela equipe de segurança, a porta afetada precisa ser reativada administrativamente através da CLI do Switch seguindo a ordem correta:

```gns3
SW-ACESSO-01# configure terminal
SW-ACESSO-01(config)# interface FastEthernet0/2
SW-ACESSO-01(config-if)# shutdown       ! Limpa o estado latente de erro (err-disable)
SW-ACESSO-01(config-if)# no shutdown    ! Restabelece a energia física e o tráfego lógico da porta
```

> 💡 **Nota de Mercado & Evolução Tecnológica:**  
> Embora o *Port Security* estático (modo *Sticky*) seja altamente eficiente e amplamente empregado na atualidade para proteger ativos fixos críticos — tais como **servidores em data centers, câmeras de monitoramento (CFTV) e terminais de autoatendimento (caixas eletrônicos)** —, o mercado corporativo de grande porte adota uma abordagem dinâmica para portas de usuários comuns.  
> 
> Para evitar os alarmes falsos gerados pela alta rotatividade de notebooks nas mesas de trabalho, a indústria moderna utiliza o padrão **IEEE 802.1X (Network Access Control)** integrado a servidores centrais de autenticação (como *Cisco ISE* ou *Aruba ClearPass*). Nesse modelo avançado, a porta do switch nasce bloqueada por padrão e o dispositivo só obtém acesso à rede corporativa após validar com sucesso um certificado digital ou credenciais corporativas válidas, mitigando o ataque na origem sem a necessidade de intervenção manual do administrador.


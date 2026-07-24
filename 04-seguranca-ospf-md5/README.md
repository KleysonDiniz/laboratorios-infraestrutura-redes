# 🛡️ Projeto 04: Hardening de Roteamento Dinâmico (Segurança OSPF com MD5)

## 📌 Cenário e Objetivo do Laboratório
Este projeto demonstra a vulnerabilidade do protocolo OSPF v2 em sua configuração padrão de fábrica e a implementação de segurança através de Hardening com autenticação criptografada MD5. O cenário simula dois roteadores legítimos interligados por um Switch Gigabit Catalyst 3650, sofrendo uma tentativa de infiltração e injeção de rotas falsas por um dispositivo atacante conectado ao mesmo barramento físico.

---

## 📐 1. Topologia da Rede e Endereçamento IP

Abaixo está o design físico e lógico do ambiente de trânsito multiacesso (WAN) e da rede local protegida (LAN):

![Topologia do Laboratório](01-topologia.png)

### 📊 Tabela de Endereçamento:
*   **Rede de Trânsito WAN:** `10.4.4.0/24` (Matriz: `.1` | Filial: `.2` | Invasor: `.66`)
*   **Rede Interna LAN:** `192.168.44.0/24` (Gateway Matriz: `.1` | PC_Matriz: `.10`)

---

## 🥷 2. O Cenário de Vulnerabilidade (Antes do Hardening)

Sem segurança ativa, o protocolo OSPF aceita adjacências de qualquer dispositivo que envie pacotes do tipo *Hello* correspondentes. O atacante se infiltra na rede, estabelece vizinhança e rouba a tabela de rotas internas da empresa.

### Evidência do Ataque no Roteador_Invasor:
Abaixo, comprova-se o invasor com vizinhança em estado `FULL` e a rota interna `192.168.44.0/24` injetada em sua tabela lógica:

![Invasor com Acesso](02-cenario-vulnerabilidade.png)

### Visão da Infraestrutura Corporativa Comprometida:
Tanto a Matriz quanto a Filial aceitam o vizinho malicioso (`66.66.66.66`) sem nenhuma restrição:

![Vizinhança Insegura Geral](03-vendo-invasor.png)

---

## 🛡️ 3. Implementação do Hardening (Criptografia MD5)

Para mitigar a ameaça, aplicamos a autenticação por Message Digest (MD5) diretamente na interface de trânsito dos roteadores legítimos (Roteador_Matriz e Roteador_Filial). 

```ios
! Comando aplicado na Interface GigabitEthernet0/0 dos roteadores legítimos (Roteador_Matriz e Roteador_Filial)
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 kleyson
```

### O Efeito Imediato do Hardening na Matriz:
Assim que o comando é inserido, o roteador exige pacotes assinados digitalmente. Como os vizinhos não possuem a chave, a vizinhança é ejetada imediatamente por estouro de temporizador (*Dead timer expired*):

![Queda da Vizinhança](04-queda-vizinhanca-hardening.png)

> 💡 *Dica de Diagnóstico: Em ambientes de produção, o comando `debug ip ospf adj` pode ser utilizado em modo de privilégio para analisar erros de incompatibilidade de chaves em tempo real.*

---

## 🏆 4. Validação Final do Ambiente Seguro

### 1. Reestabelecimento do Link Confiável
Após padronizar a mesma chave na Filial, o link legítimo reativa a adjacência em modo seguro. O invasor permanece bloqueado permanentemente por não possuir a senha:

![Rede Segura Restaurada](05-restauracao-conexao.png)

### 2. Isolamento e Cegueira Total do Atacante
Abaixo, a prova de sucesso da segurança: o `Roteador_Invasor` perde todos os vizinhos OSPF e a tabela de rotas perde o conhecimento sobre a rede privada da empresa (a linha com a rota **O** sumiu completamente):

![Invasor Bloqueado](06-bloqueio-confirmado.png)

---

## 🏁 Conclusão e Próximos Passos

A implementação do Hardening OSPF com MD5 garantiu a integridade do plano de controle e da tabela de rotas da WAN corporativa.

🚀 **Próximos Passos: Segurança de Camada 2**
Agora que o trânsito entre roteadores está criptografado e seguro, o próximo risco crítico a mitigar são os ataques internos vindos de dentro della rede local na Camada 2 (Enlace), como usuários maliciosos clonando servidores DHCP ou estourando a tabela MAC das portas dos switches corporativos. Veja a solução com DHCP Snooping e Port Security no **Laboratório 05**.

# Administrar gerenciamento de trafego de rede

# 🌐 Gerenciamento de Tráfego de Rede no Microsoft Azure

## 🧠 O que é um Balanceador de Carga?

Um **balanceador de carga** distribui o tráfego entre vários servidores para garantir **desempenho, disponibilidade e redundância**.

### 🎯 Metáfora:
Imagine um **porteiro** (balanceador) que direciona **clientes** (requisições) para **atendentes** (servidores) com base na disponibilidade. Ele evita filas longas e garante que todos sejam atendidos com eficiência.

---

## 🌐 Onde ele atua?

- **Público (Externo)**: exposto à internet — usado para web apps, APIs, etc.
- **Interno (Privado)**: tráfego dentro da VNet — como entre backend e banco de dados.

---

## 🔧 Tipos de Balanceadores de Carga no Azure

| Serviço Azure           | Camada | Finalidade                                                                 |
|------------------------|--------|---------------------------------------------------------------------------|
| Azure Load Balancer    | L4     | TCP/UDP. Público ou interno. Alta performance e baixa latência.          |
| Azure Application Gateway | L7     | HTTP/HTTPS. Suporte a WAF, roteamento por URL e afinidade de sessão.     |
| Azure Front Door       | Global | Balanceamento global, aceleração de conteúdo e alta disponibilidade.     |
| Azure Traffic Manager  | DNS    | Redirecionamento baseado em DNS, latência, prioridade, entre regiões.    |

---

## 🌐 Azure Public Load Balancer

- Recebe tráfego da internet e o distribui para VMs internas.
- Mapeia: **IP público + porta externa → IP privado + porta da VM**.
- Suporte bidirecional: entrada (usuário → VM) e saída (VM → internet).

### 📌 Exemplo:
3 VMs rodando um site na porta 80 → Load Balancer distribui requisições entre elas automaticamente.

---

## 🛡️ Azure Internal Load Balancer (ILB)

- Utilizado **dentro da VNet** ou com conexões via **VPN/ExpressRoute**.
- Sem IP público → **não é acessível pela internet**.
- Direciona tráfego interno entre camadas (ex: frontend → backend).

---

## 📦 SKU do Load Balancer

| SKU    | Recursos |
|--------|----------|
| Basic  | Sem SLA, menos seguro, sem zonas de disponibilidade. **(Descontinuado)** |
| Standard | SLA de 99,99%, zonas de disponibilidade, suporte a NSG, diagnóstico. |

> O SKU define o nível de serviço do Load Balancer, **não o tipo de VM**.

---

## ⚖️ Regras do Azure Load Balancer

### 🔁 Regra de Balanceamento
- Define como o tráfego será distribuído entre VMs do backend.
- Mapeia IP + porta de entrada → IP + porta do backend pool.

### 🔄 Regras NAT
- Acesso direto a VMs usando tradução de porta.

**Exemplo:**
- `52.1.1.1:50001 → VM1:3389 (RDP)`
- `52.1.1.1:50002 → VM2:3389 (RDP)`

---

## 🧷 Persistência de Sessão (Session Persistence)

| Tipo                 | Comportamento |
|----------------------|----------------|
| Nenhuma              | Qualquer VM pode responder |
| Client IP            | Sempre direciona à mesma VM |
| Client IP + Protocolo| Usa IP + protocolo (TCP/UDP) para manter sessão |

📌 Ideal para manter sessões como **login** ou **carrinho de compras**.

---

## 🌐 Azure Application Gateway

Balanceador de carga de **camada 7 (Aplicação)** — interpreta **HTTP/HTTPS** para roteamento avançado.

### ✅ Principais Características

- Camada 7 do modelo OSI
- Interpreta: URLs, cabeçalhos, cookies, query strings
- Suporte a: SSL Offloading, redirecionamento HTTP→HTTPS, **WAF**
- Atua como **proxy reverso**

---

### 🔀 Tipos de Roteamento

| Tipo                  | Exemplo |
|-----------------------|---------|
| Caminho (Path)        | `/api/* → Backend API` |
| Multi-site (Hostname) | `blog.empresa.com → Backend Blog` |
| Cabeçalho / Query     | Direcionamento por parâmetros HTTP |

---

### 🖥️ Backends Compatíveis

- Máquinas Virtuais (VMs)
- VM Scale Sets
- Azure App Services
- Servidores locais (via VPN ou ExpressRoute)

---

### 🧱 Componentes do Application Gateway

| Componente     | Função |
|----------------|--------|
| Frontend IP    | IP público ou privado para receber tráfego |
| Listeners      | Ouve conexões HTTP/HTTPS |
| Routing Rules  | Define o roteamento de tráfego |
| Backend Pools  | VMs ou serviços de destino |
| Health Probes  | Verifica integridade dos backends |
| WAF (opcional) | Proteção contra ameaças Web |

---

## 🧪 Diagnóstico de Rede com Azure Network Watcher

Ferramenta para **monitorar, diagnosticar e visualizar redes** no Azure.

### 🔍 1. Verificação de Fluxo de IP (IP Flow Verify)

- Verifica se um pacote é **permitido ou negado** para uma VM.
- Informa qual **regra de NSG** permitiu/bloqueou.
- Útil como o `ping` e firewall em redes locais.

---

### 🧭 2. Diagnóstico do Próximo Salto (Next Hop)

- Mostra para onde o tráfego está sendo roteado a partir de uma VM.
- Detecta problemas de rotas, **UDRs** ou conflitos.

---

### 🕸️ 3. Topologia de Rede (Topology View)

- Visualização gráfica da VNet:
  - VMs
  - NICs
  - Sub-redes
  - NSGs
  - Gateways
  - Load Balancers

---

### 🧩 Requisitos para uso

- O Network Watcher deve estar habilitado na **mesma região da VNet**.
- Criado automaticamente no RG: **`NetworkWatcherRG`**

---

## ✅ Tabela de Perguntas e Respostas - Tráfego de Rede no Azure

| Pergunta | Resposta |
|---------|----------|
| O que é o Network Watcher? | Ferramenta de diagnóstico e monitoramento de rede no Azure. |
| Funcionalidades principais? | Monitoramento, Diagnóstico, Tráfego. |
| O que faz IP Flow Verify? | Mostra se o tráfego é permitido ou bloqueado por NSG. |
| O que é Next Hop? | Mostra o próximo salto de roteamento a partir de uma VM. |
| O que é a Topologia? | Visualização gráfica dos recursos de rede. |
| Load Balancer x App Gateway? | LB na camada 4 (IP/porta), AGW na camada 7 (conteúdo HTTP). |
| Quando usar App Gateway? | Quando é necessário WAF, SSL offload, roteamento por caminho. |
| O que é WAF? | Firewall de Aplicações Web — protege contra ataques como XSS/SQLi. |
| Vantagem do App Gateway em múltiplos sites? | Suporte a múltiplos domínios com roteamento baseado em host. |
| Load Balancer SKU Standard? | SLA 99,99%, suporte a zonas, mais recursos que o Basic. |
| Como garantir IP fixo no LB? | Configurar IP estático no Frontend IP. |
| Load Balancer Interno? | Distribui tráfego dentro da VNet, sem acesso externo. |
| Pool de back-end? | Define quais VMs processam o tráfego no LB. |
| NSG no Load Balancer? | Controla portas/IPs permitidos no tráfego. |
| Estratégia padrão de distribuição? | Hash de 5 tuplas: IP origem/destino, porta, protocolo. |
| Application Gateway distribui tráfego como? | Round-robin entre os backends disponíveis. |
| Critérios de roteamento no App Gateway? | Host, porta, caminho da URL. |
| Comunicação entre redes? | Feita por Peering entre VNets. |
| Em UDRs, não se pode usar qual próximo salto? | Load Balancer. |
| Para que serve uma NVA? | Controlar e filtrar tráfego com políticas personalizadas. |

---

## 🔗 Fontes Oficiais e Documentação

- [📘 Application Gateway - Visão geral](https://learn.microsoft.com/pt-br/azure/application-gateway/configuration-overview)
- [⚙️ Criar Application Gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- [📚 Módulo Microsoft Learn - Application Gateway](https://docs.microsoft.com/learn/modules/load-balance-web-traffic-with-application-gateway)
- [🔎 IP Flow Verify](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)
- [🧭 Next Hop Overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)
- [🗺️ Visualização de Topologia](https://docs.microsoft.com/azure/network-watcher/view-network-topology)
- [🔧 AZ-700 Lab - Load Balancer](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20and%20configure%20an%20Azure%20load%20balancer)
- [⚙️ AZ-700 Lab - Application Gateway](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Deploy%20Azure%20Application%20Gateway)
- [📘 Guia AZ-104 - Ex. 10](https://mslabs.cloudguides.com/en-us/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%2010)
- [📖 Load Balancer Overview](https://learn.microsoft.com/pt-br/azure/load-balancer/load-balancer-overview)

---

> 🔐 **Licença**: Este conteúdo é voltado para fins educacionais e de estudo para a certificação **AZ-104** feito por mim.



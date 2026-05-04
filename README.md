# PrimoLink - Integra | evolution-go | wuzapi | chatwoot

# PrimoLink Gateway 🚀
**The ultimate enterprise bridge between WhatsApp (Evolution GO / WuzAPI) and Chatwoot. Just link and use!**

O **PrimoLink Gateway** é um middleware de alta performance e baixa latência, desenhado para orquestrar a comunicação bidirecional em tempo real entre APIs de WhatsApp (**Evolution GO** e **WuzAPI**) e o CRM Omnichannel **Chatwoot**.

Diferente de soluções legadas, o PrimoLink foi reconstruído para ser **stateless** e **escalável**, utilizando uma stack moderna com PostgreSQL para persistência e Redis para cache de mapeamento ultrarrápido.

---
<p dir="auto"><a target="_blank" rel="noopener noreferrer" href="/slot-primolink-gateway.png"><img src="/slot-primolink-gateway.png" width="100%" alt="PrimoLink | Gateway | Evolution GO | WuzAPI | Chatwoot" style="max-width: 100%;"></a></p>
---

## 🎯 Por que escolher o PrimoLink?
- **Compatível com várias APIs**: Conecte-se nativamente à **Evolution GO** ou à **WuzAPI** (ou ambas simultaneamente) com paridade total de recursos.
- **Modo Espelho (Mirror Mode)**: O que o agente envia pelo celular físico aparece instantaneamente no Chatwoot como Nota Privada com mídia real.
- **Inteligência de Threading (Media Fallback)**: Sistema exclusivo que recupera mídias citadas direto do Chatwoot, garantindo que reações e respostas nunca percam o contexto visual, mesmo se o link original do WhatsApp expirar.
- **Arquitetura Enterprise**: 
  - **PostgreSQL**: Persistência robusta de logs e mapeamentos.
  - **Redis Cache**: Threading de mensagens em microssegundos.
  - **Multi-Platform**: Imagem nativa para `AMD64` (Cloud) e `ARM64` (Apple Silicon/Raspberry Pi).
- **Gestão Multi-Slot**: Gerencie grátis até **5 conexões simultâneas** e de forma isolada em uma única instância.

---

## 🛠️ Como Executar (Docker Compose)

Para produção, o PrimoLink exige um banco PostgreSQL e um Redis. Abaixo, a estrutura recomendada:

```yaml
version: '3.8'

services:
  primolink:
    image: primo/primolink:beta
    container_name: primolink
    restart: unless-stopped
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://user:pass@postgres:5432/primolink
      - REDIS_URL=redis://redis:6379
      - APP_URL=https://gateway.seudominio.com
      - PANEL_USER=admin
      - PANEL_PASS=sua_senha_forte
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:15-alpine
    restart: always
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=primolink
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    restart: always

volumes:
  pgdata:
```

---

## ⚙️ Variáveis de Ambiente

| Variável | Obrigatória | Descrição |
| :--- | :---: | :--- |
| `DATABASE_URL` | Sim | String de conexão com PostgreSQL (`postgresql://...`). |
| `REDIS_URL` | Sim | URL do Redis para cache e threading (`redis://...`). |
| `APP_URL` | Sim | URL pública onde o gateway será acessado (Crucial para Webhooks). |
| `PANEL_USER` | Sim | Usuário administrativo do painel web. |
| `PANEL_PASS` | Sim | Senha administrativa do painel web. |

---

## 🌐 Endpoints de Webhooks
*Configure estes endereços nos seus provedores e no Chatwoot:*

### 📥 Entrada (Providers -> Gateway)
- **Evolution GO:** `[APP_URL]/webhook/evogo/[slot_id]`
- **WuZAPI:** `[APP_URL]/webhook/wa/[slot_id]`

### 📤 Saída (Chatwoot -> Gateway) direto na Inbox (API)
- **Chatwoot Inbox Webhook:** `[APP_URL]/webhook/chatwoot/[slot_id]`

---

## 💾 Persistência e Segurança
- **Build Seguro**: Imagem baseada em Alpine Linux, rodando com usuário não-root.
- **Isolamento**: Cada Slot (1 a 5) possui seu próprio namespace de dados, permitindo misturar provedores e empresas na mesma instância sem vazamento de informações.

*PrimoLink Gateway: Excelência técnica para descomplicar a sua operação de atendimento via WhatsApp.*

chatwoot | evolution-go | wuzapi

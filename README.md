# PrimoLink-evolution-go-chatwoot
# PrimoLink Gateway 🚀
**The ultimate integration bridge between Evolution GO and Chatwoot. Includes up to 5 integrations for free. Just link and use!**

O **PrimoLink Gateway** é um middleware de alta performance desenhado para orquestrar e rotear a comunicação bidirecional em tempo real (webhooks) entre o [Evolution GO](https://hub.docker.com/r/evoapicloud/evolution-go) (API de WhatsApp) e o [Chatwoot](https://hub.docker.com/r/chatwoot/chatwoot) (Caixa de Entrada Omnichannel).

A imagem foi construída em cima do Alpine Linux (`node:22-alpine`) com **Multi-Stage Build**, garantindo um contêiner extremamente leve, enxuto e seguro, rodando em ambiente sem privilégios de `root`.

---

## 🎯 Principais Funcionalidades
- **Gestão Multi-Slot**: Gerencie nativamente até **5 conexões simultâneas** (instâncias) de forma isolada e totalmente gratuita.
- **Alta Performance**: Escrito em Node.js com banco de dados SQLite local embarcado (`better-sqlite3`).
- **Segurança de Nível de Produção**: 
  - Rate Limiter agressivo (Anti-DDoS).
  - Bloqueio de IP automático após falhas de Login (Anti-Bruteforce).
  - Isolamento de Headers (Helmet) e Validação Estrita de Host de Origem.
- **Painel Administrativo Embutido**: Interface reativa em React para gerenciar URLs de integração e credenciais.

---

## 🛠️ Como Executar (Quick Start)

Rodar o PrimoLink é incrivelmente simples. Recomendamos o uso do Docker Compose, mas você também pode usar o Docker CLI padrão.

### Via Docker Run

```bash
docker run -d \
  --name primolink \
  -p 3000:3000 \
  -e APP_URL="https://app.seusite.com.br" \
  -e PANEL_USER="admin" \
  -e PANEL_PASS="sua_senha_forte" \
  -v primolink_data:/app/data \
  primo/primolink:beta
```

### Via Docker Compose

Se você prefere salvar sua estrutura, crie um `docker-compose.yml`:

```yaml
version: '3.8'

services:
  primolink:
    image: primo/primolink:beta
    container_name: primolink
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - APP_URL=https://app.seusite.com.br
      - PANEL_USER=admin
      - PANEL_PASS="sua_senha_forte" # Use aspas se tiver caracteres especiais!
    volumes:
      - primolink_data:/app/data

volumes:
  primolink_data:
```

---

## ⚙️ Variáveis de Ambiente

O painel é ultra minimalista e depende apenas de três variáveis essenciais para o boot da aplicação:

| Variável | Obrigatória | Descrição |
| :--- | :---: | :--- |
| `APP_URL` | Sim | URL oficial onde o painel será acessado. Qualquer requisição vinda de outro domínio/host será barrada (`403 Forbidden`). |
| `PANEL_USER` | Sim | Usuário de acesso exclusivo para o Painel Administrativo. |
| `PANEL_PASS` | Sim | Senha do Painel Administrativo (Exemplo: `"minhasenha"`). |

> **Nota de Segurança:** As integrações (URLs do Evolution e Chatwoot, bem como Tokens) **não** são via `.env`. Você configura tudo diretamente de forma segura e visual por dentro do Painel Web.

---

## 💾 Persistência de Dados (Storage)

Para garantir que suas configurações (Slots), credenciais armazenadas e filas fiquem salvas quando o contêiner reiniciar ou sofrer deploy, você **deve mapear o volume** apontando para o diretório interno da aplicação:

- **Caminho Interno no Contêiner:** `/app/data`

Este volume salva dois itens cruciais:
1. `gateway.db` (Banco de dados SQLite robusto que guarda os logs de uso e requisições).
2. `slots.json` (Arquivo blindado que retém todas as configurações das 5 integrações).

Sem mapear o `/app/data`, sua configuração voltará ao padrão de fábrica a cada atualização da imagem!

---

## 🌐 Endpoints de Webhooks
*Você deve configurar estes caminhos lá dentro da caixa de entrada API Chatwoot e na instância da Evolution GO.*

- Evolution GO: `[Sua_APP_URL]/webhook/evogo/[id_do_slot]`
- Chatwoot: `[Sua_APP_URL]/webhook/chatwoot/[id_do_slot]`

*Feito com excelência para descomplicar a sua operação!*

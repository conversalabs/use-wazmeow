# wazmeow — WhatsApp API Gateway

Repositório público de distribuição (binários + instalador) do **wazmeow**.

## Instalação

**Linux/macOS** (instalador automático):
```bash
curl -fsSL https://raw.githubusercontent.com/guilhermejansen/use-wazmeow/main/install.sh | bash
```

Ou baixe o binário da última versão em **[Releases](https://github.com/guilhermejansen/use-wazmeow/releases/latest)**.

## Licença

O wazmeow valida a licença contra o hub central de licenciamento.
Configure a variável `LICENSE_KEY` com a sua chave.

---

## Documentação e operação atualizadas — v2.5.1

**wazmeow**, da Conversa Labs, é um gateway REST auto-hospedado para conectar números WhatsApp, trocar mensagens e distribuir eventos para suas integrações. Inclui dashboard web, sessões independentes por usuário e engine nativo de chamadas.

Este é o repositório público de apoio: documentação, contratos de API, Postman, configurações e acesso aos pacotes de distribuição. O código do servidor tem licença comercial. A integração usa o protocolo de aparelhos vinculados; **não é a API oficial da Meta/WhatsApp**. Mudanças no serviço, restrições de conta e regras do WhatsApp podem afetar os recursos. Use contas e destinatários autorizados.

[Documentação](docs/INDEX.md) · [Guias para LLMs](llms.txt) · [Releases](https://github.com/conversalabs/use-wazmeow/releases) · [Changelog](CHANGELOG.md)

## O que você pode integrar

| Grupo | Recursos |
|---|---|
| Sessões e dispositivos | Múltiplas sessões, QR, código por telefone, status, reconexão e preferências por usuário. Passkey depende da conta e do autenticador do titular. |
| Mensagens | Texto, resposta/citação, imagens, áudio, vídeo, documentos, stickers, contatos, localização, links, reações, listas e enquetes. |
| Respostas rápidas | Criar, listar e remover as respostas rápidas nativas de texto da conta (`/quick-reply`). |
| Eventos com link | Criar, listar e remover eventos (`/event/create`, `/event/list`, `/event/{eventID}`), com imagem de capa e lembretes, e enviar o convite de um evento existente (`/chat/send/event-invite`). |
| Interativos e negócios | Botões, carrosséis, flows, catálogo, produtos, coleções próprias (inclusive em análise, recusadas e vazias), pedidos, cobranças e histórico de cobranças (`/business/order/charges`), conforme contrato e licença da distribuição. |
| Conversas e organização | Presença, leitura, marcar conversa como lida ou não lida (`/chat/read`), arquivamento, fixação, silenciamento, etiquetas e favoritos; disponibilidade depende da sincronização da conta. |
| Grupos, comunidades e canais | Participantes, permissões, convites, comunidades, newsletters, consulta de canal por convite (`/newsletter/info-invite`) e status. Entrega do histórico recente a novos membros (`/group/history/*`), pelas últimas N mensagens retidas do grupo (até 200) ou por lista de IDs, com as mensagens fixadas incluídas automaticamente e marcadas como fixadas no aparelho de quem recebe, e também no mesmo pedido que adiciona os participantes. A gestão de participantes devolve a resposta do WhatsApp para cada um, inclusive o código e a validade do convite oferecido quando a privacidade da pessoa recusa a adição, e esse convite pode ser enviado no chat individual com ela (`/chat/send/group-invite`), como o "Enviar convite" do aplicativo. Administrador sempre pode enviar; membro comum quando o grupo permite. Permissões de grupo da tela do aplicativo: quem adiciona participantes (`/group/memberaddmode`), quem compartilha o convite (`/group/memberlinkmode`), quem compartilha o histórico (`/group/historysharemode`) e aprovação de novos membros (`/group/joinapproval`); o valor atual de cada uma vem em `/group/info`. Liberar o convite para todos exige adicionar participantes em todos (o WhatsApp recusa a outra combinação; a rota responde 409 nomeando a causa), e ao restringir o compartilhamento o WhatsApp gera um link de convite novo, invalidando o anterior (liberar de novo não gira o link); o gateway não gira nem revoga por conta própria. O aplicativo restringe o convite no mesmo gesto em que restringe "adicionar participantes" a admins num grupo comum: `/group/memberaddmode` muda só o ajuste dele por padrão e reproduz esse gesto com o campo opcional `sync_invite_share`. Bloquear encaminhadas muitas vezes (`/group/frequentlyforwarded`; o WhatsApp ainda não oferece o ajuste e recusa a gravação, e a rota responde `setting_not_available`), envio para análise dos admins (`/group/adminreports`), cancelar o próprio pedido de entrada (`/group/join/cancel`) e aceitar o convite que um admin envia como mensagem (`/group/join` e `/group/inviteinfo` com `Inviter`). Privacidade avançada da conversa (`/group/limitsharing`), envio e lista de mensagens para análise dos admins (`/group/adminreports/report` e `/group/adminreports/list`) e denúncia do grupo ao WhatsApp (`/group/report`) e formulário de entrada (`/group/membershipform*`: perguntas, pedidos com respostas e entrada respondendo; sem a revisão por formulário liberada na conta, os pedidos vêm sem as respostas, como no aplicativo); `/group/info` traz o estado atual desses ajustes. Convite por SMS ou e-mail a quem não tem WhatsApp não é oferecido. Nas comunidades: as duas permissões da tela do aplicativo (`/community/group-add-mode`, `/community/member-add-mode`, leitura em `/community/settings`), sugestões de grupo com aprovação, recusa e cancelamento (`/community/group-suggestions/*`), criação de grupo já dentro da comunidade (`/community/group/create`), lista completa dos grupos inclusive os não ingressados (`/community/all-groups`), transferência de propriedade e desativação (`/community/transfer-ownership`, `/community/deactivate`); veja o [guia de comunidades](docs/communities.md). Administração de canal: editar nome, descrição, foto e reações permitidas (`/newsletter/edit`), seguidores com o papel de cada um (`/newsletter/followers`), admins e convites pendentes (`/newsletter/admins`), aceitar e revogar convite de admin (`/newsletter/admin-invite/accept`, `/newsletter/admin-invite/revoke`), rebaixar admin (`/newsletter/demote-admin`), transferir a propriedade (`/newsletter/transfer-ownership`) e convidar para seguir (`/newsletter/follower-invite`). A lista de seguidores identifica apenas quem os aplicativos oficiais mostrariam — quem administra o canal e quem está nos contatos da sessão; os demais vêm sem nada que os identifique, com o papel e a data em que passaram a seguir — e não tem paginação: a consulta oficial leva só uma quantidade, no máximo 5000. Transferir a propriedade e rebaixar admin conferem as pré-condições do aplicativo antes de enviar (só o dono transfere, nunca para si mesmo, só para quem já é admin; o dono não deixa de ser admin do próprio canal), recusando com 409 e motivo próprio sem chamar o servidor; transferir deixa quem transferiu como admin. Fixar e desfixar publicações (`/newsletter/pin`, `/newsletter/unpin`) — publicação com mais de 30 dias não é fixável e, com quatro fixadas, uma nova substitui a mais antiga, então a resposta traz o conjunto fixado inteiro com a validade de cada uma. Ler as próprias reações e votos (`/newsletter/my-reactions`) e as capacidades de admin do canal (`/newsletter/capabilities`). Ler quem reagiu a uma publicação (`/newsletter/reaction-senders`) e quem votou em cada opção da enquete (`/newsletter/poll-voters`), com quantos o WhatsApp listou — não a contagem total de reações ou votos; essas leituras nomeiam apenas quem está nos contatos da sessão, o mesmo critério dos aplicativos. Votar numa enquete, trocar e retirar o voto (`/newsletter/poll-vote`, opções pelo nome, conferidas contra a enquete antes de enviar); `/newsletter/messages` e `/newsletter/message-updates` trazem em `poll` as opções, os votos de cada uma e o total. Rotular publicação como conteúdo de IA ou parceria paga (`/newsletter/label-ai-content`, `/newsletter/label-paid-partnership`; o rótulo é irremovível) e ler as restrições do canal (`/newsletter/enforcements`). Encontrar canais públicos: o diretório por lista com filtro de país e de categoria e paginação por cursor (`/newsletter/directory`), a prévia por categoria (`/newsletter/directory/categories`), a busca por nome (`/newsletter/search`), os recomendados à conta (`/newsletter/recommended`, que escondem como o aplicativo os canais já seguidos e os suspensos) e os parecidos com um canal (`/newsletter/similar`), com os limites que o aplicativo pede e a marcação de quais canais a conta já segue. Denúncias que a conta fez, com andamento e recurso (`/newsletter/reports`), denunciar um canal sem amostras de publicações (`/newsletter/report`), perfil de admin do canal (`/newsletter/admin-profile`, `/newsletter/admin-profile/setting`) e estatísticas só agregadas (`/newsletter/insights`). Perguntas e respostas no canal: o dono ou um admin pergunta (`/newsletter/question/send`, texto, imagem ou vídeo), lê as respostas com filtros, busca e cursor (`/newsletter/question/responses`), responde publicamente a uma resposta (`/newsletter/question/reply`), curte (`/newsletter/question/response/like`), destaca, oculta e reexibe (`/newsletter/question/response/state`) e bloqueia quem respondeu de responder às perguntas daquele canal (`/newsletter/question/response/block`); o seguidor responde (`/newsletter/question/respond`, de 1 a 256 caracteres contados como o aplicativo conta), lê e apaga as próprias respostas (`/newsletter/question/my-responses`, `/newsletter/question/my-responses/delete`). Quem respondeu aparece como nos aplicativos ("Followers can't see who responded, but admins can."): para o dono e os admins, o nome da agenda de quem é contato ou o apelido de quem não é, e a foto, nunca o número nem o identificador interno; seguidores não veem quem respondeu. Cada escrita é enviada uma vez só, com 504 quando o resultado fica incerto e 503 quando a sessão não estava conectada e nada foi enviado. Não disponíveis na API: denunciar uma resposta ao WhatsApp (sistema geral de denúncia, que a API não implementa), desbloquear quem respondeu (os aplicativos não têm o comando), pergunta em áudio ou vídeo redondo, perguntas em status de canal, editar uma pergunta (os aplicativos não deixam) e encaminhamento próprio de pergunta (é o encaminhamento comum de `/chat/send`). Cada operação respeita as permissões da conta. |
| Eventos | Webhooks individuais/globais, RabbitMQ, Amazon SQS, Redis Streams e WebSocket; filtros, retries, buffer persistente, DLQ e replay administrativo. |
| Mídia e armazenamento | Upload/download, thumbnails e S3 compatível, com configuração global/por usuário e retenção configurável. |
| Pareamento em aplicativos | Connector incorporável com token efêmero, QR/código e encaminhamento do passo de passkey ao titular. |
| Rede | Proxy por sessão e rota móvel nativa via Tailscale/tsnet, com versionamento e controle de prontidão. |
| Chamadas nativas | Controle de chamadas no gateway, áudio PCM por WebSocket, reprodução e gravação no S3 da própria sessão (sem ele, o início da gravação responde `400`). Vídeo H.264 e recursos de grupo/link têm condições próprias. |
| Operação | Dashboard, `/health`, monitoramento SSE, métricas, Sentry e tracing OpenTelemetry/Jaeger, conforme configuração. |

Não há promessa de capacidade ilimitada: dimensione sessões, mídia, filas e conexões conforme seus recursos e sua carga real.

## Licença e identidade da distribuição

| Item | Valor |
|---|---|
| Produto | wazmeow |
| Repositório público | [conversalabs/use-wazmeow](https://github.com/conversalabs/use-wazmeow) |
| Imagem | `ghcr.io/conversalabs/wazmeow` |
| Imagem do serviço de mídia de chamadas | `ghcr.io/conversalabs/wazmeow-callworker` (`amd64` e `arm64`) |
| Aplicativo do atendente e SDK de chamadas | Os mesmos arquivos neutros da Conversa Labs para todas as distribuições, na [release](https://github.com/conversalabs/use-wazmeow/releases) |
| Registro | GitHub Container Registry (GHCR) |
| Prefixo das variáveis do servidor | `WAZMEOW_` |
| Licença | Comercial, informada em `LICENSE_KEY` |

Chamadas, botões, carrosséis, flows, shop e envios de pedidos/cobranças não têm bloqueio Enterprise nesta distribuição. A licença comercial continua obrigatória e validada no startup; autenticação, validação de payload e disponibilidade da sessão continuam aplicáveis.

`LICENSE_KEY` não usa o prefixo do produto. `DATABASE_URL`, `GLOBAL_*`, `CONNECTOR_*` e `NETWORK_ROUTE_*` também mantêm esses nomes. O token administrativo é `WAZMEOW_ADMIN_TOKEN`; ele é diferente da licença e do token de cada sessão.

## Quick Start com Docker Compose

Pré-requisitos: Docker com Compose v2 e uma licença válida. O exemplo cria um PostgreSQL dedicado, preserva seus dados em volume e expõe o gateway somente em `127.0.0.1:8080`. Crie os dois arquivos abaixo em uma pasta nova, sem substituir os de uma instalação existente.

### 1. Configure `.env`

```dotenv
DOCKER_TAG=v2.5.1
LICENSE_KEY=SUBSTITUA_PELA_SUA_LICENCA
WAZMEOW_ADMIN_TOKEN=SUBSTITUA_POR_UM_TOKEN_ALEATORIO
POSTGRES_PASSWORD=SUBSTITUA_POR_UMA_SENHA_ALEATORIA_HEXADECIMAL
```

Gere valores diferentes para o token administrativo e a senha, por exemplo com `openssl rand -hex 32`. A senha hexadecimal pode entrar na URI PostgreSQL sem escape adicional. Proteja o `.env` e mantenha-o fora do Git. Nunca use as strings de substituição como credenciais.

### 2. Salve como `compose.yaml`

```yaml
services:
  postgres:
    image: postgres:16-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: app
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:?defina POSTGRES_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app -d app"]
      interval: 5s
      timeout: 5s
      retries: 10
  gateway:
    image: ghcr.io/conversalabs/wazmeow:${DOCKER_TAG:?defina DOCKER_TAG}
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy
    ports:
      - "127.0.0.1:8080:8080"
    environment:
      LICENSE_KEY: ${LICENSE_KEY:?defina LICENSE_KEY}
      WAZMEOW_ADMIN_TOKEN: ${WAZMEOW_ADMIN_TOKEN:?defina WAZMEOW_ADMIN_TOKEN}
      WAZMEOW_ADDRESS: http://localhost:8080
      WAZMEOW_PORT: "8080"
      DATABASE_URL: postgres://app:${POSTGRES_PASSWORD:?defina POSTGRES_PASSWORD}@postgres:5432/app?sslmode=disable
      GLOBAL_EVENT_BUFFER_USE_DATABASE: "true"
volumes:
  postgres_data:
```

O `sslmode=disable` acima vale para a rede interna deste exemplo. Em produção, configure o banco e seu TLS, publique o gateway atrás de HTTPS e ajuste `WAZMEOW_ADDRESS` para a URL que seus consumidores realmente acessam. Deixe `SESSION_DEVICE_NAME` sem configuração para herdar a identidade nativa do dispositivo.

### 3. Inicie e confira

```bash
docker compose pull
docker compose up -d
curl --fail-with-body http://localhost:8080/health
```

Abra o dashboard em `http://localhost:8080/dashboard/` e a referência da instância em `http://localhost:8080/api/`. Se o startup falhar, consulte `docker compose logs gateway`; licença, conexão com banco e migrações precisam estar válidas antes do pareamento.

O exemplo fixa a versão `v2.5.1`. Leia as notas da versão antes de atualizar; `latest` pode mudar. Os arquivos completos [`.env.sample`](.env.sample), [Compose](docker-compose.yml) e [Swarm](docker-compose-swarm.yaml) são referências que precisam de configuração para seu ambiente.

## Primeira sessão pela API

Os comandos abaixo usam Bash/Zsh, `curl` e `openssl`. Execute-os na pasta do `.env` criado acima, já preenchido, para reutilizar o mesmo token administrativo.

```bash
set -a
. ./.env
set +a
BASE_URL='http://localhost:8080'
USER_TOKEN="$(openssl rand -hex 32)"

curl --fail-with-body -X POST "$BASE_URL/admin/users" \
  -H "Authorization: $WAZMEOW_ADMIN_TOKEN" \
  -H 'Content-Type: application/json' \
  --data "{\"name\":\"primeira-sessao\",\"token\":\"$USER_TOKEN\",\"events\":\"Message\"}"
```

Guarde o token de usuário em seu gerenciador de segredos. A criação devolve o ID e as configurações da sessão; ela não significa que o WhatsApp já está pareado.

```bash
curl --fail-with-body -X POST "$BASE_URL/session/connect" \
  -H "token: $USER_TOKEN" \
  -H 'Content-Type: application/json' \
  --data '{"Subscribe":["Message"],"Immediate":true}'

curl --fail-with-body "$BASE_URL/session/qr" -H "token: $USER_TOKEN"
curl --fail-with-body "$BASE_URL/session/status" -H "token: $USER_TOKEN"
```

Use o QR no dashboard ou renderize a imagem devolvida por `/session/qr`. O QR pode demorar a ficar disponível. `Immediate:true` retorna antes do término da conexão; confirme `connected` e `loggedIn` no status. Para código por telefone, consulte `POST /session/pairphone` no OpenAPI. Se a conta exigir passkey, siga o [guia de passkey](docs/passkey-integration.md).

Depois de parear, um envio de texto para **um destinatário autorizado** segue este formato. Substitua `DESTINO_E164` por país, DDD e número somente com dígitos:

```bash
DESTINO_E164='SUBSTITUA_PELO_NUMERO_AUTORIZADO'
curl --fail-with-body -X POST "$BASE_URL/chat/send/text" \
  -H "token: $USER_TOKEN" \
  -H 'Content-Type: application/json' \
  --data "{\"Phone\":\"$DESTINO_E164\",\"Body\":\"Olá! Mensagem de integração.\"}"
```

### Autenticação e respostas

| Superfície | Credencial |
|---|---|
| Gestão administrativa, como `/admin/users` | Header `Authorization: <ADMIN_TOKEN>`; também aceita `Bearer <ADMIN_TOKEN>` |
| Sessões, chat, grupos, negócios e chamadas | Header `token: <USER_TOKEN>` da sessão |
| Connector no navegador | Token efêmero `ct_…`, emitido pelo backend com o token real |
| SSE de monitoramento administrativo | Token de sessão de monitoramento emitido por `/admin/events/session`; consulte o OpenAPI |

Não use o admin token no header `token`. Em REST, prefira headers a parâmetros de URL. Clientes WebSocket/EventSource de navegador podem exigir `?token=…`; proteja esses URLs contra logs e compartilhamento.

As respostas JSON normalmente usam `{ "code": 200, "success": true, "data": {} }`; falhas trazem `success:false` e `error`. Confira o status HTTP e o schema da operação. Leia o payload em `data` quando houver esse envelope.

## Documentação, OpenAPI e LLMs

| Arquivo | Conteúdo |
|---|---|
| [docs/INDEX.md](docs/INDEX.md) | Índice dos guias públicos |
| [llms.txt](llms.txt) | Ponto de entrada para assistentes e descoberta dos contratos |
| [api/llm.txt](api/llm.txt) | Chamadas nativas: autenticação, controle, PCM/H.264, exemplos e limites |
| [api/connector-llm.txt](api/connector-llm.txt) | Connector: emissão de token, embed, pareamento e segurança |
| [api/network-route-llm.txt](api/network-route-llm.txt) | Rota móvel: provisionamento, versões, prontidão e recuperação |
| [api/spec.yml](api/spec.yml) | OpenAPI 3, referência para integração e geração de clientes |
| [api/swagger.json](api/swagger.json) / [api/swagger.yaml](api/swagger.yaml) | Contratos Swagger gerados |
| [wazmeow.postman_collection.json](wazmeow.postman_collection.json) | Collection Postman da marca |
| [wazmeow.postman_environment.json](wazmeow.postman_environment.json) | Environment Postman; preencha host, admin e token da sessão |

Os arquivos `api/*` deste repositório correspondem aos servidos em `/api/*` pelo gateway. Use os contratos da versão instalada; o conteúdo de `main` pode acompanhar documentação mais recente. Ao usar um LLM, forneça também os limites abaixo e confira os nomes e tipos dos campos no OpenAPI.

| Guia | Para que serve |
|---|---|
| [Instalação](docs/INSTALL.md) | Distribuição e configuração inicial |
| [Configuração](docs/CONFIGURATION.md) | Banco, variáveis, transportes e observabilidade |
| [Eventos e webhooks](docs/webhook-events.md) | Envelope, enriquecimento e eventos de domínio |
| [Connector](docs/connector.md) | Incorporar o pareamento no seu aplicativo |
| [Passkey](docs/passkey-integration.md) | Passo do titular no navegador e confirmação |
| [Importação de sessão](docs/session-import.md) | Alternativa BETA com snapshot sensível |
| [Rota móvel](docs/network-route.md) | Egresso por sessão, persistência e operação |
| [Chamadas: modos, licença e implantação](docs/calls-overview.md) | Comece aqui: os dois modos de uso, a licença e o que instalar |
| [Chamadas com serviço de mídia: instalação](docs/calls-media-install.md) | Do zero à primeira ligação: provisão, serviço de mídia, aplicativo do atendente e SDK |
| [Áudio de chamadas](docs/call-audio-implementation-guide.md) | Controle, stream e gravação |
| [Vídeo de chamadas](docs/call-video-guide.md) | H.264, codecs, grupos e limitações |
| [Integração de chamadas no navegador](docs/whatsapp-web-call-integration.md) | Fluxos de integração com o gateway |


O [contrato de eventos por transporte](docs/webhook-events.md#contrato-de-saída-por-transporte--global-e-individual) documenta webhook/RabbitMQ individuais e globais, SQS, WebSocket e Redis MessagePack, catálogo de eventos, enrichment, ACK/deduplicação e normalizador de referência para seu integrador.

## Connector, passkey e importação

Seu backend chama `POST /session/connector/token` com o token real da sessão e entrega somente o token efêmero ao widget em `/connector.js`. Configure `CONNECTOR_CORS_ORIGINS` com as origens permitidas. Tokens efêmeros e estado de passkey são locais ao processo; mantenha afinidade de réplica durante o pareamento.

Passkey exige o autenticador do titular e a cerimônia WebAuthn em `https://web.whatsapp.com`. O bookmarklet servido em `/passkey-bookmarklet.html` encaminha a resposta ao gateway; inclua `https://web.whatsapp.com` nas origens permitidas em `CONNECTOR_CORS_ORIGINS`. O companion exige Chrome/Chromium e só deve ser usado quando seu executável estiver incluído no pacote da plataforma. Não há extensão passkey distribuída para instalar nem garantia de funcionamento sem interação do titular.

`POST /session/import` é **BETA**. O snapshot contém segredos equivalentes à sessão e depende da estrutura do WhatsApp Web; um dump incompleto pode retornar `422`. Prefira o pareamento normal/por passkey e use importação apenas como decisão explícita do operador.

## Chamadas: disponibilidade e limites

- O engine nativo roda junto ao gateway. Habilite `callsEnabled` em `PUT /call/config` e reconecte a sessão. Use `manual` para seu aplicativo controlar o atendimento e o áudio; `webhook` atende automaticamente e emite eventos.
- Saída com mídia usa `POST /call/dial`; `/call/initiate` responde `501` por padrão e só vira alias de dial com `GLOBAL_CALL_INITIATE_DIAL_ALIAS=true`.
- Áudio bidirecional: `/call/{call_id}/stream`, PCM **s16le, 16 kHz, mono**, quadros de **960 amostras / 1920 bytes / 60 ms**. O áudio começa após o atendimento; sinalização de chamada não prova fluxo de mídia.
- Vídeo: H.264 Annex-B no stream próprio, com codec no cliente. A API não converte automaticamente câmera, WebRTC, SDP ou SIP; confira o guia e valide sua combinação de cliente, conta e rede. Limites de participantes são impostos pelo WhatsApp.
- Os modos `bot`, `ivr` e `ai` são aceitos na configuração, mas o voice agent embutido está **indisponível**: nesses modos as chamadas recebidas são rejeitadas. Preencher URLs de STT/LLM/TTS não habilita o agente. Uma integração externa pode processar PCM em `manual`.
- Gravação exige `callRecord:true` e **S3 do usuário**. Gravação e stream PCM são mutuamente exclusivos na mesma chamada. O áudio fica em memória/S3; controle e partes ficam no banco. Pausas acima de 2 s podem não entrar no arquivo.
- Rotas móveis com `failClosed:true` e `tcpOnly:true` bloqueiam o engine nativo. A política permissiva não ativa esse guard, mas o SOCKS continua sendo TCP: não interprete `failClosed:false` como suporte a mídia UDP pela rota móvel nem como garantia de chamadas.

Os componentes do **modo com serviço de mídia** fazem parte da distribuição a partir da 2.5.0: a imagem do **serviço de mídia** (`ghcr.io/conversalabs/wazmeow-callworker`, `amd64` e `arm64`), a **ferramenta de provisão**, que vem na imagem da API, e, na [release](https://github.com/conversalabs/use-wazmeow/releases), o **aplicativo do atendente** para macOS 14+ e Windows e o **SDK de chamadas** (`@conversalabs/calls-sdk`). O aplicativo e o SDK são os mesmos arquivos neutros da Conversa Labs para todas as distribuições. Esse modo é **Beta** nesta versão, no macOS e no Windows; o aplicativo para Windows ainda não foi testado numa máquina Windows e ainda não tem certificado de assinatura de código, então o Windows mostra o aviso do SmartScreen na instalação. Ponte SIP não existe em nenhum modo. O passo a passo, da instalação à primeira ligação, está no [guia de instalação com serviço de mídia](docs/calls-media-install.md).

## Eventos, armazenamento e backups

Configure o webhook e as assinaturas da sessão para receber os eventos necessários. Para entrega persistente, mantenha o buffer no banco e acompanhe filas, tentativas, DLQ e arquivo administrativo. Consumidores devem tolerar duplicatas e deduplicar pelos identificadores de evento; replay pode repetir efeitos. O envelope enriquecido permanece sob `data`, com extensões de domínio em `data.v1` quando aplicável.

S3 pode ser global ou por usuário para mídia comum; gravações de chamada exigem o S3 do usuário. Defina retenção e permissões do bucket. URLs assinadas expiram e não substituem a chave permanente do objeto.

Mantenha backup consistente do banco da aplicação **e do store de sessões**, das configurações/chaves e dos objetos S3 necessários. Inclua o buffer/arquivo persistente e, se usar armazenamento SQLite separado, seus arquivos de dados. O exemplo Compose mantém o PostgreSQL em `postgres_data`; recriar o container preserva esse volume, mas removê-lo apaga o banco.

O estado da rota móvel fica cifrado no banco. Preserve as tabelas de estado e a chave automática do banco ou `NETWORK_ROUTE_STATE_KEY`, caso configurada. Prefira `failClosed:true`, `tcpOnly:true` e só considere a rota pronta com `ready:true` e versões desejada/aplicada iguais. Remover uma rota muda o egresso da sessão e deve ser decisão explícita, nunca fallback automático.

Antes de atualizar, faça backup e ensaie restauração. Leia migrações e notas de versão; voltar somente a imagem não garante reversão do schema. Evite executar a mesma sessão restaurada simultaneamente em dois gateways.

## Binários, instaladores e integridade

Baixe o pacote correspondente ao seu sistema e arquitetura em [Releases](https://github.com/conversalabs/use-wazmeow/releases). Mantenha o binário e os assets `static/` do mesmo pacote para que o dashboard, o SDK JavaScript que ele carrega (`ZZ.sdk`) e `/api/` permaneçam compatíveis. Esse SDK do dashboard não é o SDK de chamadas do modo com serviço de mídia.

As releases disponibilizam arquivos por plataforma e `checksums.txt`. Confira o SHA-256 do arquivo baixado contra a entrada correspondente, por exemplo com `sha256sum <arquivo>` no Linux, `shasum -a 256 <arquivo>` no macOS ou `Get-FileHash <arquivo> -Algorithm SHA256` no PowerShell.

Os auxiliares [install.sh](install.sh) (Linux/macOS) e [install.bat](install.bat) (Windows) pertencem ao gateway existente. Leia suas opções e as notas da release antes de executar; instalação, permissões de serviço e compatibilidade precisam ser verificadas no seu ambiente. Esses scripts instalam somente o gateway. O **serviço de mídia** de chamadas e o **aplicativo do atendente** têm instalação própria, descrita no [guia de instalação com serviço de mídia](docs/calls-media-install.md); ponte SIP não existe.

## Suporte

Para licença e suporte do **wazmeow**, contate [suporte@conversalabs.com.br](mailto:suporte@conversalabs.com.br). Para problemas de documentação e reprodução sem dados privados, use as [issues do repositório público](https://github.com/conversalabs/use-wazmeow/issues).

Informe versão, sistema/arquitetura, endpoint, status HTTP e passos mínimos. Remova tokens, licença, telefone/JID, QR, dump de sessão, chaves, áudio e URLs assinadas dos anexos e logs compartilhados.

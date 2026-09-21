# Bitnik Rule Forge

Ferramenta de ficheiro único (HTML/CSS/JS, sem dependências, sem build) para o fluxo de design do [Bitnik Studio](https://github.com/dferreiramarques/bitnikgames-design-system).

Serve para transformar a lógica de um jogo, em quatro passos, num documento de regras e num protótipo jogável:

1. **Fluxo** — desenha a lógica do jogo como nós `DATA` / `FLOW` / `ACTION` / `SCORE` (o taxonomy BGE), ligados entre si num canvas.
2. **Cartões de Lógica** — escreve as regras como cartões Gherkin (`Dado` / `Quando` / `Então`), cada um marcado com o tipo de nó a que corresponde.
3. **Regras** — escreve como se joga, um texto por bloco do fluxo (mais secções gerais, como o Objetivo), e exporta o documento de regras em Markdown ou HTML.
4. **Gerar & Rever** — monta um prompt estruturado para o teu AI (OpenCode, ChatGPT, Gemini, Claude…) gerar um PWA dummy de ficheiro único que implementa as regras descritas; colas o HTML devolvido, revês, testas e registas cada versão aceite num histórico de "commits". (Dentro do Claude.ai também há geração direta pela API.)

## Aprender a usar

A ferramenta pode usar-se sem ajuda de ninguém:

- **▶ Tour** (botão no cabeçalho, também oferecido na primeira visita): percurso guiado de cerca de 4 minutos pelos quatro separadores, com destaque nos elementos e, se quiseres, o exemplo Tic Tac Toe carregado para acompanhar.
- **? Ajuda** (botão no cabeçalho): consulta rápida dentro da app, com começar em 5 minutos, os quatro tipos, como escrever um cartão, atalhos e problemas comuns.
- **[docs/GUIA.md](docs/GUIA.md)**: guia de consulta completo, com um exercício guiado de 10 minutos, referência de cada ecrã e botão, checklist para escrever cartões, como trabalhar com um AI, formato dos ficheiros e resolução de problemas.
- **[examples/capivaras.json](examples/capivaras.json)**: um projeto completo para importar.

## Taxonomia BGE

O *Bitnik Game Engine* (BGE) classifica toda a lógica de um jogo em quatro tipos de nó. Esta taxonomia é o vocabulário partilhado entre o separador "Fluxo" (nós no canvas) e o separador "Cartões de Lógica" (regras Gherkin) — cada cartão e cada nó tem sempre um destes quatro tipos.

### 🇵🇹 Português

| Tipo | Cor | Definição | Quando usar | Exemplo (Tic Tac Toe) |
|---|---|---|---|---|
| **DATA** | azul | Estado observável e condições do jogo — o que *existe* ou *é verdade* num dado momento (variáveis, contadores, posse de tokens). | Para representar informação que outras regras vão ler ou verificar (a cláusula "Dado que…" de um cartão). | "Tabuleiro 3x3 (9 casas)", "Jogador atual (X ou O)" |
| **FLOW** | verde | Sequência, turnos e fases — controla *quando* algo pode acontecer e em que ordem. | Para marcar transições de fase/ronda ou pontos de sincronização entre jogadores. | "Início de jogo", "Turno do jogador", "Fim de jogo" |
| **ACTION** | laranja | Jogadas do jogador (ou do sistema, ex. timeout) — o que é *feito*. | Para o gatilho de uma regra (a cláusula "Quando…" de um cartão). | "Jogador marca uma casa livre" |
| **SCORE** | dourado | Efeitos e pontuação — a *consequência* de uma ação validada. | Para o resultado de uma regra (a cláusula "Então…" de um cartão), normalmente com impacto na pontuação ou no estado. | "Verificar 3 em linha", "Verificar empate" |

**Regra prática:** um cartão Gherkin típico começa num `DATA` (a condição), é despoletado por uma `ACTION`, pode depender de um `FLOW` (a fase certa), e termina num efeito `SCORE`. Se uma regra não encaixa claramente num destes quatro tipos, é provável que devesse ser dividida em mais do que um cartão/nó.

### 🇬🇧 English

| Type | Color | Definition | When to use it | Example (Tic Tac Toe) |
|---|---|---|---|---|
| **DATA** | blue | Observable state and game conditions — what *exists* or *is true* at a given moment (variables, counters, token ownership). | To represent information other rules will read or check (a card's "Given…" clause). | "3x3 board (9 cells)", "Current player (X or O)" |
| **FLOW** | green | Sequence, turns and phases — controls *when* something is allowed to happen and in what order. | To mark phase/round transitions or synchronization points between players. | "Game start", "Player turn", "Game over" |
| **ACTION** | orange | Player (or system, e.g. timeout) moves — what gets *done*. | For a rule's trigger (a card's "When…" clause). | "Player marks a free cell" |
| **SCORE** | gold | Effects and scoring — the *consequence* of a validated action. | For a rule's outcome (a card's "Then…" clause), usually affecting score or state. | "Check 3 in a row", "Check draw" |

**Rule of thumb:** a typical Gherkin card starts from a `DATA` condition, is triggered by an `ACTION`, may depend on a `FLOW` gate (the right phase), and ends in a `SCORE` effect. If a rule doesn't clearly fit one of these four types, it's likely doing too much and should be split into more than one card/node.

## Uso

Abre [index.html](index.html) diretamente no browser. Não precisa de servidor nem de build step.

O passo "Gerar dummy PWA com LLM" faz `fetch` para `https://api.anthropic.com/v1/messages` **sem enviar `Authorization`**. Isto só funciona dentro do preview de artifacts do Claude.ai, que injeta a chave automaticamente do lado do proxy. Aberto como ficheiro local (`file://`) ou hospedado fora desse ambiente, o pedido falha (CORS/401) — os separadores "Fluxo" e "Cartões de Lógica" continuam a funcionar normalmente, apenas a geração por LLM depende desse ambiente.

## Modelo de dados

Todo o estado de um jogo vive num único objeto `state`:

```js
state = {
  gameName: string,
  nodes:   [{ id, kind, label, x, y }],       // kind ∈ DATA | FLOW | ACTION | SCORE
  edges:   [{ from, to }],                     // ids de nodes
  cards:   [{ id, kind, title, given, when, then, scope?, ref? }],  // scope: general | player | component | node
  rules:   [{ id, ref, title, text }],                              // secções do documento de regras
  commits: [{ id, ts, gameName, cardCount, nodeCount, code }]
}
```

- **Nós de fluxo** (`nodes`/`edges`): representam o diagrama arrastável no separador "Fluxo". `kind` determina a cor (`--data`, `--flow`, `--action`, `--score` no CSS).
- **Cartões** (`cards`): a regra de negócio em Gherkin, com um `kind` do mesmo taxonomy e um **âmbito** (`scope`): `general` (jogo inteiro), `player` (restrições do jogador), `component` (ligado a um nó DATA) ou `node` (ligado a um bloco do fluxo). Nos dois últimos, `ref` é o id do nó. Um cartão sem `scope` conta como geral.
- **Secções de regras** (`rules`): os parágrafos do documento de regras, pela ordem do documento. `ref` é o id do bloco a que o texto pertence (no máximo uma secção por bloco) ou `null` numa secção geral (Objetivo, Preparação…). Se um bloco for apagado, os cartões e as secções que apontavam para ele ficam guardados e marcados como "sem bloco" (e voltam a ligar-se se anulares).
- **Commits** (`commits`): snapshots imutáveis de código gerado e aceite — cada um guarda o HTML completo do PWA dummy gerado nessa versão, mais quantos nós/cartões existiam nessa altura.

## Persistência

Cada jogo é guardado como um **projeto nomeado** (fluxo + cartões + texto das regras + histórico de commits), através de um pequeno adaptador de storage (`storageAdapter` em [index.html](index.html):233-252):

- Dentro do preview do Claude.ai, usa `window.storage` (API persistente do artifact).
- Fora desse ambiente (ficheiro local aberto no browser), faz fallback automático para `localStorage`.

Chaves usadas:

| Chave | Conteúdo |
|---|---|
| `bitnik-rule-forge:projects:index` | lista `[{slug, name, updatedAt}]` de todos os projetos guardados |
| `bitnik-rule-forge:current-slug` | slug do último projeto aberto (para retomar ao recarregar) |
| `bitnik-rule-forge:project:<slug>` | `state` completo (JSON) desse projeto |

O `slug` é derivado do nome do jogo (`slugify`), com sufixo numérico em caso de colisão. Uma cópia de trabalho não guardada (`currentSlug === null`) não é autoguardada — "Guardar" pede logo um nome ("Guardar como…"). Para não haver perdas: um indicador junto aos botões mostra "Por guardar" / "Guardado", o browser avisa ao fechar a página com trabalho por guardar, e "Carregar", "Importar…" e o exemplo pedem confirmação antes de substituir trabalho por guardar.

## Interface

### 1 · Fluxo
Editor de nós com pan, zoom e minimapa. As boas práticas implementadas e a comparação com outras ferramentas estão em [docs/BENCHMARK.md](docs/BENCHMARK.md).

- **Criar nós:** `+ Nó` (tipo escolhido no dropdown) ou duplo clique no fundo. O nome pede-se logo (Enter aceita o nome por omissão).
- **Ligar:** arrasta a bolinha que aparece à direita de um nó para outro nó (fica verde se a ligação é válida, vermelha se não). Largar no vazio cria um nó novo já ligado. Também funciona "Ligar nós" (ou a tecla `L`) com dois cliques. Ligações duplicadas ou de um nó a si próprio são recusadas com uma mensagem.
- **Editar:** duplo clique ou `F2` renomeia; com um bloco selecionado, o **painel do bloco** (à direita do canvas) permite mudar o nome e o tipo, ver os cartões Gherkin desse bloco (e criar mais) e escrever o texto que ele ocupa no documento de regras. Clicar numa ligação seleciona-a; "Apagar ligação" (ou `Del`) remove-a sem afetar os nós.
- **Navegar:** arrastar o fundo move a vista, `Ctrl` + roda faz zoom, os botões `−`/`+`/`⤢` ajustam o zoom e a vista, e o minimapa permite saltar para outra zona.
- **Selecionar:** `Shift` + clique ou `Shift` + arrastar (caixa); `Ctrl+A` seleciona tudo. As ligações dos nós selecionados ficam realçadas.
- **Organizar:** o botão "Organizar" coloca os nós numa grelha sem ligações por trás de outros nós, com o fluxo de cima para baixo.
- **Anular / refazer:** `Ctrl+Z` / `Ctrl+Y` (100 passos, só para o fluxo). A grelha (12 px) pode ser desligada em "Grelha".
- **Teclado:** `Tab` navega entre nós, `Enter` seleciona (ou liga, no modo ligar), setas movem a seleção, `Del` apaga, `Esc` cancela. A lista completa está em "Atalhos e gestos", por baixo do canvas.

### 2 · Cartões de Lógica
Cartões estilo ficha, cada um com título, tag de `kind` e o triplo Gherkin (Dado/Quando/Então). Suporta criar, editar e apagar.

- **Âmbito:** geral, jogador, componente (nó DATA) ou bloco do fluxo. Os cartões aparecem agrupados por âmbito; os de componente e de bloco seguem a ordem do fluxo. Há filtros por âmbito e por bloco.
- **Cobertura:** por cima dos cartões, a app diz se todos os blocos têm pelo menos um cartão e, se não, lista os que faltam; clicar num deles abre o formulário já preenchido para esse bloco. Cartões cujo bloco foi apagado ficam marcados "bloco em falta".
- **Formulário:** escolher um bloco preenche o tipo do cartão com o do bloco. Um cartão de bloco ou de componente tem de ter bloco.

### 3 · Regras
O documento de regras: texto escrito por pessoas, para pessoas.

- Feito de **secções**: uma por bloco (o título é o nome do bloco, e o texto é o mesmo que se edita no painel do bloco) e secções gerais com título livre (Objetivo, Preparação, Dicas…).
- **Ordem:** `↑` `↓` mudam a posição de cada secção. Blocos novos entram pela ordem do fluxo; "Criar todas" cria as secções em falta de uma vez.
- **Formatação:** linha em branco separa parágrafos, "- " no início da linha faz uma lista, `**negrito**` e `*itálico*`.
- **Exportar:** "Exportar .md" (Markdown), "Exportar .html" (documento autónomo, que se imprime para PDF a partir do browser) e "Copiar Markdown". "Incluir cartões Gherkin" acrescenta os cartões de cada bloco a cada secção, mais um anexo com as regras gerais e as do jogador. Secções sem texto ficam de fora. Há também uma pré-visualização do documento.

### 4 · Gerar & Rever
- Resume o estado atual (nº de nós, ligações, cartões).
- "Copiar prompt para o teu AI" monta um prompt estruturado (`buildPrompt()`: papel, objetivo, taxonomia BGE, fluxo, texto das regras, cartões agrupados por âmbito e por bloco, requisitos e formato de saída) e copia-o para a área de transferência. Cola-o num AI à tua escolha (OpenCode, ChatGPT, Gemini, Claude…), que deve devolver um ficheiro HTML; cola o código na caixa abaixo (o markdown e o texto à volta são removidos automaticamente).
- "Gerar com API (só no Claude.ai)" faz o pedido diretamente à API da Anthropic; só funciona dentro do preview de artifacts do Claude.ai.
- O código é editável na textarea antes de aceitar.
- "Commit às regras" só fica disponível depois de marcar a checkbox "Revi o código gerado" — grava uma entrada no histórico de commits do projeto atual. Cada entrada tem **Ver código** (coloca-o na caixa, sem alterar o commit) e **Descarregar**.
- "Descarregar .html" exporta o código da textarea como ficheiro standalone.

## Exemplo incluído

O botão "Exemplo: Tic Tac Toe" (`loadTicTacToeExample()`) pré-carrega um fluxo de 9 nós, 12 cartões Gherkin (com âmbito e bloco) e 10 secções de regras do jogo do galo — um exemplo pequeno que usa os quatro tipos BGE e inclui o ciclo de turnos. Fica guardado como o projeto `tic-tac-toe-exemplo`; se já o tinhas com alterações tuas, a app avisa antes de o substituir pelo original.

## Importar / Exportar

Os botões **Importar…** e **Exportar** (barra de projetos) carregam e descarregam um projeto em `.json` — o fluxo, os cartões (com âmbito e bloco) e o texto das regras, sem o histórico de commits. Importar cria sempre um projeto novo (nunca sobrescreve um existente) e valida o ficheiro antes de o aceitar: tipos `DATA`/`FLOW`/`ACTION`/`SCORE`, ids `n1…`/`c1…` e ligações só entre nós que existem.

Formato do ficheiro:

```json
{
  "gameName": "Capivaras",
  "nodes": [{ "id": "n1", "kind": "FLOW", "label": "Iniciar jogo", "x": 10, "y": 10 }],
  "edges": [{ "from": "n1", "to": "n2" }],
  "cards": [{ "id": "c1", "kind": "FLOW", "scope": "node", "ref": "n1", "title": "…", "given": "que…", "when": "…", "then": "…" }],
  "rules": [{ "id": "r1", "ref": "n1", "title": "Iniciar jogo", "text": "Texto da secção…" },
            { "id": "r2", "ref": null, "title": "Objetivo", "text": "…" }]
}
```

### [examples/capivaras.json](examples/capivaras.json)

Modelo do jogo [Capivaras](https://capivaras.bitnik.games) (15 nós, 18 ligações, 24 cartões com âmbito e bloco, e 18 secções de texto das regras escritas a partir do `REGRAS.md`) escrito a partir da lógica implementada no `server.js` do repo `capivaras` (baralho de 36 cartas, apostas secretas, revelação simultânea, token do pássaro com roubo e empates, bónus de nenúfares, pontuação final, bots do modo solo, aposta automática e ligação caída). Descarrega-o e usa **Importar…**.

Onde `REGRAS.md` e o código divergem, o ficheiro segue o código — ver o cartão `c14`: no fim de cada ronda **todas** as cartas da mesa (ganhas ou não) vão para o descarte, por isso na segunda passagem pelo baralho voltam a entrar as 36.

## Stack

Vanilla JS, HTML, CSS — sem frameworks, sem dependências externas (à exceção de fontes do Google Fonts), seguindo os princípios de ficheiro único usados no resto do Bitnik Studio.

## Limitações conhecidas

- O botão "Gerar com API" depende do ambiente de preview do Claude.ai (ver secção "Uso"); não há campo para inserir uma chave de API própria. Fora dele, usa "Copiar prompt".
- Sem tratamento de erro granular no `fetch` de geração — falhas de rede ou respostas inesperadas caem todas na mesma mensagem genérica de erro.
- Sem testes automatizados no repositório (o editor foi verificado manualmente no browser).
- `localStorage` (fallback fora do Claude.ai) é por browser/dispositivo — não há sincronização entre máquinas; usa Exportar/Importar para partilhar projetos.
- Editor de fluxo: sem pinch-to-zoom no toque, undo/redo só para nós e ligações (não cartões), ligações sempre retas e sem rótulos, e o "Organizar" pode deixar ligações por trás de nós em grafos densos (ver [docs/BENCHMARK.md](docs/BENCHMARK.md)).

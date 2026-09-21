# Bitnik Rule Forge

Ferramenta de ficheiro único (HTML/CSS/JS, sem dependências, sem build) para o fluxo de design do [Bitnik Studio](https://github.com/dferreiramarques/bitnikgames-design-system).

Serve para transformar a lógica de um jogo em três passos:

1. **Fluxo** — desenha a lógica do jogo como nós `DATA` / `FLOW` / `ACTION` / `SCORE` (o taxonomy BGE), ligados entre si num canvas.
2. **Cartões de Lógica** — escreve as regras como cartões Gherkin (`Dado` / `Quando` / `Então`), cada um marcado com o tipo de nó a que corresponde.
3. **Gerar & Rever** — usa a API da Anthropic (chamada diretamente do browser) para gerar um PWA dummy de ficheiro único que implementa as regras descritas, permite rever/editar o código antes de aceitar, e regista cada versão aceite num histórico de "commits".

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
  cards:   [{ id, kind, title, given, when, then }],
  commits: [{ id, ts, gameName, cardCount, nodeCount, code }]
}
```

- **Nós de fluxo** (`nodes`/`edges`): representam o diagrama arrastável no separador "Fluxo". `kind` determina a cor (`--data`, `--flow`, `--action`, `--score` no CSS).
- **Cartões** (`cards`): a regra de negócio em Gherkin, associada a um `kind` do mesmo taxonomy.
- **Commits** (`commits`): snapshots imutáveis de código gerado e aceite — cada um guarda o HTML completo do PWA dummy gerado nessa versão, mais quantos nós/cartões existiam nessa altura.

## Persistência

Cada jogo é guardado como um **projeto nomeado** (fluxo + cartões + histórico de commits), através de um pequeno adaptador de storage (`storageAdapter` em [index.html](index.html):233-252):

- Dentro do preview do Claude.ai, usa `window.storage` (API persistente do artifact).
- Fora desse ambiente (ficheiro local aberto no browser), faz fallback automático para `localStorage`.

Chaves usadas:

| Chave | Conteúdo |
|---|---|
| `bitnik-rule-forge:projects:index` | lista `[{slug, name, updatedAt}]` de todos os projetos guardados |
| `bitnik-rule-forge:current-slug` | slug do último projeto aberto (para retomar ao recarregar) |
| `bitnik-rule-forge:project:<slug>` | `state` completo (JSON) desse projeto |

O `slug` é derivado do nome do jogo (`slugify`), com sufixo numérico em caso de colisão. Uma cópia de trabalho não guardada (`currentSlug === null`) não é autoguardada — "Guardar" pede logo um nome ("Guardar como…").

## Interface

### 1 · Fluxo
Canvas com grelha onde se adicionam nós (`+ Nó`, escolhendo o `kind` no dropdown), se arrastam livremente (mouse e touch), e se ligam com "Ligar nós" (clicar em dois nós cria uma aresta dirigida, desenhada em SVG com seta). Uma ligação existente pode ser selecionada clicando na própria linha (fica realçada) e removida com "Apagar ligação", sem afetar os nós em cada ponta.

### 2 · Cartões de Lógica
Grelha de cartões estilo ficha, cada um com título, tag de `kind` e o triplo Gherkin (Dado/Quando/Então). Suporta criar, editar e apagar.

### 3 · Gerar & Rever
- Resume o estado atual (nº de nós, ligações, cartões).
- "Gerar dummy PWA com LLM" monta um prompt (`buildPrompt()`) com o fluxo e os cartões, pede a Claude um único ficheiro HTML autocontido que implemente **apenas** as regras descritas, comentando qual nó/cartão cada bloco de lógica implementa.
- O código gerado é editável na textarea antes de aceitar.
- "Commit às regras" só fica disponível depois de marcar a checkbox "Revi o código gerado" — grava uma entrada no histórico de commits do projeto atual.
- "Descarregar .html" exporta o código da textarea como ficheiro standalone.

## Exemplo incluído

O botão "Exemplo: Tic Tac Toe" (`loadTicTacToeExample()`) pré-carrega um fluxo de 9 nós e 7 cartões Gherkin do jogo do galo — um exemplo pequeno que usa os quatro tipos BGE e inclui o ciclo de turnos. Fica guardado como o projeto `tic-tac-toe-exemplo`.

## Stack

Vanilla JS, HTML, CSS — sem frameworks, sem dependências externas (à exceção de fontes do Google Fonts), seguindo os princípios de ficheiro único usados no resto do Bitnik Studio.

## Limitações conhecidas

- A geração por LLM depende do ambiente de preview do Claude.ai (ver secção "Uso"); não há campo para inserir uma chave de API própria.
- Sem tratamento de erro granular no `fetch` de geração — falhas de rede ou respostas inesperadas caem todas na mesma mensagem genérica de erro.
- Sem testes automatizados.
- `localStorage` (fallback fora do Claude.ai) é por browser/dispositivo — não há sincronização entre máquinas nem exportação/importação de projetos além do download do HTML gerado num commit.
- Arrastar nós no canvas não tem alternativa por teclado (acessibilidade).

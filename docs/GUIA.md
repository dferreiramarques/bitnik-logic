# Guia do Rule Forge

Guia de consulta para usar o Rule Forge sem ajuda de ninguém. Se preferires aprender a usar a ferramenta, abre-a e carrega em **▶ Tour** (cerca de 4 minutos); dentro da app, **? Ajuda** tem um resumo deste guia.

**Índice:** [1. O que é](#1-o-que-é) · [2. Primeiros 10 minutos](#2-primeiros-10-minutos-exercício-guiado) · [3. Conceitos](#3-conceitos) · [4. Referência ecrã a ecrã](#4-referência-ecrã-a-ecrã) · [5. Atalhos](#5-atalhos-e-gestos) · [6. Escrever cartões e regras](#6-escrever-cartões-e-regras) · [7. Trabalhar com um AI](#7-trabalhar-com-um-ai) · [8. Guardar e partilhar](#8-guardar-e-partilhar) · [9. Problemas comuns](#9-problemas-comuns) · [10. Limites](#10-limites-conhecidos) · [11. Glossário](#11-glossário)

---

## 1. O que é

O Rule Forge ajuda a passar de "tenho uma ideia de regras" para "tenho um documento de regras e um protótipo jogável que as cumpre":

1. **Fluxo**: desenhas como o jogo funciona, com blocos ligados entre si.
2. **Cartões de Lógica**: escreves cada regra exata numa frase estruturada (Dado / Quando / Então).
3. **Regras**: explicas, por palavras tuas, como se joga, um texto por bloco, e exportas o documento.
4. **Gerar & Rever**: pedes a um AI um protótipo HTML que implemente essas regras, testas, corriges as regras se for preciso, e guardas a versão.

O fio que liga tudo é o **bloco** do fluxo: cada bloco pode ter **cartões Gherkin** (a regra exata) e um **texto nas regras** (o parágrafo que a explica).

Corre no browser, num só ficheiro, sem instalar nada e sem conta. Os dados ficam no teu browser (ver [secção 8](#8-guardar-e-partilhar)).

**Para quem:** designers de jogos que querem validar e documentar regras depressa, sem escrever código.

---

## 2. Primeiros 10 minutos (exercício guiado)

Vais carregar um projeto pronto, alterá-lo, escrever a regra nova e exportar o documento.

**Passo 1: abrir um projeto de exemplo.** Carrega em **Exemplo: Tic Tac Toe**. Aparece um fluxo de 9 blocos, 12 cartões e 10 secções de texto do jogo do galo.

**Passo 2: explorar o fluxo.** No separador **1 · Fluxo** arrasta um bloco, faz zoom com `Ctrl` + roda do rato, carrega em **Organizar** e depois `Ctrl`+`Z` para voltar atrás. Repara nas cores: cada tipo de bloco tem a sua (ver [secção 3](#3-conceitos)).

**Passo 3: abrir um bloco.** Clica em `Jogador marca uma casa livre`. À direita aparece o **painel do bloco**: o nome e o tipo, os **2 cartões Gherkin** deste bloco e o **Texto nas regras**, o parágrafo que este bloco ocupa no documento.

**Passo 4: acrescentar uma regra ao fluxo.** Quer-se registar quem ganha num placar:
1. Escolhe o tipo **SCORE** e carrega em **+ Nó**; escreve o nome `Atualizar placar` e `Enter`.
2. Arrasta a bolinha (à direita) do bloco `Verificar 3 em linha` até ao novo bloco.

**Passo 5: escrever a regra exata (cartão).** Com o novo bloco selecionado, carrega em **+ Cartão neste bloco** e preenche:

| Campo | Texto |
|---|---|
| Título | Atualizar placar |
| Âmbito / Bloco | *(já vêm preenchidos: Bloco, Atualizar placar)* |
| Tipo | SCORE |
| Dado que… | um jogador venceu com 3 em linha |
| Quando… | o jogo termina |
| Então… | o placar desse jogador aumenta em 1 e o placar fica visível no ecrã |

Carrega em **Guardar cartão**.

**Passo 6: explicar a regra às pessoas (texto).** Volta a **1 · Fluxo**, seleciona o bloco `Atualizar placar` e, em **Texto nas regras**, escreve, por exemplo: *Quando alguém ganha, o seu placar sobe um ponto. O placar de cada jogador está sempre visível.*

**Passo 7: ver e exportar o documento.** Abre **3 · Regras**. A secção `Atualizar placar` aparece na ordem do fluxo. Abre **Pré-visualizar o documento**, marca **Incluir cartões Gherkin** se quiseres as regras exatas no documento, e carrega em **Exportar .md** ou **Exportar .html**.

**Passo 8: gerar o protótipo.** Em **4 · Gerar & Rever** carrega em **Copiar prompt para o teu AI**, cola o prompt no AI que preferires (ver [secção 7](#7-trabalhar-com-um-ai)) e pede-lhe que responda com o ficheiro HTML. Cola o HTML na caixa grande, descarrega, abre no browser e joga até ao fim: o placar deve subir.

**Passo 9: guardar a versão.** Se estiver bem, marca **Revi o código gerado** e carrega em **Commit às regras**. Se não estiver bem, volta ao passo 5 e torna o cartão mais concreto.

> **Antes de sair:** o exemplo fica guardado no browser, mas qualquer projeto novo só fica gravado depois de carregares em **Guardar**.

---

## 3. Conceitos

### Os quatro tipos de bloco (taxonomia BGE)

Todos os blocos e cartões têm um destes tipos:

| Tipo | Cor | O que representa | Cláusula habitual do cartão | Exemplo (Tic Tac Toe) |
|---|---|---|---|---|
| **DATA** | azul | Estado e condições: o que existe ou é verdade | Dado | Tabuleiro 3x3, Jogador atual |
| **FLOW** | verde | Sequência, turnos e fases: quando algo pode acontecer | (condiciona o Quando) | Início de jogo, Turno do jogador |
| **ACTION** | laranja | Jogadas do jogador ou do sistema: o que se faz | Quando | Jogador marca uma casa livre |
| **SCORE** | dourado | Efeitos e pontuação: a consequência | Então | Verificar 3 em linha |

Uma regra típica começa numa condição **DATA**, é despoletada por uma **ACTION**, pode depender de um **FLOW** e acaba num efeito **SCORE**. Se uma regra não cabe em nenhum tipo, provavelmente devia ser dividida. Há uma versão bilingue com mais detalhe no [README](../README.md#taxonomia-bge).

### Bloco, cartão e texto: como se ligam

- O **bloco** (nó do fluxo) é a peça: "Jogador marca uma casa livre".
- O **cartão Gherkin** é a regra exata dessa peça: "Dado que a casa está vazia, quando o jogador clica, então fica marcada".
- O **texto nas regras** é a explicação para pessoas: "Escolhe uma casa vazia e clica nela para a marcar com o teu símbolo."

Um bloco pode ter vários cartões e um texto. Os cartões alimentam o protótipo; o texto alimenta o documento de regras (e também ajuda o AI a perceber a intenção).

### Âmbito dos cartões

Nem toda a regra pertence a um bloco. Cada cartão tem um **âmbito**:

| Âmbito | Para quê | Ligado a | Exemplo |
|---|---|---|---|
| **Geral** | Regras do jogo inteiro | nada | Jogam de 2 a 6 jogadores |
| **Jogador** | O que um jogador pode ou não pode fazer | nada | Ninguém vê as apostas dos outros até à revelação |
| **Componente** | Uma peça do jogo: baralho, tabuleiro, token | um bloco **DATA** | O baralho tem 36 cartas |
| **Bloco** | A regra de uma jogada, fase ou pontuação | um bloco do fluxo | Empate: ninguém ganha a carta |

### Documento de regras

O separador **Regras** junta o texto de todos os blocos, mais secções gerais (Objetivo, Preparação, Dicas…), num só documento que se exporta em Markdown ou HTML.

### Fluxo vs. cartões vs. texto

- O **fluxo** mostra a *estrutura*: que peças existem e em que ordem se ligam.
- Os **cartões** dizem a *regra exata*, com números e condições.
- O **texto** explica *como se joga*, para quem vai ler as regras.

O AI recebe os três. Um fluxo sem cartões dá um protótipo vazio; cartões sem fluxo dão um protótipo sem ordem; o texto ajuda a resolver dúvidas.

### Cartão Gherkin

Uma regra em três frases:

- **Dado que** a situação de partida
- **Quando** o que acontece (um só acontecimento)
- **Então** o resultado observável

### Commit

No Rule Forge, um *commit* é uma **versão do protótipo que revistaste e aceitaste**, guardada no projeto junto com o número de nós e cartões dessa altura. Não tem relação com o Git.

### Projeto

Um jogo com o seu fluxo, cartões, texto das regras e commits, guardado com um nome. Podes ter vários.

---

## 4. Referência ecrã a ecrã

### 4.1 Cabeçalho e projetos

| Elemento | O que faz |
|---|---|
| **▶ Tour** | Abre o tour guiado. |
| **? Ajuda** | Abre a ajuda de consulta (conceitos, atalhos, problemas comuns). |
| **Jogo** | Nome do jogo. Vai para o prompt, para o título do documento de regras e para os nomes dos ficheiros. |
| **Projetos guardados** + **Carregar** | Abre o projeto escolhido. Se tens trabalho por guardar, pede confirmação antes de o substituir. |
| **Guardar como…** | Guarda o trabalho atual como projeto novo, com o nome que escolheres. |
| **Guardar** | Na primeira vez pede um nome (como "Guardar como…"). Depois disso, o projeto guarda-se **automaticamente** a cada alteração. |
| **Apagar** | Apaga o projeto escolhido no seletor (ou o atual), depois de pedir confirmação. Não se pode desfazer. |
| **Exemplo: Tic Tac Toe** | Carrega o exemplo. Se já tens o exemplo guardado com alterações tuas, avisa antes de o substituir pelo original. |
| **Importar…** | Cria um projeto novo a partir de um ficheiro `.json` (ver [secção 8](#8-guardar-e-partilhar)). |
| **Exportar** | Descarrega o fluxo, os cartões e o texto das regras do projeto em `.json`. |
| Indicador | **● Por guardar** (a amarelo): tens trabalho que ainda não está gravado. **✓ Guardado automaticamente**: está tudo gravado. |

Se fechares a página com trabalho por guardar, o browser avisa.

### 4.2 Separador 1 · Fluxo

**Barra de ferramentas**

| Botão | O que faz |
|---|---|
| Seletor de tipo + **+ Nó** | Cria um bloco do tipo escolhido no centro da vista e pede o nome. |
| **Ligar nós** (`L`) | Modo em que dois cliques (origem, depois destino) criam uma ligação. |
| **↶ / ↷** | Anular / refazer (até 100 passos; só para blocos e ligações). |
| **Organizar** | Arruma os blocos em grelha, com o fluxo de cima para baixo e sem ligações por trás de blocos. Anula-se com `Ctrl`+`Z`. |
| **Grelha** | Alinha os blocos a uma grelha de 12 px ao arrastar. |
| **Apagar nó** / **Apagar ligação** | Apagam a seleção (`Del` faz o mesmo). Apagar um bloco apaga as suas ligações, mas **não** os cartões nem o texto: ficam guardados e marcados "sem bloco" (ver [problemas comuns](#9-problemas-comuns)). |

**Canvas**

| Gesto | O que faz |
|---|---|
| Duplo clique no fundo | Cria um bloco do tipo escolhido, onde clicaste. |
| Duplo clique num bloco (ou `F2`) | Renomeia. |
| Arrastar um bloco | Move-o (move todos os selecionados). |
| Arrastar a bolinha à direita de um bloco até outro | Cria uma ligação. O destino fica **verde** se é válido e **vermelho** se é recusado (ligação repetida ou de um bloco a si próprio). Largar no vazio cria um bloco novo já ligado. |
| Clicar numa ligação | Seleciona-a (fica realçada); `Del` apaga-a. |
| Arrastar o fundo | Move a vista. |
| `Ctrl` + roda do rato, ou botões **− / + / ⤢** | Zoom; ⤢ ajusta tudo à janela (`F`). |
| Minimapa (canto inferior direito) | Clicar ou arrastar leva-te a outra zona. |
| `Shift` + clique / `Shift` + arrastar no fundo | Seleção múltipla / seleção por caixa. |

**Painel do bloco** (à direita do canvas em janelas largas, por baixo dele em janelas estreitas)

Mostra o que estiver selecionado:

| Seleção | O que aparece |
|---|---|
| Nenhuma | Uma explicação do painel. |
| **Um bloco** | O tipo (etiqueta colorida), o **nome** e o **tipo** editáveis, quantas ligações entram e saem; os **cartões Gherkin** deste bloco (**Editar** abre o cartão, **+ Cartão neste bloco** cria um já ligado a ele); a caixa **Texto nas regras** (guarda-se sozinha); e **Ver no documento de regras**, que salta para a secção deste bloco. |
| Vários blocos | Uma mensagem a dizer quantos estão selecionados. |
| Uma ligação | De que bloco para que bloco vai, e **Apagar ligação**. |

Num bloco **DATA**, **+ Cartão neste bloco** propõe o âmbito *Componente*; nos outros, *Bloco*.

**Etiquetas nos blocos.** No canto de cada bloco do canvas aparecem duas etiquetas pequenas: **▤ N** (quantos cartões Gherkin tem) e **¶** (se tem texto nas regras). A tracejado, significa que falta. Um **⚠** vermelho diz que há cartões do bloco com o tipo ou o âmbito por acertar. Assim, o fluxo mostra de relance o que já está escrito nos cartões e nas regras.

**Verificações no painel.** Por baixo do nome do bloco, o painel avisa, com um botão de correção quando faz sentido:

| Aviso | Botão |
|---|---|
| O cartão «X» é ACTION mas o bloco é SCORE (o tipo do cartão difere do do bloco) | **Igualar ao bloco** |
| O cartão «X» é de Componente, mas o bloco já não é DATA | **Passar a Bloco** |
| Há cartões mas ainda não escreveste o texto nas regras deste bloco | **Escrever texto** |
| Há texto nas regras mas nenhum cartão Gherkin neste bloco | **+ Cartão** |

Os avisos são só avisos: podes ignorá-los (por exemplo, um bloco ACTION com um cartão de tipo SCORE pode ser de propósito).

### 4.3 Separador 2 · Cartões de Lógica

- **+ Cartão** abre o formulário: **Título**, **Tipo**, **Âmbito**, o bloco (se o âmbito o pedir), **Dado que…**, **Quando…**, **Então…**. O título e as três frases são obrigatórios (o tipo vem sempre preenchido).
- **Âmbito:** *Geral*, *Jogador*, *Componente* (lista só os blocos DATA) ou *Bloco* (lista todos, pela ordem do fluxo). Nos dois últimos é obrigatório escolher o bloco; ao escolhê-lo, o tipo do cartão passa a ser o do bloco (podes mudá-lo).
- **Filtros:** *Todos / Gerais / Jogador / Componentes / Por bloco* (com o número de cartões) e um seletor **Todos os blocos**. Se tens um filtro ativo, **+ Cartão** já abre com esse âmbito ou bloco.
- **Agrupamento:** os cartões aparecem em quatro grupos: *Regras gerais*, *Restrições do jogador*, *Componentes* e *Por bloco do fluxo*. Nos dois últimos, seguem a ordem do fluxo.
- **Cobertura:** a linha por cima diz se todos os blocos têm cartões. Se não, lista os que faltam; clicar num abre o formulário para esse bloco. Um aviso mostra os cartões cujo bloco já não existe (marcados "⚠ bloco em falta"), os que têm um tipo diferente do do bloco ("⚠ tipo ≠ bloco") e os de Componente cujo bloco já não é DATA.
- Cada cartão tem **Editar**, **Ver no fluxo** (seleciona o bloco) e **Apagar**. Apagar não tem anular. **Guardar cartão** grava; **Cancelar** fecha sem gravar.

### 4.4 Separador 3 · Regras

O documento de regras, feito de secções.

| Elemento | O que faz |
|---|---|
| **+ Secção geral** | Cria uma secção com título livre (Objetivo, Preparação, Dicas…). Entra no topo, depois das outras gerais que já estejam no topo. |
| Linha de progresso | "N de M blocos têm texto nas regras." |
| Têm cartões mas ainda não têm texto | Lista os blocos que já têm cartões Gherkin mas não têm texto nas regras. Clicar num cria a secção e põe o cursor nela. |
| Blocos sem secção | Lista, em botões, os blocos que ainda não têm secção. Clicar num cria-a e põe o cursor nela; **Criar todas (N)** cria uma secção vazia para cada um, pela ordem do fluxo. |
| Cada secção | O **título** (o nome do bloco, ou um campo editável nas gerais), a **caixa de texto**, **↑ ↓** (mudar a ordem), **Ir ao bloco**, **Apagar**, e em baixo o número de palavras e quantos cartões Gherkin o bloco tem (clicar mostra-os no separador Cartões). |
| Secção "bloco apagado" | Aparece a vermelho quando o bloco foi apagado. O texto não se perde: **Tornar geral** mantém-no como secção geral; se anulares o apagamento do bloco, volta a ligar-se. |
| **Incluir cartões Gherkin** | Faz com que a exportação acrescente, a cada secção de bloco, os cartões desse bloco, e no fim um anexo com as regras gerais e as do jogador. |
| **Copiar Markdown** / **Exportar .md** / **Exportar .html** | Exportam o documento (ver abaixo). |
| **Pré-visualizar o documento** | Mostra o documento como vai ficar, e atualiza-se enquanto escreves. |

**Escrever o texto.** Uma linha em branco separa parágrafos. Uma linha que começa por `- ` faz parte de uma lista. `**negrito**` e `*itálico*` aparecem formatados no HTML e na pré-visualização; no `.md` ficam como escreveste.

**O que se exporta.** Um título com o nome do jogo e depois, pela ordem que vês, cada secção que tenha texto (as vazias ficam de fora e a app diz quantas). Ficheiros: `nome-do-jogo-regras.md` e `nome-do-jogo-regras.html`. O HTML é um documento completo e autónomo; para ter um PDF, abre-o no browser e imprime.

**Guardar.** O texto guarda-se sozinho meio segundo depois de parares de escrever, e logo ao sair da caixa, ao mudar de separador ou ao fechar a página.

Se esvaziares por completo o texto de um bloco no painel do bloco, a sua secção (vazia) desaparece do documento; volta quando voltares a escrever.

### 4.5 Separador 4 · Gerar & Rever

| Elemento | O que faz |
|---|---|
| Resumo | Quantos blocos, ligações, cartões e secções de regras com texto tem o projeto, e avisa se há blocos sem cartões, blocos com cartões mas sem texto nas regras, cartões com o tipo diferente do bloco ou de Componente num bloco que já não é DATA. |
| **Copiar prompt para o teu AI** | Junta o fluxo, o texto das regras e os cartões num prompt e copia-o. Precisa de pelo menos um cartão. |
| **Ver prompt** | Mostra o prompt (também serve para o copiar à mão, se o botão não conseguir). |
| **Gerar com API (só no Claude.ai)** | Pede diretamente à API da Anthropic. Só funciona dentro do preview de artifacts do Claude.ai. |
| Caixa de código | Onde colas (ou vês) o HTML. Ao sair da caixa, o markdown e o texto à volta do HTML são removidos. |
| **Revi o código gerado** | Tem de estar marcado para poder fazer commit. |
| **Descarregar .html** | Descarrega o que estiver na caixa, para abrires no browser e jogares. |
| **Commit às regras** | Guarda a versão no histórico do projeto (precisa de código e da caixa "Revi" marcada). |
| Histórico de commits | Lista as versões. **Ver código** coloca o código dessa versão na caixa (sem alterar o commit); **Descarregar** descarrega-o. |

---

## 5. Atalhos e gestos

Só funcionam no separador **Fluxo** e quando não estás a escrever num campo de texto.

| Atalho | Ação |
|---|---|
| `Duplo clique` no fundo | Cria um bloco |
| `Duplo clique` / `F2` num bloco | Renomeia |
| Arrastar a bolinha do bloco | Liga a outro bloco |
| Arrastar o fundo | Move a vista |
| `Ctrl` + roda do rato | Zoom |
| `Shift` + clique / arrastar no fundo | Seleção múltipla / por caixa |
| `Setas` (`Shift` = passo maior) | Movem a seleção |
| `Del` | Apaga a seleção |
| `Ctrl`+`Z` / `Ctrl`+`Y` (ou `Ctrl`+`Shift`+`Z`) | Anular / refazer |
| `Ctrl`+`A` | Seleciona todos os blocos |
| `Esc` | Cancela o que estás a fazer / limpa a seleção |
| `L` | Liga/desliga o modo "Ligar nós" |
| `F` | Ajusta à janela |
| `+` `-` `0` | Zoom in / out / 100% |
| `Tab` / `Enter` | Navega entre blocos / seleciona (ou liga, no modo ligar) |

No tour: `←` e `→` navegam e `Esc` sai.

---

## 6. Escrever cartões e regras

### Os cartões (a regra exata)

A qualidade do protótipo depende quase toda dos cartões.

**Checklist**

- [ ] **Uma regra por cartão.** Se precisas de "e também…", são dois cartões.
- [ ] **Concreto.** Números, nomes e estados do jogo.
- [ ] **Um só "Quando".** Um acontecimento por cartão.
- [ ] **"Então" observável.** Algo que se veja ou meça no ecrã.
- [ ] **Tipo certo**, pelo efeito principal (condição → DATA, ordem → FLOW, jogada → ACTION, pontos → SCORE).
- [ ] **Âmbito certo** (ver abaixo).
- [ ] **Casos de erro e de fim** também têm cartão (jogada inválida, empate, fim de jogo).
- [ ] **Todos os blocos têm cartões.** A linha de cobertura diz-te os que faltam.

**Como escolher o âmbito**

| Pergunta | Âmbito |
|---|---|
| A regra vale para o jogo inteiro, sem estar numa jogada ou fase? | **Geral** |
| Diz o que um jogador pode ou não pode saber ou fazer? | **Jogador** |
| Descreve uma peça (baralho, tabuleiro, token) e o que ela contém? | **Componente** (liga ao bloco DATA) |
| Descreve o que acontece numa jogada, fase ou pontuação? | **Bloco** (liga ao bloco do fluxo) |

**Exemplos**

| Fraco | Melhor |
|---|---|
| Dado que o tabuleiro está quase cheio | Dado que as 9 casas estão preenchidas e ninguém venceu |
| Quando o jogador joga bem | Quando o jogador tem 3 símbolos iguais numa linha, coluna ou diagonal |
| Então acontece a pontuação | Então o placar desse jogador aumenta em 1 |
| Quando o jogador aposta e depois se revela | *(dois cartões: um para apostar, outro para revelar)* |

**Erros frequentes**

- Cartões que descrevem *interface* ("aparece um botão verde") em vez de regras: o AI gera um protótipo bonito e sem regras.
- Regras que só existem na tua cabeça: o AI não as pode adivinhar. Se o protótipo fizer algo estranho, quase sempre faltou um cartão.
- Contradições entre cartões (dois cartões que dão resultados diferentes para a mesma situação).

### O texto nas regras (a explicação)

O texto não é uma cópia do cartão. Escreve-o para alguém que nunca viu o jogo.

| Cartão (regra exata) | Texto (explicação) |
|---|---|
| Dado que a casa escolhida já está marcada, quando o jogador clica nessa casa, então a jogada é rejeitada e o turno continua a ser do mesmo jogador | Escolhe uma casa vazia e clica nela para a marcar com o teu símbolo. Não podes marcar uma casa ocupada: se tentares, a jogada é ignorada e continuas a ser tu a jogar. |

**Boas práticas**

- **Um parágrafo por bloco**, na voz do jogador ("Escolhes…", "Ganhas…").
- **Diz o porquê, não só o quê**, quando ajuda ("apostar sempre na carta mais valiosa nem sempre compensa: se for óbvia, outro jogador escolhe-a também").
- **Não te contradigas com os cartões.** Se o texto diz uma coisa e o cartão outra, o AI segue os cartões, mas quem lê o documento segue o texto.
- Usa **secções gerais** para o que não é de um bloco: tema, objetivo, preparação, dicas, glossário.
- Usa listas (`- `) para valores e tabelas simples (pontuações, composição do baralho).

---

## 7. Trabalhar com um AI

O Rule Forge não gera o protótipo sozinho: ajuda-te a pedi-lo bem a um AI à tua escolha (OpenCode, ChatGPT, Gemini, Claude…).

**O que o prompt contém.** Um papel, o objetivo, as definições dos quatro tipos BGE, o nome do jogo, os blocos e as ligações (com nomes, não só ids), o **texto das regras** (como apoio para perceber a intenção), os **cartões agrupados por âmbito** (gerais, jogador, componentes e por bloco, cada um com o bloco a que pertence), o **design system da bitnikgames**, os requisitos técnicos e o formato de saída. Vê-lo em **Ver prompt**.

**Design system.** O prompt pede que o protótipo use o [design system da bitnikgames](https://github.com/dferreiramarques/bitnikgames-design-system) (a documentação, com os tokens e os componentes, está no README desse repositório). Como um AI nem sempre consegue abrir links, e as pré-visualizações de chat costumam bloquear CSS externo, o prompt leva o próprio CSS do sistema (cerca de 10 mil caracteres) e diz ao AI para o colar num `<style>` e usar as suas classes: botões `.btn`, etiquetas `.badge`, e, para o jogo, `.chip` (uma cor por jogador), `.game-card`, `.badge-circle`, `.modal-overlay`/`.modal-box`, `.tabs` e `.toast`. O único recurso externo é o link das fontes Google (Baloo 2 e Inter); sem rede, o navegador usa a fonte do sistema. Se o texto das regras contradisser um cartão, o prompt diz ao AI que os cartões mandam.

**Como usar**

1. Confirma que os cartões estão como queres (a linha de cobertura nos Cartões e o resumo em Gerar dizem-te se falta algum bloco) e carrega em **Copiar prompt para o teu AI**.
2. Cola o prompt numa conversa nova do AI.
3. O prompt pede um **ficheiro HTML** (um *artifact* ou ficheiro, se o AI os tiver; senão, um bloco de código). Copia o código.
4. Cola-o na caixa de código do Rule Forge, descarrega, abre e joga.

**Como rever o protótipo**

1. Joga o cenário de **cada cartão**, um a um, e vê se o resultado é o do "Então".
2. Procura no código os comentários `// ASSUNÇÃO:`. Cada um marca uma dúvida que o AI resolveu sozinho; se a resolução não te serve, escreve um cartão que a defina.
3. Confirma que os comentários do código dizem a que bloco e cartão corresponde cada parte (`// ACTION n5 · c2: …`), para saberes onde mexer.
4. Só depois disso marca **Revi o código gerado** e faz commit.

**Se o resultado não serve:** melhora os cartões (não o código) e gera de novo. Editar o código à mão na caixa é possível, mas essa alteração não volta aos cartões.

---

## 8. Guardar e partilhar

**Onde ficam os dados.** No `localStorage` do browser onde trabalhas (ou na área persistente do Claude.ai, quando abres a ferramenta lá). Isto significa que:

- não há conta, nem sincronização entre dispositivos;
- outro browser, outro computador, o modo anónimo e a limpeza dos dados do site **não** veem (ou apagam) os teus projetos;
- por isso, **exporta** os projetos importantes.

**Trabalho por guardar.** Um projeto novo só é gravado depois de carregares em **Guardar** ou **Guardar como…**. O indicador junto aos botões mostra o estado; conta também o texto das regras.

**Exportar / Importar.** **Exportar** descarrega `nome-do-jogo.json` com o fluxo, os cartões e o texto das regras (o histórico de commits não vai no ficheiro). **Importar…** cria sempre um projeto **novo**, nunca sobrescreve um existente, e valida o ficheiro. Não confundas com **Exportar .md / .html** do separador Regras: esses exportam só o documento de leitura.

Formato do ficheiro:

```json
{
  "gameName": "Nome do jogo",
  "nodes": [{ "id": "n1", "kind": "FLOW", "label": "Iniciar jogo", "x": 10, "y": 10 }],
  "edges": [{ "from": "n1", "to": "n2" }],
  "cards": [{ "id": "c1", "kind": "FLOW", "scope": "node", "ref": "n1",
              "title": "Início do jogo", "given": "que…", "when": "…", "then": "…" }],
  "rules": [{ "id": "r1", "ref": "n1", "title": "Iniciar jogo", "text": "Texto da secção…" },
            { "id": "r2", "ref": null, "title": "Objetivo", "text": "…" }]
}
```

Regras:
- `kind` é `DATA`, `FLOW`, `ACTION` ou `SCORE`.
- Os ids são uma letra e um número: `n` (blocos), `c` (cartões), `r` (secções). Cada ligação tem de ligar dois blocos que existem.
- `scope` (opcional) é `general`, `player`, `component` ou `node`; `ref` (opcional) é o id de um bloco. Um cartão sem `scope` conta como geral.
- `rules` (opcional): `ref` é o id do bloco ou `null` (secção geral); no máximo uma secção por bloco; até 50 000 caracteres de texto por secção.
- Uma referência a um bloco que não está no ficheiro é aceite e aparece como "sem bloco" (assim um ficheiro exportado com blocos apagados volta a importar-se).
- Tamanho máximo do ficheiro: 1 MB. Os ficheiros antigos, sem `scope` nem `rules`, continuam válidos.

**Exemplos prontos:** [examples/capivaras.json](../examples/capivaras.json) é um modelo completo do jogo *Capivaras*: 15 blocos, 18 ligações, 24 cartões (com âmbito e bloco) e 18 secções de texto das regras, escritas a partir do `REGRAS.md`. Descarrega o ficheiro e importa-o; depois abre **3 · Regras** e exporta o documento.

**Copiar para outro browser.** Exporta no browser de origem e importa no de destino.

---

## 9. Problemas comuns

| Problema | O que fazer |
|---|---|
| **O meu trabalho desapareceu** | Trabalho que nunca guardaste não é gravado; usa **Guardar**. Modo anónimo, outro browser ou limpar dados do site apagam os projetos. Faz cópias com **Exportar**. |
| **"Gerar com API" dá erro** | Só funciona no Claude.ai. Fora dele usa **Copiar prompt para o teu AI**. |
| **O botão "Copiar prompt" não copia** | Alguns browsers bloqueiam a cópia automática. A caixa **Ver prompt** abre; seleciona e copia à mão. |
| **O AI devolveu texto à volta do código** | Cola na mesma: o texto antes de `<!DOCTYPE` e depois de `</html>` e o markdown são removidos. |
| **O código veio cortado a meio** | Pede ao AI "continua", ou usa um modelo com respostas mais longas. Projetos com muitos cartões e texto geram prompts e respostas longos. |
| **O protótipo não tem o aspeto da bitnikgames** | Confirma no código que o CSS do prompt foi colado num `<style>` no `<head>` e que os botões, cartas e etiquetas usam classes como `.btn`, `.game-card` e `.badge`. Se não, diz ao AI: "usa o design system do prompt, sem inventar cores nem fontes". O prompt fica longo (o CSS tem cerca de 10 mil caracteres): se o AI o cortar, usa um modelo com mais contexto. |
| **O protótipo não cumpre uma regra** | Torna o cartão mais concreto (números, estados) e gera de novo. Procura os `// ASSUNÇÃO`. |
| **O protótipo tem regras que eu não escrevi** | O AI preencheu lacunas. Adiciona cartões que as definam, ou que as proíbam. |
| **Um bloco aparece como "sem cartões"** | A regra desse bloco não está escrita. Clica no bloco na linha de cobertura dos Cartões e cria o cartão. |
| **Apaguei um bloco: e os cartões e o texto?** | Não se perdem: os cartões ficam "⚠ bloco em falta" e a secção fica "bloco apagado". Se anulares (`Ctrl`+`Z`) voltam a ligar-se. Também os podes ligar a outro bloco (editando o cartão) ou tornar a secção geral. |
| **Vejo ⚠ num bloco ou num cartão** | É um aviso de coerência: o tipo do cartão difere do do bloco, um cartão de Componente está num bloco que já não é DATA, ou falta o texto ou os cartões. Abre o bloco: o painel diz o que é e propõe a correção. |
| **Não consigo guardar um cartão** | Um cartão de âmbito Bloco ou Componente precisa de um bloco escolhido; o Componente só aceita blocos DATA. |
| **A secção de um bloco desapareceu do documento** | Se esvaziaste o texto no painel do bloco, a secção vazia é removida. Volta a criá-la nos botões "Blocos sem secção" ou escrevendo de novo no painel. |
| **O documento exportado não tem uma secção** | As secções sem texto ficam de fora. A app diz quantas. |
| **O que escrevi em `**negrito**` aparece com asteriscos** | No `.md` ficam como escreveste (o Markdown formata-os). No `.html` e na pré-visualização aparecem em negrito. |
| **Não consigo ligar dois blocos** | Ligações repetidas e de um bloco a si próprio são recusadas; a razão aparece por cima do canvas. |
| **"Importar" falha** | A mensagem junto aos botões diz o problema (ver formato na [secção 8](#8-guardar-e-partilhar)). |
| **"Organizar" deixou ligações por trás de blocos** | Acontece em grafos densos. Arrasta os blocos, ou `Ctrl`+`Z` para anular. |
| **Apaguei um cartão sem querer** | Os cartões não têm "anular". Se tens uma exportação, importa-a como projeto novo. |
| **O exemplo não volta ao original** | Carrega em **Exemplo: Tic Tac Toe** e confirma a substituição. |
| **O tour ou a ajuda deixaram de aparecer** | Usa **▶ Tour** e **? Ajuda** no cabeçalho. O aviso de primeira visita só aparece uma vez por browser. |

---

## 10. Limites conhecidos

- O botão da API só funciona no Claude.ai; fora dele usa "Copiar prompt".
- Sem contas nem sincronização: os dados ficam no browser.
- Anular/refazer cobre só blocos e ligações, não os cartões nem o texto das regras (as caixas de texto têm o anular normal do browser enquanto escreves).
- Um bloco tem no máximo uma secção de texto nas regras.
- O documento exportado é texto: não tem imagens nem tabelas complexas (as listas e o negrito funcionam).
- Sem *pinch-to-zoom* no toque (há botões e `Ctrl` + roda); o minimapa fica escondido em ecrãs estreitos.
- As ligações são retas e não têm rótulos. Em grafos muito densos (dezenas de blocos), **Organizar** pode deixar ligações por trás de blocos.
- Não há testes automatizados no repositório; a ferramenta foi verificada manualmente no browser.

Mais detalhes técnicos e a comparação com outras ferramentas: [BENCHMARK.md](BENCHMARK.md).

---

## 11. Glossário

| Termo | Significado |
|---|---|
| **BGE** | Bitnik Game Engine. A taxonomia de quatro tipos (DATA, FLOW, ACTION, SCORE) usada para classificar a lógica de um jogo. |
| **Bloco / nó** | Uma peça do fluxo (um estado, uma fase, uma jogada ou um efeito). |
| **Ligação** | Uma seta de um bloco para outro, a indicar que o segundo vem depois do primeiro. |
| **Cartão** | Uma regra exata escrita em Dado / Quando / Então. |
| **Âmbito** | A que é que um cartão diz respeito: ao jogo inteiro (geral), ao jogador, a um componente ou a um bloco. |
| **Secção** | Um parágrafo do documento de regras: uma por bloco, ou geral (Objetivo, Preparação…). |
| **Documento de regras** | O texto que explica como se joga, feito das secções por ordem, exportável em .md ou .html. |
| **Gherkin** | O formato de escrita de regras "Dado / Quando / Então", vindo do teste de software. |
| **Prompt** | O texto que se envia a um AI a pedir-lhe o protótipo. |
| **Protótipo (dummy)** | Um jogo mínimo, sem arte, feito só para validar as regras. |
| **Commit** | Uma versão do protótipo que revistaste e aceitaste, guardada no projeto. |
| **Artifact** | Um ficheiro (aqui, HTML) que alguns AIs criam ao lado da conversa, em vez de o escreverem como texto. |
| **Design system** | O conjunto de tokens e componentes CSS da bitnikgames ([repositório](https://github.com/dferreiramarques/bitnikgames-design-system)) que os protótipos gerados usam. |
| **Snap / grelha** | Alinhar os blocos, ao arrastar, a uma grelha invisível. |

# Benchmark: editores de diagramas e nós

Data dos dados: 2026-09-21. Objetivo: decidir se o editor de fluxo do Rule Forge devia ser trocado por uma biblioteca, e que boas práticas de UX copiar.

**Restrições do projeto** (ver [README](../README.md)): ficheiro único em HTML/CSS/JS, sem dependências e sem build; publicado em GitHub Pages; grafos pequenos (10–50 nós) com quatro tipos de nó.

## 1. Dados objetivos

Recolhidos a 2026-09-21 pela API do GitHub e pelo registo npm.

| Ferramenta | Licença | Último push (GitHub) | Última versão npm | Tamanho npm (descompactado) | Requer |
|---|---|---|---|---|---|
| [React Flow](https://github.com/xyflow/xyflow) (`@xyflow/react`) | MIT | 2026-09-17 | 12.11.6 (2026-09-01) | 1,2 MB | React |
| Svelte Flow (`@xyflow/svelte`) | MIT | (mesmo repo) | 1.6.6 (2026-09-01) | 325 KB | Svelte |
| [JointJS](https://github.com/clientIO/joint) (`@joint/core`) | MPL-2.0 | 2026-09-17 | 4.3.3 (2026-09-04) | 6,1 MB | nada (JS puro) |
| [Rete.js](https://github.com/retejs/rete) | MIT | 2026-09-13 | 2.0.6 (2025-06-30) | 221 KB | plugin de render (React, Vue, Angular, Svelte…) |
| [Cytoscape.js](https://github.com/cytoscape/cytoscape.js) | MIT | 2026-09-16 | 3.34.3 (2026-09-07) | 5,6 MB | nada |
| [Drawflow](https://github.com/jerosoler/Drawflow) | MIT | 2024-10-19 | 0.0.60 (2024-09-03) | 64 KB | nada (JS puro) |
| [LiteGraph.js](https://github.com/jagenjo/litegraph.js) | MIT | 2024-08-01 (fork da Comfy-Org: 2026-01-14) | 0.7.18 (2024-01-08) | 3,1 MB | nada |
| GoJS | comercial (`license.html`) | n/d | 4.0.4 (2026-09-14) | 8,0 MB | nada |
| tldraw | licença própria (`LICENSE.md`) | 2026-09-21 | 5.4.2 (2026-09-10) | 14,5 MB | React |
| [dagre](https://github.com/dagrejs/dagre) (só auto-layout) | MIT | 2026-08-08 | 3.1.1 (2026-08-08) | 1,4 MB | nada |
| [elkjs](https://github.com/kieler/elkjs) (só auto-layout) | EPL-2.0 ou GPL-3.0 | 2026-09-17 | 0.12.0 (2026-07-17) | 7,9 MB | nada |

Notas: as estrelas do GitHub (React Flow 38 mil, tldraw 50 mil, Rete 12 mil, Cytoscape 11 mil, Drawflow 6 mil, JointJS 5 mil) só indicam popularidade. Para uma ferramenta pública, licenças "próprias" (tldraw, GoJS) pedem leitura antes de usar.

## 2. O que cada uma traz de origem

Só está marcado o que foi confirmado numa fonte. "n/v" = não verificado.

| | Minimapa | Undo/redo | Auto-layout | Snap à grelha | Seleção por caixa | Teclado | Validação de ligações |
|---|---|---|---|---|---|---|---|
| React Flow | sim | não (só exemplo) | não (exemplos com dagre/elkjs) | sim | sim | sim | sim |
| JointJS (grátis) | não | não | não | n/v | n/v | n/v | n/v |
| JointJS+ (pago) | sim | sim | sim | n/v | n/v | n/v | n/v |
| Drawflow | não indicado | não indicado | não indicado | n/v | n/v | n/v | n/v |
| GoJS | sim | n/v | sim | n/v | n/v | n/v | n/v |
| Rete, LiteGraph, Cytoscape, tldraw | n/v | n/v | n/v | n/v | n/v | n/v | n/v |

Fontes: [documentação do React Flow](https://reactflow.dev/learn) (confirma minimapa, snap, seleção por caixa, teclado, validação; undo/redo e auto-layout só como exemplos), [artigo da JointJS](https://www.jointjs.com/blog/javascript-diagramming-libraries) (fonte de um concorrente, por isso com viés; descreve JointJS grátis vs JointJS+, React Flow e GoJS) e o [README do Drawflow](https://github.com/jerosoler/Drawflow) (zoom, ligar por arrasto, reroute, JSON, toque; não menciona undo nem minimapa).

## 3. Boas práticas de UX encontradas

Resumo de guias e bibliotecas de editores de nós ([xyflow/awesome-node-based-uis](https://github.com/xyflow/awesome-node-based-uis), [Foblex Flow](https://flow.foblex.com/docs/angular-node-based-ui-library), [JointJS](https://www.jointjs.com/blog/javascript-diagramming-libraries)):

1. Ligar arrastando a partir de uma "pega" do nó, com validação (ligações inválidas recusadas e visíveis como tal).
2. Pan, zoom, ajustar à janela e minimapa.
3. Snap à grelha, guias de alinhamento e auto-layout.
4. Undo/redo que restaura posições **e** ligações.
5. Nós simples, com o detalhe num painel lateral.
6. Teclado: mover, apagar e ligar sem rato; semântica ARIA.
7. Seleção múltipla e por caixa.

## 4. Decisão

**Manter o editor próprio.** Razões:

- As bibliotecas mais completas exigem React ou uma licença paga, e quebram a regra "ficheiro único, sem dependências".
- As que funcionam em JS puro (JointJS, Drawflow) ou não trazem o que faltava (undo/redo, auto-layout, minimapa no JointJS grátis; Drawflow não os menciona) ou têm o desenvolvimento parado (Drawflow, LiteGraph original).
- Nenhuma das opções principais oferece undo/redo ou auto-layout de origem (React Flow apenas em exemplos), por isso teríamos de os escrever na mesma.

## 5. O que o Rule Forge tinha e o que tem agora

| Prática | Antes | Agora |
|---|---|---|
| Ligar arrastando de uma pega | não (dois cliques em "Ligar nós") | sim; largar num nó liga, largar no vazio cria um nó ligado; nó destino fica verde (válido) ou vermelho (inválido) |
| Validação de ligações | permitia duplicadas | recusa duplicadas e ligações a si próprio, com mensagem |
| Renomear nós | não (nome fixo "DATA 3") | duplo clique, F2 ou painel de detalhe (nome e tipo) |
| Criar nós | botão, posição aleatória | botão (centro da vista) ou duplo clique no fundo |
| Pan / zoom / ajustar | não (canvas fixo) | arrastar o fundo; Ctrl+roda ou botões; ajustar à janela (F) |
| Minimapa | não | sim (clicar ou arrastar navega) |
| Snap à grelha | não | sim (12 px, desligável) |
| Undo / redo | não | sim, 100 passos, Ctrl+Z / Ctrl+Y (nós e ligações) |
| Auto-layout | não | botão "Organizar" |
| Seleção múltipla | não | Shift+clique, Shift+arrastar (caixa), Ctrl+A; mover e apagar em grupo |
| Teclado | não | Tab entre nós, Enter/Espaço seleciona, setas movem, Del apaga, Esc cancela, L liga |
| Acessibilidade | nenhuma | nós focáveis com `role="button"` e `aria-label`, região `aria-live` para mensagens, foco preservado ao redesenhar |
| Contexto visual | todas as ligações iguais | as ligações do nó selecionado ficam realçadas e as restantes esbatidas |
| Gestos de toque | rato e toque separados | Pointer Events (rato, toque e caneta) |

### Auto-layout: como funciona e limites medidos

Um layout em camadas clássico (estilo dagre) deixou 7 das 18 ligações do exemplo Capivaras a passar por trás de outros nós, porque as nossas ligações são retas. O "Organizar" procura, por recozimento simulado numa grelha, uma colocação que evite ligações atrás de nós, cruzamentos e fluxo para cima, com semente fixa (mesmo grafo, mesmo resultado).

Medido com o código final (Chrome, sem sobreposição de nós em nenhum caso):

| Grafo | Nós / ligações | Tempo | Ligações a passar por trás de nós |
|---|---|---|---|
| Tic Tac Toe (exemplo) | 9 / 11 | ~0,2 s | 0 |
| Capivaras (exemplo) | 15 / 18 | ~0,5 s | 0 |
| Aleatório | 20 / 26 | ~0,7 s | 0 |
| Aleatório | 30 / 42 | ~0,6 s | 0 |
| Aleatório denso | 50 / 68 | ~0,9 s | 13 (a app avisa e sugere ajuste manual) |

Redesenhar as ligações durante um arrasto com 50 nós custa cerca de 14 ms por movimento.

### Limites conhecidos

- Não há pinch-to-zoom no toque (há botões e Ctrl+roda); o minimapa fica escondido em ecrãs estreitos (< 640 px).
- Undo/redo cobre só o fluxo (nós e ligações), não os cartões.
- Os grafos densos (dezenas de nós) podem ficar com ligações por trás de nós após "Organizar".
- Ligações são sempre retas; não há rótulos nas ligações.
- Este benchmark não testou as bibliotecas na prática: usa dados de repositórios, npm e documentação. As células "n/v" ficaram por verificar.

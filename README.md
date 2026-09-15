# Bitnik Rule Forge

Ferramenta de ficheiro único (HTML/CSS/JS, sem dependências) para o fluxo de design do [Bitnik Studio](https://github.com/dferreiramarques/bitnikgames-design-system):

1. **Fluxo** — desenha a lógica do jogo como nós DATA / FLOW / ACTION / SCORE (o taxonomy BGE), ligados entre si.
2. **Cartões de Lógica** — escreve as regras como cartões Gherkin (Dado/Quando/Então), cada um marcado com o tipo de nó a que corresponde.
3. **Gerar & Rever** — usa a API da Anthropic (chamada diretamente do browser, sem chave necessária no ambiente do Claude.ai) para gerar um PWA dummy que implementa as regras descritas, revê o código gerado, e faz commit da versão validada.

## Guardar / carregar jogos

Cada jogo fica guardado como um projeto nomeado (fluxo + cartões + histórico de commits). Dentro do preview do Claude.ai isto usa a API de armazenamento persistente do artifact; aberto como ficheiro local no browser, faz fallback automático para `localStorage` do próprio browser.

## Exemplo incluído

Um botão "Exemplo: Capivaras" pré-carrega um fluxo e cartões simplificados inspirados no jogo de apostas [Capivaras](../capivaras) — ilustrativo, não é a réplica exata das regras em produção.

## Uso

Abre `index.html` diretamente no browser. Não precisa de servidor nem de build step.

## Stack

Vanilla JS, HTML, CSS — sem frameworks, seguindo os princípios de ficheiro único usados no resto do Bitnik Studio.

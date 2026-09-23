# CLAUDE.md — Landing Page Xis do Gaúcho Tchê (food truck em SC)

Este arquivo é o contrato de trabalho entre mim (dev/PO do projeto) e você (Claude Code) para este repositório. Leia por inteiro antes de escrever qualquer linha de código. Se algo aqui e uma instrução minha num prompt específico conflitarem, a instrução mais recente no chat vence — mas avise que houve conflito antes de agir.

## 1. Contexto do projeto

Site institucional (landing page, one-page) do **Xis do Gaúcho Tchê**: um food truck de gaúchos que vende xis calota no estado de Santa Catarina. O site é feito para os donos, que já aprovaram o design no Figma. Este NÃO é um projeto de estudo/fictício — é para entregar de verdade. Já demorou; a prioridade agora é converter o Figma em código funcional, fiel ao design aprovado, sem inventar seções novas.

Eu vou te dar as instruções **na ordem em que o site é lido de cima para baixo**: Navbar primeiro, depois Hero, Cardápios, Contatos e por último Horários e Localização (o site **não tem footer**). Cada seção vem com cores em hexadecimal e dimensões em pixels tiradas direto do Figma. Não avance para a próxima seção até eu confirmar que a atual está aprovada.

## 2. Regra de ouro: MOBILE FIRST

Já cometi o erro de projetar desktop-first num projeto anterior. Aqui a ordem é invertida e não é negociável:

1. Todo CSS parte do layout **mobile** (viewport ~360–430px) como base, sem media query.
2. Breakpoints usam `min-width` para escalar PARA CIMA (tablet, depois desktop) — nunca `max-width` para encolher.
   - No Tailwind isso já é o padrão: classe sem prefixo = mobile; `sm:`/`md:`/`lg:` = min-width. Não usar variantes `max-*`.
3. Ao implementar qualquer seção, construa e valide mobile primeiro, só depois adicione os breakpoints maiores.
4. Se eu mandar uma medida em pixels sem especificar o breakpoint, assuma que é a medida mobile, a menos que eu diga "no desktop" explicitamente.
5. **O Figma só tem a versão desktop.** Quando eu passar só as medidas desktop, os valores mobile ficam a seu critério (bom senso, proporcionais e legíveis em 360–430px) — não precisa perguntar, mas liste no fim da entrega quais valores mobile você escolheu.

## 3. Stack e estrutura de arquivos

HTML + **Tailwind CSS** + `style.css` próprio + JS vanilla. Sem framework JS. O `index.html` deve abrir direto no navegador e funcionar.

- **Tailwind:** usar sempre que der (classes utilitárias no HTML).
- **`style.css`:** arquivo separado, para o que o Tailwind não cobre bem (ex: `background-image`, `@font-face`/fontes, ajustes finos).
- Carregamento do Tailwind: **CLI standalone** (`tailwindcss.exe`, v4, executável único, sem npm/Node). Ele gera o `tailwind.css` estático e minificado, só com as classes usadas no `index.html`. O CDN não é mais usado.
- ⚠️ **Toda vez que mudar/adicionar classe Tailwind no `index.html`, regerar o CSS** — senão a classe nova simplesmente não funciona:
  ```
  ./tailwindcss.exe -i tailwind.input.css -o tailwind.css --minify
  ```
  (em edições longas dá pra deixar rodando com `--watch` no lugar de `--minify`, e minificar no final)
- O `tailwind.input.css` usa `source(none)` + `@source "./index.html"` para ler só o HTML (senão o Tailwind varre a pasta e gera classes citadas neste CLAUDE.md).
- O uso de Tailwind como framework CSS está **liberado explicitamente** pelo dev — não tratar como violação de "sem framework". Framework JS continua fora (JS segue vanilla).

```
/ (raiz do repo)
├── index.html          # única página HTML de toda a aplicação
├── style.css           # CSS próprio, separado (decidido)
├── tailwind.css        # GERADO pelo CLI — não editar à mão
│                       # JS: DENTRO do index.html, num <script> no fim do <body> (decidido — sem script.js)
├── tailwind.input.css  # entrada do CLI (só pra build — não precisa subir pra hospedagem)
├── tailwindcss.exe     # CLI standalone (~110MB, só pra build — NÃO subir pra hospedagem)
└── /imagens            # todas as imagens, sem subpastas
    ├── Green background 1.png                    # ÚNICO background do site (Hero)
    ├── Polar 1.png / Logo do ifood.png           # Navbar
    ├── XDeCimaEsquerda.png / XDeBaixoDireita.png # Hero (PNG com sombra embutida)
    ├── CardapioLanche.jpeg / CardapioBebida.jpeg # Cardápios
    ├── LogoIfoodPreta.png / WhatsappLogoColorido.png / Polar 2.png # Contatos
    └── Horários.png / Local.png                  # Horários e Localização
```

**Para publicar o site**, basta subir: `index.html`, `style.css`, `tailwind.css` e a pasta `imagens/`.

Regras de estrutura:
- **Uma única página HTML** (`index.html`). Não crie páginas adicionais nem rotas.
- Todas as imagens ficam em `/imagens`, **sem subpastas**.
- Existe **um único background** (`imagens/Green background 1.png`), aplicado via `background-image` no CSS, não `<img>` — é assim que o dono pediu. Todo o resto do visual (cores, listras, blocos) é feito com CSS/Tailwind.
- Imagens de produto (cardápio, lanches) que precisam de `alt` semântico/SEO podem ser `<img>` normais dentro de `/imagens`.
- Dependências externas aprovadas: **Tailwind CSS** e **Google Fonts** (ver seção 6). Nenhuma outra sem aprovação.

## 4. Seções do site (ordem de construção)

Vou detalhar cada uma em prompts separados, com cor e medida exatas do Figma. Abaixo está o resumo do que esperar — não implemente pixels ou cores "no chute"; se eu não tiver passado ainda, pergunte, não invente.

### 4.1 Navbar
- 3 listras horizontais remetendo à bandeira do Rio Grande do Sul (cores exatas virão por mim).
- Nome do estabelecimento centralizado: **Xis do Gaúcho Tchê**.
- Foto pequena da cerveja "Polar" (ícone/logo, canto esquerdo) — `imagens/Polar 1.png`.
- Comportamento mobile: definir se vira menu hamburguer ou fica fixa e simples — vou confirmar quando chegarmos nela.

### 4.2 Hero
- Contém um pouco da história do food truck + fotos dos lanches/ambiente.
- Background é `imagens/Green background 1.png`.
- Mobile: texto e imagem empilham verticalmente; garantir contraste de texto legível sobre a foto de fundo (usar overlay escuro semi-transparente se necessário — perguntar antes de aplicar).

### 4.3 Cardápios
- Desktop: duas fotos lado a lado.
- Mobile: as mesmas fotos viram **carrossel** (swipe/touch, com ou sem setas — a definir).
- Sem lib externa de carrossel a menos que eu aprove uma; prefira vanilla JS simples (scroll-snap é uma opção válida a sugerir).

### 4.4 Contatos
- Ícone clicável do iFood → link do iFood deles (**ainda pendente** — aparece 2x: navbar e contatos, ambos `href="#"` com `<!-- TODO -->`).
- Ícone clicável do WhatsApp → `https://wa.me/5547991305568` (+55 (47) 99130-5568, confirmado).
- Não invente números/URLs; se faltar, `<!-- TODO: link real -->`.

### 4.5 Horários e Localização
- Quadro de horários (imagem) + mapa estático clicável.
- Endereço atual (eles se mudaram): **Rua Bahia, 3144 — ao lado do Super Ferreira**.
- Link do mapa (confirmado pelo dev): `https://www.google.com/maps/search/?api=1&query=Rua+Bahia%2C+3144%2C+Blumenau+-+SC`.

### ~~4.6 Footer~~
- **Não existe** — foi um erro no planejamento inicial. Não criar footer.

## 5. Fluxo de trabalho (loop-engineering)

Isto é o que rege como trabalhamos juntos sessão a sessão:

1. Eu descrevo UMA seção por vez (cores hex, larguras/alturas em px, comportamento esperado mobile e desktop).
2. Você implementa **só essa seção**, mobile-first, dentro do `index.html`/`style.css` existente — sem tocar em seções já aprovadas, a menos que eu peça explicitamente.
3. Eu confiro visualmente (abrindo no navegador) e te dou o próximo passo: aprovação → próxima seção, ou correção → ajuste na mesma seção.
4. Nunca pule para uma seção mais à frente na lista da seção 4 antes de eu confirmar a atual.
5. Nunca adicione seções, componentes ou "melhorias" que eu não pedi (ex: newsletter, botão de voltar ao topo, animações extras) sem perguntar primeiro.
6. Se uma instrução minha for ambígua (cor não especificada, comportamento de hover não definido), pergunte antes de assumir — não adivinhe estética.

## 6. Convenções de código

- HTML semântico (`<nav>`, `<header>`, `<section>`, `<footer>`, etc.).
- **Tailwind primeiro:** se dá para fazer com classe utilitária, faça no HTML. Cores e medidas exatas do Figma via valores arbitrários (ex: `bg-[#1A7A3A]`, `h-[12px]`) ou via tokens do tema — nunca aproximar para a paleta padrão do Tailwind.
- **`style.css`:** nomes de classe em kebab-case, organizados por seção (comentário `/* ===== NAVBAR ===== */` etc. separando cada bloco).
- Cores: sempre usar as hex exatas que eu passar — não arredondar nem "aproximar" para uma cor de paleta padrão.
- Evitar `!important` — se for necessário, comentar o motivo.
- JS vanilla, sem jQuery, sem frameworks.
- Comentar blocos de JS não triviais (ex: lógica do carrossel).

### Tipografia (Google Fonts)
- **Títulos:** Holtwood One SC — peso regular (400).
- **Textos:** Inter — semi-bold (600).

## 7. Fora de escopo (não fazer sem aprovação explícita)

- Checkout, carrinho de compras, ou qualquer forma de pagamento no site.
- Sistema de login/conta.
- CMS ou painel administrativo.
- Páginas adicionais além do `index.html`.
- Animações complexas (parallax, scroll-reveal) não pedidas.
- Qualquer biblioteca/framework externo além de Tailwind e Google Fonts sem eu aprovar antes.

## 8. Padrão para casos não especificados

Quando algo não estiver coberto explicitamente aqui ou no meu prompt da seção em andamento, seguir boas práticas convencionais (HTML semântico, Tailwind/CSS responsivo mobile-first, JS vanilla limpo), priorizando simplicidade e fidelidade ao Figma já aprovado pelos donos — não criatividade extra.

## 9. Contexto do meu tempo hoje

Estou codando isso durante uma janela livre na faculdade (trabalho de TCC em equipe, minha parte — modelagem do DB — já está pronta). Sessões podem ser curtas e interrompidas; ao retomar, releia o estado atual do `index.html`/`style.css` antes de continuar, não assuma que lembra o progresso de memória.

## 10. Progresso

- [x] Navbar — **aprovada** (não mexer sem pedido explícito).
- [x] Hero — **aprovado** (não mexer sem pedido explícito). Layout desktop a partir de 1280px (`xl:`). Obs: a sombra da foto de baixo (PNG com sombra embutida) vaza um pouco abaixo do Hero — tratar ao montar Cardápios.
- [x] Cardápios — **aprovado** (não mexer sem pedido explícito). Carrossel scroll-snap 1 por tela no mobile, lado a lado a partir de `md:`, pop-up `<dialog>` de tela cheia ao tocar. Faixa do título com bordas pretas em cima/embaixo (2px mobile, 5px md+) — padrão de separação entre partes da página.
- [x] Contatos — **aprovado** (não mexer sem pedido explícito). Faixa de links iFood/WhatsApp + bloco da Polar. WhatsApp com link real; iFood ainda `#` com TODO. Decisão: manter `Polar 1.png` (navbar) e `Polar 2.png` (contatos) como estão — não otimizar/unificar.
- [x] Horários e Localização — **aprovado** (não mexer sem pedido explícito).

**Todas as seções aprovadas.**

## 11. Pendências

- [x] Migrar do CDN para o Tailwind CLI standalone — feito (`tailwind.css`, 14,7KB). Comparação pixel a pixel CDN × estático em 360/800/1920px: idêntico.
- [ ] Link do iFood (navbar + contatos) — o dev vai confirmar com os donos.

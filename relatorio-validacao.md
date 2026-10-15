# Validação — `index.html` (Gestão do Futebol 2027–2029)

**Fase 1: diagnóstico.** Nenhuma linha do `index.html` foi alterada ainda. O original está preservado em `index.original.html` (idêntico ao arquivo enviado).
Data: 15/10/2026 · Ferramenta: Playwright 1.56.1 (Node) · Servido por `python3 -m http.server` e aberto também por `file://`.

Legenda: **[Verificado — print]** / **[Verificado — log]** = visto no navegador (print em `./validacao/` ou métrica no log) · **[Inferência]** = deduzido do código ou do comportamento · **[Não verificado]** = não consegui testar.

---

## 1. Resumo executivo

- **4 problemas críticos**, todos reproduzidos: (C1) o quadro “Status e folha” da cena de elenco **nunca aparece**, porque sobra um `</div>`; (C2) no celular, a barra de passos do rodapé **cobre o fim do conteúdo** e o topo de várias cenas fica **cortado sob o menu**; (C3) a **abertura** (escudo, linha de apoio e título) fica **cortada sob o menu** em telas baixas; (C4) no toque, **“Pausar” não pausa** o modo Apresentar.
- **7 médios** e **6 baixos**. Nenhum erro de console, nenhum `pageerror`, nenhuma rolagem horizontal em 29 configurações.
- **Só Chromium foi testado.** O proxy da rede bloqueou o download de Firefox e WebKit. Tudo o que for específico de Safari ou Firefox está como **[Não verificado]**.

---

## 2. Ambiente e limitações do teste

| Item | Situação |
|---|---|
| Chromium 141 (build 1194, pré-instalado) | OK |
| **Firefox** | **Não instalado.** `npx playwright install firefox` recebeu 403 do proxy (`playwright.download.prss.microsoft.com`; também testei `cdn.playwright.dev` e 3 espelhos azureedge). [Verificado — log] |
| **WebKit** | **Não instalado**, mesmo motivo. Os perfis “iPhone/iPad” rodaram no **Chromium com user agent, tela, `isMobile` e `hasTouch` de iOS**. Isso testa layout e toque, mas **não** o motor do Safari. |
| Google Fonts | Acessível (só GET da folha de estilo e das fontes; o conteúdo do arquivo não sai da máquina). |
| `file://` | Testado no desktop 1366×768 e no iPhone SE: carrega, fontes OK, 61/63 paradas, 0 erros. [Verificado — log] |
| Serviços de terceiros | Nenhum usado. Nada publicado. |

**Emulação não é aparelho real.** O Safari do iOS tem barra de endereço dinâmica, rubber-banding, `env(safe-area-inset-*)` diferente de zero e quirks de `position: sticky` e `100vh`. O Chromium emulado não reproduz nada disso: `env()` sempre vale 0 e não há barra dinâmica. A lista do que ficou para o aparelho real está na seção 7.

---

## 3. Matriz dispositivo × motor × tema

Viewports “nominais” = tamanho da tela. Viewports “-safari/-chrome” = área útil real com a barra do navegador (descritores do Playwright).
**Tema:** a página fixa `color-scheme: dark` e não tem nenhuma regra `prefers-color-scheme`. Claro e escuro deram **o mesmo resultado** (print idêntico byte a byte no iPhone SE; no desktop a diferença é só o pulso animado do indicador). [Verificado — print] em 2 configurações e [Inferência] nas demais, por isso tema é uma coluna só.
Firefox e WebKit: **[Não verificado]** em todas as linhas.

Problemas presentes em **todas** as configurações com cenas fixas (não repetidos na tabela): **C1** (elenco), **M1** (palavras pela metade na 1ª parada) e, em todos os perfis de toque, **C4** (Pausar).

| Dispositivo | Orientação | Viewport | Chromium (claro = escuro) | Paradas | Problemas específicos |
|---|---|---|---|---|---|
| iPhone SE | retrato | 375×667 | **Problema** | 63 | C2 (base, pilares, princípios, formação), C3 (escudo no topo a 13 px, sob o menu de 57 px) |
| iPhone SE | paisagem | 667×375 | **Problema** | 59 | C3 (escudo a −65 px), M2 (campo tático com ~25 px), C2 |
| iPhone 14/15 | retrato | 390×844 | OK* | 62 | — |
| iPhone 14 (área Safari) | retrato | 390×664 | **Problema** | 63 | C2 (pilares, princípios, formação), C3 (escudo a 28 px) |
| iPhone 14 | paisagem | 750×340 | OK (empilhado) | — | modo empilhado, como esperado |
| iPhone 15 Pro Max | retrato | 430×932 | OK* | 61 | — |
| iPhone 15 Pro Max (área Safari) | retrato | 430×739 | **Problema** | 62 | C2 (princípios, formação) |
| iPhone 15 Pro Max | paisagem | 814×380 | **Problema** | 58 | C3, M2, C2 em 7 cenas |
| Pixel 7 | retrato | 412×915 | OK* | 62 | — |
| Pixel 7 (área Chrome) | retrato | 412×839 | OK* | 62 | — |
| Pixel 7 | paisagem | 863×360 | **Problema** | 58 | C3, M2, C2 em 8 cenas |
| Galaxy S9+ | retrato | 320×658 | **Problema** | 63 | C2 (base, princípios, formação, governança), C3 (escudo a 9 px) |
| Galaxy S9+ | paisagem | 658×320 | OK (empilhado) | — | — |
| Galaxy S9+ `?estatico` / sem JS | retrato | 320×658 | **Problema** | — | M4 (“OPORTUNIZAR” estoura o card e é cortado na borda) |
| iPad Mini | retrato / paisagem | 768×1024 / 1024×768 | OK* | 60 / 61 | — |
| iPad Pro 11 | retrato / paisagem | 834×1194 / 1194×834 | OK* | 60 / 60 | — |
| iPad Pro 12.9 | retrato / paisagem | 1024×1366 / 1366×1024 | OK* | 60 / 60 | — |
| Galaxy Tab S4 | retrato / paisagem | 712×1138 / 1138×712 | OK* | 61 / 61 | — |
| Desktop | — | 1280×720 | OK* | 61 | B4 (botões flutuantes encostam no texto à direita) |
| Desktop | — | 1366×768 | OK* | 61 | B4 |
| Desktop | — | 1440×900 | OK* | 60 | — |
| Desktop | — | 1920×1080 | OK* | 60 | — |
| Desktop | — | 2560×1440 | OK* | 60 | — |
| Desktop, janela baixa | — | 1366×620 | **Problema** | 61 | C3 (escudo a 20 px, sob o menu), B4 |
| Zoom 150% de 1366×768 | — | 911×512 @1,5x | **Problema** | 62 | C3 (escudo a −4 px), B4 |
| Zoom 150% de 1920×1080 | — | 1280×720 @1,5x | OK* | 61 | B4 |
| Movimento reduzido (1440×900 e iPhone 14) | — | — | **Problema** | 60 / 62 | M5 (animações CSS continuam); a navegação funciona e fica instantânea |
| Fontes bloqueadas (5 perfis) | — | — | OK* | iguais | sem rolagem horizontal; B3 (“Especialização” vaza do card no fallback) |

\* “OK” = sem problema específico além de C1 e M1 (e de C4 nos perfis de toque).

**Comuns a todas as configurações** [Verificado — log]: 0 erros de console, 0 `pageerror`, 0 requisições quebradas (exceto as fontes, no teste de bloqueio), `scrollWidth == innerWidth` (sem rolagem horizontal), menu numa linha só sem espremer os links, escudo do menu com 38×38 px (vetor nítido em DPR 2 a 4,5). Ir e voltar passam pelo **mesmo conjunto de paradas**, na mesma ordem, nos 6 perfis em que testei a volta. [Verificado — log]

**Paradas por cena** (iPhone SE retrato; desktop é igual com ±1 em Compromissos e Notas): abertura 1 · propósito 3 · base 8 · pilares 4 · princípios 6 · bola parada 4 · formação 4 · SACI 5 · transição 4 · **elenco 4 (para 3 quadros, ver C1)** · desenvolvimento 5 · governança 8 · compromissos 2–4 · fechamento 2–3.

---

## 4. Problemas priorizados

### CRÍTICO

#### C1 — Elenco: o quadro “Status e folha” nunca aparece, e uma parada repete o quadro anterior com zeros
- **O que acontece:** a cena tem 4 bolinhas e 4 paradas, mas só 3 quadros ficam visíveis. O quarto quadro (Chave/Núcleo/Rotação/Periférico, folha, lastro) **nunca é exibido** no modo cena. As paradas 2 e 3 mostram o mesmo “Perfil etário”, e a parada 2 aparece **com bolinhas vazias e números em 0**, porque o preenchimento só dispara com `loc > 0.2` e essa parada fica em `loc = 0.125`. [Verificado — print] `desk1366x768_chromium_paisagem_40…43-elenco.png` (montagem: 41 = zeros, 42 = mesmo quadro, 43 = “Perfil de ativo”; o “Status e folha” não aparece)
- **Causa:** sobra um `</div>` na linha 794, que fecha `#elFrames` antes do 4º `.frame`. Esse quadro vira filho de `.wrap`, fica fora de `$$('#elFrames .frame')`, com `position:absolute` e `opacity:0`. [Verificado — log: `elFrames.length` = 3; STOPS.elenco = `fr(4)`]
```html
<!-- index.html:790-795 -->
            <div class="dc">…Performance…</div>
          </div>          <!-- fecha .dcols -->
        </div>            <!-- fecha o 3º .frame -->
        </div>            <!-- ← SOBRANDO: fecha #elFrames cedo -->
        <div class="frame" data-anim>
          <p class="eyebrow">Status e folha</p>
```
- **Efeito colateral** [Inferência]: o quadro invisível fica por cima da área da cena e pode capturar cliques.
- **Correção proposta:** mover esse `</div>` para depois do 4º quadro (linha 806). O número total de `div` não muda.

#### C2 — Celular e tablet em telas baixas: a barra de passos cobre o rodapé e o topo da cena fica cortado sob o menu
- **O que acontece:** no toque, a cena reserva 72 px embaixo para a barra (`padding-bottom:72px`), mas a altura dos quadros e o teste de “cabe?” ignoram essa reserva. O conteúdo fica 56 px maior que o espaço, centraliza e vaza **para cima** (sobrancelha “01 · IDENTIDADE PARA JOGAR” some sob o menu) e **para baixo** (Lastro, legenda e último card ficam sob a barra). [Verificado — print] `iphoneSE_chromium_retrato_05-base.png` (card “24 componentes” cortado), `_17-princ.png` (Lastro sob a barra, sobrancelha cortada), `iphone14-safari_chromium_retrato_30-form.png`, `galaxyS9p_chromium_retrato_56-gov.png`
- **Onde:** iPhone SE, iPhone 14/15 PM na área real do Safari, Galaxy S9+ e todas as paisagens de celular. Cenas: base, pilares, princípios, formação, governança e, em paisagem, também elenco, transição e fechamento.
```css
/* index.html:75 */  .pinned .frames{…height:calc(var(--sh) - 32px)}        /* não desconta os 72px */
/* index.html:405 */ @media (hover:none),(pointer:coarse){ .pinned>.stage{padding-bottom:72px} }
```
```js
// index.html:1031 — clientHeight inclui o padding; no toque sobram 56px a mais
var avail=st.clientHeight-30;
```
- **Correção proposta:** (a) `avail` = altura interna do palco (`clientHeight` menos `padding-top` e `padding-bottom`) menos uma pequena folga; (b) no bloco `pointer:coarse`, `.pinned .frames{height:calc(var(--sh) - 88px)}`. A escala automática que já existe (`fitScale`/`fitBoxes`) passa a reduzir só o necessário.

#### C3 — Abertura cortada sob o menu em telas baixas
- **O que acontece:** a abertura está em `FRAMED`, então o ajuste de escala nunca roda para ela. Quando escudo, linha de apoio, título, frase-mãe e metadados não cabem, o bloco centralizado vaza para cima e o **escudo e a linha de apoio somem sob o menu fixo**. [Verificado — print] `iphoneSE_chromium_paisagem_00-abertura.png` (escudo e topo do título cortados), `iphoneSE_chromium_retrato_00-abertura.png` (escudo cortado), `desk1366x620_chromium_paisagem_00-abertura.png`. [Verificado — log] topo do escudo vs base do menu (57 px): SE 13 px, iPhone 14 Safari 28, S9+ 9, 1366×620 20, zoom 150% −4, paisagens −63 a −72.
```js
// index.html:1003 e 1030
var FRAMED={hero:1,prop:1,bola:1,elenco:1,close:1};
if(FRAMED[k])return;   // a abertura não tem .frame/.fbox: nada a ajusta
```
- **Correção proposta:** tirar `hero` de `FRAMED`. A abertura passa pelo mesmo `fitScale` das demais cenas e encolhe só o necessário. Abaixo de 0,66 de escala ela cai no modo empilhado, como as outras.

#### C4 — Toque: “Pausar” não pausa o modo Apresentar
- **O que acontece:** no toque, o `touchstart` em qualquer lugar chama `stopAuto()`, inclusive no próprio botão. Em seguida o `click` encontra `aria-pressed="false"` e **reinicia** o autoplay. [Verificado — log] iPhone SE: depois de tocar em Pausar, `aria-pressed` continuou `true` e a página seguiu rolando nos 5 s seguintes. No mouse funciona, porque o `mousedown` já exclui `.stepnav`.
```js
// index.html:1286
['wheel','touchstart'].forEach(function(ev){window.addEventListener(ev,function(){stopAuto();},{passive:true});});
// index.html:1287 (mouse já faz certo)
document.addEventListener('mousedown',function(e){if(!e.target.closest('.stepnav'))stopAuto();});
```
- **Correção proposta:** no `touchstart`, ignorar toques dentro de `.stepnav`, igual ao `mousedown`. Tocar em “próximo” ou “anterior” durante o autoplay continua parando o autoplay pelo próprio handler do botão.

### MÉDIO

#### M1 — Propósito: a 1ª parada mostra a frase com as últimas palavras ainda apagadas
- Na parada 1 de Propósito, “…cultura **e identidade.**” fica pela metade (opacidade 0,14–0,6), em todas as configurações. [Verificado — print] `desk1366x768_chromium_paisagem_02-prop.png`
- Causa: a parada fica no meio do quadro (`loc = 0.5`), mas o preenchimento só termina em `loc = 0.6`. Nas outras cenas (tese, bola, coda, fechamento) a conta fecha antes da parada.
```js
// index.html:1071
fillWords(propW,clamp(loc[0]/0.6));
```
- **Correção proposta:** `/0.5`, o mesmo valor da bola parada. A frase fica completa exatamente na parada.

#### M2 — Celular deitado (360–430 px de altura) continua no modo cena, com campo tático minúsculo
- Com altura útil a partir de 360 px, a página continua fixando as cenas. Em SE paisagem (375), 15 Pro Max paisagem (380) e Pixel 7 paisagem (360), quase todas as cenas ficam “tight”/encolhidas e o **campo tático vira um selo de ~25–100 px**, porque `calc((var(--sh) - 300px) * 1.544)` fica perto de zero. [Verificado — print] `iphoneSE_chromium_paisagem_14-princ.png`. O roteiro pede que a paisagem baixa (~390 px) caia no modo empilhado.
```js
// index.html:1020
cine=!/[?&]estatico\b/.test(location.search) && VH>=360 && VH<=2600;
```
- **Correção proposta:** subir o piso para `VH>=460` [Inferência: na matriz, as paisagens de celular têm até 430 px de altura e a menor janela de desktop/zoom tem 512 px; 460 separa os dois grupos]. Celular deitado passa a ver o modo empilhado, que já está legível (seção 5).

#### M3 — Alvos de toque menores que 44×44 px
- [Verificado — log, iPhone SE] princípios do campo (`#plist li`): **170×30 px**; links do menu: **36 px de altura**; botão Apresentar no toque: **40×44**. Prev e Next: 44×44 (OK).
- **Correção proposta** (só em `pointer:coarse`, sem mudar o visual no desktop): `#plist li{min-height:44px}`, links do menu com `padding-block` para 44 px (cabe no menu de 56 px), `#stepPlay{min-width:44px}`.

#### M4 — Modo empilhado (sem JS / `?estatico` / ≤375 px): verbos do SACI estouram o card
- Em 2 colunas, “OPORTUNIZAR” e “CONHECER” (30 px, sem quebra) passam da largura do card. Em 320 px, “OPORTUNIZAR” é **cortado na borda da tela** pelo `overflow-x:clip`. [Verificado — log] `P.verb 141 > 91 px` (320) e `141 > 119` (375). [Verificado — print] `iphoneSE_chromium_retrato_nojs-pagina.png`
```css
/* index.html:188 */ @media (max-width:900px){.verbs{grid-template-columns:repeat(2,minmax(0,1fr))}}
```
- **Correção proposta:** `@media (max-width:480px){.verbs{grid-template-columns:minmax(0,1fr)}}`. Isso só afeta o modo empilhado: no modo cena, `.verbs` é `display:block`.

#### M5 — `prefers-reduced-motion`: as animações da abertura continuam
- Com `reduce`, a rolagem fica instantânea e a navegação funciona (60/62 paradas) [Verificado — log]. Mas 15 animações CSS seguem rodando: `rise` ×4 (título subindo), `fade` ×5, `draw` ×5 (linhas do campo) e `cue` (pulso infinito do indicador de rolagem). [Verificado — log `document.getAnimations()`]
- **Correção proposta:** em `@media (prefers-reduced-motion:reduce)`, `animation:none` para `.hero-in h1 .w, .motto, .hero-meta, .hero-ro, .scroll-cue, .scroll-cue::after, .kb-hint, .hero-pitch *`. Os elementos já aparecem no estado final sem a animação.

#### M6 — Foco por teclado fraco nos princípios do campo
- A ordem de tabulação é coerente: escudo/início → 8 links → 6 princípios → Apresentar → anterior → próximo [Verificado — log]. Links e botões têm anel de 2 px `--red-soft`. Os `li` dos princípios usam o anel padrão do navegador (`auto 1px`, cinza-escuro sobre fundo quase preto), quase invisível. [Verificado — print] `desk1366x768_chromium_paisagem_foco-princ.png`
- **Correção proposta:** incluir `.plist li:focus-visible` na regra já existente da linha 31, com o mesmo anel.

#### M7 — Safe area do iPhone na horizontal **[Não verificado]**
- Com `viewport-fit=cover`, o conteúdo pode ir para baixo do notch na horizontal, mas `.wrap` e `.nav-in` só usam `padding-inline:24px` (ou 18 px), sem `env(safe-area-inset-left/right)`. A barra de passos já respeita `safe-area-inset-bottom`. [Inferência] No iPhone deitado, o escudo do menu e o início dos textos ficam sob o notch. O Chromium sempre devolve 0 para `env()`, então não dá para ver no teste.
- **Correção proposta:** `padding-left:max(24px, env(safe-area-inset-left))` e o equivalente à direita, em `.wrap` e `.nav-in`. Vira no-op onde o inset é 0.

### BAIXO

| # | Problema | Evidência | Proposta |
|---|---|---|---|
| B1 | **Barra de endereço móvel:** `--vh` é medido no carregamento e só é refeito se a altura mudar mais de 150 px. Quando a barra do Safari recolhe (+~80 px), o palco fica 80 px mais curto que a tela e aparece uma faixa escura sob a cena (no fechamento vermelho, isso aparece). | [Verificado — print, emulando a mudança de altura] `iphone14-safari_chromium_retrato_barra-recolhida-fechamento.png`; no Safari real **[Não verificado]** | **Não corrigir.** É troca consciente: recalcular a cada movimento da barra muda a altura das cenas no meio da rolagem e dá pulo. `dvh` causa o mesmo problema; `svh` mantém a faixa. Fica para o teste em aparelho. |
| B2 | Fechamento: `.closing .fine` (branco 75%) sobre a parte mais clara do degradê dá 3,49:1. No ponto onde o texto fica (parte inferior, mais escura), o contraste deve passar de 4,5:1. | [Inferência, cálculo WCAG] | Não mexer (é cor). Branco sobre `--red` = 5,39:1 e vermelho `.hl` sobre fundo = 3,57:1 (texto grande, passa AA). |
| B3 | Sem as fontes do Google, “Especialização” passa do card na formação (fallback mais largo que o Archivo condensado). Com Archivo, entre 721 e ~800 px, sobra 16 px dentro do padding, sem aparecer. | [Verificado — print] `desk1920x1080_chromium_paisagem_semfontes-form.png` | Não corrigir (só acontece offline; quebrar a palavra mudaria o desenho). |
| B4 | Desktop: os botões flutuantes (Apresentar, ↑, ↓) encostam no fim de linhas longas à direita quando a janela tem menos de ~1300 px (ex.: zoom 150%, “voltados ao rendimento” no elenco). | [Verificado — print] `zoom150-1366x768_chromium_paisagem_43-elenco.png` | Opcional. Reservar margem à direita mudaria a composição; prefiro sua decisão. |
| B5 | A folha do Google Fonts bloqueia a renderização. Bloqueada ou offline, falha rápido e a página abre com o fallback (0 erros, layout íntegro). Numa rede que “segura” a conexão, a primeira pintura espera o timeout. | [Verificado — log] bloqueio rápido; rede lenta **[Inferência]** | Opcional: carregar a folha de forma assíncrona. Não aplico sem aprovação, porque muda o momento em que a fonte troca. |
| B6 | No fechamento, o indicador do menu continua “09/09 · COMPROMISSOS” (o fechamento não é um capítulo `data-ch`). | [Verificado — print] | Comportamento de design. Não mexer. |

### O que está OK (verificado)
- **Campo tático:** nos 4 perfis (desktop, SE, S9+, iPad Mini), clicar ou tocar em cada um dos 6 princípios mostra **exatamente um** movimento por vez. Seta, jogadores, legenda `n/6` e item ativo ficam corretos, e a legenda tem 15–18 px. [Verificado — log + prints `*_princ-1…6.png`]
- **Paradas:** sem pulos e sem paradas impossíveis. Ir e voltar dão o mesmo conjunto. 2 e 3 cliques rápidos avançam exatamente 2 e 3 paradas. Enter, ↓, →, PageDown e Espaço seguem a mesma sequência. [Verificado — log] O único “limbo” é o do elenco (C1).
- **Apresentar (desktop):** percorreu do início ao fim em 258 s, parou sozinho no fim (`aria-pressed=false`) e para com a roda do mouse e com clique em Pausar. [Verificado — log] Que ele não pula paradas é **[Inferência]**: usa o mesmo `go(1)` validado acima. Vou medir parada a parada na reexecução.
- **Toque cancela a rolagem suave:** um `touchStart` no meio da animação interrompe o trajeto (a página não seguiu para o destino). [Verificado — log] O menu horizontal rola com o dedo (`scrollLeft` 168 → 0). [Verificado — log] Nada depende de hover: as 4 regras `:hover` são só realce visual.
- **Sem JS e `?estatico`:** conteúdo empilhado e legível, sem sobreposição e sem rolagem horizontal em 320, 375 e 1366 px (exceto M4). [Verificado — print `*_nojs-pagina.png`, `*_estatico-pagina.png`]
- **Desempenho** [Verificado — log, Chromium com CPU 4× mais lenta]: HTML com 100 kB (98 kB transferidos, sem imagens externas); primeira pintura em 1,06 s (desktop) e 0,97 s (Pixel 7); rolagem contínua de 25 s pela página toda: mediana de 17 ms por quadro, p95 de 33 ms, 0 tarefas longas, 2 quadros acima de 50 ms em 1.249. **Nada relevante a corrigir.**
- **Escudo:** `<symbol>` com `role="img"` e `aria-label="Escudo do Internacional"` nas 3 ocorrências; vetor nítido em todas as densidades.

---

## 5. Plano de correção (aguardando sua aprovação)

Uma correção por vez, com a menor mudança possível, sem tocar em conteúdo, textos, números, cores, tipografia ou escudo:

| Ordem | Item | Mudança | Tamanho |
|---|---|---|---|
| 1 | C1 | mover 1 `</div>` (linha 794 → depois da 806) | 2 linhas |
| 2 | C2 | `avail` descontando o padding do palco + 1 regra CSS no bloco `pointer:coarse` | ~3 linhas |
| 3 | C3 | tirar `hero` de `FRAMED` | 1 linha |
| 4 | C4 | `touchstart` ignora `.stepnav` | 1 linha |
| 5 | M1 | `/0.6` → `/0.5` | 1 linha |
| 6 | M2 | `VH>=360` → `VH>=460` | 1 linha |
| 7 | M3 | alvos ≥44 px só em `pointer:coarse` | ~3 linhas CSS |
| 8 | M4 | 1 coluna nos verbos do SACI ≤480 px (só modo empilhado) | 1 linha CSS |
| 9 | M5 | `animation:none` em `reduce` | 1 linha CSS |
| 10 | M6 | anel de foco nos `li` | 1 seletor |
| 11 | M7 | `env(safe-area-inset-left/right)` em `.wrap` e `.nav-in` | ~2 linhas CSS |
| — | B1–B6 | **não corrijo**, salvo pedido seu (B4 e B5 são opcionais) | — |

Depois disso: rodo de novo a matriz inteira e os testes dirigidos, com prints “antes/depois” (sufixo `_depois`), e entrego `git diff`, os resultados e a mensagem de commit sugerida (sem push).

---

## 6. Prints

Ficam em `./validacao/` (679 arquivos, ~151 MB), no padrão `dispositivo_motor_orientacao_NN-cena.png`. `NN` é o número da parada; prefixo `00-abertura` = estado inicial. Em 7 perfis há um print por parada (iPhone SE retrato, iPhone 14 Safari, S9+, iPad Mini retrato, 1366×768, 1366×620 e SE paisagem). Nos demais, só as paradas com problema. Extras: `*_princ-1…6`, `*_semfontes*`, `*_nojs-pagina`, `*_estatico-pagina`, `*_tema-light/dark-pilares`, `*_apresentar-*`, `*_foco-princ`, `*_barra-recolhida-fechamento`, `*_empilhado-pagina`.

---

## 7. Limitações declaradas

1. **Firefox e WebKit não rodaram** (download bloqueado pela política de rede). Nenhuma afirmação deste relatório vale para Safari ou Firefox, a não ser como [Inferência].
2. **Emulação ≠ aparelho real.** Ficou para teste manual: barra de endereço dinâmica do Safari/Chrome móvel (B1); rubber-banding no topo e no fim; `position: sticky` dentro de `overflow-x: clip` no Safari < 16; `env(safe-area-inset-*)` com notch e Dynamic Island, em retrato e paisagem (M7); inércia do toque no iOS durante a rolagem suave; `backdrop-filter` no menu e na barra (custo no iPhone mais antigo); toque no menu horizontal com rolagem por inércia.
3. **Zoom do navegador** foi emulado como viewport menor + DPR 1,5. O zoom real do Safari/Chrome também muda `innerHeight`, o que deve dar o mesmo resultado [Inferência].
4. A detecção de “texto sob a barra” no desktop usa a caixa do elemento, não a da linha. Por isso a lista bruta no log tem falsos positivos (ex.: legenda curta numa caixa larga). No relatório só entrou o que confirmei no print.
5. Desempenho medido só no Chromium com CPU 4× mais lenta, não em GPU de celular.

---

## 8. Checklist manual (iPhone, iPad e um Android)

1. Abra o link no Safari (iPhone e iPad) e no Chrome (Android), em retrato. O escudo, a linha “Sport Club Internacional · Departamento de Futebol” e o título aparecem inteiros, abaixo do menu?
2. Role devagar com o dedo pela página toda. Em alguma cena um texto, card ou quadro fica sob o menu ou sob a barra de passos do rodapé? (Atenção a Base, Pilares, Identidade, Formação, Elenco e Estrutura.)
3. Em “Inteligência”, avance com a seta da barra: devem aparecer **4 quadros** diferentes (26 · Perfil etário · Perfil de ativo · **Status e folha**), sem repetir nenhum.
4. Role até a barra do Safari recolher e volte um pouco até ela reaparecer. Surge faixa vazia sob a cena ou pulo de conteúdo?
5. Gire o aparelho para paisagem com uma cena aberta. O conteúdo continua legível (no celular, deve ficar empilhado) e nada fica sob o notch ou a ilha?
6. Volte para retrato e confirme que a página voltou ao modo cena, no mesmo ponto.
7. Toque em “Apresentar” e deixe passar 3 ou 4 quadros. Depois toque em **Pausar**: deve parar de vez. Toque de novo em Apresentar e então arraste a tela com o dedo: também deve parar.
8. Em “Identidade”, toque em cada um dos 6 princípios. Deve aparecer um movimento por vez no campo, com seta e legenda “n/6” legíveis.
9. Vá até o fim. O fechamento vermelho mostra o escudo inteiro e as 4 linhas, sem corte em cima e sem nada atrás da barra de passos?
10. (iPad com teclado, opcional) Use Tab e Enter/setas: o anel de foco aparece em todos os itens, inclusive nos 6 princípios?

---

# Fase 2 — Correções aplicadas e reteste

Aprovado pelo usuário: todas as correções de C1 a M7, limite de **460 px** e os opcionais **B4** e **B5**. B1, B2, B3 e B6 ficaram como estavam, como combinado.
Diff completo: `correcoes.diff` (equivale a `git diff index.html`; +23 / −8 linhas). Nenhum texto, número, cor, fonte ou traço do escudo foi alterado.
Prints “depois” em `./validacao/depois/`, com os mesmos nomes dos prints “antes” em `./validacao/` para comparar lado a lado.

**No repositório vai só uma amostra** (38 prints, ~23 MB): os pares antes/depois de cada correção citada nas tabelas. Os ~1.190 prints completos da varredura ficaram fora do commit, a pedido do usuário. Por isso, alguns nomes citados na Fase 1 (ex.: as paradas de todos os perfis) não estão no repositório.

## Correções

| # | Mudança no `index.html` |
|---|---|
| C1 | Removido o `</div>` que sobrava antes do quadro “Status e folha”. `#elFrames` agora tem 4 quadros. |
| C2 | `avail` = altura interna do palco, descontando o padding real. No toque, `.pinned .frames{height:calc(var(--sh) - 88px)}`. |
| C3 | `hero` saiu de `FRAMED`. A abertura passa pelo mesmo ajuste de escala das outras cenas. |
| C4 | O `touchstart` dentro de `.stepnav` não chama mais `stopAuto()`. A roda do mouse continua parando o autoplay. |
| M1 | `fillWords(propW, loc/0.5)` (era `/0.6`). |
| M2 | Modo cena só com `VH >= 460` (era 360). |
| M3 | No toque: `#stepPlay` com no mínimo 44 px de largura; links do menu com `padding:12px 0`; `#plist li` com `min-height:44px`. |
| M4 | Até 480 px de largura, verbos do SACI em 1 coluna (só afeta o modo empilhado). |
| M5 | Com `prefers-reduced-motion: reduce`, `animation:none` nos elementos da abertura e nas linhas do campo. |
| M6 | `.plist li:focus-visible` usa o mesmo anel de 2 px dos links e botões. |
| M7 | `.wrap` e `.nav-in` com `padding-left/right: max(24px, env(safe-area-inset-*))` (18 px nas telas pequenas). |
| B4 | Desktop com mouse, janela de 700 px ou mais: as cenas fixas (exceto a abertura) reservam à direita a largura dos botões flutuantes, só quando a janela é estreita (`161px − (100vw − 1180px)/2`, zero a partir de ~1500 px). |
| B5 | A folha do Google Fonts carrega sem bloquear a pintura (`media="print"` + `onload`, com `<noscript>` de reserva). A página reajusta as cenas no evento `loadingdone` das fontes. |

## Reteste: antes × depois (mesma matriz de 29 configurações, Chromium)

| Teste | Antes | Depois | Evidência |
|---|---|---|---|
| Erros de console / `pageerror` / requisições quebradas | 0 | **0** | [Verificado — log] |
| Rolagem horizontal | nenhuma | **nenhuma** | [Verificado — log] |
| Elenco: quadros distintos nas 4 paradas | 3 (um repetido, um com zeros) | **4** (26 · Perfil etário · Perfil de ativo · Status e folha) | [Verificado — print] `depois/desk1366x768_chromium_paisagem_40…43-elenco.png` |
| Paradas com texto cortado sob o menu ou sob a barra | 12 das 29 configurações | **0 de 29** | [Verificado — log + prints] `depois/iphoneSE_chromium_retrato_05-base.png`, `_17-princ.png`, `depois/galaxyS9p_chromium_retrato_56-gov.png` |
| Topo do escudo da abertura (o menu termina em 57 px) | 9, 13, 20, 28, −4, −72 px nos piores casos | **≥ 73 px em todas** | [Verificado — log + print] `depois/iphoneSE_chromium_retrato_00-abertura.png` |
| Propósito, 1ª parada, palavras pela metade | 1 a 3 palavras (todas as configs) | **0** | [Verificado — log] |
| Paisagem de celular (320–380 px de altura) | modo cena, campo com ~25 px | **empilhado** (SE, 14, 15 PM, Pixel 7, S9+) | [Verificado — print] `depois/*_paisagem_empilhado-pagina.png` |
| Ir e voltar: mesmo conjunto de paradas | sim | **sim** (7 perfis) | [Verificado — log] |
| Teclado (Enter, ↓, →, PageDown, Espaço) e 2/3 cliques rápidos | OK | **OK** (mesma sequência; 2 cliques → 2ª parada, 3 → 3ª) | [Verificado — log] |
| Apresentar: paradas percorridas | chegou ao fim (pulos não medidos) | **61 paradas, exatamente as mesmas do botão**, fim limpo (`aria-pressed=false`) | [Verificado — log] |
| Toque em “Pausar” | **não pausava** (continuava rolando) | **pausa** e a página fica parada | [Verificado — log] `depois/iphoneSE_chromium_retrato_apresentar-pausar.png` |
| Toque cancela a rolagem suave | cancelava | **cancela** (parou em 17716 e não andou mais) | [Verificado — log] |
| 6 princípios: um movimento por vez | OK | **OK** nos 4 perfis | [Verificado — log + prints] `depois/*_princ-1…6.png` |
| Alvos de toque (iPhone SE) | Apresentar 40×44; links 36 px; princípios 30 px | Apresentar **44×44**; links **48 px**; princípios **35 px na tela** (ver nota) | [Verificado — log] |
| Anel de foco nos princípios | `auto 1px` escuro, quase invisível | **2 px `--red-soft`** | [Verificado — print] `depois/desk1366x768_chromium_paisagem_foco-princ.png` |
| Movimento reduzido: animações rodando | 15 | **0**; navegação instantânea, 60/62 paradas sem problema | [Verificado — log] |
| SACI empilhado em 320 px | “OPORTUNIZAR” cortado | **1 coluna, sem corte** | [Verificado — log + print] `depois/galaxyS9p_chromium_retrato_nojs-pagina.png` |
| Fontes bloqueadas | problemas iguais aos de cima | **0 paradas com problema**; sem rolagem horizontal | [Verificado — log] |
| Primeira pintura (CPU 4× mais lenta) | 1060 ms (desktop) / 968 ms (Pixel 7) | **600 ms / 416 ms** | [Verificado — log] |
| Rolagem contínua (CPU 4× mais lenta) | p50 17 ms, p95 33 ms, 0 tarefas longas | **igual** (p50 17, p95 33, 0 tarefas longas) | [Verificado — log] |
| `file://` | OK | **OK** (61/63 paradas, 0 erros) | [Verificado — log] |

**Nota sobre M3:** no CSS, cada princípio tem 44 px de altura mínima. Mas no iPhone SE a cena “Identidade” inteira é encolhida para caber na tela (escala ~0,8), então o alvo fica com **35 px** visíveis. Ainda é maior que os 30 px de antes, mas abaixo de 44. Chegar a 44 px reais exigiria mudar a composição da cena no celular pequeno. Fica registrado como **pendência baixa**. Nos aparelhos a partir de 390×844 a cena não encolhe e o alvo tem 44 px [Inferência].

**Nota sobre B5:** com a folha de fontes assíncrona, a página pinta primeiro com a fonte do sistema e troca para Archivo/Plex assim que elas chegam (FOUT curto, em geral menos de 1 s). Depois da troca, as cenas são reajustadas. Isso foi verificado no Chromium [Verificado — log: 3 famílias carregadas e cenas recalculadas]. No Safari, o evento `loadingdone` existe desde o iOS 10 [Inferência; Não verificado em aparelho].

## Pendências que continuam

- **Firefox e WebKit seguem sem teste** (download bloqueado pela rede).
- **B1:** faixa vazia quando a barra do Safari recolhe. Mantida de propósito; confira no aparelho (checklist, passo 4).
- **M7:** o respeito ao notch só pode ser conferido no iPhone real (checklist, passo 5).
- Princípios com 35 px no iPhone SE (nota acima).

## Mensagem de commit sugerida (não foi feito commit nem push)

```
Corrige layout, rolagem e toque da apresentação Gestão do Futebol

- Elenco: remove </div> sobrando que escondia o quadro "Status e folha"
  e criava parada repetida (com números zerados) na cena
- Toque: a altura útil das cenas desconta a barra de passos; nada fica
  sob a barra nem cortado sob o menu
- Abertura entra no ajuste de escala; escudo e título não somem sob o menu
- Toque em "Pausar" passa a pausar o modo Apresentar
- 1ª parada de Propósito mostra a frase inteira preenchida
- Modo cena só a partir de 460 px de altura (celular deitado empilha)
- Alvos de toque maiores, anel de foco nos princípios, verbos do SACI em
  1 coluna até 480 px, safe-area lateral no iPhone, sem animações da
  abertura com movimento reduzido
- Desktop: botões flutuantes não cobrem o fim das linhas em janela estreita
- Google Fonts sem bloquear a renderização, com reajuste ao carregar

Mantém index.original.html, relatorio-validacao.md e os prints em validacao/.
```

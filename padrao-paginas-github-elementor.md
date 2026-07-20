# Padrão de publicação: página em HTML (GitHub) embutida no Elementor

Este documento descreve o método definitivo, já testado e funcionando, para publicar páginas standalone (feitas em HTML/CSS/JS puro) dentro do site WordPress via Elementor, usando GitHub Pages como hospedagem do arquivo e um iframe com auto-ajuste de altura.

Use este padrão para qualquer página nova desse tipo — o fluxo é sempre o mesmo, só muda o conteúdo.

---

## 1. Visão geral

```
[ Repositório no GitHub ] → [ GitHub Pages publica o HTML ] → [ iframe no Elementor carrega a URL ] → [ altura se ajusta sozinha ]
```

- O HTML/CSS/JS da página fica **num repositório GitHub próprio**, publicado via **GitHub Pages**.
- No Elementor, um widget **HTML** contém só um `<iframe>` apontando para a URL do GitHub Pages.
- A altura do iframe se ajusta automaticamente ao conteúdo através da biblioteca **iframe-resizer** (open-source, `davidjbradshaw/iframe-resizer`) — sem isso, o iframe fica com uma altura fixa pequena ou gera barra de rolagem dupla.
- Toda atualização de conteúdo passa a ser só um `git push` — não precisa tocar no Elementor de novo.

---

## 2. Estrutura do repositório

```
nome-do-repo/
├── index.html      ← obrigatório ter esse nome (GitHub Pages carrega ele por padrão)
├── imagem1.jpg
├── imagem2.jpg
└── ... (demais assets, se houver)
```

- Repositório pode ser **privado** — o site publicado continua sendo público (é assim que o GitHub Pages funciona), só o código-fonte fica fechado.
- Ativar em **Settings → Pages → Source: Deploy from a branch → main → / (root)**.
- URL final: `https://<usuario>.github.io/<repo>/`

---

## 3. Duas regras obrigatórias no `index.html`

### 3.1 — Terminar o arquivo com o script do iframe-resizer

A **última linha antes de `</body>`** precisa sempre ser esta:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/4.3.9/iframeResizer.contentWindow.min.js"></script>
```

Sem essa linha, o Elementor não recebe nenhum sinal de altura e o iframe fica pequeno/quebrado. **Essa é a causa mais comum de a página "não funcionar" depois de publicada.**

> ⚠️ Se essa linha for perdida (por exemplo, ao subir uma versão antiga do arquivo por cima), o problema reaparece. Sempre confirme visualmente essa linha antes de dar `git push` — veja o checklist no fim deste documento.

### 3.2 — Evitar unidades `vh` no CSS

Não usar `vh` (viewport height) em elementos que fazem parte do fluxo normal da página (hero, banners, etc.):

```css
/* ❌ Evitar */
.hero { min-height: 100vh; }

/* ✅ Usar valor fixo em px */
.hero { min-height: 820px; }
```

**Por quê:** dentro de um iframe sem scroll próprio, `vh` é calculado com base na altura do próprio iframe — e como o iframe-resizer ajusta essa altura dinamicamente, isso pode criar um ciclo de realimentação (o iframe cresce → o `vh` cresce → o conteúdo mede maior → o iframe cresce mais → repete), resultando numa rolagem infinita.

`vh` dentro de elementos com `position: fixed` (ex: um modal/lightbox que cobre a tela inteira) é seguro, porque não afeta a altura do documento.

---

## 4. Código para colar no Elementor (lado do widget)

1. Criar a página no Elementor normalmente (template do tema, com cabeçalho/rodapé do site).
2. Arrastar um widget **HTML** (não usar plugins de terceiros tipo "Custom iFrame" — já testamos, não é confiável para conteúdo cross-domain).
3. Colar exatamente este código, trocando só a `src` do iframe:

```html
<style>
  #meu-iframe-site {
    width: 1px;
    min-width: 100%;
    border: none;
    display: block;
  }
</style>

<iframe id="meu-iframe-site" src="https://sandro-vtx.github.io/NOME-DO-REPO/"></iframe>

<script src="https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/4.3.9/iframeResizer.min.js"></script>
<script>
  window.addEventListener('DOMContentLoaded', function () {
    iFrameResize({ checkOrigin: false }, '#meu-iframe-site');
  });
</script>
```

> Se cada página nova tiver seu próprio iframe, dê um `id` diferente para cada um (ex: `#iframe-pagina-x`) para não conflitar caso mais de um apareça na mesma página do site.

---

## 5. Fluxo de atualização de conteúdo (para o dia a dia)

```bash
cd caminho/para/o/repositorio
git status        # confirma que não tem nada pendente
git pull          # traz qualquer atualização de outra pessoa antes de mexer
# ... editar o index.html ...
git add .
git commit -m "descrição da mudança"
git push
```

Depois do push, o GitHub Pages atualiza automaticamente em 1–2 minutos. Teste em aba anônima (`Ctrl+Shift+N`) para não pegar cache antigo do navegador.

---

## 6. Checklist antes de todo `git push`

- [ ] `git pull` foi feito antes de começar a editar (evita sobrescrever trabalho do outro)
- [ ] A última linha do `index.html`, antes de `</body>`, ainda é o `<script src=".../iframeResizer.contentWindow.min.js">`
- [ ] Não foram reintroduzidas unidades `vh` em elementos de fluxo normal (hero, banners, seções)
- [ ] Se o arquivo veio de um download/cópia antiga no computador, confirme que é a versão mais recente antes de sobrescrever

---

## 7. Problemas já resolvidos (para não repetir)

| Sintoma | Causa | Status |
|---|---|---|
| Duas barras de rolagem (da página + do iframe) | Iframe com altura fixa não acompanhando o conteúdo | Resolvido com iframe-resizer |
| Rolagem infinita / altura crescendo sem parar | `vh` em `.hero`/`.image-hero`/`.image-strip` combinado com script de medição contínua | Resolvido trocando `vh` por `px` fixo e usando iframe-resizer (sem loop de mutação) |
| Iframe travado em ~150px, sem se ajustar | Script `iframeResizer.contentWindow.min.js` ausente no `index.html` (removido por sobrescrita acidental de uma versão antiga do arquivo) | Resolvido restaurando a linha e confirmando via checklist |
| Plugin "Custom iFrame" (WordPress) não detecta altura | Protocolo de comunicação proprietário e não documentado, incompatível com sites cross-domain que não "falam" o formato dele | Abandonado — usar sempre HTML puro + iframe-resizer |

---

## 8. Passo a passo resumido para uma página nova do zero

1. Criar repositório novo no GitHub (privado, se preferir).
2. Ativar GitHub Pages (Settings → Pages → branch `main` → `/root`).
3. Subir o `index.html` (+ imagens) garantindo o script do item 3.1 no final do arquivo.
4. Confirmar a URL pública funcionando isoladamente (`https://usuario.github.io/repo/`).
5. No Elementor, criar a página, colar o bloco de código do item 4.
6. Publicar e testar em aba anônima.
7. Pronto — daqui em diante, qualquer mudança de conteúdo é só editar o `index.html` local e dar `git push`.

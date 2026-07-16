# Handoff — Studio VTX · Página Light Steel Frame
**Data:** junho 2026 · _atualizado em 23/06/2026_  
**Arquivo principal:** `C:\STUDIOVTX\CLAUDE\WEBSITE\studiovtx-lsf-page.html`

> **📄 Sessão 23/06/2026 — ver `DESENVOLVIMENTO-MARCA-SITE.md`** (mesma pasta).
> Cobre: auditoria do manual de marca v2, alinhamento do site à identidade (cor petróleo, acessibilidade, tipografia), ajuste de copy, **mapa dos pontos de captura de lead para a integração do CRM**, e pendências atualizadas.

---

## Estado atual da página

Página standalone HTML (single-file) para ser embutida no site WordPress studiovtx.com.br. Sem `<nav>` próprio (usa o menu do WP). Fonte: Montserrat. Paleta: preto `#0A0A0A` / branco / cinza + **petróleo `#127D8E`** como cor-assinatura em pontos de ênfase (CTAs, status) — alinhada ao manual de marca v2 em 23/06/2026.

### Ordem das seções

1. HERO — título + subtítulo + chips animados (ZERO surpresas / 60%)
2. IMAGE HERO — banner full-bleed `image_hero_opt.jpg` com créditos
3. WHAT IS LSF — fundo preto, 2 colunas: texto + 4 cards de vantagem
4. STATS BAR — 7 projetos / 2 em construção / 60% / 0 surpresas
5. PROCESS — 5 etapas (briefing → obra e entrega)
6. DEPOIMENTOS — carrossel 2 colunas por slide, 5 projetos, foto circular
7. IMAGE STRIP — 3 imagens lado a lado (obra · estrutura / detalhe construtivo / renderização)
8. PORTFOLIO — grid com lightbox, 5 projetos
9. MANIFESTO — posicionamento + _por que LSF + _nossa posição + _projeto + obra
10. PARCEIROS — grid 4 colunas (Espaço SMART, Leal, Imperatriz, Finabase)
11. CTA — WhatsApp `+55 41 41415471` com mensagem pré-definida
12. FOOTER

---

## Imagens na pasta WEBSITE

| Arquivo | Uso |
|---|---|
| `image_hero_opt.jpg` | Banner full-bleed (IMAGE HERO) |
| `strip_1of3_opt.jpg` | Image strip — obra · estrutura |
| `strip_2of3_opt.jpg` | Image strip — detalhe construtivo |
| `strip_3of3_opt.jpg` | Image strip — renderização |
| `projeto_2_opt.jpg` | Card lsf01 — casa[das araucárias] |
| `projeto_ar_opt.jpg` | Card lsf02 — casa[AR] |
| `projeto_4_opt.jpg` | Card lsf03 — casa[beverly hills] |
| `projeto_5_opt.jpg` | Card lsf04 — casa[andrade] |
| `hero_blueprint_opt.jpg` | Card lsf05 — casa[forma design] |
| `bg_lsf_planta_opt.jpg` | Planta no hero-right (rotacionada 90°, opacity ~22%) |

### Pastas de lightbox

- `projetos/lsf01/` — 6 imagens Casa da Mata (araucárias), todas com `_opt.jpg`
- `projetos/lsf02/` — 5 imagens Casa AR, todas com `_opt.jpg`
- `projetos/lsf03/` — 7 imagens Casa Carvalho (beverly hills), todas com `_opt.jpg`
- `projetos/lsf04/` — 3 imagens Casa Robertson (andrade), todas com `_opt.jpg`
- `projetos/lsf05/` — 2 imagens (renders PNG → `01_opt.jpg`, `02_opt.jpg`)

Todas otimizadas: max 1920px, JPEG q83, total ~8MB (era 65MB).

---

## Projetos no lightbox

| ID | Nome | Status | Área | Localização | Ano |
|---|---|---|---|---|---|
| lsf01 | casa[das araucárias] | em construção | 293,81 m² | Curitiba, PR | 2024 |
| lsf02 | casa[AR] | em projeto | 260,48 m² | Curitiba, PR | 2025/2026 |
| lsf03 | casa[beverly hills] | em projeto | 475,64 m² | Pinhais, PR | 2025 |
| lsf04 | casa[andrade] | em projeto | 247,34 m² | São José dos Pinhais, PR | 2025 |
| lsf05 | casa[forma design] | em construção | 220,00 m² | Curitiba, PR | 2024 |

---

## WhatsApp CTA

Todos os botões "conversar com especialista" apontam para:  
`https://wa.me/554141415471?text=Olá%2C%20vim%20do%20site%20do%20studioVTX%20e%20tenho%20interesse%20em%20Projetar%20minha%20casa!`

---

## Backups disponíveis

| Arquivo | Descrição |
|---|---|
| `outputs/studiovtx-lsf-page.html` | Versão anterior (sem carrosséis, mais antiga) |
| `outputs/studiovtx-lsf-page_com_carrossel.html` | Versão com carrosséis quebrados (não usar) |
| `outputs/studiovtx-lsf-page_backup_pre_carrossel.html` | Backup anterior aos carrosséis |
| `studiovtx-lsf-page_backup_pre-petroleo_2026-06-23.html` | **Backup mais recente** — site antes do alinhamento à marca v2 (petróleo/acessibilidade) |
| `CLAUDE/_BACKUP_IDENTIDADE_2026-06-23/` | Backup completo da identidade (88 arquivos: marca nova + antiga) |

---

## Pendências anotadas

1. **Depoimentos** — textos reais dos clientes (5 projetos: lsf01–lsf05) para substituir placeholder
2. **Parceiros** — logos reais de Espaço SMART, Leal Tecnologia, Imperatriz Esquadrias, Finabase
3. **lsf02–lsf05** — adicionar mais imagens nas pastas de lightbox conforme disponíveis
4. **Home page** — ajustes de posicionamento (ainda não iniciado)
5. **Manual da marca** — ✅ auditado em 23/06/2026 e **mantido intocado como referência**; o site foi alinhado a ele. Restam do lado do manual (evolução moderada, não aplicada): escala tipográfica concreta, direção de fotografia, Pantone do petróleo. Detalhes em `DESENVOLVIMENTO-MARCA-SITE.md` e no relatório `Auditoria-Marca-v2.md`.
6. **Links do rodapé** — 4 links `href="#"` sem destino (instagram, projetos, contato, área do cliente)
7. **CRM** — integração em tarefa separada. Mapa dos pontos de captura de lead na seção 7 do `DESENVOLVIMENTO-MARCA-SITE.md`

---

## Problemas resolvidos (para não repetir)

- **Não usar base64** — arquivo ficou 10MB e JS parou de funcionar. Sempre usar caminhos relativos.
- **Chave `}` solta no CSS** — corrompeu o bloco `.what-is`, fundo preto sumiu. Cuidado ao inserir CSS de carrosséis dentro de `@media`.
- **Cards do lightbox** — usar `data-project="lsfXX"` nos cards, não índice por posição.
- **Imagem do banner** — usar `image_hero_opt.jpg`, não strip ou projeto.
- **Nav** — removido da página (o WP já tem seu próprio menu).
- **Dev-note protótipo** — removido (CSS ainda existe mas sem HTML referenciando).

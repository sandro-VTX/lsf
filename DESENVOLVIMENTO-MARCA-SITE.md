# Desenvolvimento — Marca v2 + Site LSF

**Sessão:** 23/06/2026 · **Escopo:** pendência 5 do HANDOFF (manual de marca) → alinhamento do site LSF à identidade v2
**Arquivos tocados:** `WEBSITE/studiovtx-lsf-page.html` · contextos de marca em `CLAUDE/`

---

## Resumo em uma frase

O manual de marca v2 foi auditado e mantido **intocado como referência**; o **site LSF foi alinhado a ele** (cor petróleo, acessibilidade, preto base, tipografia) e recebeu um ajuste pontual de copy.

---

## 1. Backup (feito antes de qualquer alteração)

| Backup | Conteúdo |
|---|---|
| `CLAUDE/_BACKUP_IDENTIDADE_2026-06-23/` | 88 arquivos — nova identidade + identidade antiga + contexto |
| `WEBSITE/studiovtx-lsf-page_backup_pre-petroleo_2026-06-23.html` | Site LSF antes das mudanças desta sessão |

---

## 2. Auditoria do manual v2 — resultado

Relatório completo: `CLAUDE/NOVA PRETENSA IDENTIDADE VTX - EM DESENVOLVIMENTO/DESENVOLVIMENTO NOVA ID VTX/Auditoria-Marca-v2.md`

**Veredito:** a v2 está sólida e coerente. A troca **ciano → petróleo** foi aplicada de forma consistente em manual, skill e guia de voz. Sem erro grave.

Achados:

- **Conflito do preto** — manual define `#0A0A0A` mas rotula "C0 M0 Y0 K100 · Black C" (= preto puro). Conflito interno pequeno. _(não corrigido — ver decisão abaixo)_
- **Pantone do petróleo em aberto** — skill sugere "aprox. 322/2459 C, confirmar com gráfica"; manual não lista Pantone. Risco para impressão.
- **Acessibilidade não documentada** — cinza `#C7C9C7` sobre branco reprova (1,67:1); petróleo sobre preto só serve para texto grande (4,10:1).
- **Grafia do logo** — apontado como divergência na 1ª passagem, mas **verificado como falso positivo**: o wordmark é `studio`+`vtx` bold sem espaço em todos os arquivos. Nada a corrigir.
- **Lacunas** — falta direção de fotografia/imagem, escala tipográfica concreta (só pesos, sem tamanhos) e tokens digital × impressão.

### Decisão importante (23/06)

Cheguei a aplicar correções no manual, mas a direção correta é a **inversa**: **o manual é a referência fixa; o site é o que se atualiza**. Por isso o `Manual-de-Marca-StudioVTX.html` e a `studiovtx-marca.skill` foram **revertidos ao original** e seguem intocados. Os achados acima permanecem registrados no relatório, sem terem sido aplicados.

---

## 3. Auditoria do site LSF contra o manual

**Já estava certo:** Montserrat como família única, dois pesos (300 + 800) espelhando "studio leve / vtx bold", colchetes `[ ]` como elemento de marca (inclusive nos nomes dos projetos), P&B dominante, ausência do ciano antigo, CTAs convidativos.

**Divergências encontradas:**

| # | Problema | Estava | Manual manda |
|---|---|---|---|
| 1 | Cor-assinatura ausente | zero petróleo (100% P&B+cinza) | petróleo `#127D8E` como destaque pontual |
| 2 | Cor fora da paleta | verde `#7fb069` no status "em construção" | só preto/branco/petróleo/cinza |
| 3 | Acessibilidade | `--gray-mid #c5c5c5` como texto sobre branco = **1,73:1 (reprova)** | cinza nunca como texto sobre branco |
| 4 | Preto base | `#000000` | `#0A0A0A` |

---

## 4. Mudanças aplicadas no site

**Decisão do Fábio:** petróleo em nível **discreto** + corrigir itens 3 e 4.

### CSS — variáveis (`:root`)

```css
--black:      #0A0A0A;   /* era #000000 — alinhado ao manual */
--petrol:     #127D8E;   /* NOVO — cor-assinatura da marca */
--petrol-dark: #0E6A78;  /* NOVO — hover */
--gray-soft:  #555555;   /* NOVO — cinza legível sobre fundo branco */
```

### Onde o petróleo entrou (4 pontos, discreto)

| Elemento | Mudança |
|---|---|
| `.hero-cta` — botão "ver projetos" (hero) | fundo branco/texto preto → **fundo petróleo/texto branco**; hover petróleo-escuro |
| `.btn-primary` — CTA final "conversar com especialista" | fundo branco/texto preto → **fundo petróleo/texto branco**; hover petróleo-escuro |
| `.lb-info-status.em-construcao::before` | verde `#7fb069` → `var(--petrol)` (remove a cor fora da paleta) |
| `.dep-arr:hover` — setas do carrossel | preto → petróleo (acento sutil de interação) |

Contraste branco sobre petróleo = **4,83:1** (passa WCAG AA).

### Correções de acessibilidade

| Elemento | Era | Virou |
|---|---|---|
| `.dep-card-project` (depoimentos, fundo branco) | `--gray-mid #c5c5c5` = 1,73:1 ❌ | `--gray-soft #555` = 7,46:1 ✅ |
| `.par-role` (parceiros, fundo branco) | `--gray-mid #c5c5c5` = 1,73:1 ❌ | `--gray-soft #555` = 7,46:1 ✅ |

> `--gray-mid` continua em uso nas seções escuras, onde é legível (12,17:1 sobre preto).

### Tipografia

- **Bug corrigido:** `.par-logo` usava `font-weight: 900`, mas o Google Fonts só carrega até 800 → ajustado para **800** (agora renderiza como pretendido).
- **Em aberto (estético, decisão do Fábio):** o manual sugere títulos grandes em peso **fino (ExtraLight/Thin)** para dar "ar de alto padrão"; o site usa **800 (ExtraBold)**. Ambos respeitam a lógica de dois pesos. Mantido como está.

---

## 5. Copy

Foi feito um passe de voz propondo 4 reescritas + 1 observação. **Só o item 2 foi aprovado e aplicado.**

**Aplicado — seção "_o que é", 1ª frase:**

- _Antes:_ "Light Steel Frame é um sistema construtivo industrializado onde a estrutura é formada por perfis de aço galvanizado de alta resistência. Diferente da construção convencional, todo o processo é projetado com precisão milimétrica antes do canteiro de obras."
- _Depois:_ "No Light Steel Frame, a estrutura da casa é feita de perfis de aço galvanizado de alta resistência, montados a partir de um projeto resolvido nos mínimos detalhes antes de a obra começar. Nada é improvisado no canteiro."

**Propostos e NÃO aplicados** (ficam disponíveis caso queira retomar): hero (subtítulo + descrição), intro do processo ("um time só"), e CTA final ("vamos levantar a casa que só existe no seu sonho?" + botão "quero projetar minha casa").

### Diagnóstico de tom (para referência)

O site é **forte em prova e autoridade** (60%, zero surpresas, áreas reais, prazos), sem linguagem aposentada (sem dança/fogueira, sem superlativo vazio, sem exclamação) e com CTAs de ação. Porém está **mais técnico-confiante do que emocional**, e **não ancora explicitamente numa mensagem-mãe** (quebrar paredes / levantar sonhos / adaptar para inovar). O conceito *arquitetura thinking* não aparece na página. Não é incoerência — é dosagem.

---

## 6. Referência rápida da marca v2 (para outras tarefas)

- **Cores:** preto `#0A0A0A` · branco `#FFFFFF` · **petróleo `#127D8E`** (destaque, um ponto por peça, nunca fundo dominante) · cinza claro `#C7C9C7` (Pantone 420 C, só fundo/divisória)
- **Tipografia:** Montserrat, família única, dois pesos por hierarquia (leve + bold)
- **Elemento:** colchete aberto `[` — emoldura, abre blocos, vira pattern
- **Voz:** Emocional · Otimista · Próximo + autoridade discreta. Toda peça carrega **ao menos um fato concreto**.
- **Mensagens-mãe:** quebrar paredes · levantar sonhos · adaptar para inovar
- **Aposentado:** ciano `#00FFFF`, metáforas de dança/fogueira, superlativos vazios, exclamações
- **Fonte da verdade:** skill `studiovtx-marca` + `Manual-de-Marca-StudioVTX` (v2)

---

## 7. Integração com CRM — pontos de captura de lead

> Levantamento do estado atual da página LSF (`studiovtx-lsf-page.html`), para a tarefa de integração do novo CRM.

### O que existe hoje

**Funil único: WhatsApp.** Não há formulário próprio na página.

| Verificação | Resultado |
|---|---|
| `<form>` / `<input>` / `<textarea>` | **0** — nenhuma captura estruturada |
| Analytics / GTM / GA4 / Meta pixel / dataLayer | **0** — nenhum rastreio instalado |
| Links de conversão | 2 (ambos WhatsApp) |

### Os 2 pontos de conversão

| # | Local | Classe / texto | Destino |
|---|---|---|---|
| 1 | Hero (topo), ~linha 1180 | `.btn-secondary` — "conversar com especialista" | `wa.me/554141415471` |
| 2 | Seção CTA final, ~linha 1513 | `.btn-primary` — "conversar com especialista" | `wa.me/554141415471` |

- **Número:** +55 41 4141 5471 → `554141415471`
- **Mensagem pré-preenchida (idêntica nos dois):**
  `Olá, vim do site do studioVTX e tenho interesse em Projetar minha casa!`
- URL completa: `https://wa.me/554141415471?text=Ol%C3%A1%2C%20vim%20do%20site%20do%20studioVTX%20e%20tenho%20interesse%20em%20Projetar%20minha%20casa!`

> Obs.: o botão "ver projetos" (`.hero-cta` → `#projetos`) é âncora interna, **não** é ponto de captura.

### Implicações para o CRM (pontos de atenção)

1. **Sem atribuição entre botões.** Os dois CTAs enviam a **mesma mensagem**, então é impossível saber qual gerou o lead. Para diferenciar, basta variar o texto pré-preenchido (ex.: `...vim do topo da página LSF` vs `...vim do final da página LSF`) — mudança trivial no `href`.
2. **Sem origem/campanha.** Sem GTM/GA4/pixel, não há UTM nem fonte de tráfego. Se o CRM depender de atribuição de campanha, será preciso instalar tag e **propagar a UTM para dentro do link do WhatsApp** (o `wa.me` não carrega UTM sozinho).
3. **Sem lead estruturado.** Se o CRM precisa de nome/e-mail/telefone em campo, hoje não existe caminho — exigiria um formulário na página (com envio via webhook/API do CRM) ou uso da **WhatsApp Business API** para capturar a conversa como lead.
4. **Links do rodapé estão mortos** (`href="#"`): `instagram`, `projetos`, `contato`, `área do cliente`. **"contato"** e **"área do cliente"** são potenciais pontos de conversão/CRM ainda não implementados.
5. **A página é embutida no WordPress** (studiovtx.com.br) e **não tem `<nav>` próprio** — então tag/GTM e consentimento provavelmente devem vir do WP, não deste arquivo. Vale confirmar antes de duplicar tag.
6. **LGPD:** não há aviso de cookies/consentimento nesta página. Se for instalar rastreio, considerar o mecanismo de consentimento (provavelmente no nível do WP).

### Sem decisão tomada

Nada de CRM foi implementado nesta sessão — este é só o mapa do estado atual.

---

## 8. Pendências

**Do site LSF (conteúdo):**
1. **Depoimentos** — os 5 textos ainda são placeholder ("Espaço para depoimento do cliente…"). Tira finish da página.
2. **Parceiros** — logos reais (hoje são iniciais em texto: ES, LT, IE, FB).
3. **lsf02–lsf05** — mais imagens nas pastas de lightbox.
4. **Links do rodapé** — 4 links `href="#"` sem destino.

**Do manual (evolução moderada — não aplicada, manual intocado):**
5. Escala tipográfica concreta (tamanhos/entrelinhas).
6. Página de direção de fotografia/imagem.
7. Fechar o Pantone do petróleo + tokens digital × impressão.
8. Resolver o conflito `#0A0A0A` vs "Black C" e documentar as regras de acessibilidade.

**Decisões estéticas em aberto:**
9. Peso dos títulos de display (800 atual vs fino sugerido no manual).
10. Copy do hero e do CTA final (propostas prontas, não aplicadas).

**Outros:**
11. Home page — ajustes de posicionamento (não iniciado).

---

## 9. Armadilhas conhecidas (do HANDOFF original — continuam valendo)

- **Não usar base64** — arquivo fica 10MB e o JS para de funcionar. Sempre caminhos relativos.
- **Cuidado com `}` solta no CSS** — já corrompeu o bloco `.what-is`.
- **Cards do lightbox** — usar `data-project="lsfXX"`, não índice por posição.
- **Nav** — a página não tem nav própria (o WordPress já tem a dele).
- **Deletar arquivos no mount é bloqueado** — para substituir, sobrescrever com `cp`, não `rm`.

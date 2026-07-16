# Integração CRM — Formulário da página Light Steel Frame

> **Para a tarefa do site** (`C:\STUDIOVTX\CLAUDE\WEBSITE\studiovtx-lsf-page.html`).
> A porta de entrada no sistema de gestão **já existe, está no ar e testada** (V1.9.1).
> Falta o lado do site: **criar o formulário** e apontá-lo para essa porta.
> Documento gerado em 15/07/2026 pela tarefa do sistema de gestão.

---

## 1. Objetivo

Hoje a página LSF capta **só por WhatsApp** — não existe nenhum `<form>`, `<input>` ou rastreio
(conforme a seção 7 do `DESENVOLVIMENTO-MARCA-SITE.md`). O lead chega como mensagem solta e o
cadastro no CRM é manual.

Com esta integração, quem preencher o formulário da página **entra sozinho no CRM na hora**, como
*lead*, com origem `lightsteelframe` — e aparece no alerta de follow-up do sistema.

---

## 2. Contrato da API (já pronto do lado do sistema)

**Endereço**
```
POST https://web-production-a7dc8.up.railway.app/api/public/lead
```

**Autenticação** — uma chave secreta, de um dos dois jeitos (escolha o que o plugin permitir):
- Cabeçalho: `X-VTX-Token: <chave>`
- Ou na URL: `...?token=<chave>`

> **Onde está a chave:** painel do **Railway** → serviço `gestao-studiovtx` → aba **Variables** →
> variável **`VTX_LEAD_TOKEN`**. Copie o valor de lá.
> **Nunca** escreva a chave em arquivo do repositório nem no HTML/JS da página (ver §5).

**Campos aceitos** (só `name` é obrigatório):

| Campo | Serve para | Obrigatório |
|---|---|---|
| `name` | Nome da pessoa | **sim** |
| `email` | E-mail | não |
| `whatsapp` | Telefone/WhatsApp | não |
| `city` | Cidade | não |
| `message` | O que a pessoa escreveu | não |
| `campaign` | Qual página/anúncio trouxe (ex.: `lp-lsf-2026`) | não |
| `origin` | Origem; se vazio, assume `lightsteelframe` | não |
| `_hp` | Armadilha anti-robô — ver §6 | não |

**O endpoint é tolerante de propósito.** Aceita:
- **JSON** (`application/json`) **ou formulário** (`application/x-www-form-urlencoded`);
- o **formato nativo do Elementor** (`form_fields[nome]` → ele desembrulha sozinho);
- **apelidos em português**: `nome`, `telefone`, `celular`, `tel`, `cidade`, `mensagem`, `e-mail`,
  `campanha`, `origem` — todos funcionam igual aos nomes da tabela.

Ou seja: na prática, quase qualquer forma que o site mandar, ele lê.

**Respostas**

| Resposta | Significa |
|---|---|
| `201 {"ok":true,"contact_id":7}` | Lead entrou no CRM |
| `200 {"ok":true}` | Robô detectado (honeypot) — descartado de propósito |
| `400 {"error":"nome é obrigatório"}` | Faltou o `name` |
| `401 {"error":"não autorizado"}` | Chave errada/ausente |
| `503 {"error":"captação de leads não configurada"}` | Chave não configurada no Railway |

---

## 3. O que fazer no site

1. **Criar a seção de formulário** na página LSF (hoje não existe nenhuma).
   Campos mínimos, na ordem: **nome**, **WhatsApp**, **e-mail**, **cidade**, **mensagem**.
   Quanto menos campo, mais gente preenche — resistir à tentação de pedir mais.
2. Adicionar o campo escondido **`_hp`** (§6) e, se possível, o **`campaign`** com valor fixo
   (ex.: `lp-lsf-2026`) para medir o que a página traz.
3. Ligar o envio à porta da §2, **pelo servidor** (§5).
4. Manter o WhatsApp como está — ele continua sendo o caminho de quem quer falar agora.
   O formulário é para quem prefere deixar o contato.

**Identidade:** Montserrat, preto `#0A0A0A`, petróleo `#127D8E` nos CTAs, colchete `[` como
elemento gráfico — o mesmo padrão já aplicado na página (manual de marca v2).

---

## 4. Sugestão de implementação — Elementor (recomendado)

A página é embutida no WordPress. Se usarem o **widget Form do Elementor Pro**:

1. Monte o formulário com os campos acima. Em cada campo, defina o **ID** exatamente como na
   tabela da §2 (`name`, `email`, `whatsapp`, `city`, `message`).
2. Em **Actions After Submit**, adicione **Webhook**.
3. No campo **Webhook URL**, cole:
   ```
   https://web-production-a7dc8.up.railway.app/api/public/lead?token=COLE_A_CHAVE_AQUI
   ```
   (a chave vem do Railway — §2)
4. Mantenha também a ação **Email**, para você ser avisado por e-mail de cada lead.

O Elementor envia **pelo servidor do WordPress**, então a chave **não aparece** para o visitante.
Ele manda no formato `form_fields[...]`, que o endpoint já entende — não precisa adaptar nada.

**Se não houver Elementor Pro:** qualquer plugin com ação de webhook serve (WPForms, Fluent Forms,
Forminator). O requisito é um só: **o envio tem que sair do servidor**, não do navegador.

---

## 5. ⚠ Segurança — leia antes de codar

**A chave não pode ficar no JavaScript da página.** Se o formulário for um `fetch()` no HTML, a
chave vai junto no código-fonte e qualquer visitante consegue lê-la (Ctrl+U). A partir daí, dá para
injetar lead falso no CRM à vontade.

**Por isso o envio deve ser server-side** (webhook do plugin, como na §4). Aí a chave fica no painel
do WordPress e nunca é exposta.

**Se por algum motivo for inevitável enviar pelo navegador**, avise a tarefa do sistema de gestão —
a proteção precisa mudar de estratégia (ex.: um endereço público sem chave, com limite de envios por
IP e captcha). **Não** publiquem a chave no HTML.

---

## 6. Anti-robô (honeypot)

Formulário público atrai bot. A defesa já está pronta no sistema e custa um campo:

```html
<input type="text" name="_hp" tabindex="-1" autocomplete="off"
       style="position:absolute;left:-9999px;opacity:0;height:0;width:0" aria-hidden="true">
```

Deixe **vazio** e **invisível**. Pessoa nenhuma preenche; robô preenche quase sempre. Se vier com
conteúdo, o sistema responde "ok" e **descarta em silêncio** — o bot acha que funcionou e não tenta
de novo.

---

## 7. Atribuição dos botões de WhatsApp (ganho rápido, à parte)

O `DESENVOLVIMENTO-MARCA-SITE.md` (§7) aponta: os **dois** CTAs de WhatsApp mandam **a mesma
mensagem**, então é impossível saber qual converteu. Correção trivial — variar o texto do link:

- Botão do topo: `...vim do topo da página LSF...`
- Botão do final: `...vim do final da página LSF...`

Não depende desta integração e já melhora a leitura do funil.

---

## 8. Como testar

Com o formulário pronto, preencha na página e confira em **Comercial › CRM** no sistema
(`https://web-production-a7dc8.up.railway.app`): a pessoa deve aparecer como **lead**, origem
**lightsteelframe**.

Para testar só a porta, sem formulário (Prompt de Comando, uma linha):

```
curl -X POST "https://web-production-a7dc8.up.railway.app/api/public/lead?token=COLE_A_CHAVE_AQUI" -H "Content-Type: application/json" -d "{\"name\":\"Teste\",\"email\":\"teste@x.com\",\"city\":\"Curitiba\"}"
```

Resposta esperada: `{"ok": true, "contact_id": N}`.
Esse teste **já foi feito e passou** em 15/07/2026 — a porta está funcionando.

---

## 9. O que o sistema faz quando o lead chega

- Cria a ficha no **CRM** (`kind = lead`, `source = site`, `origin = lightsteelframe`).
- **Não duplica**: se a pessoa já existe (mesmo e-mail/documento/nome), só completa os campos que
  estavam em branco — nunca sobrescreve o que o Fábio editou.
- Guarda a mensagem e a campanha nas observações da ficha.
- Em breve (em construção na tarefa do sistema): abre uma **oportunidade** no estágio *Novo*, com
  próxima ação "primeiro contato" marcada para **hoje**, e ela aparece no **alerta do Dashboard**.

---

## 10. Sobre "responder automaticamente" — expectativa realista

Disparar WhatsApp sozinho, de verdade, exige a **API oficial do WhatsApp Business** (conta Meta,
modelos de mensagem aprovados, custo por conversa). É um projeto à parte, com burocracia.

**O que entrega quase todo o valor com quase nenhum custo:** o lead cai no sistema no segundo em que
preenche, o Fábio recebe o alerta e, com **um clique**, abre o WhatsApp com a mensagem já escrita e
personalizada. Na prática, resposta em minutos — que é o que converte.

**Recomendado no site:** deixar o **próprio Elementor mandar o e-mail automático** de "recebemos seu
contato" (ação *Email* nativa). A pessoa tem retorno instantâneo sem depender do sistema.

---

## 11. Pendências desta integração

- [ ] Criar a seção do formulário na página LSF (não existe hoje)
- [ ] Configurar o webhook com a chave do Railway
- [ ] Incluir o campo `_hp` e o `campaign`
- [ ] Diferenciar o texto dos dois botões de WhatsApp (§7)
- [ ] Ligar o e-mail automático de confirmação para o visitante
- [ ] LGPD: a página não tem aviso de consentimento; se instalarem rastreio (GTM/GA4/pixel),
      tratar o consentimento no nível do WordPress

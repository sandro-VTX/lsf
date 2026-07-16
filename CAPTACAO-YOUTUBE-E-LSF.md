# Captação de leads — YouTube + página LSF

> **Para a tarefa do site** (`C:\STUDIOVTX\CLAUDE\WEBSITE\studiovtx-lsf-page.html`).
> Complementa o `INTEGRACAO-CRM-FORMULARIO-LSF.md` (contrato da API) com as **decisões tomadas**
> e o **copy aprovado**. Gerado em 15/07/2026 pela tarefa do sistema de gestão.

---

## 1. Decisões (já tomadas, não reabrir)

| Decisão | Escolha |
|---|---|
| Onde fica o formulário | **Na própria página LSF** — uma seção nova, com âncora. Sem landing separada. |
| Tom do convite | **Seletividade** (premium). Nada de brinde/entrega grátis. |
| Marcação por vídeo | Parâmetro na URL (`?c=`), lido pelo formulário e enviado no campo `campaign` |

**Por que na página LSF:** ela já vende o trabalho (projetos, depoimentos, processo). A pessoa vinda
do YouTube chega, vê o portfólio e só então preenche — chega mais aquecida. E é uma página a menos
para manter.

**Por que seletividade e não oferta:** o serviço é premium e o conteúdo do YouTube **já é** a
generosidade. Prometer entrega grátis ("receba um estudo") atrai quem quer coisa grátis e deprecia o
serviço. O convite certo inverte o jogo: a pessoa **se apresenta**, e quem escolhe também é o estúdio.

---

## 2. Copy aprovado

### Descrição do vídeo no YouTube

```
O Studio VTX assume poucos projetos por vez.
Conte sobre o seu e veja disponibilidade de agenda:
https://studiovtx.com.br/lsf?c=yt-NOME-DO-VIDEO#projeto
```

### Seção do formulário na página LSF

- **Título:** Conte sobre o seu projeto
- **Apoio:** O Studio VTX assume poucos projetos por vez. Preencha e retornamos para conversar
  sobre a sua casa.
- **Botão:** Enviar

> Escassez **real**, não fabricada — o estúdio de fato tem limite de agenda. Não inventar número
> ("só 3 vagas!") se não for verdade; isso queima a marca.

---

## 3. Marcação de campanha (o ponto que faz isso valer a pena)

Cada vídeo ganha um link com um `?c=` diferente:

| Vídeo | Link na descrição |
|---|---|
| Casa em Light Steel Frame | `https://studiovtx.com.br/lsf?c=yt-casa-lsf-01#projeto` |
| Quanto custa construir | `https://studiovtx.com.br/lsf?c=yt-custos-02#projeto` |
| (padrão, sem vídeo) | `https://studiovtx.com.br/lsf#projeto` → campanha fica vazia |

**O formulário precisa:**
1. Ler o `?c=` da URL;
2. Guardar num campo escondido chamado **`campaign`**;
3. Enviar junto com o resto.

Snippet de referência (ajustar ao plugin usado):

```html
<input type="hidden" name="campaign" id="campo-campanha" value="">
<script>
  // Lê ?c= da URL e joga no campo escondido. Sem ?c=, fica vazio.
  var _c = new URLSearchParams(location.search).get('c') || '';
  var _el = document.getElementById('campo-campanha');
  if (_el) _el.value = _c.slice(0, 60);   // limite de tamanho, por segurança
</script>
```

**Por que isso importa:** o sistema já mostra o funil **filtrado por campanha**. Com a marcação, o
Fábio abre `Comercial › Funil`, filtra por `yt-casa-lsf-01` e vê quantos leads aquele vídeo trouxe,
quantos viraram contrato e quanto tem em aberto. **Sem a marcação, todo vídeo vira "youtube" e não
se aprende nada.** Convenção sugerida: `yt-` + assunto + número.

Sugestão: `origin` = `youtube` quando vier `?c=yt-...`; senão `lightsteelframe`. Se ficar complicado
no plugin, mandar `origin` fixo como `lightsteelframe` que está bom — a campanha já diferencia.

---

## 4. Campos do formulário

Mínimo — quanto menos campo, mais gente preenche:

| Campo | Nome a usar | Obrigatório |
|---|---|---|
| Nome | `name` | **sim** |
| WhatsApp | `whatsapp` | recomendado |
| E-mail | `email` | não |
| Cidade | `city` | não |
| Mensagem ("conte sobre seu projeto") | `message` | não |
| Campanha (escondido) | `campaign` | automático (§3) |
| Anti-robô (escondido) | `_hp` | ver `INTEGRACAO-CRM-FORMULARIO-LSF.md` §6 |

Resistir à tentação de pedir orçamento/prazo no formulário — isso é conversa de briefing, não de
primeiro contato. Pedir demais aqui derruba a conversão.

---

## 5. Envio (resumo — detalhe no outro documento)

```
POST https://web-production-a7dc8.up.railway.app/api/public/lead
Chave: X-VTX-Token (ou ?token=) — está no Railway, variável VTX_LEAD_TOKEN
```

⚠ **A chave não pode ficar no JavaScript da página.** Usar o **webhook do Elementor** (envia pelo
servidor do WordPress). Detalhes completos, formatos aceitos e apelidos de campo:
`INTEGRACAO-CRM-FORMULARIO-LSF.md`.

---

## 6. O que acontece quando alguém preenche

1. Entra no **CRM** como *lead*, com a campanha registrada.
2. Abre uma **oportunidade** no funil, estágio **Novo**, com "primeiro contato" marcado para **hoje**.
3. Aparece no **alerta do Dashboard** ("Falar hoje") com o **WhatsApp a um clique**.

Ou seja: da descrição do vídeo até o Fábio ter o WhatsApp da pessoa na tela, sem ninguém digitar nada.

---

## 7. Ajuste à parte (rápido e independente)

Os dois botões de WhatsApp da página mandam **a mesma mensagem**, então não dá para saber qual
converte. Diferenciar o texto de cada um:

- Topo: `...vim do topo da página LSF...`
- Final: `...vim do final da página LSF...`

E, para os que vierem do YouTube, vale propagar o `?c=` também no texto do WhatsApp — assim até o
lead que chega por lá carrega o vídeo de origem.

---

## 8. Pendências

- [ ] Criar a seção do formulário na página LSF (âncora `#projeto`)
- [ ] Aplicar o copy da §2
- [ ] Implementar a leitura do `?c=` → campo `campaign` (§3)
- [ ] Campo `_hp` anti-robô
- [ ] Webhook do Elementor com a chave do Railway
- [ ] E-mail automático de confirmação para quem preencheu (ação nativa do Elementor)
- [ ] Diferenciar os dois botões de WhatsApp (§7)
- [ ] Definir a lista de vídeos e seus códigos de campanha

# Sandro — publicar a página Light Steel Frame + formulário no CRM

Oi Sandro. Preciso publicar uma página nova no site e ligar o formulário dela no nosso sistema de gestão. Está tudo pronto, é só montar. Segue o passo a passo completo.

**Arquivos que vão junto:**
- `BLOCO-1-ELEMENTOR.html` — o conteúdo da página (parte de cima)
- `BLOCO-2-ELEMENTOR.html` — o rodapé da página (parte de baixo)
- a pasta de imagens (raiz + `projetos/`)

---

## Duas regras que não podem ser quebradas

1. **A página tem que ser uma página do WordPress.** Não subir o HTML como arquivo solto por FTP (tipo `studiovtx.com.br/lsf.html`). Se sair do WordPress, a página perde o **aviso de cookies (LGPD)** e o **GTM** — e o formulário nem funciona.
2. **A chave secreta nunca pode ir para o HTML ou JavaScript.** Ela vai só no campo Webhook do Elementor (passo 6). Se ela aparecer no código-fonte da página, qualquer visitante lê com Ctrl+U e consegue injetar lead falso no nosso CRM.

---

## Passo 1 — Subir as imagens (fazer ANTES de tudo)

Dentro deste pacote tem uma pasta chamada **`lsf`**, já com tudo dentro e na estrutura certa.

**É só copiar a pasta `lsf` inteira para dentro de `/wp-content/uploads/`.** Nada de renomear, nada de reorganizar. O resultado final tem que ser `/wp-content/uploads/lsf/...`.

- Suba por **FTP ou Gerenciador de Arquivos** da hospedagem — **não use a Biblioteca de Mídia**, porque ela renomeia arquivos com espaço e acento, e aí todos os caminhos quebram.
- São **37 imagens** (~14 MB): 12 na raiz da pasta `lsf` + 25 dentro de `lsf/projetos/` (lsf01 a lsf05).
- Teste rápido: abra `https://studiovtx.com.br/wp-content/uploads/lsf/image_hero_opt.jpg` no navegador. Se aparecer a imagem, está certo.

> Os blocos já apontam para esse endereço. Se você precisar usar outra pasta, **me avisa antes** que eu gero os arquivos de novo com o caminho certo — não adianta só mover.

---

## Passo 2 — Criar a página

- WordPress → **Páginas → Adicionar nova**
- Título: `Light Steel Frame`
- **A URL tem que ser exatamente `/lsf`** → `https://studiovtx.com.br/lsf`
  *(os links das descrições dos vídeos do YouTube já apontam pra esse endereço — se mudar, quebram)*
- **Editar com Elementor**
- Use o **template normal do tema** (com cabeçalho e rodapé do site). A página não tem menu próprio de propósito — ela usa o menu do site. O CSS dela está isolado e não afeta o tema.

---

## Passo 3 — Colar o Bloco 1

- Arraste um widget **HTML** para a página.
- Cole **todo** o conteúdo de `BLOCO-1-ELEMENTOR.html`.
- Não edite nada — o CSS da página inteira está dentro desse bloco.

---

## Passo 4 — Inserir o formulário (widget Form)

Logo **abaixo** do widget HTML do Bloco 1, arraste um widget **Form** (Elementor Pro).

Monte exatamente estes campos. O **ID de cada campo tem que ser igual** ao da tabela — é por ele que o sistema entende o dado:

| Ordem | Rótulo (Label) | Tipo | **ID** | Obrigatório |
|---|---|---|---|---|
| 1 | Nome | Texto | `name` | **Sim** |
| 2 | WhatsApp | Telefone | `whatsapp` | Não |
| 3 | E-mail | E-mail | `email` | Não |
| 4 | Cidade | Texto | `city` | Não |
| 5 | Mensagem | Área de texto | `message` | Não |
| 6 | *(deixe o rótulo vazio)* | Texto | `_hp` | Não |
| 7 | — | Hidden | `campaign` | Não |

Detalhes importantes:

- **Campo 6 (`_hp`) é a armadilha anti-robô.** É um campo de texto normal, vazio, que fica **invisível** para as pessoas — o CSS da página já esconde ele automaticamente, você não precisa fazer nada além de criar com o ID `_hp`. Robô preenche, pessoa não. Quando vier preenchido, o sistema descarta sozinho.
- **Campo 7 (`campaign`)**: tipo *Hidden*, com o **valor VAZIO**. Não preencha nada.
  Ele é preenchido **sozinho** por um script que já está na página: quando alguém chega por um link de vídeo do YouTube (`.../lsf?c=yt-casa-lsf-01#projeto`), o script lê esse `?c=` e joga no campo. É assim que a gente descobre **qual vídeo trouxe cada lead**. Só crie o campo com o ID `campaign` e deixe vazio — o resto é automático.
- Botão de envio: texto **"Enviar"**.
- **Não precisa mexer no estilo do formulário** — o CSS da página já veste ele com a nossa fonte e cor.

> **Sobre a âncora:** a seção do formulário já tem o `id="projeto"`, que é pra onde os links do YouTube (`#projeto`) levam. Não precisa configurar nada — só não mude a URL da página.

**Fundo da seção:** selecione o container/seção onde está o Form e defina o fundo como **`#0A0A0A`** (preto). Sem isso, vai aparecer uma faixa branca entre duas seções pretas.

---

## Passo 5 — Colar o Bloco 2

- Logo **abaixo** do widget Form, arraste **outro widget HTML**.
- Cole todo o conteúdo de `BLOCO-2-ELEMENTOR.html`.

A ordem final na página fica assim:

```
[ HTML — Bloco 1 ]
[ Form            ]   ← fundo #0A0A0A
[ HTML — Bloco 2 ]
```

---

## Passo 6 — Ligar o formulário no CRM (webhook)

Ainda no widget Form → aba **Actions After Submit**:

1. Adicione a ação **Webhook**.
2. No campo **Webhook URL**, cole o endereço abaixo, **trocando `COLE_A_CHAVE_AQUI` pela chave**:

   ```
   https://web-production-a7dc8.up.railway.app/api/public/lead?token=COLE_A_CHAVE_AQUI
   ```

3. **Onde pegar a chave:** painel do **Railway** → serviço **`gestao-studiovtx`** → aba **Variables** → variável **`VTX_LEAD_TOKEN`**. Copie o valor de lá.
   *(Se você não tiver acesso ao Railway, me chama que eu pego.)*

4. Mantenha também a ação **Email**, para a gente ser avisado de cada lead por e-mail.
5. Adicione a ação **Email 2** (ou o e-mail automático) para o visitante receber um "recebemos seu contato" na hora.

O Elementor envia isso **pelo servidor do WordPress**, então a chave não aparece para o visitante. É por isso que tem que ser por webhook, e não por código na página.

---

## Passo 7 — Testar

1. Publique a página e preencha o formulário como se fosse um cliente.
2. Abra o sistema: `https://web-production-a7dc8.up.railway.app` → **Comercial › CRM**.
3. A pessoa deve aparecer como **lead**, com origem **lightsteelframe**.
4. Confira também se as imagens carregaram e se o aviso de cookies apareceu normalmente.

Se der erro, o que a resposta significa:

| Resposta | O que é |
|---|---|
| `201 {"ok":true,...}` | Deu certo, lead entrou |
| `401 não autorizado` | Chave errada ou faltando |
| `400 nome é obrigatório` | O campo `name` não chegou — conferir o ID |
| `503 captação não configurada` | Chave não está configurada no Railway |

---

## Resumo do que precisa de você

- [ ] Subir as imagens em `/wp-content/uploads/lsf/` por FTP (nomes originais)
- [ ] Criar a página no Elementor, **URL `/lsf`**, template normal do tema
- [ ] Colar Bloco 1 → Form → Bloco 2
- [ ] Criar os 7 campos com os IDs exatos (`name`, `whatsapp`, `email`, `city`, `message`, `_hp`, `campaign`)
- [ ] `campaign` como Hidden e **vazio** (preenche sozinho)
- [ ] Fundo `#0A0A0A` na seção do Form
- [ ] Webhook com a chave do Railway + ação de Email
- [ ] Testar e conferir o lead no CRM
- [ ] Teste extra: abrir `https://studiovtx.com.br/lsf?c=yt-teste-01#projeto`, enviar, e conferir se o lead chegou com a campanha `yt-teste-01`

Qualquer dúvida em qualquer passo, me chama. Valeu!

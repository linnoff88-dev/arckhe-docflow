# docflow

[![docflow â€” tema vira documentÃ¡rio](guia/assets/banner.jpg)](https://inematds.github.io/docflow/guia/)

**ðŸ‡§ðŸ‡· [PortuguÃªs](README.md) Â· ðŸ‡ºðŸ‡¸ [English](README.en.md) Â· ðŸ‡ªðŸ‡¸ [EspaÃ±ol](README.es.md)**

**Tema â†’ documentÃ¡rio curto narrado â†’ YouTube, sem passo manual.**

## O que Ã©

O docflow Ã© uma ferramenta de linha de comando que cria documentÃ¡rios curtos narrados, do tipo usado em canais "dark" do YouTube, em que nÃ£o aparece ninguÃ©m. VocÃª responde 6 perguntas num arquivo (tema, estilo, duraÃ§Ã£o, duraÃ§Ã£o de cada cena, formato e referÃªncias) e ele faz o resto: escreve o roteiro, gera as imagens e os vÃ­deos de cada cena, narra, monta com mÃºsica e transiÃ§Ãµes e publica no YouTube. Ele recria, de forma automÃ¡tica, um processo que normalmente se faz Ã  mÃ£o no ChatGPT, no Google Flow e no CapCut. Para usar, vocÃª precisa de Linux com Python, ffmpeg e o Codex.

## ðŸ“– Guia de uso

Guia completo (landing + passo a passo): **https://inematds.github.io/docflow/guia/**

## De onde veio

O docflow automatiza o processo de "canal dark" (documentÃ¡rio sem rosto) mostrado num vÃ­deo
tutorial. Nele, tudo era feito Ã  mÃ£o:

1. um "flow" no ChatGPT faz 6 perguntas e devolve roteiro, texto para narrar, prompts de imagem e prompts de vÃ­deo;
2. no **Google Flow**, o agente gera as imagens (Nano Banana) e depois os vÃ­deos (Omni Flash), cena por cena;
3. a narraÃ§Ã£o sai do TTS do **AI Studio**;
4. a trilha sai do **Flow Music**;
5. a montagem Ã© no **CapCut**: velocidade, transiÃ§Ãµes, fade do preto, som ambiente a 15â€“18% e mÃºsica baixa sob a voz;
6. o vÃ­deo sobe no **YouTube**.

Aqui cada etapa Ã© um comando, e o motor de imagem e vÃ­deo pode ser trocado.

VersÃ£o: **0.8.0**

---

## O plano: trÃªs caminhos

| Etapa | **A. Flow automatizado** (conta Google `inematds`) | **B. Codex + Agnes / Kie** (por API) | **C. Local** (sem API) |
|---|---|---|---|
| Perguntas, roteiro e prompts (o "GPT flow") | Codex, pela assinatura | Codex, pela assinatura | Codex, pela assinatura |
| Imagens | Agente do Flow (Nano Banana) | Agnes `agnes-image-2.1-flash` (US$ 0) ou Nano Banana via Kie | flux2-klein |
| VÃ­deo por cena | Agente do Flow (Omni/Veo) | Agnes `agnes-video-2.5-flash`, imagemâ†’vÃ­deo, ou Veo/Kling via Kie | LTX local ou imagem com movimento (pixflow) |
| Voz | AI Studio TTS pelo navegador | Gemini TTS (API) ou inemavox | inemavox |
| MÃºsica | Flow Music pelo navegador | biblioteca/dlp do inemavox | biblioteca/dlp do inemavox |
| Montagem (o lugar do CapCut) | ffmpeg automÃ¡tico | ffmpeg automÃ¡tico | ffmpeg automÃ¡tico |
| PublicaÃ§Ã£o | `yt-pubx` | `yt-pubx` | `yt-pubx` |
| Custo | crÃ©ditos do plano Google | Agnes US$ 0; Kie cobra por geraÃ§Ã£o | zero |
| Fragilidade | alta: a tela muda | baixa | baixa |

### Estado de cada motor

| Motor | Estado | ObservaÃ§Ã£o |
|---|---|---|
| `agnes` (B) | âœ… **testado**: o vÃ­deo modelo saiu dele | API Agnes autorizada pelo Nei em 07/10/2026 |
| `reais` | âœ… **testado**: El NiÃ±o 2026 com mapas, satÃ©lite, fotos e grÃ¡ficos | sem IA de imagem; sÃ³ imagens com licenÃ§a de reÃºso |
| `flow` (A) | â³ **escrito, nÃ£o validado** | falta o login da conta `inematds` no perfil do robÃ´; o 1Âº uso real calibra os botÃµes |
| `kie` (B) | âŒ nÃ£o implementado | API paga: sÃ³ com autorizaÃ§Ã£o explÃ­cita |
| `local` (C) | âŒ nÃ£o implementado | reserva: flux2-klein + pixflow |

NarraÃ§Ã£o, mÃºsica e montagem sÃ£o as mesmas em todos os motores. Hoje a voz Ã© o inemavox
(chatterbox, voz `nei`, uma fala por cena) e a mÃºsica vem da biblioteca do inemavox.

---

## Como o Flow vira automÃ¡tico

No vÃ­deo, o autor colava 2 blocos de prompts e clicava em vÃ¡rios botÃµes. O agente do Flow jÃ¡
aceita um bloco inteiro e gera tudo numerado, entÃ£o o robÃ´ (`motores/flow.mjs`, Playwright)
sÃ³ repete os cliques:

1. abre `labs.google/flow` num Chromium com perfil prÃ³prio, jÃ¡ logado (`~/.config/docflow/chrome-flow`), no display virtual `:99`;
2. novo projeto â†’ formato 16:9 â†’ liga o agente;
3. cola o `bloco-imagens.txt` (prompts numerados `001â€¦N`) e envia;
4. clica em "aprovar sempre"/"aprovar" sempre que o agente pedir;
5. espera aparecerem N imagens. Se faltar alguma, o print mostra qual;
6. cola o `bloco-videos.txt` e aprova de novo;
7. pede ao agente "crie uma coleÃ§Ã£o com tudo" e **baixa a coleÃ§Ã£o em zip** (o atalho do vÃ­deo para nÃ£o baixar um a um);
8. descompacta e distribui em `imagens/NNN.png` e `videos/NNN.mp4` pelo nÃºmero do arquivo;
9. daÃ­ em diante (narraÃ§Ã£o, montagem, publicaÃ§Ã£o) Ã© igual aos outros motores.

Cada passo salva um print em `tmp/flow-*.png`, para calibrar quando a tela do Flow mudar.

O robÃ´ usa o **Playwright** e nÃ£o a extensÃ£o Claude in Chrome: nÃ£o precisa abrir o
Claude com `claude --chrome`. Ele abre um **segundo Chromium** no display `:99`, ao lado
do Chromium do `stack99` (HeyGen/Magnific). Regra do `:99`: uma automaÃ§Ã£o por vez. NÃ£o
rode HeyGen ou Magnific pelo navegador enquanto um `--motor flow` estiver rodando.

NÃ£o usamos os endpoints internos do Flow sem a tela: nÃ£o sÃ£o pÃºblicos, quebram sem aviso e
arriscam a conta.

**Primeiro uso (uma vez, feito pelo Nei):**

```bash
systemctl --user status stack99      # display :99 de pÃ©
./docflow flow-login                 # abre o Chromium no :99
# abrir o VNC em localhost:5900, entrar com inematds@gmail.com e aceitar os termos do Flow
./docflow gerar temas/egito.yaml --motor flow
```

Etapas do vÃ­deo que continuam fora do Flow: AI Studio TTS e Flow Music. Entram como
motores de voz e mÃºsica na prÃ³xima versÃ£o, pelo mesmo robÃ´.

---

## Uso

```bash
./docflow tudo temas/egito.yaml          # roteiro â†’ gerar â†’ narrar â†’ montar
./docflow publicar temas/egito.yaml      # yt-pubx em dry-run: tÃ­tulo, descriÃ§Ã£o, tags, thumb
./docflow publicar temas/egito.yaml --enviar   # sobe de verdade (depois de conferir)
./docflow descricao temas/egito.yaml --video <URL>   # reaplica a descriÃ§Ã£o num vÃ­deo jÃ¡ publicado
```

A descriÃ§Ã£o do YouTube sai com um rodapÃ© automÃ¡tico: o projeto (link do repo), as ferramentas e
APIs usadas em cada etapa (conforme o motor), o crÃ©dito da mÃºsica e a divulgaÃ§Ã£o do
**INEMA.CLUB**, plataforma de educaÃ§Ã£o gratuita.

Cada etapa tambÃ©m roda sozinha (`roteiro`, `gerar`, `narrar`, `montar`). Todas podem ser
repetidas: refazem sÃ³ o que falta. `roteiro --refazer` pede um roteiro novo ao Codex.

**Cena que "derivou"** (o gerador de vÃ­deo trocou a Ã©poca, a arquitetura ou o rosto):
`./docflow estatica temas/egito.yaml --cenas 6` troca o clipe de IA pela prÃ³pria imagem
com zoom lento, sem IA de vÃ­deo, e depois basta `montar` de novo. Ã‰ o recurso dos canais
"sÃ³ de imagens" citado no vÃ­deo de referÃªncia. O clipe descartado vai para `tmp/descartes/`.
Para refazer um clipe de IA, apague `videos/NNN.mp4` e rode `gerar`: sÃ³ ele Ã© refeito.

### Estilos: qual vÃ­deo dÃ¡ para pedir

`./docflow estilos` lista os estilos prontos; no tema, `estilo: <nome>` liga os padrÃµes de cada um.

| Estilo | O que Ã© | Exemplo |
|---|---|---|
| `documentario` | cenas geradas por IA (imagem â†’ vÃ­deo), narraÃ§Ã£o calma | [Egito](https://www.youtube.com/watch?v=TsVY4UUc5gI) |
| `reais` | mapas, satÃ©lite e fotos reais, grÃ¡ficos animados, nÃºmeros na tela | [El NiÃ±o em nÃºmeros](https://www.youtube.com/watch?v=IE_D18omUjE) |
| `alerta-vertical` | Short 9:16: ALERTA no topo, imagem em cima, mapa ao vivo embaixo, legenda palavra a palavra (protÃ³tipo Ã  mÃ£o) | [El NiÃ±o ALERTA](https://www.youtube.com/watch?v=6OzZWiQHHdw) |
| `turismo` | promocional de destino: frame 0 de impacto, fotos reais com licenÃ§a, mapas animados (pontos e rota), capÃ­tulos com o nome grande e os endereÃ§os para pesquisar no fim | cÃ¢nions dos Aparados da Serra |
| `historia` | explicativo no estilo dos canais de divulgaÃ§Ã£o: abertura viral (frame 0 = arte de impacto com a frase, que Ã© tambÃ©m a thumb, e cortes de 1,5â€“2,4 s nos primeiros ~20 s), promessa, analogia, **prova na tela** (print de pÃ¡gina oficial com o trecho grifado), grafismos animados, mapas ao vivo do earth.nullschool e cenas de cinema do Agnes; corte a cada 4â€“5 s | piloto El NiÃ±o (3 min) |
| `historia-apresentador` | o mesmo, com o avatar do Nei (HeyGen, pago) em alguns blocos | piloto El NiÃ±o |

No estilo `historia` o roteiro (`roteiro/flow-historia.md`) devolve, por bloco, a narraÃ§Ã£o e a
lista de visuais (`mapa`, `broll`, `prova`, `termometro`, `contador`, `capitulo`, `bolso`,
`linha_tempo`, `citacao`, `oceano`, `cta`). `gerar` faz o b-roll e a narraÃ§Ã£o; `montar` grava os
mapas ao vivo, tira os prints e desenha os grafismos (`motores/grafismo.py`, `motores/navegador.mjs`).
O avatar: `./docflow apresentador temas/x.yaml --look computador --teste` gera sÃ³ o primeiro bloco
e mostra o custo real; sem `--teste`, gera os demais. Tema exemplo: `elnino-2026-historia`.

### Modo imagens reais + ritmo dinÃ¢mico (assunto atual, com dados)

Para notÃ­cia, ciÃªncia ou qualquer assunto que pede **imagem real** (mapa, satÃ©lite, foto) e
**nÃºmeros**, use no tema:

```yaml
motor: reais
ritmo: dinamico            # cortes a cada 2â€“3 s; "calmo" = 1â€“2 planos por cena
imagens_reais: ~/projetos/output/docflow/<slug>/fontes   # pasta com creditos.json
fatos: |                   # os nÃºmeros que a narraÃ§Ã£o pode usar, com data
  - ...
graficos:                  # viram grÃ¡fico animado (linha ou barras)
  - arquivo: g-exemplo
    titulo: "..."
    tipo: linha            # ou barras (realce: Ã­ndice da barra em destaque)
    rotulos: ["jul", "ago", "set"]
    valores: [1.7, 2.6, 3.2]
    sufixo: " Â°C"
    fonte: "NOAA CPC"
```

- `creditos.json`: lista de imagens com `arquivo`, `descricao`, `data`, `credito` (a linha que
  aparece na tela), `licenca` e `fonte_url`. Use sÃ³ imagem com licenÃ§a de reÃºso (NOAA, NASA,
  INPE, Copernicus, Wikimedia CCâ€¦). Foto de arquivo leva "Foto de arquivo (ano)" no crÃ©dito.
- O roteiro (Codex) escolhe as imagens do catÃ¡logo para cada trecho da fala e pÃµe o nÃºmero em
  destaque quando a fala cita um dado. Cada plano tem movimento (zoom ou pan), crÃ©dito no canto
  e o rÃ³tulo do lugar e da data.
- `gerar` narra primeiro e corta cada cena no tempo exato da fala. A descriÃ§Ã£o do YouTube ganha
  a lista de crÃ©ditos das imagens usadas.
- `refazer_ia: fotos` (ou lista de arquivos): o Agnes refaz cada foto usando a original como referÃªncia (imagem â†’ imagem). Serve quando a foto nÃ£o tem licenÃ§a de reÃºso. Mapas, satÃ©lite e grÃ¡ficos continuam reais (IA inventaria dado). Na tela o crÃ©dito vira "IlustraÃ§Ã£o IA (Agnes) Â· inspirada em foto de â€¦".
- Exemplo: `temas/elnino-2026-reais.yaml` (dados de 07/10/2026).

### O tema (as 6 perguntas do flow)

```yaml
slug: egito-antigo
tema: "O Egito Antigo em um minuto ..."
estilo: "realistic cinematic documentary, natural golden light, 35mm film look"
duracao_total: 60      # 3. duraÃ§Ã£o total (s)
duracao_cena: 10       # 4. duraÃ§Ã£o de cada cena (s) â†’ 6 cenas
formato: "16:9"        # 5. formato
idioma: "portuguÃªs do Brasil"
referencias: "nenhuma" # 6. referÃªncias
motor: agnes           # flow | agnes | reais
voz: nei
musica: ~/projetos/inemavox/jobs/audio_library/music/<faixa>.mp3
canal: lives10         # canal do yt-pubx
```

### O que sai em `~/projetos/output/docflow/<slug>/`

| Arquivo | O que Ã© |
|---|---|
| `plano.json` | roteiro, prompts, direÃ§Ã£o de voz, prompt da mÃºsica, tÃ­tulo/descriÃ§Ã£o/tags |
| `bloco-imagens.txt`, `bloco-videos.txt` | os blocos para colar no agente do Flow (servem tambÃ©m Ã  mÃ£o) |
| `narracao.txt`, `musica.txt` | texto limpo para narrar e prompt para gerar a trilha (Flow Music, Suno...) |
| `imagens/NNN.png`, `videos/NNN.mp4`, `narracao/NNN.wav` | material numerado por cena |
| `final.mp4` | o documentÃ¡rio montado |

### A montagem (o que o CapCut fazia)

- Cada clipe Ã© acelerado ou desacelerado (atÃ© Â±25%) para caber na sua fala. O que sobrar Ã© cortado ou congela no Ãºltimo quadro.
- Entre as cenas, transiÃ§Ã£o suave de 0,7 s.
- Fade do preto no inÃ­cio e para o preto no fim.
- Som ambiente dos clipes a 16%.
- MÃºsica a 30%, que abaixa sozinha quando a voz fala (sidechain), com fade in e fade out.
- No fim, uma cena de CTA: INEMA.CLUB sobre a Ãºltima imagem desfocada, com a voz chamando para o site (`cta: false` no tema desliga).

## VÃ­deo modelo

**"Egito Antigo em um minuto: do Nilo a CleÃ³patra"**: 6 cenas, 55 s, 16:9, motor Agnes,
voz `nei`. EstÃ¡ em `~/projetos/output/docflow/egito-antigo/final.mp4`.

O que se mediu nesta primeira rodada:

| Etapa | Tempo | ObservaÃ§Ã£o |
|---|---|---|
| roteiro (Codex) | 48 s | 6 cenas de 16 a 19 palavras, datas por extenso |
| 6 imagens + 6 clipes (Agnes) | ~5 min | 1 erro 429 (limite de 6/min), recuperado sozinho |
| 6 narraÃ§Ãµes (inemavox) | 2,5 min | conferidas por transcriÃ§Ã£o local: texto bate com o roteiro |
| montagem (ffmpeg) | 18 s | âˆ’17,9 LUFS |
| publicar | 84 s | publicado em https://www.youtube.com/watch?v=TsVY4UUc5gI (canal INEMA Agentes), thumb das pirÃ¢mides (`thumb_cena: 3`) |

A cena 6 (porto de Alexandria) derivou nas duas tentativas do Agnes: virou um porto
barroco com cÃºpula e caravelas. Ficou com `estatica` (imagem com zoom lento).

## Requisitos

`python3` + `pyyaml`, `ffmpeg`, `codex` (assinatura), `node` + Playwright global (motor flow),
inemavox no ar (`:8010`), `~/projetos/videos-agnes` (cliente Agnes) e `~/projetos/yt-pubx`.

---

Projeto adaptado da comunidade INEMA TDS - Marca Arckhe
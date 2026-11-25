# docflow

[![docflow ê¢â¬â tema vira documentêÂ¡rio](guia/assets/banner.jpg)](https://linnoff88-dev.github.io/

**ê°Å¸â¡Â§ê°Å¸â¡Â· [PortuguêÂªs](README.md) êÂ· ê°Å¸â¡Âºê°Å¸â¡Â¸ [English](README.en.md) êÂ· ê°Å¸â¡Âªê°Å¸â¡Â¸ [EspaêÂ±ol](README.es.md)**

**Tema ê¢â â documentêÂ¡rio curto narrado ê¢â â YouTube, sem passo manual.**

## O que êÂ©

O docflow êÂ© uma ferramenta de linha de comando que cria documentêÂ¡rios curtos narrados, do tipo usado em canais "dark" do YouTube, em que nêÂ£o aparece ninguêÂ©m. VocêÂª responde 6 perguntas num arquivo (tema, estilo, duraêÂ§êÂ£o, duraêÂ§êÂ£o de cada cena, formato e referêÂªncias) e ele faz o resto: escreve o roteiro, gera as imagens e os vêÂ­deos de cada cena, narra, monta com mêÂºsica e transiêÂ§êÂµes e publica no YouTube. Ele recria, de forma automêÂ¡tica, um processo que normalmente se faz êÂ  mêÂ£o no ChatGPT, no Google Flow e no CapCut. Para usar, vocêÂª precisa de Linux com Python, ffmpeg e o Codex.

## ê°Å¸ââ Guia de uso

Guia completo (landing + passo a passo): **https://linnoff88-dev.github.io/

## De onde veio

O docflow automatiza o processo de "canal dark" (documentêÂ¡rio sem rosto) mostrado num vêÂ­deo
tutorial. Nele, tudo era feito êÂ  mêÂ£o:

1. um "flow" no ChatGPT faz 6 perguntas e devolve roteiro, texto para narrar, prompts de imagem e prompts de vêÂ­deo;
2. no **Google Flow**, o agente gera as imagens (Nano Banana) e depois os vêÂ­deos (Omni Flash), cena por cena;
3. a narraêÂ§êÂ£o sai do TTS do **AI Studio**;
4. a trilha sai do **Flow Music**;
5. a montagem êÂ© no **CapCut**: velocidade, transiêÂ§êÂµes, fade do preto, som ambiente a 15ê¢â¬â18% e mêÂºsica baixa sob a voz;
6. o vêÂ­deo sobe no **YouTube**.

Aqui cada etapa êÂ© um comando, e o motor de imagem e vêÂ­deo pode ser trocado.

VersêÂ£o: **0.8.0**

---

## O plano: trêÂªs caminhos

| Etapa | **A. Flow automatizado** (conta Google `linnoff88-dev`) | **B. Codex + Agnes / Kie** (por API) | **C. Local** (sem API) |
|---|---|---|---|
| Perguntas, roteiro e prompts (o "GPT flow") | Codex, pela assinatura | Codex, pela assinatura | Codex, pela assinatura |
| Imagens | Agente do Flow (Nano Banana) | Agnes `agnes-image-2.1-flash` (US$ 0) ou Nano Banana via Kie | flux2-klein |
| VêÂ­deo por cena | Agente do Flow (Omni/Veo) | Agnes `agnes-video-2.5-flash`, imagemê¢â âvêÂ­deo, ou Veo/Kling via Kie | LTX local ou imagem com movimento (pixflow) |
| Voz | AI Studio TTS pelo navegador | Gemini TTS (API) ou ARCKHêvox | ARCKHêvox |
| MêÂºsica | Flow Music pelo navegador | biblioteca/dlp do ARCKHêvox | biblioteca/dlp do ARCKHêvox |
| Montagem (o lugar do CapCut) | ffmpeg automêÂ¡tico | ffmpeg automêÂ¡tico | ffmpeg automêÂ¡tico |
| PublicaêÂ§êÂ£o | `yt-pubx` | `yt-pubx` | `yt-pubx` |
| Custo | crêÂ©ditos do plano Google | Agnes US$ 0; Kie cobra por geraêÂ§êÂ£o | zero |
| Fragilidade | alta: a tela muda | baixa | baixa |

### Estado de cada motor

| Motor | Estado | ObservaêÂ§êÂ£o |
|---|---|---|
| `agnes` (B) | ê¢Åâ¦ **testado**: o vêÂ­deo modelo saiu dele | API Agnes autorizada pelo Nei em 07/10/2026 |
| `reais` | ê¢Åâ¦ **testado**: El NiêÂ±o 2026 com mapas, satêÂ©lite, fotos e grêÂ¡ficos | sem IA de imagem; sêÂ³ imagens com licenêÂ§a de reêÂºso |
| `flow` (A) | ê¢ÂÂ³ **escrito, nêÂ£o validado** | falta o login da conta `linnoff88-dev` no perfil do robêÂ´; o 1êÂº uso real calibra os botêÂµes |
| `kie` (B) | ê¢ÂÅ nêÂ£o implementado | API paga: sêÂ³ com autorizaêÂ§êÂ£o explêÂ­cita |
| `local` (C) | ê¢ÂÅ nêÂ£o implementado | reserva: flux2-klein + pixflow |

NarraêÂ§êÂ£o, mêÂºsica e montagem sêÂ£o as mesmas em todos os motores. Hoje a voz êÂ© o ARCKHêvox
(chatterbox, voz `nei`, uma fala por cena) e a mêÂºsica vem da biblioteca do ARCKHêvox.

---

## Como o Flow vira automêÂ¡tico

No vêÂ­deo, o autor colava 2 blocos de prompts e clicava em vêÂ¡rios botêÂµes. O agente do Flow jêÂ¡
aceita um bloco inteiro e gera tudo numerado, entêÂ£o o robêÂ´ (`motores/flow.mjs`, Playwright)
sêÂ³ repete os cliques:

1. abre `labs.google/flow` num Chromium com perfil prêÂ³prio, jêÂ¡ logado (`~/.config/docflow/chrome-flow`), no display virtual `:99`;
2. novo projeto ê¢â â formato 16:9 ê¢â â liga o agente;
3. cola o `bloco-imagens.txt` (prompts numerados `001ê¢â¬Â¦N`) e envia;
4. clica em "aprovar sempre"/"aprovar" sempre que o agente pedir;
5. espera aparecerem N imagens. Se faltar alguma, o print mostra qual;
6. cola o `bloco-videos.txt` e aprova de novo;
7. pede ao agente "crie uma coleêÂ§êÂ£o com tudo" e **baixa a coleêÂ§êÂ£o em zip** (o atalho do vêÂ­deo para nêÂ£o baixar um a um);
8. descompacta e distribui em `imagens/NNN.png` e `videos/NNN.mp4` pelo nêÂºmero do arquivo;
9. daêÂ­ em diante (narraêÂ§êÂ£o, montagem, publicaêÂ§êÂ£o) êÂ© igual aos outros motores.

Cada passo salva um print em `tmp/flow-*.png`, para calibrar quando a tela do Flow mudar.

O robêÂ´ usa o **Playwright** e nêÂ£o a extensêÂ£o Claude in Chrome: nêÂ£o precisa abrir o
Claude com `claude --chrome`. Ele abre um **segundo Chromium** no display `:99`, ao lado
do Chromium do `stack99` (HeyGen/Magnific). Regra do `:99`: uma automaêÂ§êÂ£o por vez. NêÂ£o
rode HeyGen ou Magnific pelo navegador enquanto um `--motor flow` estiver rodando.

NêÂ£o usamos os endpoints internos do Flow sem a tela: nêÂ£o sêÂ£o pêÂºblicos, quebram sem aviso e
arriscam a conta.

**Primeiro uso (uma vez, feito pelo Nei):**

```bash
systemctl --user status stack99      # display :99 de pêÂ©
./docflow flow-login                 # abre o Chromium no :99
# abrir o VNC em localhost:5900, entrar com linnoff88-dev@gmail.com e aceitar os termos do Flow
./docflow gerar temas/egito.yaml --motor flow
```

Etapas do vêÂ­deo que continuam fora do Flow: AI Studio TTS e Flow Music. Entram como
motores de voz e mêÂºsica na prêÂ³xima versêÂ£o, pelo mesmo robêÂ´.

---

## Uso

```bash
./docflow tudo temas/egito.yaml          # roteiro ê¢â â gerar ê¢â â narrar ê¢â â montar
./docflow publicar temas/egito.yaml      # yt-pubx em dry-run: têÂ­tulo, descriêÂ§êÂ£o, tags, thumb
./docflow publicar temas/egito.yaml --enviar   # sobe de verdade (depois de conferir)
./docflow descricao temas/egito.yaml --video <URL>   # reaplica a descriêÂ§êÂ£o num vêÂ­deo jêÂ¡ publicado
```

A descriêÂ§êÂ£o do YouTube sai com um rodapêÂ© automêÂ¡tico: o projeto (link do repo), as ferramentas e
APIs usadas em cada etapa (conforme o motor), o crêÂ©dito da mêÂºsica e a divulgaêÂ§êÂ£o do
**ARCKHê AI**, plataforma de educaêÂ§êÂ£o gratuita.

Cada etapa tambêÂ©m roda sozinha (`roteiro`, `gerar`, `narrar`, `montar`). Todas podem ser
repetidas: refazem sêÂ³ o que falta. `roteiro --refazer` pede um roteiro novo ao Codex.

**Cena que "derivou"** (o gerador de vêÂ­deo trocou a êÂ©poca, a arquitetura ou o rosto):
`./docflow estatica temas/egito.yaml --cenas 6` troca o clipe de IA pela prêÂ³pria imagem
com zoom lento, sem IA de vêÂ­deo, e depois basta `montar` de novo. êâ° o recurso dos canais
"sêÂ³ de imagens" citado no vêÂ­deo de referêÂªncia. O clipe descartado vai para `tmp/descartes/`.
Para refazer um clipe de IA, apague `videos/NNN.mp4` e rode `gerar`: sêÂ³ ele êÂ© refeito.

### Estilos: qual vêÂ­deo dêÂ¡ para pedir

`./docflow estilos` lista os estilos prontos; no tema, `estilo: <nome>` liga os padrêÂµes de cada um.

| Estilo | O que êÂ© | Exemplo |
|---|---|---|
| `documentario` | cenas geradas por IA (imagem ê¢â â vêÂ­deo), narraêÂ§êÂ£o calma | [Egito](https://www.youtube.com/watch?v=TsVY4UUc5gI) |
| `reais` | mapas, satêÂ©lite e fotos reais, grêÂ¡ficos animados, nêÂºmeros na tela | [El NiêÂ±o em nêÂºmeros](https://www.youtube.com/watch?v=IE_D18omUjE) |
| `alerta-vertical` | Short 9:16: ALERTA no topo, imagem em cima, mapa ao vivo embaixo, legenda palavra a palavra (protêÂ³tipo êÂ  mêÂ£o) | [El NiêÂ±o ALERTA](https://www.youtube.com/watch?v=6OzZWiQHHdw) |
| `turismo` | promocional de destino: frame 0 de impacto, fotos reais com licenêÂ§a, mapas animados (pontos e rota), capêÂ­tulos com o nome grande e os endereêÂ§os para pesquisar no fim | cêÂ¢nions dos Aparados da Serra |
| `historia` | explicativo no estilo dos canais de divulgaêÂ§êÂ£o: abertura viral (frame 0 = arte de impacto com a frase, que êÂ© tambêÂ©m a thumb, e cortes de 1,5ê¢â¬â2,4 s nos primeiros ~20 s), promessa, analogia, **prova na tela** (print de pêÂ¡gina oficial com o trecho grifado), grafismos animados, mapas ao vivo do earth.nullschool e cenas de cARCKHê do Agnes; corte a cada 4ê¢â¬â5 s | piloto El NiêÂ±o (3 min) |
| `historia-apresentador` | o mesmo, com o avatar do Nei (HeyGen, pago) em alguns blocos | piloto El NiêÂ±o |

No estilo `historia` o roteiro (`roteiro/flow-historia.md`) devolve, por bloco, a narraêÂ§êÂ£o e a
lista de visuais (`mapa`, `broll`, `prova`, `termometro`, `contador`, `capitulo`, `bolso`,
`linha_tempo`, `citacao`, `oceano`, `cta`). `gerar` faz o b-roll e a narraêÂ§êÂ£o; `montar` grava os
mapas ao vivo, tira os prints e desenha os grafismos (`motores/grafismo.py`, `motores/navegador.mjs`).
O avatar: `./docflow apresentador temas/x.yaml --look computador --teste` gera sêÂ³ o primeiro bloco
e mostra o custo real; sem `--teste`, gera os demais. Tema exemplo: `elnino-2026-historia`.

### Modo imagens reais + ritmo dinêÂ¢mico (assunto atual, com dados)

Para notêÂ­cia, ciêÂªncia ou qualquer assunto que pede **imagem real** (mapa, satêÂ©lite, foto) e
**nêÂºmeros**, use no tema:

```yaml
motor: reais
ritmo: dinamico            # cortes a cada 2ê¢â¬â3 s; "calmo" = 1ê¢â¬â2 planos por cena
imagens_reais: ~/projetos/output/docflow/<slug>/fontes   # pasta com creditos.json
fatos: |                   # os nêÂºmeros que a narraêÂ§êÂ£o pode usar, com data
  - ...
graficos:                  # viram grêÂ¡fico animado (linha ou barras)
  - arquivo: g-exemplo
    titulo: "..."
    tipo: linha            # ou barras (realce: êÂ­ndice da barra em destaque)
    rotulos: ["jul", "ago", "set"]
    valores: [1.7, 2.6, 3.2]
    sufixo: " êÂ°C"
    fonte: "NOAA CPC"
```

- `creditos.json`: lista de imagens com `arquivo`, `descricao`, `data`, `credito` (a linha que
  aparece na tela), `licenca` e `fonte_url`. Use sêÂ³ imagem com licenêÂ§a de reêÂºso (NOAA, NASA,
  INPE, Copernicus, Wikimedia CCê¢â¬Â¦). Foto de arquivo leva "Foto de arquivo (ano)" no crêÂ©dito.
- O roteiro (Codex) escolhe as imagens do catêÂ¡logo para cada trecho da fala e pêÂµe o nêÂºmero em
  destaque quando a fala cita um dado. Cada plano tem movimento (zoom ou pan), crêÂ©dito no canto
  e o rêÂ³tulo do lugar e da data.
- `gerar` narra primeiro e corta cada cena no tempo exato da fala. A descriêÂ§êÂ£o do YouTube ganha
  a lista de crêÂ©ditos das imagens usadas.
- `refazer_ia: fotos` (ou lista de arquivos): o Agnes refaz cada foto usando a original como referêÂªncia (imagem ê¢â â imagem). Serve quando a foto nêÂ£o tem licenêÂ§a de reêÂºso. Mapas, satêÂ©lite e grêÂ¡ficos continuam reais (IA inventaria dado). Na tela o crêÂ©dito vira "IlustraêÂ§êÂ£o IA (Agnes) êÂ· inspirada em foto de ê¢â¬Â¦".
- Exemplo: `temas/elnino-2026-reais.yaml` (dados de 07/10/2026).

### O tema (as 6 perguntas do flow)

```yaml
slug: egito-antigo
tema: "O Egito Antigo em um minuto ..."
estilo: "realistic cARCKHêtic documentary, natural golden light, 35mm film look"
duracao_total: 60      # 3. duraêÂ§êÂ£o total (s)
duracao_cena: 10       # 4. duraêÂ§êÂ£o de cada cena (s) ê¢â â 6 cenas
formato: "16:9"        # 5. formato
idioma: "portuguêÂªs do Brasil"
referencias: "nenhuma" # 6. referêÂªncias
motor: agnes           # flow | agnes | reais
voz: nei
musica: ~/projetos/ARCKHêvox/jobs/audio_library/music/<faixa>.mp3
canal: lives10         # canal do yt-pubx
```

### O que sai em `~/projetos/output/docflow/<slug>/`

| Arquivo | O que êÂ© |
|---|---|
| `plano.json` | roteiro, prompts, direêÂ§êÂ£o de voz, prompt da mêÂºsica, têÂ­tulo/descriêÂ§êÂ£o/tags |
| `bloco-imagens.txt`, `bloco-videos.txt` | os blocos para colar no agente do Flow (servem tambêÂ©m êÂ  mêÂ£o) |
| `narracao.txt`, `musica.txt` | texto limpo para narrar e prompt para gerar a trilha (Flow Music, Suno...) |
| `imagens/NNN.png`, `videos/NNN.mp4`, `narracao/NNN.wav` | material numerado por cena |
| `final.mp4` | o documentêÂ¡rio montado |

### A montagem (o que o CapCut fazia)

- Cada clipe êÂ© acelerado ou desacelerado (atêÂ© êÂ±25%) para caber na sua fala. O que sobrar êÂ© cortado ou congela no êÂºltimo quadro.
- Entre as cenas, transiêÂ§êÂ£o suave de 0,7 s.
- Fade do preto no inêÂ­cio e para o preto no fim.
- Som ambiente dos clipes a 16%.
- MêÂºsica a 30%, que abaixa sozinha quando a voz fala (sidechain), com fade in e fade out.
- No fim, uma cena de CTA: ARCKHê AI sobre a êÂºltima imagem desfocada, com a voz chamando para o site (`cta: false` no tema desliga).

## VêÂ­deo modelo

**"Egito Antigo em um minuto: do Nilo a CleêÂ³patra"**: 6 cenas, 55 s, 16:9, motor Agnes,
voz `nei`. EstêÂ¡ em `~/projetos/output/docflow/egito-antigo/final.mp4`.

O que se mediu nesta primeira rodada:

| Etapa | Tempo | ObservaêÂ§êÂ£o |
|---|---|---|
| roteiro (Codex) | 48 s | 6 cenas de 16 a 19 palavras, datas por extenso |
| 6 imagens + 6 clipes (Agnes) | ~5 min | 1 erro 429 (limite de 6/min), recuperado sozinho |
| 6 narraêÂ§êÂµes (ARCKHêvox) | 2,5 min | conferidas por transcriêÂ§êÂ£o local: texto bate com o roteiro |
| montagem (ffmpeg) | 18 s | ê¢Ëâ17,9 LUFS |
| publicar | 84 s | publicado em https://www.youtube.com/watch?v=TsVY4UUc5gI (canal ARCKHê Agentes), thumb das pirêÂ¢mides (`thumb_cena: 3`) |

A cena 6 (porto de Alexandria) derivou nas duas tentativas do Agnes: virou um porto
barroco com cêÂºpula e caravelas. Ficou com `estatica` (imagem com zoom lento).

## Requisitos

`python3` + `pyyaml`, `ffmpeg`, `codex` (assinatura), `node` + Playwright global (motor flow),
ARCKHêvox no ar (`:8010`), `~/projetos/videos-agnes` (cliente Agnes) e `~/projetos/yt-pubx`.

---

Projeto adaptado da comunidade ARCKHê TDS - ARCKHÉe
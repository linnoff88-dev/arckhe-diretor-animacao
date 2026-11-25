# diretor-animacao

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


**O Diretor de AnimaêÂ§êÂ£o** ê¢â¬â transforma **imagens prontas** (fotos reais e/ou ilustraêÂ§êÂµes) + **narraêÂ§êÂ£o** (êÂ¡udio ou texto) em um **filme MP4 profissional, sem IA generativa de vêÂ­deo**. A skill analisa cada imagem com visêÂ£o, segmenta a narraêÂ§êÂ£o em beats e decide por imagem a cêÂ¢mera, duraêÂ§êÂ£o, transiêÂ§êÂ£o, look e parallax, seguindo gramêÂ¡tica cARCKHêtogrêÂ¡fica. O render êÂ© determinêÂ­stico, via motor [pixflow](../pixflow) v2.3+.

## Recursos

- **Skill `diretor-animacao`** (`skill/diretor-animacao/SKILL.md`) ê¢â¬â fluxo completo VER ê¢â â OUVIR ê¢â â DECIDIR ê¢â â DECUPAGEM ê¢â â TRILHA ê¢â â SPEC + RENDER ê¢â â VERIFICAR.
- **AnêÂ¡lise visual por imagem**: tipo (foto ê¢â â parallax 2.5D pleno; desenho ê¢â â parallax 0 ê¢â¬â regra validada por A/B), conteêÂºdo, emoêÂ§êÂ£o e ponto de interesse `at: [x,y]` para framing.
- **DireêÂ§êÂ£o por beat de narraêÂ§êÂ£o**: 18 movimentos de cêÂ¢mera (incl. crane, aerial, dolly zoom, whip pan) + `framing` from/to, com regras duras de alternêÂ¢ncia, amplitude (ê¢â°Â¤2 dramêÂ¡ticos seguidos) e **fim suspenso** (êÂºltimo plano êÂ© o mais calmo).
- **TransiêÂ§êÂµes por fronteira, nunca uniformes**: `cut` (padrêÂ£o, ~70%) / `whip` / `whip_left` / `zoom_blur` / `dip_to_black` / `crossfade`.
- **Multi-shot**: imagens ricas viram 2ê¢â¬â4 shots, com a narraêÂ§êÂ£o podendo atravessar o corte.
- **GramêÂ¡tica cARCKHêtogrêÂ¡fica** completa em `skill/diretor-animacao/references/gramatica-direcao.md` (tabela mestra movimento êâ beat, curva de 3 atos).
- **`montar-trilha.mjs`** ê¢â¬â monta a trilha de êÂ¡udio a partir da decupagem compensando os overlaps de transiêÂ§êÂ£o (timing de fonte êÂºnica: a narraêÂ§êÂ£o governa as duraêÂ§êÂµes).
- **`DICAS-CORRECOES.md`** ê¢â¬â 9 liêÂ§êÂµes de render cross-projeto (anti-pisca, frames pretos, anti-fantasma etc.).
- **`piloto-f0/`** ê¢â¬â pilotos F0ê¢â¬Â¦F1d, experimentos (fotos reais, sem depth) e demo F2, com specs `*.movie.yaml` e decupagens que validaram cada regra.
- **`referencias/`** ê¢â¬â estudo Ring Spark Burst (storyboard, prompt e liêÂ§êÂµes).
- **`PLANO.md`** ê¢â¬â plano de viabilidade, adendos de cada decisêÂ£o e backlog (upgrade de transiêÂ§êÂµes, eventos de luz, partêÂ­culas, speed-rampê¢â¬Â¦).

## DependêÂªncias

| DependêÂªncia | Para quêÂª | Onde |
|---|---|---|
| **pixflow v2.3+** | Motor de render (spec YAML `pixflow.movie/v1`): parallax 2.5D, 18 movimentos, transiêÂ§êÂµes, looks, respiraêÂ§êÂ£o | `~/projetos/pixflow/skill` ê¢â¬â `node cli/pixflow-motion.mjs render spec.yaml out.mp4` |
| **Node.js** (ê¢â°Â¥18) | Scripts `.mjs` (montar-trilha, gen-fotos) e CLI do pixflow | ê¢â¬â |
| **FFmpeg / ffprobe** | Medir duraêÂ§êÂ£o da narraêÂ§êÂ£o, encode final, verificaêÂ§êÂ£o de frames | sistema |
| **Depth-Anything-V2** | Mapas de profundidade do parallax (via pixflow) | dependêÂªncia do pixflow |
| **Remotion + Three.js/WebGL** | ComposiêÂ§êÂ£o e render dos shaders (via pixflow) | dependêÂªncia do pixflow |
| **HyperFrames TTS** (Kokoro, voz `pf_dora`) | Gerar narraêÂ§êÂ£o quando a entrada êÂ© texto: `npx hyperframes tts` | opcional |
| **flux2-klein** (`localhost:8000`) | GeraêÂ§êÂ£o das imagens dos pilotos (`piloto-f0/gen-fotos.mjs`) | opcional, sêÂ³ p/ pilotos |
| **Claude Code com visêÂ£o** | A skill abre cada imagem (Read/visêÂ£o) antes de dirigir | ê¢â¬â |

Checagem rêÂ¡pida: `cd ~/projetos/pixflow/skill && node cli/pixflow-motion.mjs check-deps`.

## Uso

1. Instale a skill (symlink): `ln -s $(pwd)/skill/diretor-animacao ~/.claude/skills/diretor-animacao`
2. No Claude Code, dêÂª um conjunto de imagens + narraêÂ§êÂ£o e peêÂ§a: *"vira filme"*, *"anima essas imagens"*, *"dirige esse material"*.
3. A skill mostra a **decupagem** (cena êÂ· imagem êÂ· beat êÂ· movimento êÂ· transiêÂ§êÂ£o êÂ· duraêÂ§êÂ£o) para aprovaêÂ§êÂ£o antes do render.

## Exemplo completo

[`exemplos/pequeno-principe.md`](exemplos/pequeno-principe.md) ê¢â¬â primeiro filme real da skill: 21 ilustraêÂ§êÂµes + 22 WAVs de narraêÂ§êÂ£o ê¢â â filme de 2min11 com 26 shots, mêÂºsica CC0 com ducking, vento, whooshes e shimmer sintetizado. Inclui a decupagem completa, o fluxo de mêÂºsica/SFX via ARCKHêvox/Freesound, o pipeline comando a comando e as liêÂ§êÂµes novas.

## Regras de ouro (validadas por A/B)

- Foto real = parallax pleno êÂ· desenho = parallax 0.
- TransiêÂ§êÂ£o nunca uniforme; corte seco êÂ© o padrêÂ£o.
- Fim suspenso ê¢â¬â terminar no plano mais calmo.
- Imagem ê¢â°Â¥1920px se zoom >1.6êâ; 16:9 padrêÂ£o, 9:16 sob pedido.
- Nunca calcular starts de êÂ¡udio na mêÂ£o ê¢â¬â sempre `montar-trilha.mjs`.

## Estrutura

```
skill/diretor-animacao/   # a skill (SKILL.md, gramêÂ¡tica, montar-trilha.mjs)
piloto-f0/                # pilotos, experimentos e specs .movie.yaml
referencias/              # estudos de referêÂªncia (Ring Spark Burst)
PLANO.md                  # plano de viabilidade + adendos + backlog
DICAS-CORRECOES.md        # liêÂ§êÂµes de render
HANDOFF.md                # estado da êÂºltima sessêÂ£o
```

> MP4s, imagens e êÂ¡udios dos pilotos ficam fora do git (ver `.gitignore`).

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>
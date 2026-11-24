# diretor-animacao

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


**O Diretor de AnimaÃ§Ã£o** â€” transforma **imagens prontas** (fotos reais e/ou ilustraÃ§Ãµes) + **narraÃ§Ã£o** (Ã¡udio ou texto) em um **filme MP4 profissional, sem IA generativa de vÃ­deo**. A skill analisa cada imagem com visÃ£o, segmenta a narraÃ§Ã£o em beats e decide por imagem a cÃ¢mera, duraÃ§Ã£o, transiÃ§Ã£o, look e parallax, seguindo gramÃ¡tica cARCKHÉtogrÃ¡fica. O render Ã© determinÃ­stico, via motor [pixflow](../pixflow) v2.3+.

## Recursos

- **Skill `diretor-animacao`** (`skill/diretor-animacao/SKILL.md`) â€” fluxo completo VER â†’ OUVIR â†’ DECIDIR â†’ DECUPAGEM â†’ TRILHA â†’ SPEC + RENDER â†’ VERIFICAR.
- **AnÃ¡lise visual por imagem**: tipo (foto â†’ parallax 2.5D pleno; desenho â†’ parallax 0 â€” regra validada por A/B), conteÃºdo, emoÃ§Ã£o e ponto de interesse `at: [x,y]` para framing.
- **DireÃ§Ã£o por beat de narraÃ§Ã£o**: 18 movimentos de cÃ¢mera (incl. crane, aerial, dolly zoom, whip pan) + `framing` from/to, com regras duras de alternÃ¢ncia, amplitude (â‰¤2 dramÃ¡ticos seguidos) e **fim suspenso** (Ãºltimo plano Ã© o mais calmo).
- **TransiÃ§Ãµes por fronteira, nunca uniformes**: `cut` (padrÃ£o, ~70%) / `whip` / `whip_left` / `zoom_blur` / `dip_to_black` / `crossfade`.
- **Multi-shot**: imagens ricas viram 2â€“4 shots, com a narraÃ§Ã£o podendo atravessar o corte.
- **GramÃ¡tica cARCKHÉtogrÃ¡fica** completa em `skill/diretor-animacao/references/gramatica-direcao.md` (tabela mestra movimento Ã— beat, curva de 3 atos).
- **`montar-trilha.mjs`** â€” monta a trilha de Ã¡udio a partir da decupagem compensando os overlaps de transiÃ§Ã£o (timing de fonte Ãºnica: a narraÃ§Ã£o governa as duraÃ§Ãµes).
- **`DICAS-CORRECOES.md`** â€” 9 liÃ§Ãµes de render cross-projeto (anti-pisca, frames pretos, anti-fantasma etc.).
- **`piloto-f0/`** â€” pilotos F0â€¦F1d, experimentos (fotos reais, sem depth) e demo F2, com specs `*.movie.yaml` e decupagens que validaram cada regra.
- **`referencias/`** â€” estudo Ring Spark Burst (storyboard, prompt e liÃ§Ãµes).
- **`PLANO.md`** â€” plano de viabilidade, adendos de cada decisÃ£o e backlog (upgrade de transiÃ§Ãµes, eventos de luz, partÃ­culas, speed-rampâ€¦).

## DependÃªncias

| DependÃªncia | Para quÃª | Onde |
|---|---|---|
| **pixflow v2.3+** | Motor de render (spec YAML `pixflow.movie/v1`): parallax 2.5D, 18 movimentos, transiÃ§Ãµes, looks, respiraÃ§Ã£o | `~/projetos/pixflow/skill` â€” `node cli/pixflow-motion.mjs render spec.yaml out.mp4` |
| **Node.js** (â‰¥18) | Scripts `.mjs` (montar-trilha, gen-fotos) e CLI do pixflow | â€” |
| **FFmpeg / ffprobe** | Medir duraÃ§Ã£o da narraÃ§Ã£o, encode final, verificaÃ§Ã£o de frames | sistema |
| **Depth-Anything-V2** | Mapas de profundidade do parallax (via pixflow) | dependÃªncia do pixflow |
| **Remotion + Three.js/WebGL** | ComposiÃ§Ã£o e render dos shaders (via pixflow) | dependÃªncia do pixflow |
| **HyperFrames TTS** (Kokoro, voz `pf_dora`) | Gerar narraÃ§Ã£o quando a entrada Ã© texto: `npx hyperframes tts` | opcional |
| **flux2-klein** (`localhost:8000`) | GeraÃ§Ã£o das imagens dos pilotos (`piloto-f0/gen-fotos.mjs`) | opcional, sÃ³ p/ pilotos |
| **Claude Code com visÃ£o** | A skill abre cada imagem (Read/visÃ£o) antes de dirigir | â€” |

Checagem rÃ¡pida: `cd ~/projetos/pixflow/skill && node cli/pixflow-motion.mjs check-deps`.

## Uso

1. Instale a skill (symlink): `ln -s $(pwd)/skill/diretor-animacao ~/.claude/skills/diretor-animacao`
2. No Claude Code, dÃª um conjunto de imagens + narraÃ§Ã£o e peÃ§a: *"vira filme"*, *"anima essas imagens"*, *"dirige esse material"*.
3. A skill mostra a **decupagem** (cena Â· imagem Â· beat Â· movimento Â· transiÃ§Ã£o Â· duraÃ§Ã£o) para aprovaÃ§Ã£o antes do render.

## Exemplo completo

[`exemplos/pequeno-principe.md`](exemplos/pequeno-principe.md) â€” primeiro filme real da skill: 21 ilustraÃ§Ãµes + 22 WAVs de narraÃ§Ã£o â†’ filme de 2min11 com 26 shots, mÃºsica CC0 com ducking, vento, whooshes e shimmer sintetizado. Inclui a decupagem completa, o fluxo de mÃºsica/SFX via ARCKHÉvox/Freesound, o pipeline comando a comando e as liÃ§Ãµes novas.

## Regras de ouro (validadas por A/B)

- Foto real = parallax pleno Â· desenho = parallax 0.
- TransiÃ§Ã£o nunca uniforme; corte seco Ã© o padrÃ£o.
- Fim suspenso â€” terminar no plano mais calmo.
- Imagem â‰¥1920px se zoom >1.6Ã—; 16:9 padrÃ£o, 9:16 sob pedido.
- Nunca calcular starts de Ã¡udio na mÃ£o â€” sempre `montar-trilha.mjs`.

## Estrutura

```
skill/diretor-animacao/   # a skill (SKILL.md, gramÃ¡tica, montar-trilha.mjs)
piloto-f0/                # pilotos, experimentos e specs .movie.yaml
referencias/              # estudos de referÃªncia (Ring Spark Burst)
PLANO.md                  # plano de viabilidade + adendos + backlog
DICAS-CORRECOES.md        # liÃ§Ãµes de render
HANDOFF.md                # estado da Ãºltima sessÃ£o
```

> MP4s, imagens e Ã¡udios dos pilotos ficam fora do git (ver `.gitignore`).

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>
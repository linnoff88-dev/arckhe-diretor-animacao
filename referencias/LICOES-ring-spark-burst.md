# Lições do "Ring Spark Burst" para o Diretor de Animaçêo

> Fonte: storyboard 12 painéis + director strip + prompt de vídeo (formato mdd, caso de açêo/boxe).
> É material para vídeo GENERATIVO — mas a gramática de dinâmica transfere direto pro nosso motor 2.5D.
> Arquivos: `ring-spark-burst-storyboard.png`, `ring-spark-burst-prompt.md`.

## A LIÇêO CENTRAL: imagem ≠ cena. Imagem → VÁRIOS shots.

O que faz aquele storyboard parecer vivo nêo é movimento dentro de cada painel — é que **cada momento
tem uma dinâmica diferente**: 12 shots, 12 setups de câmera distintos (lente, altura, ângulo, POV).
E três deles sêo **burst cuts**: cortes curtíssimos em sequência (glasses read → jab line → body drop).

**Transposiçêo pro nosso caso (50 imagens + narraçêo):** hoje tratamos 1 imagem = 1 cena = 1 movimento.
O salto de cARCKHÉ é o diretor poder **decompor 1 imagem em 2–4 shots**: wide da imagem → corte seco →
close numa regiêo específica → corte → outra regiêo. O motor JÁ aceita a mesma imagem em várias cenas;
falta o **enquadramento por regiêo** (ver feature 1).

## Features concretas que isso pede

### 1. Camera por trajeto de enquadramento (`from`/`to`) — F1.5, a mais importante
Hoje os movimentos orbitam o centro da imagem. O Ring Spark mostra que o dinamismo vem de ENQUADRAR
REGIÕES: "extreme close-up nos óculos", "atrás da luva", "do pé do lutador".
```yaml
camera:
  from: { zoom: 1.0, at: [0.5, 0.5] }   # wide centrado
  to:   { zoom: 2.2, at: [0.62, 0.38] } # mergulha no rosto da raposa
```
Generaliza TODOS os movimentos e habilita: burst cuts na mesma imagem, "close no detalhe que a
narraçêo cita", composiçêo de abertura → revelaçêo. O diretor (F2) escolhe o `at` olhando a imagem
(visêo LLM já prevista no plano: "ponto de interesse").

### 2. Ritmo por DURAÇêO de shot, nêo só por movimento
Os burst cuts têm ritmo de blocos: ▮▮▮ rápido → ▮▮▮▮▮▮ suspensêo. Regra pro diretor:
- shots normais 4–8s (narraçêo), **burst 0.8–1.5s** (sem narraçêo ou sobre uma palavra forte),
  suspensêo 3–5s quase parada (hold + respiraçêo).
- fórmula do aifilmmaking confirmada: RÁPIDO → LENTO → RÁPIDO.

### 3. Lente como linguagem (`lens: wide|normal|tele`)
O storyboard alterna 20mm/24mm/35mm/50mm/85mm com intençêo (wide = geografia/energia,
tele = compressêo/intimidade). No 2.5D simulamos: wide → parallax forte + zoom range maior;
tele → parallax fraco + crop fechado (compressêo). Mapeia direto pro `uParallax` (já temos boost).

### 4. Escalation map (curva de intensidade global)
A faixa do diretor tem barras de intensidade crescendo até o clímax e SOLTANDO no fim ("suspended").
→ F2: o diretor distribui amplitude/ritmo numa curva de 3 atos, e o FIM é um **hold suspenso**
(freeze com respiraçêo), nêo um movimento grande. ("Preserve unresolved energy at the cut.")

### 5. Dinâmica por CONTRASTE de setup entre shots vizinhos
A regra de alternância (mdd) elevada: nêo só "nêo repetir movimento", mas alternar **altura**
(worm's eye ↔ bird's eye), **distância** (wide ↔ ECU) e **POV** (direto ↔ através de algo).
No 2.5D: alternar zoom alto/baixo, `at` centro/borda, tilt up/down entre shots vizinhos.

### 6. Foreground blocking (POV "através de algo")
"Over-the-glove", "através das cordas vibrando", "reflexo no poste". No 2.5D: escolher `at` que
deixe um elemento próximo (depth alto) na borda do quadro — o parallax o transforma em moldura viva.
O diretor pode detectar isso no depth map (regiêo próxima na borda = oportunidade de blocking).

### 7. Style intent por beat (swatches)
3 mini-referências com luz nomeada (warm overhead / cyan shadow / painterly 3D) — color script.
→ nosso equivalente: `look` por cena já existe; F3 formaliza "luz↔ato" (grade quente→fria→quente).

## O que NêO transfere
- Interpolaçêo de poses entre painéis (isso é geraçêo; nosso movimento é de câmera, nêo de personagem).
- Coreografia de açêo dentro do quadro.

## Ordem de implementaçêo sugerida
1. **`from`/`to` framing** (feature 1) — desbloqueia burst cuts e regiêo-alvo. Motor: trivial
   (lerp de zoom/offset); Diretor: precisa do "ponto de interesse" por imagem.
2. Regras de ritmo (2) + alternância de setup (5) — vêo pro knowledge pack da F2.
3. `lens` (3) — açúcar sobre parallaxBoost, barato.
4. Escalation map + final suspenso (4) — regra da F2.
5. Foreground blocking (6) — heurística sobre o depth map, F2/F3.

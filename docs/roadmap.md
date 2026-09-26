# Roadmap e desenho do sistema de controle

## Contexto do problema

O MD1 (e o HB-1) dosa café em grão por peso: reservatório → roda estrela acionada por motor de passo → grãos caem numa balança (célula de carga) até bater o peso alvo.

O desafio central não é motor nem balança isoladamente — é que existe **dead time**: quando o motor para, ainda tem grão "em voo" entre a roda e o recipiente, e a leitura da célula de carga tem atraso de filtragem/assentamento mecânico. Um controle fechado ingênuo (PID contínuo peso→motor) faria overshoot feio por causa disso. Grãos diferentes (tamanho/densidade) também mudam quanto cai por rotação, então uma calibração física fixa não é suficiente a longo prazo — mas isso só vale a pena resolver depois que o mecanismo básico for validado.

## Fases

### Fase 0 — Mecânica e elétrica (atual)
- [x] Modelagem 3D
- [ ] Impressão e montagem
- [ ] Bring-up isolado de cada subsistema antes de integrar: motor gira sob comando do driver, célula de carga lê peso cru com ruído aceitável, tela desenha algo, knob + botão respondem

### Fase 1 — MVP de firmware (sem aprendizado automático)

Escopo deliberadamente cortado: precisão é requisito (0.3g importa para café especial), mas *automação do ajuste* não precisa nascer na v1.

Máquina de estados não-bloqueante:
`FILL_FAST → TRICKLE → SETTLE → DONE` (+ estados de erro: `JAMMED`, `HOPPER_EMPTY`, `CUP_REMOVED`)

- **US1 — Dosar:** girar o knob seleciona o peso alvo, clique dispara a dose. Fase rápida roda até `peso ≈ alvo - TRICKLE_MARGIN_G`; depois pulsos curtos (trickle) com pausa de assentamento entre eles até bater o alvo; leitura final só é considerada após settle completo.
  - `TRICKLE_MARGIN_G`: constante única, chute inicial **~1g**, a recalibrar com dados reais do primeiro teste mecânico (peso pedido vs. peso real por dose).
- **US2 — Calibrar célula de carga:** rotina com peso de referência conhecido (~10g, mesmo princípio do pezinho do MD1), tara + fator de escala, salvo em NVS.
- **US3 (manual) — Fine-tuning:** depois de uma dose, tela mostra o erro (alvo vs. real); usuário ajusta `TRICKLE_MARGIN_G` girando o knob antes da próxima dose. Mesma variável que uma futura camada automática (EMA de overshoot) vai ajustar sozinha — não é retrabalho, é a mesma variável trocando de dono.

### UI (com os 3 inputs físicos: knob-giro, knob-clique, botão solto)
- Tela padrão: número grande = peso alvo (ajustável por giro), clique = dispara dose; durante a dose o número vira peso ao vivo com indicador de fase.
- Botão solto, long-press: menu raso de 2 itens — "Calibrar" (US2) e "Ajuste fino" (US3 manual).

### Fase 2 — Pós-MVP (não bloqueia o MVP)
- Aprendizado automático do overshoot por grão (EMA sobre `TRICKLE_MARGIN_G`), substituindo o ajuste manual da US3
- Presets / perfis salvos por tipo de grão
- Estados de erro mais refinados (detecção de entupimento, reservatório vazio)

## Decisões em aberto
- Licença do projeto (firmware vs. arquivos CAD podem ter licenças diferentes)
- Se/quando vale a pena migrar US3 de manual para automático — decisão dirigida por dado real de campo, não por calendário

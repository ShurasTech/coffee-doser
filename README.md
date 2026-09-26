# coffee-doser

Dosador de grãos de café open source / DIY, inspirado no Madball MD1 (funil + roda estrela dosadora + balança). Peças impressas em 3D, controle com ESP32.

Objetivo: acordar, girar o knob pro peso de grão que a receita pede, apertar, e o café especial cai pesado certinho — sem ficar tirando grão a grão na mão pra bater 0.3g de precisão.

## Status atual

- ✅ Modelagem 3D concluída
- ⏳ Impressão e montagem mecânica
- ⏳ Bring-up elétrico isolado (motor, célula de carga, tela)
- ⏳ Firmware MVP

Ver [docs/roadmap.md](docs/roadmap.md) para o plano completo e o desenho do sistema de controle.

## Hardware

- Motor de passo + driver
- ESP32
- Célula de carga (até 200g) + amplificador (HX711 ou equivalente)
- Tela TFT
- Input: knob rotativo com clique + 1 botão solto (3 inputs físicos no total)
- Peças estruturais (funil, roda estrela dosadora, recipiente) impressas em 3D

## Licença

A definir — pendente de decisão (sugestão: MIT para firmware, CERN-OHL-S ou CC-BY-SA para as peças/CAD, mas ainda em aberto).

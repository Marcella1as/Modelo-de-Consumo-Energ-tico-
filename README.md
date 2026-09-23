# Validação do Modelo de Hover (Li et al., 2024)

## 1. O que já foi concluído

- Identificamos a melhor janela real de hover (velocidade < 0,2 m/s, altitude estável) entre **~113,0 s e ~117,8 s**, dentro da faixa relatada (1:50–2:00), do `log_250_2026-9-4-15-12-16`.
- **Não há nenhum dado elétrico no log**: `BATT_MONITOR = 0` (e BATT2–9 também), mensagem `BAT` com 0 registros, `ESC.Volt`/`ESC.Curr` sempre zerados.
<img width="1489" height="472" alt="Screenshot from 2026-09-23 09-02-52" src="https://github.com/user-attachments/assets/ced4c3af-b1b9-4c3b-84d4-ced6e49eb580" />
 
   https://ardupilot.org/copter/docs/parameters.html#batt-monitor-battery-monitoring
   https://holybro.com/collections/power-modules-pdbs/products/pm02-v3-12s-power-module
   https://docs.holybro.com/power-module-and-pdb/power-module/analog-power-module-setup


## 2. Necessidade de novos ensaios

- [ ] Instalar/configurar módulo de potência.
- [ ] Configurar `BATT_MONITOR` corretamente e calibrar `BATT_VOLT_MULT` / `BATT_AMP_PERVOLT`.
- [ ] Verificar se os ESCs atuais suportam telemetria completa (Volt/Curr/Temp por motor), não só RPM.
- [ ] Reavaliar o roteiro de ensaios com base no artigo e planejar uma nova bateria de voos.

## 3. Preparação dos algoritmos (extração de dados e modelagem)

**Extração de dados do log:**
- [ ] Extração de `BAT.Volt`, `BAT.Curr` (e `ESC.Volt`/`ESC.Curr` por motor, se disponíveis) na janela de hover.
- [ ] Extração de `BARO.Press`/`BARO.Temp` → cálculo de `ρ`.
- [ ] Extração de `ESC.RPM` por motor → cálculo de `v_r` (velocidade de ponta de pá).

**Modelagem (Eqs. 34–43 do artigo):**
- [ ] Implementar `T_h = m·g` (Eq. 42).
- [ ] Implementar `v_ih = √(T_h / (2ρAp))` (Eq. 38).
- [ ] Implementar `P_in = κ·√(T_h³ / (2ρAp))` (Eq. 39).
- [ ] Implementar `P_pr = ρ·C_b·Ap·v_r³ / 8` (Eq. 40).
- [ ] Somar `P_h = P_in + P_pr + P_e` (Eq. 41/43).
- [ ] Integrar `E_h = ∫P_h dt` ao longo da janela de hover.

**Comparação modelo × medição:**
- [ ] Calcular `GAP_potência` e `GAP_energia` (previsto vs. medido).
- [ ] Testar sensibilidade a `κ` (1,0 vs. 1,15).
- [ ] Testar sensibilidade a `v_r` medido (real) vs. genérico (120 m/s, usado no artigo).

## 4. Parâmetros ainda pendentes 

- Massa total (`m`)
- Raio da hélice (`R_r`)
- Coeficiente de arrasto de pá (`C_b`) — usar 0,0008 como padrão da literatura
- Fator de correção induzida (`κ`) — usar 1,15 como padrão, testar sensibilidade
- Potência da eletrônica embarcada (`P_e`), se disponível

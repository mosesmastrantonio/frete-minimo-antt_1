<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Calculadora Frete Mínimo ANTT</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <style>
    * {
      box-sizing: border-box;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    }

    body {
      margin: 0;
      padding: 0;
      background: #0f172a;
      color: #e5e7eb;
      display: flex;
      justify-content: center;
    }

    .app-container {
      width: 100%;
      max-width: 480px;
      padding: 16px;
    }

    .card {
      background: #020617;
      border-radius: 16px;
      padding: 16px 18px 20px;
      box-shadow: 0 20px 40px rgba(0,0,0,0.5);
      border: 1px solid #1f2937;
    }

    h1 {
      font-size: 1.2rem;
      margin: 0 0 4px;
      text-align: center;
    }

    .subtitle {
      font-size: 0.8rem;
      color: #9ca3af;
      text-align: center;
      margin-bottom: 16px;
    }

    .field-group {
      margin-bottom: 12px;
    }

    label {
      display: block;
      font-size: 0.78rem;
      margin-bottom: 4px;
      color: #d1d5db;
    }

    input, select {
      width: 100%;
      padding: 8px 10px;
      border-radius: 10px;
      border: 1px solid #374151;
      background: #020617;
      color: #f9fafb;
      font-size: 0.9rem;
      outline: none;
    }

    input:focus, select:focus {
      border-color: #38bdf8;
      box-shadow: 0 0 0 1px #38bdf8;
    }

    .hint {
      font-size: 0.7rem;
      color: #9ca3af;
      margin-top: 2px;
    }

    .button {
      margin-top: 8px;
      width: 100%;
      padding: 10px;
      border-radius: 999px;
      border: none;
      background: linear-gradient(90deg, #22c55e, #16a34a);
      color: #f9fafb;
      font-weight: 600;
      font-size: 0.95rem;
      cursor: pointer;
    }

    .button:active {
      opacity: 0.9;
      transform: scale(0.99);
    }

    .results-card {
      margin-top: 14px;
      background: #020617;
      border-radius: 12px;
      padding: 10px 12px;
      border: 1px solid #1f2937;
      font-size: 0.85rem;
    }

    .results-title {
      font-weight: 600;
      margin-bottom: 6px;
      font-size: 0.9rem;
    }

    .result-row {
      display: flex;
      justify-content: space-between;
      margin-bottom: 4px;
    }

    .result-label {
      color: #9ca3af;
    }

    .result-value {
      font-weight: 600;
      color: #e5e7eb;
    }

    .result-main {
      margin-top: 6px;
      padding-top: 6px;
      border-top: 1px dashed #374151;
    }

    .badge {
      display: inline-block;
      font-size: 0.65rem;
      padding: 2px 8px;
      border-radius: 999px;
      background: #1e293b;
      color: #e5e7eb;
      margin-bottom: 6px;
    }

    .footer-note {
      margin-top: 10px;
      font-size: 0.7rem;
      color: #6b7280;
      text-align: center;
    }
  </style>
</head>
<body>
  <div class="app-container">
    <div class="card">
      <h1>Frete Mínimo ANTT</h1>
      <div class="subtitle">Granel sólido · Tarifa empresa + pedágio + ICMS</div>

      <div class="field-group">
        <label for="kmTotal">Distância total (km)</label>
        <input type="number" id="kmTotal" placeholder="Ex.: 1380" inputmode="decimal" />
      </div>

      <div class="field-group">
        <label for="pedagio">Pedágio por eixo (R$)</label>
        <input type="number" id="pedagio" placeholder="Ex.: 105" step="0.01" inputmode="decimal" />
      </div>

      <div class="field-group">
        <label for="margem">Margem da transportadora</label>
        <input type="number" id="margem" placeholder="Ex.: 7 ou 0,07" step="0.01" inputmode="decimal" />
        <div class="hint">Pode ser em % (7) ou decimal (0,07)</div>
      </div>

      <div class="field-group">
        <label for="icms">ICMS</label>
        <input type="number" id="icms" placeholder="Ex.: 7 ou 0,07" step="0.01" inputmode="decimal" />
        <div class="hint">Pode ser em % (7) ou decimal (0,07)</div>
      </div>

      <div class="field-group">
        <label for="eixos">Qtd. de eixos do veículo</label>
        <select id="eixos">
          <option value="">Selecione...</option>
          <option value="2">2 eixos</option>
          <option value="3">3 eixos</option>
          <option value="4">4 eixos</option>
          <option value="5">5 eixos</option>
          <option value="6">6 eixos</option>
          <option value="7">7 eixos</option>
          <option value="9">9 eixos</option>
        </select>
      </div>

      <button class="button" onclick="calcularFrete()">Calcular frete</button>

      <div id="results" class="results-card" style="display:none;">
        <div class="badge">Resultado</div>

        <div class="results-title">Tarifa por tonelada</div>
        <div class="result-row">
          <div class="result-label">Empresa + pedágio + ICMS</div>
          <div class="result-value" id="resPorTon">R$ 0,00/t</div>
        </div>

        <div class="result-main">
          <div class="results-title">Valor total da viagem</div>
          <div class="result-row">
            <div class="result-label">Base frete (ANTT)</div>
            <div class="result-value" id="resFreteBase">R$ 0,00</div>
          </div>
            <div class="result-row">
            <div class="result-label">Pedágio total</div>
            <div class="result-value" id="resPedagioTotal">R$ 0,00</div>
          </div>
          <div class="result-row">
            <div class="result-label">Valor final empresa + pedágio + ICMS</div>
            <div class="result-value" id="resViagemFinal">R$ 0,00</div>
          </div>
        </div>
      </div>

      <div class="footer-note">
        Cálculo baseado na tabela de frete mínimo ANTT para granel sólido.
      </div>
    </div>
  </div>

  <script>
    const toneladasPorEixo = {
      2: 7,
      3: 15,
      4: 20,
      5: 27,
      6: 32,
      7: 37,
      9: 50
    };

    const ccdPorEixo = {
      2: 3.705,
      3: 4.6875,
      4: 5.3526,
      5: 6.0301,
      6: 6.7408,
      7: 7.313,
      9: 8.242
    };

    const ccPorEixo = {
      2: 426.61,
      3: 519.67,
      4: 565.14,
      5: 615.26,
      6: 663.07,
      7: 753.88,
      9: 808.17
    };

    function formatMoney(value) {
      return value.toLocaleString("pt-BR", {
        style: "currency",
        currency: "BRL",
        minimumFractionDigits: 2
      });
    }

    function calcularFrete() {
      const kmTotal = parseFloat((document.getElementById("kmTotal").value || "").replace(",", "."));
      const pedagio = parseFloat((document.getElementById("pedagio").value || "").replace(",", "."));
      const margemInput = parseFloat((document.getElementById("margem").value || "").replace(",", "."));
      const icmsInput = parseFloat((document.getElementById("icms").value || "").replace(",", "."));
      const eixos = parseInt(document.getElementById("eixos").value, 10);

      if (isNaN(kmTotal) || isNaN(pedagio) || isNaN(margemInput) || isNaN(icmsInput) || isNaN(eixos)) {
        alert("Preencha todos os campos corretamente.");
        return;
      }

      const ton = toneladasPorEixo[eixos];
      const ccd = ccdPorEixo[eixos];
      const cc = ccPorEixo[eixos];

      if (!ton || !ccd || !cc) {
        alert("Combinação de eixos não encontrada na tabela.");
        return;
      }

      const margemDec = margemInput > 1 ? margemInput / 100 : margemInput;
      const icmsDec   = icmsInput   > 1 ? icmsInput   / 100 : icmsInput;

      const freteBaseTotal = ccd * kmTotal + cc;
      const pedagioTotal   = pedagio * eixos;
      const pedagioPorTon  = pedagioTotal / ton;

      const tarifaMotorista = freteBaseTotal / ton;
      const tarifaEmpresaSemPedIcms = tarifaMotorista * (1 + margemDec);

      const tarifaEmpresaMaisPedMaisIcms =
        (tarifaEmpresaSemPedIcms + pedagioPorTon) * (1 + icmsDec);

      const valorViagemFinal = tarifaEmpresaMaisPedMaisIcms * ton;

      document.getElementById("resPorTon").textContent       = formatMoney(tarifaEmpresaMaisPedMaisIcms) + "/t";
      document.getElementById("resFreteBase").textContent    = formatMoney(freteBaseTotal);
      document.getElementById("resPedagioTotal").textContent = formatMoney(pedagioTotal);
      document.getElementById("resViagemFinal").textContent  = formatMoney(valorViagemFinal);

      document.getElementById("results").style.display = "block";
    }
  </script>
</body>
</html>

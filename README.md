# peg-alginate-formulation-tool
Interactive formulation tool for predicting PEG–alginate hydrogel mechanical properties
recipe tool · HTML
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Hydrogel Recipe Tool</title>
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }
body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif; background: #f7f9f9; color: #0D1B2A; min-height: 100vh; padding: 24px; }
.container { max-width: 900px; margin: 0 auto; }
.header { margin-bottom: 28px; }
.header h1 { font-size: 22px; font-weight: 700; color: #0D1B2A; margin-bottom: 4px; }
.header p { font-size: 13px; color: #718096; }
.card { background: #fff; border-radius: 12px; border: 0.5px solid #E2E8F0; padding: 20px 24px; margin-bottom: 16px; }
.card h2 { font-size: 13px; font-weight: 700; color: #1B7F79; text-transform: uppercase; letter-spacing: 0.8px; margin-bottom: 16px; }
.inputs-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
.input-group { display: flex; flex-direction: column; gap: 6px; }
.input-group label { font-size: 12px; font-weight: 600; color: #4A5568; }
.input-row { display: flex; align-items: center; gap: 10px; }
input[type=range] { flex: 1; height: 4px; accent-color: #1B7F79; cursor: pointer; }
.val-box { font-size: 13px; font-weight: 700; color: #0D1B2A; min-width: 60px; text-align: right; background: #F7F9F9; border: 0.5px solid #E2E8F0; border-radius: 6px; padding: 4px 8px; }
.unit { font-size: 11px; color: #718096; }
.range-labels { display: flex; justify-content: space-between; font-size: 10px; color: #A0AEC0; margin-top: 2px; }
.search-btn { width: 100%; padding: 12px; background: #1B7F79; color: #fff; border: none; border-radius: 8px; font-size: 14px; font-weight: 600; cursor: pointer; margin-top: 4px; transition: background 0.2s; }
.search-btn:hover { background: #0F6E56; }
.results-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px; }
.results-header h2 { font-size: 13px; font-weight: 700; color: #1B7F79; text-transform: uppercase; letter-spacing: 0.8px; }
.badge { font-size: 11px; background: #E1F5EE; color: #085041; padding: 3px 10px; border-radius: 20px; font-weight: 600; }
.result-item { border: 0.5px solid #E2E8F0; border-radius: 10px; padding: 14px 16px; margin-bottom: 8px; display: grid; grid-template-columns: auto 1fr auto; gap: 12px; align-items: center; background: #FAFAFA; transition: border-color 0.2s; }
.result-item:hover { border-color: #1B7F79; background: #F0FAF7; }
.rank { font-size: 18px; font-weight: 700; color: #CBD5E0; width: 28px; text-align: center; }
.result-main { }
.result-arch { font-size: 13px; font-weight: 700; color: #0D1B2A; margin-bottom: 4px; }
.result-tags { display: flex; gap: 6px; flex-wrap: wrap; }
.tag { font-size: 11px; padding: 2px 8px; border-radius: 12px; font-weight: 600; }
.tag-conc { background: #EEF2F7; color: #4A5568; }
.tag-hz { background: #FDF3DC; color: #633806; }
.tag-arch { background: #EEEDFE; color: #26215C; }
.result-vals { text-align: right; }
.pred-val { font-size: 13px; font-weight: 700; color: #0D1B2A; }
.pred-label { font-size: 10px; color: #718096; }
.match-score { display: flex; flex-direction: column; align-items: flex-end; gap: 4px; }
.score-bar-wrap { width: 80px; height: 6px; background: #E2E8F0; border-radius: 3px; overflow: hidden; }
.score-bar { height: 100%; border-radius: 3px; background: #1B7F79; transition: width 0.4s; }
.score-label { font-size: 10px; color: #718096; }
.disclaimer { font-size: 11px; color: #A0AEC0; margin-top: 12px; line-height: 1.5; }
.r2-badges { display: flex; gap: 8px; margin-top: 8px; }
.r2-badge { font-size: 11px; padding: 3px 10px; border-radius: 20px; }
.r2-good { background: #E1F5EE; color: #085041; }
.r2-mod { background: #FDF3DC; color: #633806; }
.empty-state { text-align: center; padding: 32px; color: #A0AEC0; font-size: 13px; }
</style>
</head>
<body>
<div class="container">
  <div class="header">
    <h1>Hydrogel Recipe Tool</h1>
    <p>Enter target mechanical properties to find the closest PEG-alginate formulation</p>
    <div class="r2-badges">
      <span class="r2-badge r2-good">Stiffness model R² = 0.80</span>
      <span class="r2-badge r2-mod">τ₁ model R² = 0.25</span>
    </div>
  </div>
 
  <div class="card">
    <h2>Target Mechanical Properties</h2>
    <div class="inputs-grid">
      <div class="input-group">
        <label>Target Young's Modulus</label>
        <div class="input-row">
          <input type="range" id="kpa-slider" min="0.1" max="35" step="0.1" value="10" oninput="updateVal('kpa')">
          <div class="val-box"><span id="kpa-val">10.0</span> <span class="unit">kPa</span></div>
        </div>
        <div class="range-labels"><span>0.1 kPa</span><span>35 kPa</span></div>
      </div>
      <div class="input-group">
        <label>Target τ₁ (stress relaxation timescale)</label>
        <div class="input-row">
          <input type="range" id="tau-slider" min="1.0" max="2.5" step="0.01" value="1.5" oninput="updateVal('tau')">
          <div class="val-box"><span id="tau-val">1.50</span> <span class="unit">s</span></div>
        </div>
        <div class="range-labels"><span>1.0 s</span><span>2.5 s</span></div>
      </div>
    </div>
    <button class="search-btn" onclick="search()">Find Closest Formulations</button>
  </div>
 
  <div class="card" id="results-card" style="display:none">
    <div class="results-header">
      <h2>Top Matches</h2>
      <span class="badge" id="results-count"></span>
    </div>
    <div id="results-list"></div>
    <p class="disclaimer">Predictions based on multiple linear regression (kPa R²=0.80, τ₁ R²=0.25). Stiffness predictions are more reliable than τ₁ predictions. Validate experimentally before use. Match score reflects combined proximity to both targets.</p>
  </div>
</div>
 
<script>
const coeffs = {
  kPa: {
    intercept: -33.629,
    arm_number: 2.654,
    MW_kDa: 0.548,
    Concentration: 3.630,
    HZ_percent: 0.199
  },
  tau1: {
    intercept: 1.463,
    arm_number: 0.0303,
    MW_kDa: -0.0163,
    Concentration: 0.0303,
    HZ_percent: -0.00103
  }
};
 
const architectures = [
  { name: "4arm 5k",  arm: 4, mw: 5  },
  { name: "4arm 10k", arm: 4, mw: 10 },
  { name: "4arm 20k", arm: 4, mw: 20 },
  { name: "8arm 10k", arm: 8, mw: 10 },
  { name: "8arm 20k", arm: 8, mw: 20 }
];
 
const concentrations = [2, 4];
const hzValues = Array.from({length: 21}, (_, i) => i * 5);
 
function predict(arm, mw, conc, hz) {
  const kPa = coeffs.kPa.intercept +
    coeffs.kPa.arm_number * arm +
    coeffs.kPa.MW_kDa * mw +
    coeffs.kPa.Concentration * conc +
    coeffs.kPa.HZ_percent * hz;
  const tau1 = coeffs.tau1.intercept +
    coeffs.tau1.arm_number * arm +
    coeffs.tau1.MW_kDa * mw +
    coeffs.tau1.Concentration * conc +
    coeffs.tau1.HZ_percent * hz;
  return { kPa: Math.max(0, kPa), tau1: Math.max(1.0, tau1) };
}
 
function updateVal(type) {
  if (type === 'kpa') {
    document.getElementById('kpa-val').textContent = parseFloat(document.getElementById('kpa-slider').value).toFixed(1);
  } else {
    document.getElementById('tau-val').textContent = parseFloat(document.getElementById('tau-slider').value).toFixed(2);
  }
}
 
function search() {
  const targetKpa = parseFloat(document.getElementById('kpa-slider').value);
  const targetTau = parseFloat(document.getElementById('tau-slider').value);
 
  const results = [];
 
  for (const arch of architectures) {
    for (const conc of concentrations) {
      for (const hz of hzValues) {
        const pred = predict(arch.arm, arch.mw, conc, hz);
        if (pred.kPa <= 0 || pred.tau1 <= 0) continue;
 
        const kpaNorm = targetKpa > 0 ? Math.abs(pred.kPa - targetKpa) / targetKpa : Math.abs(pred.kPa - targetKpa);
        const tauNorm = Math.abs(pred.tau1 - targetTau) / targetTau;
        const score = (kpaNorm * 0.7) + (tauNorm * 0.3);
 
        results.push({ arch: arch.name, conc, hz, pred, kpaNorm, tauNorm, score });
      }
    }
  }
 
  results.sort((a, b) => a.score - b.score);
  const top = results.slice(0, 5);
  const maxScore = Math.max(...top.map(r => r.score));
 
  const list = document.getElementById('results-list');
  list.innerHTML = '';
 
  top.forEach((r, i) => {
    const matchPct = Math.max(0, Math.round((1 - r.score / (maxScore + 0.01)) * 100));
    const div = document.createElement('div');
    div.className = 'result-item';
    div.innerHTML = `
      <div class="rank">${i + 1}</div>
      <div class="result-main">
        <div class="result-arch">${r.arch}</div>
        <div class="result-tags">
          <span class="tag tag-conc">${r.conc} mM</span>
          <span class="tag tag-hz">${r.hz}% HZ</span>
          <span class="tag tag-arch">${100 - r.hz}% OX</span>
        </div>
      </div>
      <div class="result-vals">
        <div class="pred-val">${r.pred.kPa.toFixed(2)} kPa</div>
        <div class="pred-label">Predicted stiffness</div>
        <div class="pred-val" style="margin-top:4px">${r.pred.tau1.toFixed(3)} s</div>
        <div class="pred-label">Predicted τ₁</div>
      </div>
    `;
    list.appendChild(div);
  });
 
  document.getElementById('results-count').textContent = `Top ${top.length} of ${results.length} matches`;
  document.getElementById('results-card').style.display = 'block';
}
</script>
</body>
</html>
 

<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI 주식·ETF 분석기</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&display=swap');
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:'Inter',sans-serif;min-height:100vh;background:#0a0f1e;color:#e8eaf0}
.wrap{max-width:780px;margin:0 auto;padding:32px 20px}
.header{text-align:center;padding:48px 0 36px}
.logo{font-size:13px;letter-spacing:4px;text-transform:uppercase;color:#4a9eff;font-weight:500;margin-bottom:16px}
h1{font-size:38px;font-weight:300;line-height:1.2;color:#fff;letter-spacing:-1px}
h1 span{color:#4a9eff;font-weight:600}
.sub{margin-top:12px;color:#6b7a99;font-size:15px}
.search-box{background:#111827;border:1px solid #1e2d4a;border-radius:16px;padding:24px;margin-bottom:24px}
.input-row{display:flex;gap:12px}
.ticker-input{flex:1;background:#0a0f1e;border:1px solid #1e2d4a;border-radius:10px;padding:14px 18px;color:#e8eaf0;font-size:16px;font-family:inherit;outline:none;transition:border-color .2s}
.ticker-input:focus{border-color:#4a9eff}
.ticker-input::placeholder{color:#3a4a62}
.analyze-btn{background:#4a9eff;color:#fff;border:none;border-radius:10px;padding:14px 28px;font-size:15px;font-weight:600;cursor:pointer;font-family:inherit;transition:background .15s}
.analyze-btn:hover{background:#3a8ef0}
.analyze-btn:disabled{background:#1e2d4a;color:#3a4a62;cursor:not-allowed}
.examples{margin-top:14px;display:flex;gap:8px;flex-wrap:wrap;align-items:center}
.ex-label{font-size:12px;color:#3a4a62}
.ex-btn{background:#111827;border:1px solid #1e2d4a;border-radius:6px;padding:5px 12px;font-size:12px;color:#6b7a99;cursor:pointer;font-family:inherit}
.ex-btn:hover{border-color:#4a9eff;color:#4a9eff}
.status{text-align:center;padding:40px;color:#4a9eff;font-size:14px;display:none}
.spinner{width:32px;height:32px;border:2px solid #1e2d4a;border-top-color:#4a9eff;border-radius:50%;animation:spin 0.8s linear infinite;margin:0 auto 16px}
@keyframes spin{to{transform:rotate(360deg)}}
.result{display:none}
.ticker-header{display:flex;align-items:baseline;gap:14px;margin-bottom:24px;padding-bottom:20px;border-bottom:1px solid #1e2d4a;flex-wrap:wrap}
.ticker-name{font-size:28px;font-weight:600;color:#fff}
.ticker-sym{font-size:14px;color:#4a9eff;background:#0d1929;border:1px solid #1e2d4a;border-radius:6px;padding:3px 10px}
.price-row{margin-left:auto;text-align:right}
.price{font-size:26px;font-weight:300;color:#fff}
.change.up{color:#22c55e;font-size:13px}
.change.dn{color:#ef4444;font-size:13px}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:12px}
.grid-3{display:grid;grid-template-columns:1fr 1fr 1fr;gap:12px;margin-bottom:12px}
.card{background:#111827;border:1px solid #1e2d4a;border-radius:12px;padding:18px}
.card-label{font-size:11px;letter-spacing:2px;text-transform:uppercase;color:#3a4a62;margin-bottom:8px}
.card-val{font-size:20px;font-weight:500;color:#e8eaf0}
.card-sub{font-size:12px;color:#6b7a99;margin-top:4px}
.ai-card{background:#0d1929;border:1px solid #1e3a5f;border-radius:12px;padding:22px;margin-bottom:12px}
.ai-label{font-size:11px;letter-spacing:2px;text-transform:uppercase;color:#4a9eff;margin-bottom:12px;display:flex;align-items:center;gap:8px}
.ai-label::before{content:'';display:inline-block;width:6px;height:6px;background:#4a9eff;border-radius:50%}
.ai-text{font-size:14px;line-height:1.8;color:#c8d0e0;white-space:pre-wrap}
.score-row{display:flex;align-items:center;gap:14px;margin-bottom:16px}
.score-num{font-size:48px;font-weight:600;line-height:1}
.score-num.high{color:#22c55e}
.score-num.mid{color:#f59e0b}
.score-num.low{color:#ef4444}
.score-bar-bg{background:#1e2d4a;border-radius:4px;height:6px;margin-bottom:8px}
.score-bar-fill{height:6px;border-radius:4px;transition:width .8s ease}
.score-bar-fill.high{background:#22c55e}
.score-bar-fill.mid{background:#f59e0b}
.score-bar-fill.low{background:#ef4444}
.score-desc{font-size:13px;color:#6b7a99}
.error{background:#1a0a0a;border:1px solid #3a1a1a;border-radius:12px;padding:20px;color:#ef4444;font-size:14px;display:none;text-align:center}
.ad-slot{background:#0d1118;border:1px dashed #1e2d4a;border-radius:8px;padding:16px;text-align:center;color:#2a3a52;font-size:12px;margin:16px 0}
.disclaimer{font-size:11px;color:#2a3a52;text-align:center;margin-top:24px;line-height:1.8;padding-bottom:32px}
</style>
</head>
<body>
<div class="wrap">
  <div class="header">
    <div class="logo">AI Stock Analyzer</div>
    <h1>종목 하나, <span>AI 분석</span> 즉시</h1>
    <p class="sub">티커를 입력하면 재무 데이터 + AI 투자 분석을 30초 안에 제공합니다</p>
  </div>

  <div class="ad-slot">[ 광고 영역 — Google AdSense ]</div>

  <div class="search-box">
    <div class="input-row">
      <input class="ticker-input" id="tickerInput" placeholder="티커 입력 (예: AAPL, NVDA, QQQ)" maxlength="10" oninput="this.value=this.value.toUpperCase()">
      <button class="analyze-btn" id="analyzeBtn" onclick="startAnalysis()">분석하기</button>
    </div>
    <div class="examples">
      <span class="ex-label">예시</span>
      <button class="ex-btn" onclick="setTicker('AAPL')">AAPL</button>
      <button class="ex-btn" onclick="setTicker('NVDA')">NVDA</button>
      <button class="ex-btn" onclick="setTicker('MSFT')">MSFT</button>
      <button class="ex-btn" onclick="setTicker('QQQ')">QQQ</button>
      <button class="ex-btn" onclick="setTicker('TQQQ')">TQQQ</button>
      <button class="ex-btn" onclick="setTicker('TSLA')">TSLA</button>
    </div>
  </div>

  <div class="status" id="status">
    <div class="spinner"></div>
    <div id="statusText">데이터 수집 중...</div>
  </div>

  <div class="error" id="errorBox"></div>

  <div class="result" id="result">
    <div class="ticker-header">
      <div class="ticker-name" id="rName">—</div>
      <div class="ticker-sym" id="rSym">—</div>
      <div class="price-row">
        <div class="price" id="rPrice">—</div>
        <div class="change" id="rChange">—</div>
      </div>
    </div>
    <div class="ai-card">
      <div class="ai-label">AI 종합 투자 점수</div>
      <div class="score-row">
        <div class="score-num" id="scoreNum">—</div>
        <div style="flex:1">
          <div class="score-bar-bg"><div class="score-bar-fill" id="scoreBar" style="width:0%"></div></div>
          <div class="score-desc" id="scoreDesc">—</div>
        </div>
      </div>
    </div>
    <div class="grid-3">
      <div class="card"><div class="card-label">시가총액</div><div class="card-val" id="rMcap">—</div></div>
      <div class="card"><div class="card-label">P/E 비율</div><div class="card-val" id="rPe">—</div><div class="card-sub" id="rPeSub"></div></div>
      <div class="card"><div class="card-label">52주 범위</div><div class="card-val" id="r52">—</div></div>
    </div>
    <div class="grid">
      <div class="card"><div class="card-label">배당 수익률</div><div class="card-val" id="rDiv">—</div></div>
      <div class="card"><div class="card-label">베타 (변동성)</div><div class="card-val" id="rBeta">—</div><div class="card-sub" id="rBetaSub"></div></div>
    </div>
    <div class="ai-card">
      <div class="ai-label">핵심 투자 포인트</div>
      <div class="ai-text" id="aiStrength">—</div>
    </div>
    <div class="ai-card">
      <div class="ai-label">주요 리스크</div>
      <div class="ai-text" id="aiRisk">—</div>
    </div>
    <div class="ai-card">
      <div class="ai-label">초보 투자자를 위한 한줄 요약</div>
      <div class="ai-text" id="aiSummary">—</div>
    </div>
    <div class="ad-slot">[ 광고 영역 — Google AdSense ]</div>
  </div>

  <div class="disclaimer">
    본 서비스는 투자 참고용 정보를 제공하며 투자 권유가 아닙니다.<br>
    모든 투자 결정은 본인 책임이며 손실에 대해 책임지지 않습니다.
  </div>
</div>

<script>
const WORKER_URL = 'https://spayahoo-proxy.rlaalsdn0691.workers.dev';
const CLAUDE_API = 'https://api.anthropic.com/v1/messages';

function setTicker(t){ document.getElementById('tickerInput').value=t; }
document.getElementById('tickerInput').addEventListener('keydown',e=>{ if(e.key==='Enter') startAnalysis(); });

function fmt(n){
  if(!n||isNaN(n)) return 'N/A';
  if(n>=1e12) return '$'+(n/1e12).toFixed(2)+'T';
  if(n>=1e9) return '$'+(n/1e9).toFixed(1)+'B';
  if(n>=1e6) return '$'+(n/1e6).toFixed(1)+'M';
  return '$'+n.toFixed(2);
}

async function fetchYahoo(ticker){
  const res = await fetch(`${WORKER_URL}/?ticker=${ticker}`);
  const d = await res.json();
  const qs = d.quoteSummary.result[0];
  const price = qs.price;
  const sd = qs.summaryDetail;
  const ks = qs.defaultKeyStatistics;
  return {
    name: price.longName||price.shortName||ticker,
    symbol: ticker.toUpperCase(),
    price: price.regularMarketPrice.raw,
    prevClose: price.regularMarketPreviousClose.raw,
    change: ((price.regularMarketPrice.raw - price.regularMarketPreviousClose.raw)/price.regularMarketPreviousClose.raw*100).toFixed(2),
    marketCap: price.marketCap?.raw,
    pe: sd.trailingPE?.raw,
    forwardPe: sd.forwardPE?.raw,
    low52: sd.fiftyTwoWeekLow?.raw,
    high52: sd.fiftyTwoWeekHigh?.raw,
    dividendYield: sd.dividendYield?.raw,
    beta: sd.beta?.raw,
  };
}

async function askClaude(data){
  const prompt = `당신은 주식 분석 전문가입니다. 아래 데이터를 바탕으로 초보 한국 투자자를 위한 분석을 작성하세요.

종목: ${data.name} (${data.symbol})
현재가: $${data.price}
등락률: ${data.change}%
시가총액: ${fmt(data.marketCap)}
P/E: ${data.pe?.toFixed(1)||'N/A'} (Forward P/E: ${data.forwardPe?.toFixed(1)||'N/A'})
52주 범위: $${data.low52?.toFixed(2)||'?'} ~ $${data.high52?.toFixed(2)||'?'}
배당수익률: ${data.dividendYield?(data.dividendYield*100).toFixed(2)+'%':'없음'}
베타: ${data.beta?.toFixed(2)||'N/A'}

다음 JSON 형식으로만 응답하세요 (마크다운 없이):
{"score":75,"scoreDesc":"점수 이유 한줄","strength":"투자포인트1\n투자포인트2\n투자포인트3","risk":"리스크1\n리스크2","summary":"30자 이내 한줄 요약"}`;

  const res = await fetch(CLAUDE_API, {
    method:'POST',
    headers:{'Content-Type':'application/json'},
    body: JSON.stringify({
      model:'claude-sonnet-4-20250514',
      max_tokens:1000,
      messages:[{role:'user',content:prompt}]
    })
  });
  const d = await res.json();
  const raw = d.content[0].text.replace(/```json|```/g,'').trim();
  return JSON.parse(raw);
}

async function startAnalysis(){
  const ticker = document.getElementById('tickerInput').value.trim().toUpperCase();
  if(!ticker) return;
  const btn = document.getElementById('analyzeBtn');
  const status = document.getElementById('status');
  const result = document.getElementById('result');
  const errorBox = document.getElementById('errorBox');
  btn.disabled=true;
  status.style.display='block';
  result.style.display='none';
  errorBox.style.display='none';
  try{
    document.getElementById('statusText').textContent='Yahoo Finance 데이터 수집 중...';
    const data = await fetchYahoo(ticker);
    document.getElementById('statusText').textContent='AI 분석 생성 중...';
    const ai = await askClaude(data);
    document.getElementById('rName').textContent=data.name;
    document.getElementById('rSym').textContent=data.symbol;
    document.getElementById('rPrice').textContent='$'+data.price.toFixed(2);
    const chEl=document.getElementById('rChange');
    const chVal=parseFloat(data.change);
    chEl.textContent=(chVal>=0?'▲ +':'▼ ')+data.change+'%';
    chEl.className='change '+(chVal>=0?'up':'dn');
    document.getElementById('rMcap').textContent=fmt(data.marketCap);
    document.getElementById('rPe').textContent=data.pe?.toFixed(1)||'N/A';
    document.getElementById('rPeSub').textContent=data.forwardPe?'Forward: '+data.forwardPe.toFixed(1):'';
    document.getElementById('r52').textContent=data.low52&&data.high52?'$'+data.low52.toFixed(0)+'~$'+data.high52.toFixed(0):'N/A';
    document.getElementById('rDiv').textContent=data.dividendYield?(data.dividendYield*100).toFixed(2)+'%':'없음';
    const beta=data.beta;
    document.getElementById('rBeta').textContent=beta?.toFixed(2)||'N/A';
    document.getElementById('rBetaSub').textContent=beta?(beta>1.5?'고변동성':beta<0.8?'저변동성':'시장 유사'):'';
    const score=ai.score||50;
    const sEl=document.getElementById('scoreNum');
    const bEl=document.getElementById('scoreBar');
    sEl.textContent=score;
    sEl.className='score-num '+(score>=70?'high':score>=40?'mid':'low');
    bEl.style.width=score+'%';
    bEl.className='score-bar-fill '+(score>=70?'high':score>=40?'mid':'low');
    document.getElementById('scoreDesc').textContent=ai.scoreDesc||'';
    document.getElementById('aiStrength').textContent=ai.strength||'';
    document.getElementById('aiRisk').textContent=ai.risk||'';
    document.getElementById('aiSummary').textContent=ai.summary||'';
    status.style.display='none';
    result.style.display='block';
  }catch(e){
    status.style.display='none';
    errorBox.style.display='block';
    errorBox.textContent='분석 실패: '+e.message+' — 티커를 다시 확인해주세요';
  }
  btn.disabled=false;
}
</script>
</body>
</html>

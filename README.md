<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>빅맥 경험치계산기</title>
  <style>
    :root{
      --w: 392px;
      --h: 850px;
      --drawer-w: 320px;
      --gap: 8px;
      --bg: #f6f7fb;
      --card: #ffffff;
      --line: #e6e7ee;
      --text: #111;
      --muted: #666;
      --btn: #2b6cb0;
      --btn2:#4a5568;
      --danger:#c53030;
      --radius: 12px;
      --shadow: 0 8px 24px rgba(0,0,0,.10);
      --font: system-ui, -apple-system, Segoe UI, Roboto, "Noto Sans KR", Arial, sans-serif;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      min-height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      background:linear-gradient(180deg,#eef1ff,#f8f9ff);
      font-family: var(--font);
      color:var(--text);
    }
    .app{
      position:relative;
      display:flex;
      align-items:stretch;
      gap: var(--gap);
    }
    .main{
      width: var(--w);
      height: var(--h);
      background: var(--card);
      border: 1px solid var(--line);
      border-radius: var(--radius);
      box-shadow: var(--shadow);
      overflow:hidden;
      display:flex;
      flex-direction:column;
    }
    .topbar{
      padding:10px;
      border-bottom:1px solid var(--line);
      display:flex;
      gap:8px;
      align-items:center;
      background: #fbfbff;
    }
    .title{
      font-weight:700;
      font-size:14px;
      margin-left:auto;
      color:#222;
      opacity:.9;
      white-space:nowrap;
    }
    .content{
      padding:10px;
      display:flex;
      flex-direction:column;
      gap:10px;
      height:100%;
      min-height:0;
    }
    .group{
      border:1px solid var(--line);
      border-radius:10px;
      padding:10px;
      background: var(--bg);
    }
    .group h3{
      margin:0 0 8px 0;
      font-size:13px;
    }
    .grid{
      display:grid;
      grid-template-columns: 1fr 1.2fr;
      gap:8px 10px;
      align-items:center;
    }
    label{ font-size:12px; color:#222; }
    input, select{
      width:100%;
      padding:8px 10px;
      border-radius:10px;
      border:1px solid var(--line);
      background:#fff;
      outline:none;
      font-size:13px;
    }
    input:disabled{
      background:#f0f1f6;
      color:#777;
    }
    .row{
      display:flex;
      gap:8px;
      align-items:center;
      flex-wrap:wrap;
    }
    .row small{color:var(--muted)}
    .buttons{
      display:flex;
      gap:8px;
      align-items:center;
      flex-wrap:wrap;
    }
    button{
      border:none;
      border-radius:10px;
      padding:9px 12px;
      cursor:pointer;
      font-weight:700;
      font-size:13px;
      color:#fff;
      background: var(--btn);
    }
    button.secondary{ background: var(--btn2); }
    button.danger{ background: var(--danger); }
    button.ghost{
      background:transparent;
      color: var(--btn2);
      border:1px solid var(--line);
      font-weight:700;
    }

    .result{
      flex:1;
      min-height:0;
      border:1px solid var(--line);
      background:#fff;
      border-radius:10px;
      padding:10px;
      font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", monospace;
      font-size:13px;
      white-space:pre-wrap;
      overflow:auto;
    }

    .drawer{
      width:0;
      height: var(--h);
      overflow:hidden;
      background: var(--card);
      border: 1px solid var(--line);
      border-radius: var(--radius);
      box-shadow: var(--shadow);
      transition: width 220ms ease;
    }
    .drawer.open{ width: var(--drawer-w); }
    .drawer-inner{
      width: var(--drawer-w);
      height:100%;
      display:flex;
      flex-direction:column;
      min-height:0;
    }
    .drawer-head{
      padding:10px;
      border-bottom:1px solid var(--line);
      display:flex;
      align-items:center;
      justify-content:space-between;
      background:#fbfbff;
    }
    .drawer-head b{ font-size:13px; }
    .drawer-body{
      padding:10px;
      display:flex;
      flex-direction:column;
      gap:10px;
      height:100%;
      min-height:0;
    }

    .listbox{
      border:1px solid var(--line);
      border-radius:10px;
      background:#fff;
      padding:0;
      margin:0;
      list-style:none;
      height:170px;
      overflow:auto;
    }
    .listbox li{
      padding:9px 10px;
      border-bottom:1px solid #f0f1f6;
      cursor:pointer;
      font-size:13px;
      display:flex;
      justify-content:space-between;
      gap:10px;
    }
    .listbox li:last-child{ border-bottom:none; }
    .listbox li.active{
      background:#edf2ff;
      outline: 2px solid rgba(43,108,176,.15);
    }
    .muted{ color: var(--muted); font-size:12px; }
    .footer-note{
      text-align:center;
      color:#888;
      font-size:11px;
      margin-top:-2px;
    }

    .timeRow{
      display:flex;
      gap:8px;
      align-items:center;
      flex-wrap:wrap;
    }
    .timeRow input{ width:84px; }
    .small{ font-size:12px; color: var(--muted); }
  </style>
</head>
<body>
  <div class="app">

    <div class="drawer" id="meterDrawer">
      <div class="drawer-inner">
        <div class="drawer-head">
          <b>경험치측정기</b>
          <button class="ghost" id="closeMeterBtn">✕</button>
        </div>

        <div class="drawer-body">
          <div class="group" style="background:var(--bg);">
            <h3>(A) 시간기록 → 시간당 얻는 경험치</h3>

            <div class="grid">
              <label style="grid-column:1/-1;font-weight:800;">현재 (시작)</label>

              <label>현재 레벨</label>
              <input id="a_curLevel" inputmode="numeric" value="5" />

              <label>현재 경험치(%)</label>
              <input id="a_curPct" inputmode="decimal" value="0" />

              <label>시작 시간</label>
              <div class="timeRow">
                <input id="a_startH" inputmode="numeric" value="16" />
                <span class="small">시</span>
                <input id="a_startM" inputmode="numeric" value="0" />
                <span class="small">분</span>
              </div>

              <div style="grid-column:1/-1;height:1px;background:var(--line);margin:6px 0;"></div>

              <label style="grid-column:1/-1;font-weight:800;">목표 (도착)</label>

              <label>목표 레벨</label>
              <input id="a_targetLevel" inputmode="numeric" value="10" />

              <label>목표 경험치(%)</label>
              <input id="a_targetPct" inputmode="decimal" value="0" />

              <label>종료 시간</label>
              <div class="timeRow">
                <input id="a_endH" inputmode="numeric" value="16" />
                <span class="small">시</span>
                <input id="a_endM" inputmode="numeric" value="30" />
                <span class="small">분</span>
              </div>

              <label>경험치 배율</label>
              <div class="row">
                <label style="display:flex;align-items:center;gap:6px;margin:0;">
                  <input type="checkbox" id="a_mulCheck" />
                  <span style="font-size:13px;">적용</span>
                </label>
                <input id="a_mulPct" inputmode="numeric" style="width:90px;" disabled value="100" />
                <small>% (A는 나누기 적용)</small>
              </div>
            </div>

            <div class="buttons" style="margin-top:10px;">
              <button id="a_calcBtn">계산</button>
              <button class="secondary" id="a_resetBtn">초기화</button>
            </div>

            <div class="muted" style="margin-top:8px;line-height:1.4;">
              - 종료시간은 시작시간보다 반드시 늦어야 합니다. (다음날 계산 미지원)<br />
              - 시간 차이는 최대 24시간(1440분)까지만 계산합니다.
            </div>

            <div class="result" id="a_resultBox" style="margin-top:10px;height:110px;min-height:110px;">
시간당 얻는 경험치: -
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="main">
      <div class="topbar">
        <button class="secondary" id="toggleMeterBtn">경험치측정기 ▶</button>
        <button class="secondary" id="toggleDrawerBtn">사냥터경험치란 ▶</button>
        <div class="title">레벨업 체크</div>
      </div>

      <div class="content">
        <div class="group">
          <h3>정보입력</h3>

          <div class="grid">
            <label style="grid-column:1/-1;font-weight:800;">현재(시작)</label>

            <label>현재 레벨</label>
            <input id="curLevel" inputmode="numeric" value="5" />

            <label>현재 경험치(%)</label>
            <input id="curPct" inputmode="decimal" value="0" placeholder="0 ~ 99.99" />

            <div style="grid-column:1/-1;height:1px;background:var(--line);margin:6px 0;"></div>

            <label style="grid-column:1/-1;font-weight:800;">목표(도착)</label>

            <label>목표 레벨</label>
            <input id="targetLevel" inputmode="numeric" value="10" />

            <label>목표 경험치(%)</label>
            <input id="targetPct" inputmode="decimal" value="0" placeholder="0 ~ 99.99" />

            <div style="grid-column:1/-1;height:1px;background:var(--line);margin:6px 0;"></div>

            <label>사냥터선택</label>
            <select id="presetSelect"></select>

            <label>경험치 배율</label>
            <div class="row">
              <label style="display:flex;align-items:center;gap:6px;margin:0;">
                <input type="checkbox" id="mulCheck" />
                <span style="font-size:13px;">배율 적용</span>
              </label>
              <input id="mulPct" inputmode="decimal" style="width:90px;" disabled value="100" />
              <small>%</small>
            </div>
          </div>

          <div class="buttons" style="margin-top:10px;">
            <button id="calcBtn">계산</button>
            <button class="secondary" id="resetBtn">초기화</button>
          </div>
        </div>

        <div class="result" id="resultBox">총 필요 경험치: -
걸리는 시간: -</div>
        <div class="footer-note">저장 기능 제거됨 (새로고침하면 초기화)</div>
      </div>
    </div>

    <div class="drawer" id="drawer">
      <div class="drawer-inner">
        <div class="drawer-head">
          <b>사냥터경험치란</b>
          <button class="ghost" id="closeDrawerBtn">✕</button>
        </div>

        <div class="drawer-body">
          <div class="group" style="background:var(--bg);">
            <h3>사냥터리스트</h3>
            <ul class="listbox" id="presetList"></ul>

            <div class="grid" style="margin-top:10px;">
              <label>사냥터이름</label>
              <input id="pName" />

              <label>시간당경험치획득량</label>
              <input id="pValue" inputmode="numeric" />
            </div>

            <div class="buttons" style="margin-top:10px;">
              <button id="addBtn">추가</button>
              <button class="danger" id="deleteBtn">삭제</button>
            </div>

            <div class="muted" style="margin-top:8px;">
              - 이 리스트 추가/삭제는 새로고침하면 초기화됩니다.<br />
              - 소스코드 defaultData()의 presets에 넣은 항목만 "기본값"으로 유지됩니다.
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>

<script>
(function(){
  "use strict";

  var XP_TO_NEXT = {
    1: 2, 2: 6, 3: 17, 4: 37, 5: 67,
    6: 111, 7: 169, 8: 247, 9: 344, 10: 464,
    11: 609, 12: 783, 13: 985, 14: 1221, 15: 1491,
    16: 1799, 17: 2145, 18: 2535, 19: 2968, 20: 3448,
    21: 3977, 22: 4559, 23: 5193, 24: 5885, 25: 6635,
    26: 7447, 27: 8321, 28: 9263, 29: 10272, 30: 11352,
    31: 12505, 32: 13735, 33: 15041, 34: 16429, 35: 17899,
    36: 19455, 37: 21097, 38: 22831, 39: 24656, 40: 26576,
    41: 28593, 42: 30711, 43: 32931, 44: 35251, 45: 37683,
    46: 40223, 47: 42873, 48: 45639, 49: 48520, 50: 51520,
    51: 54641, 52: 57887, 53: 61257, 54: 64757, 55: 68387,
    56: 72151, 57: 76049, 58: 80087, 59: 84264, 60: 106110,
    61: 113411, 62: 121150, 63: 129351, 64: 138044, 65: 147256,
    66: 157020, 67: 167366, 68: 178333, 69: 189959, 70: 202281,
    71: 215349, 72: 229204, 73: 243902, 74: 259494, 75: 276042,
    76: 293606, 77: 312258, 78: 332071, 79: 353126, 80: 375510,
    81: 399319, 82: 424654, 83: 451632, 84: 480370, 85: 511006,
    86: 543687, 87: 578571, 88: 616838, 89: 655680, 90: 698312,
    91: 743970, 92: 795918, 93: 842442, 94: 901869, 95: 962553,
    96: 1026899, 97: 1098354, 98: 1174419, 99: 1256664, 100: 1351164,
    101: 1450249, 102: 1606624, 103: 1779646, 104: 1993203, 105: 2219985,
    106: 2486383, 107: 2784749, 108: 3118918, 109: 3493189, 110: 3890515,
    111: 4357377, 112: 4880262, 113: 5465893, 114: 6121801, 115: 6817898,
    116: 7636046, 117: 8552370, 118: 9578655, 119: 10728094, 120: 11947580,
    121: 12381234, 122: 13485213, 123: 14951015, 124: 16215412, 125: 18045123,
    126: 19754512, 127: 22547852, 128: 25541512, 129: 28481215, 130: 32515121,
    131: 36754112, 132: 41541124, 133: 46112135, 134: 52715121, 135: 58145121,
    136: 64112127, 137: 70115421, 138: 76121854, 139: 81154121
  };

  var MAX_LEVEL = 139;
  var MAX_MINUTES = 24 * 60;

  function $(id){ return document.getElementById(id); }

  var els = {
    drawer: $("drawer"),
    toggleDrawerBtn: $("toggleDrawerBtn"),
    closeDrawerBtn: $("closeDrawerBtn"),
    meterDrawer: $("meterDrawer"),
    toggleMeterBtn: $("toggleMeterBtn"),
    closeMeterBtn: $("closeMeterBtn"),
    curLevel: $("curLevel"),
    curPct: $("curPct"),
    targetLevel: $("targetLevel"),
    targetPct: $("targetPct"),
    presetSelect: $("presetSelect"),
    mulCheck: $("mulCheck"),
    mulPct: $("mulPct"),
    calcBtn: $("calcBtn"),
    resetBtn: $("resetBtn"),
    resultBox: $("resultBox"),
    presetList: $("presetList"),
    pName: $("pName"),
    pValue: $("pValue"),
    addBtn: $("addBtn"),
    deleteBtn: $("deleteBtn"),
    a_curLevel: $("a_curLevel"),
    a_curPct: $("a_curPct"),
    a_startH: $("a_startH"),
    a_startM: $("a_startM"),
    a_targetLevel: $("a_targetLevel"),
    a_targetPct: $("a_targetPct"),
    a_endH: $("a_endH"),
    a_endM: $("a_endM"),
    a_mulCheck: $("a_mulCheck"),
    a_mulPct: $("a_mulPct"),
    a_calcBtn: $("a_calcBtn"),
    a_resetBtn: $("a_resetBtn"),
    a_resultBox: $("a_resultBox")
  };

  function setResult(text){ els.resultBox.textContent = text; }
  function setAResult(text){ els.a_resultBox.textContent = text; }

  function round2(x){ return Math.round((x + 1e-12) * 100) / 100; }
  function clampPct(p){
    p = round2(p);
    if (p < 0) p = 0;
    if (p > 99.99) p = 99.99;
    return p;
  }

  function fmtDaysHoursMins(totalMinutes){
    var m = Math.max(0, Math.floor(totalMinutes));
    var days = Math.floor(m / (24*60));
    var rem = m % (24*60);
    var hours = Math.floor(rem / 60);
    var mins = rem % 60;
    return (days > 0) ? (days + "일 " + hours + "시간 " + mins + "분") : (hours + "시간 " + mins + "분");
  }

  function parsePctAllowBlank(s){
    var raw = (s || "").trim();
    if (raw === "") return 0.0;
    var v = Number(raw);
    if (!isFinite(v)) throw new Error("경험치(%)는 숫자로 입력하세요.");
    if (v > 99.99) throw new Error("경험치는최대99.99까지입력가능합니다");
    return v;
  }

  function parseHM(hVal, mVal){
    var hh = Number(String(hVal || "").trim());
    var mm = Number(String(mVal || "").trim());
    if (!Number.isInteger(hh) || hh < 0 || hh > 23) throw new Error("시간(시)은 0~23 범위로 입력하세요.");
    if (!Number.isInteger(mm) || mm < 0 || mm > 59) throw new Error("시간(분)은 0~59 범위로 입력하세요.");
    return hh * 60 + mm;
  }

  function getMultiplier(checkedEl, pctEl){
    if (!checkedEl.checked) return null;
    var pct = Number(String(pctEl.value || "").trim());
    if (!isFinite(pct) || pct <= 0) throw new Error("배율(%)은 0보다 큰 숫자로 입력하세요.");
    return round2(pct / 100.0);
  }

  function calcNeedXp(curLevel, curPct, targetLevel, targetPct){
    if (targetLevel < curLevel) return 0.0;
    if (!XP_TO_NEXT.hasOwnProperty(curLevel)) throw new Error(curLevel + "레벨 데이터가 없습니다.");
    if (targetLevel > MAX_LEVEL + 1) throw new Error("목표 레벨은 최대 " + (MAX_LEVEL + 1) + "까지만 지원합니다.");

    curPct = clampPct(curPct);

    if (targetLevel === MAX_LEVEL + 1){
      targetPct = 0.0;
    } else {
      targetPct = clampPct(targetPct);
      if (!XP_TO_NEXT.hasOwnProperty(targetLevel)) throw new Error(targetLevel + "레벨 데이터가 없습니다.");
    }

    if (targetLevel === curLevel){
      if (targetPct <= curPct) return 0.0;
      return ((targetPct - curPct)/100.0) * XP_TO_NEXT[curLevel];
    }

    var curRemain = ((100.0 - curPct)/100.0) * XP_TO_NEXT[curLevel];

    var midSum = 0.0;
    var lvl;
    for (lvl = curLevel + 1; lvl < targetLevel; lvl++){
      if (!XP_TO_NEXT.hasOwnProperty(lvl)) throw new Error(lvl + "레벨 데이터가 없습니다.");
      midSum += XP_TO_NEXT[lvl];
    }

    var targetPart = 0.0;
    if (targetLevel <= MAX_LEVEL){
      targetPart = (targetPct/100.0) * XP_TO_NEXT[targetLevel];
    }

    return curRemain + midSum + targetPart;
  }

  function defaultData(){
    return {
      presets: [
        {"name": "100제 고순", "value": 800000},
        {"name": "120제 고순", "value": 1},
        {"name": "100제 저순", "value": 1},
        {"name": "120제 저순", "value": 1}
      ]
    };
  }

  var presets = defaultData().presets.slice();
  var selectedIndex = null;

  function presetText(it){
    return it.name + " (" + Number(it.value).toLocaleString("ko-KR") + ")";
  }

  function refreshPresetSelect(){
    els.presetSelect.innerHTML = "";
    if (!presets.length){
      var opt0 = document.createElement("option");
      opt0.value = "";
      opt0.textContent = "(프리셋 없음)";
      els.presetSelect.appendChild(opt0);
      return;
    }
    for (var i=0;i<presets.length;i++){
      var opt = document.createElement("option");
      opt.value = String(i);
      opt.textContent = presetText(presets[i]);
      els.presetSelect.appendChild(opt);
    }
    els.presetSelect.value = "0";
  }

  function refreshPresetList(){
    els.presetList.innerHTML = "";
    for (var i=0;i<presets.length;i++){
      (function(i){
        var it = presets[i];
        var li = document.createElement("li");
        if (selectedIndex === i) li.className = "active";

        var left = document.createElement("span");
        left.textContent = it.name;

        var right = document.createElement("span");
        right.className = "muted";
        right.textContent = Number(it.value).toLocaleString("ko-KR");

        li.appendChild(left);
        li.appendChild(right);

        li.addEventListener("click", function(){
          selectedIndex = i;
          els.pName.value = it.name;
          els.pValue.value = String(it.value);
          refreshPresetList();
        });

        els.presetList.appendChild(li);
      })(i);
    }
  }

  function openRight(){
    els.drawer.classList.add("open");
    els.toggleDrawerBtn.textContent = "사냥터경험치란 ◀";
  }
  function closeRight(){
    els.drawer.classList.remove("open");
    els.toggleDrawerBtn.textContent = "사냥터경험치란 ▶";
  }
  function toggleRight(){
    if (els.drawer.classList.contains("open")) closeRight();
    else openRight();
  }

  function openLeft(){
    els.meterDrawer.classList.add("open");
    els.toggleMeterBtn.textContent = "경험치측정기 ▶";
  }
  function closeLeft(){
    els.meterDrawer.classList.remove("open");
    els.toggleMeterBtn.textContent = "경험치측정기 ◀";
  }
  function toggleLeft(){
    if (els.meterDrawer.classList.contains("open")) closeLeft();
    else openLeft();
  }

  function getSelectedPresetValue(){
    if (!presets.length) throw new Error("시간당 XP(사냥터 항목)을 선택하세요.");
    var idx = Number(els.presetSelect.value);
    if (!(idx >= 0 && idx < presets.length)) throw new Error("시간당 XP(사냥터 항목)을 선택하세요.");
    var v = presets[idx].value;
    if (!isFinite(v) || v <= 0) throw new Error("시간당 XP는 0보다 커야 합니다.");
    return v;
  }

  function calcMain(){
    try{
      var curLevel = Number((els.curLevel.value || "").trim() || "0");
      var curPct = parsePctAllowBlank(els.curPct.value);
      var targetLevel = Number((els.targetLevel.value || "").trim() || "0");
      var targetPct = parsePctAllowBlank(els.targetPct.value);

      if (!(curLevel >= 1 && curLevel <= MAX_LEVEL)) throw new Error("현재 레벨은 1 ~ " + MAX_LEVEL + "로 입력하세요.");
      if (!(targetLevel >= 1 && targetLevel <= (MAX_LEVEL + 1))) throw new Error("목표 레벨은 1 ~ " + (MAX_LEVEL + 1) + "로 입력하세요.");

      var perHour = getSelectedPresetValue();
      var needXp = calcNeedXp(curLevel, curPct, targetLevel, targetPct);
      if (needXp <= 0) throw new Error("목표가 현재보다 높아야 합니다. (레벨/목표% 확인)");

      var minutesBase = (needXp / perHour) * 60.0;
      var text = "총 필요 경험치: " + Math.round(needXp).toLocaleString("ko-KR") +
                 "\n걸리는 시간: " + fmtDaysHoursMins(minutesBase);

      if (els.mulCheck.checked){
        var pct = Number((els.mulPct.value || "").trim() || "0");
        if (!isFinite(pct) || pct <= 0) throw new Error("배율(%)은 0보다 큰 숫자로 입력하세요.");
        var mult = round2(pct / 100.0);
        var perHourMul = perHour * mult;
        var minutesMul = (needXp / perHourMul) * 60.0;
        text += "\n" + mult + "배 적용시 걸리는 시간: " + fmtDaysHoursMins(minutesMul);
      }

      setResult(text);
    }catch(e){
      alert(String(e.message || e));
    }
  }

  function resetMain(){
    els.curLevel.value = "5";
    els.curPct.value = "0";
    els.targetLevel.value = "10";
    els.targetPct.value = "0";
    els.mulCheck.checked = false;
    els.mulPct.value = "100";
    els.mulPct.disabled = true;
    setResult("총 필요 경험치: -\n걸리는 시간: -");
  }

  function calcA(){
    try{
      var curLevel = Number(String(els.a_curLevel.value || "").trim());
      var targetLevel = Number(String(els.a_targetLevel.value || "").trim());
      if (!Number.isInteger(curLevel) || curLevel < 1 || curLevel > MAX_LEVEL) throw new Error("현재 레벨은 1 ~ " + MAX_LEVEL + "로 입력하세요.");
      if (!Number.isInteger(targetLevel) || targetLevel < 1 || targetLevel > (MAX_LEVEL + 1)) throw new Error("목표 레벨은 1 ~ " + (MAX_LEVEL + 1) + "로 입력하세요.");

      var curPct = Number(String(els.a_curPct.value || "").trim());
      var targetPct = Number(String(els.a_targetPct.value || "").trim());
      if (!isFinite(curPct)) throw new Error("현재 경험치(%)를 입력하세요.");
      if (!isFinite(targetPct)) targetPct = 0;

      var startMins = parseHM(els.a_startH.value, els.a_startM.value);
      var endMins = parseHM(els.a_endH.value, els.a_endM.value);
      var delta = endMins - startMins;

      if (delta <= 0) throw new Error("종료시간은 시작시간보다 늦어야 합니다. (다음날 계산 미지원)");
      if (delta > MAX_MINUTES) throw new Error("시간 차이는 최대 24시간(1440분)까지만 가능합니다.");

      var needXp = calcNeedXp(curLevel, curPct, targetLevel, targetPct);
      if (needXp <= 0) throw new Error("목표가 현재보다 높아야 합니다. (레벨/목표% 확인)");

      var perHourRaw = (needXp / delta) * 60.0;
      var mult = getMultiplier(els.a_mulCheck, els.a_mulPct);
      var shown = (mult !== null) ? (perHourRaw / mult) : perHourRaw;

      var shownInt = Math.round(shown);
      setAResult("시간당 얻는 경험치: " + shownInt.toLocaleString("ko-KR"));
    }catch(e){
      alert(String(e.message || e));
    }
  }

  function resetA(){
    els.a_curLevel.value = "5";
    els.a_curPct.value = "0";
    els.a_startH.value = "16";
    els.a_startM.value = "0";
    els.a_targetLevel.value = "10";
    els.a_targetPct.value = "0";
    els.a_endH.value = "16";
    els.a_endM.value = "30";
    els.a_mulCheck.checked = false;
    els.a_mulPct.value = "100";
    els.a_mulPct.disabled = true;
    setAResult("시간당 얻는 경험치: -");
  }

  function presetAdd(){
    var name = String(els.pName.value || "").trim();
    var valS = String(els.pValue.value || "").trim().replace(/,/g,"");
    if (!name){ alert("이름을 입력하세요."); return; }
    var val = Number(valS);
    if (!isFinite(val) || Math.floor(val) !== val){ alert("값(XP/시간)은 정수로 입력하세요."); return; }
    if (val <= 0){ alert("값(XP/시간)은 0보다 커야 합니다."); return; }

    presets.push({ name:name, value:Math.floor(val) });
    selectedIndex = null;
    els.pName.value = "";
    els.pValue.value = "";
    refreshPresetSelect();
    refreshPresetList();
  }

  function presetDelete(){
    if (selectedIndex === null){
      alert("삭제할 항목을 리스트에서 선택하세요.");
      return;
    }
    var it = presets[selectedIndex];
    if (!confirm("삭제할까요?\n\n" + it.name + " (" + Number(it.value).toLocaleString("ko-KR") + ")")) return;

    presets.splice(selectedIndex, 1);
    selectedIndex = null;
    els.pName.value = "";
    els.pValue.value = "";
    refreshPresetSelect();
    refreshPresetList();
  }

  els.toggleDrawerBtn.addEventListener("click", toggleRight);
  els.closeDrawerBtn.addEventListener("click", closeRight);
  els.toggleMeterBtn.addEventListener("click", toggleLeft);
  els.closeMeterBtn.addEventListener("click", closeLeft);

  els.mulCheck.addEventListener("change", function(){
    els.mulPct.disabled = !els.mulCheck.checked;
  });

  els.a_mulCheck.addEventListener("change", function(){
    els.a_mulPct.disabled = !els.a_mulCheck.checked;
  });

  els.calcBtn.addEventListener("click", calcMain);
  els.resetBtn.addEventListener("click", resetMain);
  els.a_calcBtn.addEventListener("click", calcA);
  els.a_resetBtn.addEventListener("click", resetA);
  els.addBtn.addEventListener("click", presetAdd);
  els.deleteBtn.addEventListener("click", presetDelete);

  window.addEventListener("keydown", function(e){
    if (e.key === "Escape"){
      if (els.drawer.classList.contains("open")) closeRight();
      if (els.meterDrawer.classList.contains("open")) closeLeft();
    }
  });

  refreshPresetSelect();
  refreshPresetList();
  setResult("총 필요 경험치: -\n걸리는 시간: -");
  setAResult("시간당 얻는 경험치: -");
})();
</script>
</body>
</html>

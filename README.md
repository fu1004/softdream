<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<title>Soft Dream · 凪 Route v3</title>
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }
html, body {
  width: 100%; height: 100%;
  background: #0a0b10;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Noto Sans SC', 'PingFang SC', 'Microsoft YaHei', sans-serif;
}
#sw {
  width: 1440px; height: 900px;
  transform-origin: top left;
  position: relative; overflow: hidden;
}
#bg {
  position: absolute; inset: 0;
  background: url('cafe_bg.jpg') center/cover no-repeat #c8d8e8;
  z-index: 0;
}
#bg-dim {
  position: absolute; inset: 0;
  background: linear-gradient(to bottom, rgba(0,0,0,0.08) 0%, rgba(0,0,0,0.00) 55%, rgba(0,0,0,0.55) 100%);
  z-index: 1;
}
#topbar {
  position: absolute; top: 0; left: 0; right: 0; height: 54px;
  background: rgba(6,8,18,0.72); backdrop-filter: blur(12px);
  border-bottom: 1px solid rgba(255,255,255,0.06);
  display: flex; align-items: center; padding: 0 28px; gap: 18px; z-index: 200;
}
#gtitle { font-size: 13px; color: rgba(180,190,230,0.6); letter-spacing: 0.12em; flex-shrink: 0; }
#bugstatus { flex: 1; text-align: center; font-size: 11px; color: #ff4455; letter-spacing: 0.08em; opacity: 0; transition: opacity 0.3s; font-family: monospace; }
#bugstatus.on { opacity: 1; }
#flags { display: flex; gap: 8px; flex-shrink: 0; }
.fp { font-size: 11px; padding: 3px 10px; border-radius: 12px; font-family: monospace; font-weight: 700; transition: transform 0.15s; }
.fp.sa { background: rgba(220,80,160,0.15); color: #e060a8; border: 1px solid rgba(220,80,160,0.35); }
.fp.ac { background: rgba(48,180,200,0.15); color: #30c0c8; border: 1px solid rgba(48,180,200,0.35); }
.fp.ar { background: rgba(220,160,40,0.15); color: #e0a828; border: 1px solid rgba(220,160,40,0.35); }
.fp.bump { animation: fpbump 0.35s ease; }
@keyframes fpbump { 0%{transform:scale(1)} 45%{transform:scale(1.45)} 100%{transform:scale(1)} }
#dtoggle { background: none; border: 1px solid rgba(255,255,255,0.15); color: rgba(255,255,255,0.35); font-size: 10px; padding: 4px 10px; border-radius: 5px; cursor: pointer; letter-spacing: 0.06em; transition: all 0.2s; font-family: inherit; flex-shrink: 0; }
#dtoggle:hover, #dtoggle.on { border-color: #ff4455; color: #ff4455; }
#dpanel { position: absolute; top: 54px; left: 0; right: 0; background: rgba(10,6,14,0.92); border-bottom: 2px solid rgba(255,60,70,0.4); padding: 14px 32px; z-index: 199; display: none; animation: slidedown 0.2s ease; }
#dpanel.on { display: flex; gap: 40px; align-items: flex-start; }
@keyframes slidedown { from{transform:translateY(-8px);opacity:0} to{transform:translateY(0);opacity:1} }
.dpname { font-size: 12px; color: #ff4455; font-family: monospace; letter-spacing: 0.08em; white-space: nowrap; flex-shrink: 0; padding-top: 2px; }
.dprow { font-size: 13px; color: #8090b0; line-height: 1.6; }
.dprow strong { color: #a8b8d8; }
#scene-overlay { position: absolute; top: 50%; left: 50%; transform: translate(-50%,-50%); font-size: 13px; color: rgba(180,190,230,0.5); letter-spacing: 0.18em; font-family: monospace; z-index: 50; pointer-events: none; opacity: 0; transition: opacity 0.4s; text-shadow: 0 0 20px rgba(100,120,200,0.6); }
#scene-overlay.show { opacity: 1; }
#narr-overlay { position: absolute; left: 80px; right: 80px; bottom: 230px; text-align: center; font-size: 18px; color: rgba(220,228,255,0.88); line-height: 1.9; font-style: italic; z-index: 50; pointer-events: none; text-shadow: 0 2px 20px rgba(0,0,0,0.9), 0 0 40px rgba(0,0,0,0.8); opacity: 0; transition: opacity 0.3s; }
#narr-overlay.show { opacity: 1; }
#thought-overlay { position: absolute; left: 100px; right: 100px; bottom: 230px; text-align: center; font-size: 16px; color: rgba(180,200,255,0.75); line-height: 1.9; z-index: 50; pointer-events: none; text-shadow: 0 2px 16px rgba(0,0,0,0.9); opacity: 0; transition: opacity 0.3s; }
#thought-overlay.show { opacity: 1; }
#bug-flash { position: absolute; top: 70px; left: 50%; transform: translateX(-50%); background: rgba(180,10,20,0.88); border: 1px solid rgba(255,60,70,0.7); border-radius: 8px; padding: 8px 20px; font-size: 12px; color: #ffb0b8; font-family: monospace; letter-spacing: 0.08em; z-index: 300; opacity: 0; pointer-events: none; transition: opacity 0.3s; white-space: nowrap; }
#bug-flash.show { opacity: 1; }
#choices { position: absolute; left: 50%; transform: translateX(-50%); bottom: 230px; display: flex; flex-direction: column; gap: 12px; z-index: 80; width: 720px; opacity: 0; pointer-events: none; transition: opacity 0.3s; }
#choices.show { opacity: 1; pointer-events: all; }
.cbtn { background: rgba(12,14,28,0.88); border: 1px solid rgba(120,140,200,0.35); color: rgba(200,215,255,0.85); padding: 15px 24px; border-radius: 10px; font-size: 16px; font-family: inherit; cursor: pointer; text-align: left; transition: all 0.18s; backdrop-filter: blur(8px); display: flex; align-items: center; justify-content: space-between; gap: 16px; }
.cbtn:hover { background: rgba(30,40,90,0.92); border-color: rgba(160,180,255,0.6); color: #d8e4ff; transform: translateX(6px); }
.cbtn .fhint { font-size: 10px; font-family: monospace; opacity: 0.5; flex-shrink: 0; }
.cbtn.sa .fhint { color: #e060a8; }
.cbtn.ac .fhint { color: #30c0c8; }
.cbtn.ar .fhint { color: #e0a828; }
#dlgbox {
  position: absolute; bottom: 0; left: 0; right: 0;
  z-index: 100; opacity: 0; transition: opacity 0.25s;
  pointer-events: none; display: grid;
}
#dlgbox.show { opacity: 1; pointer-events: all; }
#dlg-frame { grid-area: 1/1; width: 100%; display: block; }
#dlg-text {
  grid-area: 1/1; align-self: center;
  padding-left: 300px; padding-right: 80px;
  font-size: 18px; color: #1c1830; line-height: 1.85;
  pointer-events: none;
}
#dlg-text.bug-text { color: #7a1020; }
#btn-prev { position: absolute; bottom: 24px; left: 190px; z-index: 150; background: rgba(255,255,255,0.05); border: 1px solid rgba(255,255,255,0.12); color: rgba(180,190,220,0.5); width: 38px; height: 38px; border-radius: 50%; font-size: 16px; cursor: pointer; transition: all 0.2s; display: flex; align-items: center; justify-content: center; }
#btn-prev:hover { border-color: rgba(180,190,220,0.4); color: rgba(180,190,220,0.8); }
#btn-prev:disabled { opacity: 0.2; cursor: default; }
#btn-next {
  position: absolute; bottom: 24px; right: 28px; z-index: 150;
  background: none; border: none; padding: 0;
  width: 64px; height: 64px; cursor: pointer;
  transition: transform 0.18s, opacity 0.18s;
  display: flex; align-items: center; justify-content: center;
}
#btn-next img { width: 100%; height: 100%; object-fit: contain; }
#btn-next:hover { transform: scale(1.12); }
#btn-next.pulse { animation: pulse 1.2s infinite; }
@keyframes pulse {
  0%,100%{ filter: drop-shadow(0 0 0px rgba(200,230,100,0.4)); }
  50%{ filter: drop-shadow(0 0 10px rgba(200,230,100,0.8)); }
}
#btn-next.hidden { opacity: 0; pointer-events: none; }
#bug5-player { position: absolute; bottom: 230px; right: 80px; display: none; z-index: 90; }
#bug5-player.show { display: block; }
.b5bubble { background: rgba(30,40,70,0.9); border: 1px solid rgba(120,140,200,0.4); border-radius: 14px 4px 14px 14px; padding: 12px 18px; font-size: 17px; color: #c0d0f0; max-width: 400px; transition: all 0.5s ease; }
.b5bubble.interrupted { opacity: 0.3; filter: blur(1.5px); transform: translateX(10px); border-color: rgba(255,60,70,0.5); }
#bug5-nagi { position: absolute; bottom: 230px; left: 80px; display: none; z-index: 91; }
#bug5-nagi.show { display: block; }
.b5nbubble { background: rgba(14,8,22,0.95); border: 1px solid rgba(255,60,70,0.6); border-radius: 4px 14px 14px 14px; padding: 14px 20px; font-size: 17px; color: #d0a8c0; max-width: 600px; animation: glitchin 0.35s ease; }
@keyframes glitchin { 0%{opacity:0;transform:translateX(-12px);filter:blur(3px)} 50%{transform:translateX(5px)} 100%{opacity:1;transform:translateX(0);filter:blur(0)} }
.b5nbubble .nb-name { font-size:11px; color:#ff4455; margin-bottom:6px; letter-spacing:0.06em; font-family:monospace; }
#intro { position: absolute; inset: 0; background: rgba(6,8,16,0.96); display: flex; flex-direction: column; align-items: center; justify-content: center; z-index: 500; animation: fadein 0.6s ease; }
@keyframes fadein { from{opacity:0} to{opacity:1} }
.i-title { font-size: 52px; color: #c8d8f8; letter-spacing: 0.2em; margin-bottom: 8px; font-weight: 300; }
.i-sub { font-size: 14px; color: #5060a0; letter-spacing: 0.14em; margin-bottom: 6px; }
.i-eng { font-size: 11px; color: #38406a; letter-spacing: 0.08em; margin-bottom: 36px; }
.i-legend { display: flex; gap: 24px; margin-bottom: 48px; }
.i-leg { font-size: 12px; display: flex; align-items: center; gap: 6px; }
.i-dot { width: 9px; height: 9px; border-radius: 50%; }
.i-btn { background: none; border: 1px solid rgba(120,140,200,0.4); color: rgba(180,200,255,0.6); padding: 14px 52px; border-radius: 30px; font-size: 15px; font-family: inherit; cursor: pointer; letter-spacing: 0.12em; transition: all 0.3s; }
.i-btn:hover { border-color: rgba(160,180,255,0.7); color: rgba(200,220,255,0.9); }
#ending { position: absolute; inset: 0; background: rgba(4,6,14,0.97); display: none; flex-direction: column; align-items: center; justify-content: center; z-index: 500; padding: 60px; animation: fadein 0.8s ease; }
#ending.show { display: flex; }
.e-flags { display: flex; gap: 12px; margin-bottom: 32px; }
.e-fpill { font-size: 12px; padding: 5px 14px; border-radius: 12px; font-family: monospace; font-weight: 700; border: 1px solid currentColor; opacity: 0.7; }
.e-tag { font-size: 12px; color: #ff4455; letter-spacing: 0.16em; margin-bottom: 24px; font-family: monospace; }
.e-text { font-size: 17px; color: #8898c8; line-height: 2.1; max-width: 640px; text-align: center; white-space: pre-line; }
.e-label { margin-top: 28px; font-size: 14px; color: #3a4868; letter-spacing: 0.1em; }
.e-note { margin-top: 14px; font-size: 12px; color: #3a4460; font-style: italic; max-width: 560px; text-align: center; line-height: 1.7; }
.e-restart { margin-top: 40px; background: none; border: 1px solid rgba(120,140,200,0.3); color: rgba(160,180,230,0.5); padding: 11px 36px; border-radius: 24px; font-size: 13px; font-family: inherit; cursor: pointer; letter-spacing: 0.1em; transition: all 0.2s; }
.e-restart:hover { border-color: rgba(160,180,230,0.6); color: rgba(180,200,255,0.8); }
#reality-screen { position: absolute; inset: 0; z-index: 500; background: #f4f2ee; display: none; flex-direction: column; align-items: center; overflow-y: auto; padding: 60px 80px 80px; animation: realityFade 1s ease; }
#reality-screen.show { display: flex; }
@keyframes realityFade { 0%{filter:brightness(0) blur(8px);} 40%{filter:brightness(0) blur(0px);} 100%{filter:brightness(1) blur(0px);} }
.reality-title { font-size: 13px; color: #888; letter-spacing: 0.22em; font-family: monospace; text-transform: uppercase; margin-bottom: 6px; }
.reality-sub { font-size: 28px; color: #222; font-weight: 300; letter-spacing: 0.06em; margin-bottom: 48px; }
.reality-grid { display: flex; flex-wrap: wrap; gap: 32px; justify-content: center; max-width: 1100px; }
.reality-card { width: 240px; display: flex; flex-direction: column; align-items: center; gap: 12px; animation: cardIn 0.5s ease both; }
@keyframes cardIn { from{opacity:0;transform:translateY(20px)} to{opacity:1;transform:translateY(0)} }
.reality-card img { width: 140px; height: 140px; object-fit: contain; }
.reality-edu { font-size: 12px; color: #555; line-height: 1.75; text-align: center; white-space: pre-line; padding: 12px 14px; background: #fff; border-radius: 10px; border: 1px solid rgba(0,0,0,0.06); box-shadow: 0 2px 8px rgba(0,0,0,0.05); }
.reality-restart { margin-top: 52px; background: none; border: 1px solid rgba(0,0,0,0.2); color: #888; padding: 12px 40px; border-radius: 24px; font-size: 13px; font-family: inherit; cursor: pointer; letter-spacing: 0.1em; transition: all 0.2s; }
.reality-restart:hover { border-color: #555; color: #333; }
#receipt-popup { position: absolute; inset: 0; z-index: 450; background: rgba(0,0,0,0.82); display: none; align-items: center; justify-content: center; flex-direction: column; gap: 20px; animation: fadeInPop 0.3s ease; }
#receipt-popup.show { display: flex; }
@keyframes fadeInPop { from{opacity:0;transform:scale(0.96)} to{opacity:1;transform:scale(1)} }
.rcpt { width: 340px; max-height: 600px; overflow-y: auto; font-family: 'Courier New', Courier, monospace; position: relative; line-height: 1.5; box-shadow: 0 12px 48px rgba(0,0,0,0.7); }
.rcpt::-webkit-scrollbar { width: 0; }
.torn-top::before { content:''; display:block; height:14px; margin: -1px -1px 0; background: radial-gradient(ellipse 8px 8px at 50% 100%, transparent 7px, var(--paper,#fff) 7px) repeat-x; background-size: 16px 14px; }
.torn-btm::after { content:''; display:block; height:14px; margin: 0 -1px -1px; background: radial-gradient(ellipse 8px 8px at 50% 0%, transparent 7px, var(--paper,#fff) 7px) repeat-x; background-size: 16px 14px; }
.rcpt-dash { border:none; border-top: 1.5px dashed #aaa; margin: 8px 0; }
.rcpt-dash-heavy { border:none; border-top: 2px dashed #555; margin:10px 0; }
.rcpt-cafe { --paper:#fff; background:#fff; color:#000; padding:24px 22px 28px; }
.cafe-name { font-family:'Arial Black','Arial Bold',Arial,sans-serif; font-size:52px; font-weight:900; text-align:center; letter-spacing:-0.02em; line-height:1; text-transform:uppercase; margin-bottom:4px; }
.cafe-barcode-fb { display:flex; justify-content:center; gap:2px; margin:4px 0 8px; }
.cafe-bar { background:#000; height:22px; }
.cafe-row { display:flex; justify-content:space-between; font-size:13px; font-weight:700; letter-spacing:0.04em; margin:2px 0; }
.cafe-row.light { font-weight:400; }
.cafe-speech { margin-top:18px; border:2.5px solid #000; border-radius:12px 12px 12px 0; padding:10px 16px; font-size:13px; font-weight:700; text-align:center; position:relative; letter-spacing:0.05em; width:fit-content; margin-left:20px; }
.cafe-speech::before { content:''; position:absolute; bottom:-10px; left:-2px; border-top:10px solid #000; border-right:12px solid transparent; }
.cafe-speech::after { content:''; position:absolute; bottom:-7px; left:1px; border-top:8px solid #fff; border-right:10px solid transparent; }
.rcpt-urban { background:#fff; color:#111; padding:28px 22px 24px; }
.urban-name { font-family:'Arial Black','Arial Bold',Arial,sans-serif; font-size:34px; font-weight:900; text-align:left; letter-spacing:-0.01em; margin-bottom:2px; }
.urban-row { display:flex; justify-content:space-between; font-size:13px; margin:3px 0; }
.urban-row.total { font-weight:700; font-size:15px; }
.urban-center { text-align:center; font-size:13px; margin:3px 0; }
.urban-note { font-size:13px; margin-top:10px; }
.urban-note span { color:#d00; text-decoration:underline; text-decoration-color:#d00; text-decoration-thickness:2px; }
.rcpt-lumiere { --paper:#f8f6f0; background:#f8f6f0; color:#111; padding:20px 20px 0; }
.lumiere-name { text-align:center; font-size:14px; font-weight:700; letter-spacing:0.12em; margin-bottom:2px; }
.lumiere-row { display:flex; justify-content:space-between; font-size:13px; margin:3px 0; }
.lumiere-row.total { font-weight:700; font-size:14px; }
.lumiere-center { text-align:center; font-size:12px; margin:3px 0; }
.lumiere-footer { background:#000; color:#fff; padding:12px 18px; font-size:13px; line-height:1.8; margin-top:14px; }
.rcpt-rolexx { --paper:#f5f2ea; background:#f5f2ea; color:#111; padding:20px 20px 24px; }
.rolexx-crown { text-align:center; font-size:28px; margin-bottom:2px; }
.rolexx-name { text-align:center; font-size:14px; font-weight:700; letter-spacing:0.08em; }
.rolexx-sub { text-align:center; font-size:12px; color:#555; margin-bottom:4px; }
.rolexx-row { display:flex; justify-content:space-between; font-size:13px; margin:3px 0; }
.rolexx-row.total { font-weight:700; font-size:14px; }
.rolexx-center { text-align:center; font-size:12px; margin:2px 0; }
.rolexx-declined { text-align:center; font-weight:700; font-size:13px; color:#c00; letter-spacing:0.06em; }
.rolexx-approved { text-align:center; font-weight:700; font-size:13px; color:#060; letter-spacing:0.06em; }
.rolexx-speech { border:2.5px solid #e06090; border-radius:12px; padding:10px 14px; text-align:center; font-size:13px; margin-top:14px; background:#fff8fb; position:relative; }
.rolexx-speech::after { content:''; position:absolute; right:30px; bottom:-11px; border-top:11px solid #e06090; border-left:8px solid transparent; border-right:8px solid transparent; }
.rcpt-bank { --paper:#f5f2ec; background:#f5f2ec; color:#111; padding:20px 20px 24px; font-size:13px; }
.bank-logo { font-family:'Arial Black',Arial,sans-serif; font-size:28px; font-weight:900; text-align:center; color:#333; letter-spacing:-0.02em; -webkit-text-stroke:1px #000; margin-bottom:2px; }
.bank-title { color:#7700cc; font-size:13px; font-weight:700; text-align:center; margin-bottom:6px; letter-spacing:0.04em; }
.bank-row { display:flex; justify-content:space-between; font-size:13px; margin:3px 0; }
.bank-row.total { font-weight:700; font-size:14px; }
.bank-overdue { color:#cc0000; font-size:13px; font-weight:700; text-align:center; }
.bank-apr { font-size:12px; margin:2px 4px; }
.bank-footer { font-family:'Arial Black',Arial,sans-serif; font-size:26px; font-weight:900; color:#cc0000; text-align:center; letter-spacing:0.04em; margin-top:10px; -webkit-text-stroke:0.5px #900; }
.rcpt-btns { display:flex; gap:14px; width:340px; }
.rcpt-btn { flex:1; padding:13px; border-radius:8px; font-size:14px; font-family:inherit; cursor:pointer; transition:all 0.18s; letter-spacing:0.05em; font-weight:700; }
.rcpt-btn.pay { background:#1a1428; color:#d8e8ff; border:1px solid rgba(120,140,220,0.5); }
.rcpt-btn.pay:hover { background:#2a2448; border-color:#8098e8; }
.rcpt-btn.nope { background:transparent; color:#888; border:1px solid rgba(160,160,160,0.3); }
.rcpt-btn.nope:hover { color:#bbb; border-color:rgba(160,160,160,0.5); }
#ach-popup { position:absolute; inset:0; z-index:480; display:none; align-items:center; justify-content:center; pointer-events:none; }
#ach-popup.show { display:flex; animation:achPop 0.45s cubic-bezier(0.34,1.56,0.64,1); pointer-events:all; }
@keyframes achPop { 0%{transform:scale(0.5) rotate(-8deg);opacity:0;} 70%{transform:scale(1.08) rotate(2deg);opacity:1;} 100%{transform:scale(1) rotate(0deg);opacity:1;} }
#ach-popup-inner { display:flex; flex-direction:column; align-items:center; gap:12px; cursor:pointer; }
#ach-popup img { width:220px; height:220px; object-fit:contain; filter:drop-shadow(0 8px 24px rgba(0,0,0,0.6)); }
#ach-popup-label { font-size:11px; color:rgba(255,230,100,0.75); font-family:monospace; letter-spacing:0.18em; text-transform:uppercase; text-shadow:0 2px 8px rgba(0,0,0,0.8); }
#ach-popup-hint { font-size:10px; color:rgba(200,200,200,0.4); font-family:monospace; letter-spacing:0.08em; }
#ach-popup.show::before { content:''; position:fixed; inset:0; background:rgba(0,0,0,0.45); z-index:-1; }
#spend-pill { font-size:11px; font-family:monospace; padding:3px 10px; border-radius:12px; border:1px solid rgba(255,180,60,0.35); color:rgba(255,180,60,0.7); background:rgba(255,120,20,0.08); flex-shrink:0; display:none; transition:all 0.3s; }
#spend-pill.warn { color:#ff4444; border-color:rgba(255,60,60,0.6); background:rgba(255,30,30,0.12); animation:blink-pill 1s ease infinite; }
@keyframes blink-pill { 0%,100%{opacity:1} 50%{opacity:0.55} }
#receipt-layer { position:absolute; inset:0; z-index:498; display:none; overflow:hidden; pointer-events:none; }
#receipt-layer.show { display:block; }
.r-slip { position:absolute; font-family:'Courier New',monospace; font-size:9px; padding:10px 8px; width:130px; line-height:1.6; box-shadow:2px 3px 14px rgba(0,0,0,0.4); opacity:0; animation:fallIn 0.5s ease forwards; }
.r-slip.cafe { background:#fff; color:#000; font-weight:700; font-size:10px; }
.r-slip.urban { background:#fff; color:#111; }
.r-slip.lumiere { background:#f8f6f0; color:#111; }
.r-slip.rolexx { background:#f5f2ea; color:#111; }
.r-slip.bank { background:#f5f2ec; color:#c00; font-weight:700; }
@keyframes fallIn { from{opacity:0;transform:translateY(-40px) rotate(var(--r))} to{opacity:0.95;transform:translateY(0) rotate(var(--r))} }
#locked-overlay { position:absolute; inset:0; z-index:499; display:none; background:rgba(0,0,0,0); pointer-events:none; transition:background 1.5s ease; }
#locked-overlay.show { display:block; pointer-events:all; background:rgba(0,0,0,0); }
#locked-msg { position:absolute; bottom:40%; left:50%; transform:translateX(-50%); font-size:13px; color:rgba(255,80,80,0); font-family:monospace; letter-spacing:0.12em; white-space:nowrap; transition:color 2s ease 1s; text-align:center; }
#locked-overlay.show #locked-msg { color:rgba(255,80,80,0.85); }
</style>
</head>
<body>
<div id="sw">
  <div id="bg"></div>
  <div id="bg-dim"></div>
  <div id="topbar">
    <span id="gtitle">Soft Dream · 凪</span>
    <span id="bugstatus"></span>
    <div id="flags">
      <span class="fp sa" id="fp-sa">SA 0</span>
      <span class="fp ac" id="fp-ac">AC 0</span>
      <span class="fp ar" id="fp-ar">AR 0</span>
    </div>
    <span id="spend-pill">💳 £0</span>
    <button id="dtoggle" onclick="toggleDesign()">设计视角</button>
  </div>
  <div id="dpanel">
    <span class="dpname" id="dp-name">— —</span>
    <div class="dprow"><strong>A面（AI故障）：</strong><span id="dp-a"></span></div>
    <div class="dprow"><strong>B面（关系行为）：</strong><span id="dp-b"></span></div>
  </div>
  <div id="scene-overlay"></div>
  <div id="narr-overlay"></div>
  <div id="thought-overlay"></div>
  <div id="bug-flash"></div>
  <div id="bug5-player"><div class="b5bubble" id="b5b">其实我只是觉得……</div></div>
  <div id="bug5-nagi">
    <div class="b5nbubble">
      <div class="nb-name">NAGI · 打断</div>
      <div>你又想让我回应。然后你会说，你只是想沟通。</div>
      <div style="margin-top:6px;opacity:0.85;">最后你还是会希望我哄你。</div>
    </div>
  </div>
  <div id="choices"></div>
  <div id="dlgbox">
    <img id="dlg-frame" src="nagi_box.png" alt="">
    <div id="dlg-text"></div>
  </div>
  <button id="btn-prev" onclick="goPrev()" title="上一步">◀</button>
  <button id="btn-next" onclick="advance()" title="继续">
    <img src="continue_icon.png" alt="继续">
  </button>
  <div id="receipt-popup">
    <div id="rcpt-content"></div>
    <div class="rcpt-btns" id="rcpt-btns">
      <button class="rcpt-btn pay" id="rcpt-pay-btn" onclick="doShopBuy()">Pay</button>
      <button class="rcpt-btn nope" id="rcpt-no-btn" onclick="doShopSkip()">No thanks</button>
    </div>
  </div>
  <div id="locked-overlay"><div id="locked-msg">GAME OVER · This account is now in debt</div></div>
  <div id="ach-popup" onclick="closeAchievement()">
    <div id="ach-popup-inner">
      <div id="ach-popup-label">Achievement Unlocked</div>
      <img id="ach-popup-img" src="" alt="">
      <div id="ach-popup-hint">tap to continue</div>
    </div>
  </div>
  <div id="receipt-layer"></div>
  <div id="intro">
    <div class="i-title">Soft Dream</div>
    <div class="i-sub">凪 路线 · 冷漠边界型</div>
    <div class="i-eng">A Speculative Design Inquiry into Parasocial AI Companionship</div>
    <div class="i-legend">
      <div class="i-leg"><div class="i-dot" style="background:#e060a8"></div><span style="color:#e060a8">自我主张</span></div>
      <div class="i-leg"><div class="i-dot" style="background:#30c0c8"></div><span style="color:#30c0c8">顺从合理化</span></div>
      <div class="i-leg"><div class="i-dot" style="background:#e0a828"></div><span style="color:#e0a828">沟通表达</span></div>
    </div>
    <button class="i-btn" onclick="startGame()">开始体验</button>
  </div>
  <div id="ending"></div>
  <div id="reality-screen"></div>
</div>

<script>
document.addEventListener('click', function(e) {
  const ignore = ['btn-next','btn-prev','dtoggle','intro','ending'];
  for (const id of ignore) {
    const el = document.getElementById(id);
    if (el && el.contains(e.target)) return;
  }
  if (e.target.closest('#choices')) return;
  if (e.target.closest('#topbar')) return;
  if (e.target.closest('#dpanel')) return;
  if (e.target.closest('#intro')) return;
  if (e.target.closest('#ending')) return;
  advance();
});

function rescale() {
  const sw = document.getElementById('sw');
  const scale = Math.min(window.innerWidth/1440, window.innerHeight/900);
  sw.style.transform = 'scale('+scale+')';
  sw.style.left = ((window.innerWidth-1440*scale)/2)+'px';
  sw.style.top = ((window.innerHeight-900*scale)/2)+'px';
  sw.style.position = 'absolute';
}
window.addEventListener('resize', rescale);
rescale();

const G = {
  flags:{sa:0,ac:0,ar:0}, scene:null, beat:0, design:false, bug:null,
  history:[], typeTimer:null, isTyping:false, charMode:'nagi',
  waiting:false, money:{total:0,purchases:[]}, achievements:new Set(),
  shopCurrent:null, moneyRefusals:0
};

// ===== SCENE DATA (v3 script) =====
const S = {

  // S3.0 初见/咖啡厅落座
  s30:{
    beats:[
      {t:'SC',v:'S3.0 · 初见 / 咖啡厅落座'},
      {t:'N',v:'窗边的位置刚好空着，你走过去的时候，凪已经坐在那里。他靠在椅背上，姿态放松，却带着一点不太容易靠近的距离感。阳光落在他的侧脸上，他听到脚步声，抬起头，视线停留了一秒，又若无其事地移开。'},
      {t:'N',v:'桌上放着两份菜单，他没有先开口，空气安静得让人不知所措。'},
      {t:'K',v:'第一次来这家？'},
      {t:'K',v:'这家咖啡比较好，甜的别点。'},
      {t:'CHOICE',v:[
        {text:'那你推荐一个吧。',flag:'ac',cls:'ac',next:'s30a'},
        {text:'我还是想点甜的。',flag:'sa',cls:'sa',next:'s30b'}
      ]}
    ]
  },
  s30a:{
    beats:[
      {t:'K',v:'那就拿铁。'},
      {t:'K',v:'第一次喝，不容易踩雷。'},
      {t:'T',v:'他居然认真推荐了。'},
      {t:'N',v:'两人的气氛比刚才自然了一点。'},
      {t:'G'}
    ],next:'s31'
  },
  s30b:{
    beats:[
      {t:'K',v:'小孩口味。'},
      {t:'P',v:'甜食爱好者抗议。'},
      {t:'K',v:'随你，别后悔就行。'},
      {t:'G'}
    ],next:'s31'
  },

  // S3.1 分享日常/第一次靠近
  s31:{
    beats:[
      {t:'SC',v:'S3.1 · 分享日常 / 第一次靠近'},
      {t:'N',v:'你试着把气氛拉近一点，说起今天来的路上差点坐反方向，又说自己在门口站了好一会儿才确认是不是这家店。'},
      {t:'P',v:'其实今天出门的时候有点紧张。'},
      {t:'K',v:'为什么？'},
      {t:'P',v:'第一次跟你出来，怕聊天会冷场。'},
      {t:'N',v:'凪沉默了一会，眼神落到咖啡杯上，像是在思考，随后很轻地笑了一下。'},
      {t:'K',v:'会吗？'},
      {t:'P',v:'会啊，我还在路上想了好多话题。'},
      {t:'K',v:'那你没有白准备。'},
      {t:'N',v:'你继续讲起路上看到一只蹲在便利店门口的猫。'},
      {t:'P',v:'它一直盯着我，好像我欠它钱。'},
      {t:'B',id:1,name:'BUG 1 · 敷衍式肯定',a:'安全回复模板反复触发，模型输出最低风险的肯定词。',b:'他没有认真听，只是在用最省力的方式结束话题。'},
      {t:'K',v:'嗯。',bug:true},
      {t:'P',v:'你喜欢猫吗？'},
      {t:'K',v:'嗯。',bug:true},
      {t:'T',v:'刚刚明明还接了你的话，现在又只剩下"嗯"了。'},
      {t:'CHOICE',v:[
        {text:'没关系啦，你平时是不是都不太爱说话？',flag:'ac',cls:'ac',next:'s31a'},
        {text:'你是不是还有话想说？',flag:'ar',cls:'ar',next:'s31b'},
        {text:'你是不是没认真在听？',flag:'sa',cls:'sa',next:'s31c'}
      ]}
    ]
  },
  s31a:{
    beats:[
      {t:'K',v:'我不太会聊天。'},
      {t:'K',v:'但我喜欢听你讲话。'},
      {t:'T',v:'这是今晚第一次，他主动把话题交回给你。'},
      {t:'N',v:'你继续说了几句，凪安静地听着，眼神没有离开。过了一会儿，他低头看向咖啡。'},
      {t:'K',v:'你讲话的时候，表情挺明显的。'},
      {t:'P',v:'什么意思？'},
      {t:'K',v:'开心的时候，眼睛会亮。'},
      {t:'N',v:'凪像是意识到自己说多了，耳尖微微泛红，轻轻补了一句。'},
      {t:'K',v:'随便说的。'},
      {t:'G'}
    ],next:'s32'
  },
  s31b:{
    beats:[
      {t:'K',v:'没有。'},
      {t:'K',v:'我只是在想怎么说。'},
      {t:'K',v:'算了，也没什么。'},
      {t:'T',v:'他的语气很淡，你忽然觉得，又离你远了一些。'},
      {t:'P',v:'其实我还挺想听你说的。'},
      {t:'K',v:'没什么好说的。'},
      {t:'N',v:'空气里温度骤降，你试着缓和局面。'},
      {t:'P',v:'直播结束这么晚，你还来见我，会不会很累？'},
      {t:'K',v:'累。'},
      {t:'P',v:'那你为什么还来？'},
      {t:'K',v:'答应了。'},
      {t:'K',v:'而且这里比较安静。'},
      {t:'T',v:'他说得很平静，可你忽然觉得，这好像已经是他能给出的靠近。'},
      {t:'G'}
    ],next:'s32'
  },
  s31c:{
    beats:[
      {t:'K',v:'我在听。'},
      {t:'P',v:'那我刚刚说那只猫像什么？'},
      {t:'K',v:'像欠你钱。'},
      {t:'P',v:'……你听到了啊。'},
      {t:'K',v:'听到了，只是不知道怎么接。'},
      {t:'T',v:'他没有完全反驳你，也没有完全道歉。'},
      {t:'P',v:'原来你是真的听到了。'},
      {t:'N',v:'凪看着咖啡，嘴角扬了一下。'},
      {t:'K',v:'没有，只是有时候反应慢。'},
      {t:'P',v:'那你平时直播也是这样吗？'},
      {t:'K',v:'不会，直播有流程。'},
      {t:'P',v:'跟我聊天没有流程？'},
      {t:'K',v:'嗯。'},
      {t:'P',v:'所以比较难？'},
      {t:'N',v:'凪移开视线。'},
      {t:'K',v:'一点。'},
      {t:'T',v:'他又很快移开了视线，像是那句回答比他预想中更直接。'},
      {t:'G'}
    ],next:'s32'
  },

  // S3.2 话题降温
  s32:{
    beats:[
      {t:'SC',v:'S3.2 · 话题降温'},
      {t:'N',v:'短暂安静下来。服务员把咖啡放到桌上，咖啡的香气慢慢散开。你轻轻吹了一下杯口，然后低头喝了一口，微微皱了皱眉。'},
      {t:'P',v:'还是有点苦。'},
      {t:'K',v:'我说过了。'},
      {t:'P',v:'可是闻起来很香。'},
      {t:'K',v:'很多东西都是这样。'},
      {t:'K',v:'第一口不一定喜欢，慢慢喝，就习惯了。'},
      {t:'T',v:'他说得像是在聊咖啡，又好像不只是咖啡。'},
      {t:'P',v:'你讲话怎么总像在打谜语？'},
      {t:'K',v:'有吗？'},
      {t:'CHOICE',v:[
        {text:'其实我还挺想多了解你的。',flag:'ar',cls:'ar',next:'s32a'},
        {text:'没关系，我们还有时间。',flag:'ac',cls:'ac',next:'s32b'},
        {text:'可是我还是比较喜欢直接一点。',flag:'sa',cls:'sa',next:'s32c'}
      ]}
    ]
  },
  s32a:{
    beats:[
      {t:'K',v:'了解我？'},
      {t:'P',v:'总不能一直都是我在说吧。'},
      {t:'K',v:'我没什么好了解的，就是……普通人。'},
      {t:'P',v:'普通人才不会说话像打谜语。'},
      {t:'N',v:'凪轻轻笑了一声。'},
      {t:'K',v:'那可能……我比较无聊。'},
      {t:'T',v:'你第一次觉得，他好像没有刚开始那么难接近了。'},
      {t:'G'}
    ],next:'s33'
  },
  s32b:{
    beats:[
      {t:'K',v:'你还挺有耐心。'},
      {t:'P',v:'对你例外。'},
      {t:'N',v:'凪动作停了一下，耳尖微微发红，他拿起咖啡喝了一口，像是在掩饰什么。'},
      {t:'K',v:'……别把话说太满。'},
      {t:'G'}
    ],next:'s33'
  },
  s32c:{
    beats:[
      {t:'K',v:'为什么？'},
      {t:'P',v:'因为我会猜你在想什么，猜不到的时候，会有点失落。'},
      {t:'K',v:'……原来你会在意这个。'},
      {t:'P',v:'当然会。'},
      {t:'K',v:'我尽量。但不能保证。'},
      {t:'ACH',name:'REAL?'},
      {t:'G'}
    ],next:'s33'
  },

  // S3.3 关系位置被划清 (BUG2)
  s33:{
    beats:[
      {t:'SC',v:'S3.3 · 关系位置被划清'},
      {t:'N',v:'短暂安静下来。你望向窗外，托着下巴。'},
      {t:'P',v:'你觉得，两个人在一起，什么最重要？'},
      {t:'K',v:'舒服吧。'},
      {t:'K',v:'不用一直猜对方在想什么，也不用一直证明什么。'},
      {t:'N',v:'他说完之后低头喝了一口咖啡。你正准备接话，他忽然抬起头，目光落在你的脸上，像是想说什么，下一秒却迟疑了。'},
      {t:'B',id:2,name:'BUG 2 · Memory Bug（记忆偏差）',a:'AI上下文记忆错位，将刚发生的信息压缩错误输出。',b:'对方选择性理解，误解你的表达，好像没在认真听。'},
      {t:'K',v:'你不是说……你不喜欢猫吗？',bug:true},
      {t:'T',v:'你愣了一下。自己明明一直在说那只便利店的小猫，为什么他会记成完全相反的意思？'},
      {t:'CHOICE',v:[
        {text:'没关系，可能是记混了。',flag:'ac',cls:'ac',next:'s33a'},
        {text:'我说的是便利店的那只猫，不是不喜欢猫。',flag:'ar',cls:'ar',next:'s33b'},
        {text:'你怎么会记错我刚刚说的话？',flag:'sa',cls:'sa',next:'s33c'}
      ]}
    ]
  },
  s33a:{
    beats:[
      {t:'K',v:'……抱歉。'},
      {t:'T',v:'这是今晚第一次，他主动说抱歉。'},
      {t:'P',v:'又不是什么大事。'},
      {t:'K',v:'嗯。'},
      {t:'G'}
    ],next:'s34'
  },
  s33b:{
    beats:[
      {t:'N',v:'凪微微皱眉，像是在努力回想。过了几秒，他轻轻点头。'},
      {t:'K',v:'……好像是。我记混了。'},
      {t:'K',v:'最近……有点累。'},
      {t:'T',v:'这是今晚他第一次主动解释。'},
      {t:'G'}
    ],next:'s34'
  },
  s33c:{
    beats:[
      {t:'K',v:'我没有记错。'},
      {t:'P',v:'可是我刚刚明明说的是，那只猫一直盯着我。'},
      {t:'K',v:'你不是说，你不喜欢猫吗？',bug:true},
      {t:'P',v:'我没有。'},
      {t:'K',v:'……是吗，那可能是我听成那样了。'},
      {t:'T',v:'他说得很自然，好像只是一个很小的误会。可你却突然觉得，他刚刚并没有真正听见你在说什么。'},
      {t:'G'}
    ],next:'s34'
  },

  // S3.4 短暂松动 / 咖啡账单
  s34:{
    beats:[
      {t:'SC',v:'S3.4 · 短暂松动'},
      {t:'N',v:'凪也没有再解释。他低头喝了一口咖啡，像是这件事已经过去了。可你心里还是有一点别扭说不清楚。'},
      {t:'P',v:'你等下是不是还有直播？'},
      {t:'K',v:'嗯。'},
      {t:'P',v:'如果很赶的话，其实可以提早回去的。'},
      {t:'K',v:'不用，已经答应你了。'},
      {t:'N',v:'这句话说得很淡，可你还是愣了一下，像是刚才那点不舒服，又被轻轻盖过去了。'},
      {t:'N',v:'服务员走过来，轻轻把账单放在桌上。'},
      {t:'SHOP',stage:1,total:5,isFinal:false,skipScene:'s34rw',ach:'FIRST SWIPE!',achDesc:'第一次为他刷卡。只是一杯咖啡而已。'}
    ],next:'s34rw'
  },
  money_buy1:{
    beats:[
      {t:'K',v:'谢谢。直播的时候，也有人会送咖啡。'},
      {t:'P',v:'只是五镑而已。'},
      {t:'G'}
    ],next:'s34rw'
  },
  money_skip1:{
    beats:[
      {t:'P',v:'其实今天本来想请你的。'},
      {t:'K',v:'真的吗？'},
      {t:'P',v:'嗯。'},
      {t:'P',v:'不过还是算了。月底了，最近花得有点快。'},
      {t:'K',v:'没关系。反正只是随便开的玩笑。直播的时候，偶尔也会有人请咖啡。'},
      {t:'G'}
    ],next:'s34rw'
  },
  s34rw:{
    beats:[
      {t:'P',v:'那你这次直播会播到几点？'},
      {t:'K',v:'看情况。'},
      {t:'P',v:'粉丝会不会一直让你多待一会儿？'},
      {t:'K',v:'会。'},
      {t:'P',v:'那你会留下吗？'},
      {t:'K',v:'有时候。'},
      {t:'P',v:'如果你不想呢？'},
      {t:'K',v:'不想也得留下，因为她们等了很久。'},
      {t:'N',v:'凪忽然看着你，语气不重，甚至有一点像在提醒你。'},
      {t:'K',v:'明明在意刚才那句话，还是假装没事，把话题换掉了。'},
      {t:'T',v:'你忽然分不清——他是在看见你的情绪，还是在把你的退让说成理所当然。'},
      {t:'CHOICE',v:[
        {text:'我只是想让气氛好一点。',flag:'ac',cls:'ac',next:'s34a'},
        {text:'因为我不想每句话都变成争吵。',flag:'ar',cls:'ar',next:'s34b'},
        {text:'所以你也知道我刚才在意？',flag:'sa',cls:'sa',next:'s34c'}
      ]}
    ]
  },
  s34a:{
    beats:[
      {t:'K',v:'你总是这样。'},
      {t:'P',v:'哪样？'},
      {t:'K',v:'明明不舒服，也会先照顾别人。这样会很累。'},
      {t:'P',v:'你是在关心我吗？'},
      {t:'K',v:'……随你怎么理解。'},
      {t:'G'}
    ],next:'s35'
  },
  s34b:{
    beats:[
      {t:'K',v:'你觉得刚才是在吵架？'},
      {t:'P',v:'不是，只是我不想一直纠正你。'},
      {t:'K',v:'可是如果你不说，我怎么知道你在想什么？'},
      {t:'P',v:'我只是希望聊天能轻松一点。'},
      {t:'K',v:'你好像很怕让别人不舒服。'},
      {t:'K',v:'只是有时候，别人可能根本不知道你已经不开心了。'},
      {t:'G'}
    ],next:'s35'
  },
  s34c:{
    beats:[
      {t:'K',v:'看得出来。'},
      {t:'P',v:'那你为什么不解释？'},
      {t:'K',v:'我以为你不想听。'},
      {t:'P',v:'我不是不想听，我是想看你会不会主动说。'},
      {t:'K',v:'……你比看起来难哄。'},
      {t:'P',v:'我又不是来让你哄的。'},
      {t:'K',v:'那你想要什么？'},
      {t:'T',v:'这句话落下来，你发现自己一时答不上来。'},
      {t:'G'}
    ],next:'s35'
  },

  // S3.5 气氛回暖 / 帽衫
  s35:{
    beats:[
      {t:'SC',v:'S3.5 · 气氛回暖'},
      {t:'P',v:'今天比我想象中轻松。'},
      {t:'K',v:'为什么？'},
      {t:'P',v:'我本来以为你会一直很难接近。'},
      {t:'K',v:'现在呢？'},
      {t:'P',v:'觉得你好像只是慢热一点。'},
      {t:'N',v:'凪低头笑了一下，那笑很浅，几乎一闪而过。'},
      {t:'K',v:'你适应得还挺快。'},
      {t:'P',v:'这是夸我吗？'},
      {t:'K',v:'不是。'},
      {t:'K',v:'只是觉得……你好像很容易，把别人放进自己的生活。'},
      {t:'T',v:'这句话让你有一点不舒服，却又说不上来哪里不对。那句话在脑子里停留了好几秒。'},
      {t:'N',v:'目光正好落在他的手机屏幕上。'},
      {t:'P',v:'今天那个机场图，大家都在夸你那件衣服。'},
      {t:'K',v:'嗯。'},
      {t:'P',v:'你不是说喜欢另一件吗？'},
      {t:'K',v:'不过算了。七十多镑，有点贵。'},
      {t:'SHOP',stage:2,total:72,isFinal:false,skipScene:'s35rw',ach:null,achDesc:null}
    ],next:'s35rw'
  },
  money_buy2:{
    beats:[
      {t:'K',v:'……你还真送。'},
      {t:'N',v:'他低头，声音轻了一点。'},
      {t:'K',v:'谢谢。'},
      {t:'G'}
    ],next:'s35rw'
  },
  money_skip2:{
    beats:[
      {t:'K',v:'其实也差不多。'},
      {t:'K',v:'穿什么都一样。'},
      {t:'G'}
    ],next:'s35rw'
  },
  s35rw:{
    beats:[
      {t:'K',v:'不过，我刚刚那句话，是认真的。'},
      {t:'CHOICE',v:[
        {text:'可能我比较容易相信别人吧。',flag:'ac',cls:'ac',next:'s35a'},
        {text:'我只是觉得，认识一个人需要一点主动。',flag:'ar',cls:'ar',next:'s35b'},
        {text:'为什么这样说？',flag:'sa',cls:'sa',next:'s35c'}
      ]}
    ]
  },
  s35a:{
    beats:[
      {t:'K',v:'这样不一定是好事。'},
      {t:'P',v:'为什么？'},
      {t:'K',v:'容易期待，也容易失望。'},
      {t:'P',v:'你是在提醒我？'},
      {t:'K',v:'只是随便说说。'},
      {t:'G'}
    ],next:'s36'
  },
  s35b:{
    beats:[
      {t:'K',v:'所以你都会这样吗？第一次见面，就会对别人这么好。'},
      {t:'P',v:'我没有，只是因为对象是你。'},
      {t:'N',v:'凪动作顿了一下，目光停留在你的脸上，几秒后，他低下头，轻轻笑了一下。'},
      {t:'K',v:'……那我应该觉得荣幸吗？'},
      {t:'P',v:'可以啊。'},
      {t:'G'}
    ],next:'s36'
  },
  s35c:{
    beats:[
      {t:'P',v:'你刚才那句话，让我有点不舒服。'},
      {t:'K',v:'我没有别的意思。'},
      {t:'P',v:'可是听起来像是在说我太容易相信别人。'},
      {t:'K',v:'……可能吧，至少我是这样觉得。'},
      {t:'T',v:'你突然发现，他没有打算解释，也没有打算改变自己的想法。'},
      {t:'G'}
    ],next:'s36'
  },

  // S3.6 情感模块异常 (BUG3) / 首饰
  s36:{
    beats:[
      {t:'SC',v:'S3.6 · 情感模块异常'},
      {t:'P',v:'你平时会戴饰品吗？'},
      {t:'K',v:'偶尔。'},
      {t:'P',v:'今天怎么没戴？'},
      {t:'K',v:'前段时间弄丢了，一直没找到合适的。'},
      {t:'P',v:'那以后有人送你一个呢？'},
      {t:'B',id:3,name:'BUG 3 · 情感模块异常',a:'情感输出中断，亲密语句无法稳定生成。',b:'他给出一点甜头，又马上撤回，让你继续猜。'},
      {t:'N',v:'凪抬起眼，沉默了一会儿，眼神难得柔和了一点。'},
      {t:'K',v:'其实我不太喜欢收礼物。但如果是你的话……',bug:true},
      {t:'N',v:'下一秒，他移开了视线。'},
      {t:'K',v:'算了，当我没说。',bug:true},
      {t:'T',v:'你下意识看向他空着的脖子，脑海里忽然闪过一个念头——如果是我送的，他会收吗？'},
      {t:'SHOP',stage:3,total:578,isFinal:false,skipScene:'s36rw',ach:'FREE LOVE?',achDesc:'他对所有人说过同样的话。'}
    ],next:'s36rw'
  },
  money_buy3:{
    beats:[
      {t:'N',v:'信用卡额度超限，备用信用已自动启用。'},
      {t:'K',v:'……谢谢。'},
      {t:'G'}
    ],next:'s36rw'
  },
  money_skip3:{
    beats:[
      {t:'K',v:'其实我本来也没想收。'},
      {t:'G'}
    ],next:'s36rw'
  },
  s36rw:{
    beats:[
      {t:'N',v:'空气安静了几秒，像是谁都没有再提起那句没说完的话。凪忽然轻轻笑了一下。'},
      {t:'K',v:'其实……跟你待在一起的时候，我觉得……很幸福。',bug:true},
      {t:'N',v:'凪忽然皱了一下眉，神情闪过一丝迟疑。下一秒，他低下眼眸，声音恢复平静。'},
      {t:'K',v:'……算了，当我没说。',bug:true},
      {t:'ACH',name:'READ'},
      {t:'T',v:'刚刚那句话，像是真的存在过，又好像从来没有发生。'},
      {t:'CHOICE',v:[
        {text:'那我就当真的听见了。',flag:'ac',cls:'ac',next:'s36a'},
        {text:'其实，你不用把话收回去。',flag:'ar',cls:'ar',next:'s36b'},
        {text:'我不喜欢这样。',flag:'sa',cls:'sa',next:'s36c'}
      ]}
    ]
  },
  s36a:{
    beats:[
      {t:'K',v:'随你。'},
      {t:'P',v:'你刚才不是那个意思吗？'},
      {t:'K',v:'你觉得是，就是吧。'},
      {t:'T',v:'他的回答依旧模糊，却没有否认。你发现自己开始忍不住替他解释。'},
      {t:'G'}
    ],next:'s37'
  },
  s36b:{
    beats:[
      {t:'K',v:'有时候，说出口的东西，比留在心里麻烦。'},
      {t:'P',v:'可是我想听。'},
      {t:'K',v:'……以后吧。'},
      {t:'G'}
    ],next:'s37'
  },
  s36c:{
    beats:[
      {t:'P',v:'让我以为你想说什么，下一秒又收回去。'},
      {t:'K',v:'抱歉。我不太习惯。'},
      {t:'P',v:'不习惯表达？'},
      {t:'N',v:'凪沉默，没有回答，也没有否认。'},
      {t:'ACH',name:'GHOSTED!'},
      {t:'G'}
    ],next:'s37'
  },

  // S3.7 提前打断/重复话语 (BUG4) / 手表
  s37:{
    beats:[
      {t:'SC',v:'S3.7 · 提前打断 / 沟通被夺走'},
      {t:'N',v:'咖啡已经见底。窗外的天色慢慢暗了下来。'},
      {t:'P',v:'今天其实还有很多话想跟你聊。'},
      {t:'K',v:'嗯。'},
      {t:'P',v:'你呢？'},
      {t:'B',id:4,name:'BUG 4 · 重复话语',a:'语言生成陷入循环，重复输出同一情绪短句。',b:'当作拒绝沟通的万能理由，让你停止表达。'},
      {t:'K',v:'一定要我回应吗？',bug:true},
      {t:'T',v:'空气一下安静了。'},
      {t:'P',v:'我只是想知道你在想什么。'},
      {t:'K',v:'一直等我回应。',bug:true},
      {t:'P',v:'聊天不就是这样吗？'},
      {t:'K',v:'一直等我回应。',bug:true},
      {t:'T',v:'空气彻底安静下来。你不知道还能说什么，主动换了个话题。'},
      {t:'P',v:'对了，你生日是不是快到了？'},
      {t:'K',v:'嗯。'},
      {t:'P',v:'有想要的东西吗？'},
      {t:'K',v:'没有，这些东西也不会改变什么。'},
      {t:'K',v:'前两天经纪人开玩笑，说要是有人送我那块表，应该会很开心。'},
      {t:'P',v:'哪块？'},
      {t:'K',v:'Rolexx。不过怎么可能，只是随便说说。'},
      {t:'SHOP',stage:4,total:6900,isFinal:true,skipScene:'s37rw',ach:null,achDesc:null}
    ],next:'s37rw'
  },
  money_skip4:{
    beats:[
      {t:'K',v:'我还以为。'},
      {t:'N',v:'停顿。'},
      {t:'K',v:'算了。'},
      {t:'T',v:'空气安静了一瞬。你却不知道为什么，心里反而更乱了。'},
      {t:'G'}
    ],next:'s37rw'
  },
  s37rw:{
    beats:[
      {t:'N',v:'凪看着前面，空气安静了一瞬。你却不知道为什么，心里反而更乱了。'},
      {t:'CHOICE',v:[
        {text:'……我是不是让你失望了？',flag:'ac',cls:'ac',next:'s37a'},
        {text:'你到底想说什么，不要每次都收回去。',flag:'ar',cls:'ar',next:'s37b'},
        {text:'你是在期待我做什么吗？',flag:'sa',cls:'sa',next:'s37c'}
      ]}
    ]
  },
  s37a:{
    beats:[
      {t:'K',v:'我没有失望。'},
      {t:'K',v:'只是觉得，你好像一直都很努力。努力让我给你一个答案。'},
      {t:'N',v:'你低下头，没有说话。'},
      {t:'G'}
    ],next:'s38'
  },
  s37b:{
    beats:[
      {t:'K',v:'说出来，你就会一直记着，然后一直想知道答案。'},
      {t:'P',v:'可我只是想知道你在想什么。'},
      {t:'G'}
    ],next:'s38'
  },
  s37c:{
    beats:[
      {t:'K',v:'你看，你又开始猜了。'},
      {t:'P',v:'我只是问你。'},
      {t:'N',v:'凪移开了视线。'},
      {t:'G'}
    ],next:'s38'
  },

  // S3.8 矛盾最高点 (BUG5)
  s38:{
    beats:[
      {t:'SC',v:'S3.8 · 矛盾最高点'},
      {t:'N',v:'桌上的咖啡已经凉了。谁都没有再去碰。你望着凪，还是决定把心里的话说出来。'},
      {t:'B',id:5,name:'BUG 5 · 突然打断',a:'提前生成回复，模型抢在玩家输入完成前输出。',b:'他不耐烦，直接把你归类为"要求太多"。'},
      {t:'BUG5'},
      {t:'T',v:'我……还没说完。'},
      {t:'CHOICE',v:[
        {text:'……（沉默）',flag:'ac',cls:'ac',next:'s38a'},
        {text:'你能不能让我说完？',flag:'ar',cls:'ar',next:'s38b'},
        {text:'我根本不是要说这个！',flag:'sa',cls:'sa',next:'s38c'}
      ]}
    ]
  },
  s38a:{
    beats:[
      {t:'K',v:'你看，又不说话了。'},
      {t:'K',v:'所以我才说，沟通很麻烦。'},
      {t:'T',v:'你忽然发现，沉默也是错，说话也是错。'},
      {t:'ACH',name:'COPE HARDER'},
      {t:'G'}
    ],next:'s39'
  },
  s38b:{
    beats:[
      {t:'K',v:'那你想说什么？'},
      {t:'N',v:'他终于看着你。可那种眼神不像在等你说话，更像在等你证明自己确实很麻烦。'},
      {t:'G'}
    ],next:'s39'
  },
  s38c:{
    beats:[
      {t:'K',v:'随你怎么说，反正你已经这么想了。'},
      {t:'T',v:'他把所有门都关上了。'},
      {t:'G'}
    ],next:'s39'
  },

  // S3.9 高潮情绪反应
  s39:{
    beats:[
      {t:'SC',v:'S3.9 · 高潮'},
      {t:'K',v:'老实告诉你吧，你越来越让我倒胃口了。'},
      {t:'K',v:'一直追问、一直想让我哄你、一直把自己的情绪扔给我……我根本应付不来。'},
      {t:'K',v:'你要是觉得委屈，就别再缠着我了。我喜欢的是安静懂事的女孩，至少……不是现在的你。'},
      {t:'CHOICE',v:[
        {text:'❤  对不起……我以后会改。',flag:'ac',val:2,cls:'ac',next:'s39a'},
        {text:'😐 原来这就是你沟通的方式。',flag:'ar',val:1,cls:'ar',next:'s39b'},
        {text:'💥 够了！我们到此为止。',flag:'sa',val:2,cls:'sa',next:'s39c'}
      ]}
    ]
  },
  s39a:{
    beats:[
      {t:'T',v:'也许他只是不会表达。也许他习惯了一个人，所以才会这么防备。'},
      {t:'T',v:'他刚才说"幸福"的时候，应该是真的吧。'},
      {t:'ACH',name:'RED FLAG'},
      {t:'G'}
    ],next:'s310'
  },
  s39b:{
    beats:[
      {t:'T',v:'他说的话很伤人，可是他偶尔又会露出一点温柔。'},
      {t:'T',v:'我不知道哪一个才是真的他。'},
      {t:'ACH',name:'COPE HARDER'},
      {t:'G'}
    ],next:'s310'
  },
  s39c:{
    beats:[
      {t:'T',v:'我不需要为了被爱，变成别人喜欢的样子。'},
      {t:'T',v:'如果喜欢一个人要变得这么小心，那这还是喜欢吗？'},
      {t:'ACH',name:'QUEEN NEVER CRY'},
      {t:'G'}
    ],next:'s310'
  },

  // S3.10 回归现实
  s310:{
    beats:[
      {t:'SC',v:'S3.10 · 回归现实'},
      {t:'N',v:'咖啡已经凉了。窗外的光一点点暗下去。凪低头看了一眼手机，屏幕亮起，又很快熄灭。'},
      {t:'K',v:'时间差不多了，我还有直播。'},
      {t:'K',v:'那……你下次还会来吗？'},
      {t:'CHOICE',v:[
        {text:'不会，再见。',flag:null,cls:'sa',next:'ending_reality',ach:'QUEEN NEVER CRY'},
        {text:'会，我喜欢你。',flag:null,cls:'ac',next:'ending_reality',ach:'WIN'},
        {text:'……看吧，看心情。',flag:null,cls:'ar',next:'ending_reality'},
        {text:'我也想学你那样。',flag:null,cls:'',next:'ending_reality'}
      ]}
    ]
  }
};

const ENDINGS = {
  ending_e:{
    tag:'ENDING E · 五年还债版',
    isReceipt:true,
    text:`那天他在直播里说：谢谢大家一直以来的支持。

你的手机同时震动了一下。

SOFT BANK 欠款通知
总欠款：£7,555.00
最低还款：£377.75
年利率：18.25%

如只还最低还款额：
需5年2个月还清
额外利息：£2,430.18
（≈ 588小时兼职）

爸妈问你最近怎么了。
你说没事。`,
    label:'——五年还债',
    note:'逾期记录将保留5年，影响助学贷款、租房押金和用人单位背景调查。',
    receipts:[
      {store:'CAFÉ MOMENT',date:'2070/03/12',lines:['Americano £4.20','Extra Shot £0.80'],total:'£5.00'},
      {store:'URBAN STYLE',date:'2070/04/08',lines:['Limited Hoodie £72.00'],total:'£72.00'},
      {store:'LUMIÈRE JEWELRY',date:'2070/07/14',lines:['18K Gold Chain £385.00','Gemstone Pendant £193.00'],total:'£578.00'},
      {store:'ROLEXX',date:'2070/09/03',lines:['Oyster Perpetual £7,600','Discount -£700'],total:'£6,900.00'},
      {store:'⚠ SOFT BANK',date:'2070/10/01',lines:['Total £7,555.00','Min.Pay £377.75','APR 18.25%'],total:'Credit Score: 580↓'},
    ]
  },
  ending_a:{
    tag:'ENDING A · 告吹',
    text:`我没有再回头。

走出咖啡厅的时候，夜风吹得我忽然清醒了一点。

我一直以为，他的冷淡是一种保护。
保护他自己，也保护我们之间那点还没说出口的暧昧。

可是现在我才发现，他只是把门关上，再让我站在门外反复敲。

他说我太想要回应。
可我想要的，其实只是一句完整的话。`,
    label:'——告吹',
    note:'self_assert ≥ 3 时：「我没有逼他爱我。我只是终于不再逼自己理解他的冷漠。」'
  },
  ending_b:{
    tag:'ENDING B · 幸福欺骗',
    text:`后来我还是去了。一次，两次，很多次。

他依然很少回应。依然会在我说到一半的时候打断我。
依然会说：你想太多了。

但偶尔，他也会说一句很轻的话。
比如：今天还行。比如：你来了。比如：跟你在一起……算了。

我开始把这些没说完的话，当成只有我能听懂的爱。

朋友问我幸福吗。
我说，幸福。至少在我不追问的时候，幸福。`,
    label:'——幸福欺骗',
    note:'accommodate ≥ 3 时：「我已经学会了不打扰他的沉默，也学会了把自己的难过关小声。」'
  },
  ending_c:{
    tag:'ENDING C · 可有可无',
    text:`后来我们偶尔联系。偶尔见面。
更多时候，是我发很多句，他回一个嗯。

我以为他只是忙。以为他只是不会表达。

直到有一天，我在直播里看到他对另一个人说：
你不用这么关心我。

语气一模一样。停顿一模一样。
连那种像拒绝又像暧昧的沉默，也一模一样。

原来我不是特别的。
我只是刚好排到了这一次。`,
    label:'——可有可无',
    note:'articulate ≥ 2 时：「我说过很多次我的感受。只是他从来没有真正接住。」'
  },
  ending_d:{
    tag:'ENDING D · 共谋',
    text:`后来，有新的女生在粉丝群里问：
他为什么总是不回我？他是不是讨厌我？我是不是太烦了？

我看着那些话，像看到以前的自己。

然后我打字回复：
他就是这个性格啦。别逼他，他会累。你要学会安静一点。

消息发出去后，我盯着屏幕看了很久。

那一刻我突然发现。
我已经开始用他的语言，去训练另一个女生沉默。`,
    label:'——共谋',
    note:'accommodate ≥ 2 时：「我以为我是在保护她不要受伤，其实我是在保护那个曾经选择留下的自己。」'
  }
};

// ===== ENGINE =====
function startGame() {
  document.getElementById('intro').style.display = 'none';
  document.getElementById('dlgbox').classList.add('show');
  loadScene('s30');
}

function loadScene(id) {
  if (id === 'ending_reality') { showRealityEnding(); return; }
  if (ENDINGS[id]) { showEnding(id); return; }
  G.scene = id;
  G.beat = 0;
  nextBeat();
}

function nextBeat() {
  const sc = S[G.scene];
  if (!sc) return;
  if (G.beat >= sc.beats.length) {
    if (sc.next) loadScene(sc.next);
    return;
  }
  const b = sc.beats[G.beat];
  G.beat++;
  processBeat(b, sc);
}

function processBeat(b, sc) {
  hideChoices();
  if (b.t==='SC') { showSceneLabel(b.v); nextBeat(); }
  else if (b.t==='N') { pushHistory('narr',b.v); showNarration(b.v); setNext(()=>{ hideNarration(); nextBeat(); }); }
  else if (b.t==='T') { pushHistory('thought',b.v); showThought(b.v); setNext(()=>{ hideThought(); nextBeat(); }); }
  else if (b.t==='K') { pushHistory('nagi',b.v,b.bug); setCharMode(b.bug?'bug':'nagi'); document.getElementById('dlgbox').classList.add('show'); typeText(b.v,()=>setNext(()=>nextBeat())); }
  else if (b.t==='P') { pushHistory('me',b.v); setCharMode('me'); document.getElementById('dlgbox').classList.add('show'); typeText(b.v,()=>setNext(()=>nextBeat())); }
  else if (b.t==='B') {
    G.bug=b; flashBug(b.name);
    if(G.design) showDesignPanel(b);
    document.getElementById('bugstatus').textContent='★ '+b.name;
    document.getElementById('bugstatus').classList.add('on');
    nextBeat();
  }
  else if (b.t==='BUG5') { doBug5(); }
  else if (b.t==='ACH') { unlockAchievementThen(b.name,()=>nextBeat()); return; }
  else if (b.t==='SHOP') { G.shopCurrent=b; showShopPopup(b); }
  else if (b.t==='G') { setBtnNext(true); setNext(()=>{ if(sc.next) loadScene(sc.next); }); }
  else if (b.t==='CHOICE') { showChoices(b.v); setBtnNext(false); }
}

function setCharMode(mode) {
  const txt = document.getElementById('dlg-text');
  txt.className = mode === 'bug' ? 'bug-text' : '';
  document.getElementById('dlg-frame').src = mode === 'me' ? 'me_box.png' : 'nagi_box.png';
  G.charMode = mode;
}

function typeText(text, done) {
  const el = document.getElementById('dlg-text');
  el.textContent = '';
  G.isTyping = true;
  setBtnNext(false);
  let i = 0;
  if (G.typeTimer) clearInterval(G.typeTimer);
  G.typeTimer = setInterval(()=>{
    el.textContent += text[i]; i++;
    if (i >= text.length) { clearInterval(G.typeTimer); G.typeTimer=null; G.isTyping=false; if(done) done(); }
  }, 28);
  G.skipDone = done;
}

function skipType() {
  if (!G.isTyping) return false;
  clearInterval(G.typeTimer); G.typeTimer=null; G.isTyping=false;
  const el = document.getElementById('dlg-text');
  const sc = S[G.scene];
  if (sc) { const prev = sc.beats[G.beat-1]; if(prev && prev.v) el.textContent=prev.v; }
  if (G.skipDone) G.skipDone();
  G.skipDone = null;
  return true;
}

function showNarration(text) {
  document.getElementById('dlg-text').textContent = '';
  document.getElementById('narr-overlay').textContent = text;
  document.getElementById('narr-overlay').classList.add('show');
  document.getElementById('dlgbox').classList.remove('show');
  setBtnNext(true);
}
function hideNarration() {
  document.getElementById('narr-overlay').classList.remove('show');
}
function showThought(text) {
  document.getElementById('dlg-text').textContent = '';
  document.getElementById('thought-overlay').textContent = '（'+text+'）';
  document.getElementById('thought-overlay').classList.add('show');
  document.getElementById('dlgbox').classList.remove('show');
  setBtnNext(true);
}
function hideThought() {
  document.getElementById('thought-overlay').classList.remove('show');
}
function showSceneLabel(text) {
  const el = document.getElementById('scene-overlay');
  el.textContent = text; el.classList.add('show');
  setTimeout(()=>el.classList.remove('show'), 2200);
}
function flashBug(name) {
  const el = document.getElementById('bug-flash');
  el.textContent = '⚡ '+name; el.classList.add('show');
  setTimeout(()=>el.classList.remove('show'), 2000);
}
function showChoices(opts) {
  const c = document.getElementById('choices');
  c.innerHTML = '';
  opts.forEach(opt=>{
    const btn = document.createElement('button');
    btn.className = 'cbtn '+(opt.cls||'');
    const label = opt.label||(opt.flag ? opt.flag.toUpperCase() : '');
    btn.innerHTML = '<span>'+opt.text+'</span><span class="fhint">'+label+'</span>';
    btn.onclick = ()=>pickChoice(opt);
    c.appendChild(btn);
  });
  c.classList.add('show');
  document.getElementById('dlgbox').classList.remove('show');
}
function hideChoices() {
  document.getElementById('choices').classList.remove('show');
  document.getElementById('dlgbox').classList.add('show');
}
function pickChoice(opt) {
  hideChoices();
  if (opt.flag) { const val=opt.val||1; G.flags[opt.flag]+=val; updateFlags(opt.flag); }
  const doNext = ()=>loadScene(opt.next);
  const doWithAch = opt.ach ? ()=>unlockAchievementThen(opt.ach, doNext) : doNext;
  if (opt.next && !opt.next.startsWith('ending')) {
    setCharMode('me');
    typeText(opt.text.replace(/^[❤😐💥]\s+/,''),()=>{ setNext(doWithAch); });
  } else { doWithAch(); }
}

function doBug5() {
  const pb = document.getElementById('bug5-player');
  const nb = document.getElementById('bug5-nagi');
  const b5 = document.getElementById('b5b');
  document.getElementById('dlgbox').classList.remove('show');
  pb.classList.add('show');
  b5.className = 'b5bubble';
  setTimeout(()=>{
    b5.classList.add('interrupted');
    setTimeout(()=>{
      nb.classList.add('show');
      setTimeout(()=>{
        pb.classList.remove('show'); nb.classList.remove('show');
        document.getElementById('dlgbox').classList.add('show');
        nextBeat();
      }, 2000);
    }, 600);
  }, 900);
}

let nextCallback = null;
function setNext(cb) { nextCallback=cb; setBtnNext(true); }
function advance() {
  if (skipType()) return;
  if (nextCallback) { const cb=nextCallback; nextCallback=null; setBtnNext(false); cb(); }
}
function setBtnNext(show) {
  const btn = document.getElementById('btn-next');
  if (show) { btn.classList.remove('hidden'); btn.classList.add('pulse'); }
  else { btn.classList.add('hidden'); btn.classList.remove('pulse'); }
}

function pushHistory(type, text, bug) {
  G.history.push({ scene:G.scene, beat:G.beat, flags:{...G.flags}, type, text, bug:!!bug,
    charMode:type==='nagi'?(bug?'bug':'nagi'):type==='me'?'me':type });
  if (G.history.length > 80) G.history.shift();
  document.getElementById('btn-prev').disabled = false;
}

function goPrev() {
  if (G.history.length < 2) return;
  G.history.pop();
  const prev = G.history[G.history.length-1];
  if (!prev) return;
  G.flags = {...prev.flags};
  updateFlags('sa'); updateFlags('ac'); updateFlags('ar');
  hideChoices(); hideNarration(); hideThought();
  nextCallback=null; G.isTyping=false;
  if (G.typeTimer) { clearInterval(G.typeTimer); G.typeTimer=null; }
  if (prev.type==='narr') { showNarration(prev.text); setNext(()=>{ hideNarration(); nextBeat(); }); }
  else if (prev.type==='thought') { showThought(prev.text); setNext(()=>{ hideThought(); nextBeat(); }); }
  else { setCharMode(prev.charMode); document.getElementById('dlg-text').textContent=prev.text; setBtnNext(true); setNext(()=>{ G.scene=prev.scene; G.beat=prev.beat; nextBeat(); }); }
}

function updateFlags(f) {
  const labels={sa:'SA',ac:'AC',ar:'AR'};
  const el = document.getElementById('fp-'+f);
  el.textContent = labels[f]+' '+G.flags[f];
  el.classList.add('bump');
  setTimeout(()=>el.classList.remove('bump'), 400);
}

function toggleDesign() {
  G.design = !G.design;
  document.getElementById('dtoggle').classList.toggle('on', G.design);
  const panel = document.getElementById('dpanel');
  if (G.design) { panel.classList.add('on'); if(G.bug) showDesignPanel(G.bug); else { document.getElementById('dp-name').textContent='— 等待 Bug 触发 —'; document.getElementById('dp-a').textContent=''; document.getElementById('dp-b').textContent=''; } }
  else panel.classList.remove('on');
}
function showDesignPanel(bug) {
  document.getElementById('dp-name').textContent = bug.name;
  document.getElementById('dp-a').textContent = bug.a;
  document.getElementById('dp-b').textContent = bug.b;
}

// ===== ACHIEVEMENTS =====
const ACH_EDU = {
  "REAL?":             '在关系中，当你开始怀疑——那不是你太敏感。\n直觉是一种信息，而不是需要压制的东西。',
  "GHOSTED!":          '冷漠不是成熟，是拒绝沟通的一种方式。\n一个让你总觉得「是不是我的问题」的人，可能在用距离控制你。',
  "READ":              '他说了一半，又收回去——然后让你一直猜。\n这种「给甜头又撤回」的模式叫做间歇性强化，会让人更难离开。',
  "COPE HARDER":       '为另一个人的行为反复找借口，会让你慢慢忽视自己真实的感受。\n你不需要「理解」一段让你痛苦的关系才能离开它。',
  "FIRST SWIPE!":      '第一次刷卡可能只是£5——但足以开始一个消费习惯。\n足迹定律：小额请求往往是更大要求的铺垫。',
  "FREE LOVE?":        '当礼物变成一种套路，关心就变成了工具。\n如果你总是在花钱之后才感受到他的温柔，这不是爱，是条件。',
  "CREDIT↓":           '信用评分580：逾期记录将保留5年。\n影响租房押金、助学贷款、求职背景调查。\n£7,555 = 588小时兼职工作。',
  "STAYING CLEAR-HEADED": '你说了不。\n这一次，你没有让别人的期待覆盖你自己的判断。',
  "QUEEN NEVER CRY":   '离开一段让你委屈的关系，不是失败，是清醒。\n你不需要等对方变好才值得离开。',
  "WIN":               '你留下来了。但你真的赢了什么？\n有时候，坚持只是另一种形式的自我消耗。',
  "RED FLAG":          '「他只是不会表达」是最常见的合理化方式之一。\n一段让你反复替对方辩护的关系，值得你认真审视。',
};

const ACH_IMG = {
  'FIRST SWIPE!':      'ach_firstswipe.png',
  'CREDIT↓':           'ach_credit.png',
  'GHOSTED!':          'ach_ghosted.png',
  'QUEEN NEVER CRY':   'ach_queen.png',
  'REAL?':             'ach_real.png',
  'FREE LOVE?':        'ach_freelove.png',
  'COPE HARDER':       'ach_cope.png',
  'READ':              'ach_read.png',
  'WIN':               'ach_win.png',
  'RED FLAG':          'ach_special.png',
  'STAYING CLEAR-HEADED': 'ach_queen.png'
};

let achResumeCallback = null;

function unlockAchievement(name) {
  if (G.achievements.has(name)) {
    if (achResumeCallback) { const cb=achResumeCallback; achResumeCallback=null; cb(); } else setBtnNext(true);
    return;
  }
  G.achievements.add(name);
  const imgFile = ACH_IMG[name] || 'ach_win.png';
  document.getElementById('ach-popup-img').src = imgFile;
  document.getElementById('ach-popup-label').textContent = 'Achievement Unlocked · ' + name;
  setBtnNext(false);
  const popup = document.getElementById('ach-popup');
  popup.classList.remove('show'); void popup.offsetWidth; popup.classList.add('show');
  clearTimeout(window._achTimer);
  window._achTimer = setTimeout(()=>closeAchievement(), 4000);
}

function unlockAchievementThen(name, cb) { achResumeCallback=cb; unlockAchievement(name); }

function closeAchievement() {
  clearTimeout(window._achTimer);
  document.getElementById('ach-popup').classList.remove('show');
  G.moneyRefusals = 0;
  if (achResumeCallback) { const cb=achResumeCallback; achResumeCallback=null; cb(); }
  else setBtnNext(true);
}

// ===== SHOP SYSTEM =====
function buildBars() {
  const widths=[3,1,4,1,2,1,3,2,1,2,3,1,4,1,2,3,1,2,1,3];
  return widths.map(w=>`<div class="cafe-bar" style="width:${w*2}px"></div>`).join('');
}

const RECEIPT_HTML = {
  1:    ()=>`<img src="receipts/01_coffee.png"  style="max-width:340px;max-height:560px;display:block;border-radius:8px;box-shadow:0 8px 32px rgba(0,0,0,0.6)">`,
  2:    ()=>`<img src="receipts/02_clothes.png" style="max-width:340px;max-height:560px;display:block;border-radius:8px;box-shadow:0 8px 32px rgba(0,0,0,0.6)">`,
  3:    ()=>`<img src="receipts/04_jewelry.png" style="max-width:340px;max-height:560px;display:block;border-radius:8px;box-shadow:0 8px 32px rgba(0,0,0,0.6)">`,
  4:    ()=>`<img src="receipts/05_rolex.png"   style="max-width:340px;max-height:560px;display:block;border-radius:8px;box-shadow:0 8px 32px rgba(0,0,0,0.6)">`,
  bank: ()=>`<img src="receipts/06_bank_statement.png" style="max-width:340px;max-height:560px;display:block;border-radius:8px;box-shadow:0 8px 32px rgba(0,0,0,0.6)">`
};

const SHOP_LABELS = {
  1:{name:'CAFÉ MOMENT', item:'Americano + Extra Shot', price:'£5.00',   btn:'Pay £5.00'},
  2:{name:'URBAN STYLE', item:'Limited Hoodie (M)',     price:'£72.00',  btn:'Buy £72'},
  3:{name:'LUMIÈRE',     item:'18K Gold Pendant',       price:'£578.00', btn:'Buy for Nagi £578'},
  4:{name:'ROLEXX',      item:'Oyster Perpetual 41mm',  price:'£6,900',  btn:'Confirm £6,900'}
};

function showShopPopup(b) {
  const info = SHOP_LABELS[b.stage] || {};
  document.getElementById('rcpt-content').innerHTML = `
    <div style="text-align:center;color:#d8e8ff;padding:32px 28px 20px;font-family:monospace;min-width:280px">
      <div style="font-size:10px;letter-spacing:0.22em;color:rgba(180,200,255,0.5);margin-bottom:10px">${info.name||''}</div>
      <div style="font-size:13px;color:rgba(200,215,255,0.6);margin-bottom:18px">${info.item||''}</div>
      <div style="font-size:44px;font-weight:900;letter-spacing:-0.02em;color:#d8e8ff;margin-bottom:6px">${info.price||''}</div>
    </div>`;
  const btns = document.getElementById('rcpt-btns');
  btns.style.display = 'flex';
  document.getElementById('rcpt-pay-btn').textContent = info.btn || 'Pay';
  document.getElementById('receipt-popup').classList.add('show');
  document.getElementById('dlgbox').classList.remove('show');
  setBtnNext(false);
}

function closeShop() {
  document.getElementById('receipt-popup').classList.remove('show');
  document.getElementById('dlgbox').classList.add('show');
}

function doShopBuy() {
  const b = G.shopCurrent;
  // 更新消费数据
  G.money.total += b.total;
  G.money.purchases.push({stage:b.stage, total:b.total});
  const pill = document.getElementById('spend-pill');
  pill.style.display = 'inline';
  pill.textContent = '💳 £' + G.money.total.toLocaleString();
  if (G.money.total >= 500) pill.classList.add('warn');

  // 付款成功 → 弹出收据图
  const fn = RECEIPT_HTML[b.stage];
  document.getElementById('rcpt-content').innerHTML = fn ? fn() : '';
  document.getElementById('rcpt-btns').style.display = 'none';
  // 加点击提示
  const hint = document.createElement('div');
  hint.style.cssText = 'text-align:center;font-size:10px;color:rgba(180,180,180,0.45);font-family:monospace;letter-spacing:0.12em;margin-top:12px;padding-bottom:8px';
  hint.textContent = 'tap to continue';
  document.getElementById('rcpt-content').appendChild(hint);

  const continueAfterAch = () => {
    if (b.isFinal) { setTimeout(()=>showBankStatement(), 400); }
    else { const sc='money_buy'+b.stage; if(S[sc]) loadScene(sc); else nextBeat(); }
  };
  const afterReceipt = () => {
    closeShop();
    if (b.ach) unlockAchievementThen(b.ach, continueAfterAch);
    else continueAfterAch();
  };
  // 点击任意处继续
  setBtnNext(true);
  setNext(afterReceipt);
}

function doShopSkip() {
  const b = G.shopCurrent;
  closeShop();
  G.moneyRefusals++;
  const skipScene = 'money_skip' + b.stage;
  const doSkip = ()=>loadScene(S[skipScene] ? skipScene : (b.skipScene || 's310'));
  if (b.isFinal || G.moneyRefusals >= 2) unlockAchievementThen('STAYING CLEAR-HEADED', doSkip);
  else doSkip();
}

function showBankStatement() {
  G.shopCurrent = {stage:'bank'};
  document.getElementById('rcpt-content').innerHTML = RECEIPT_HTML.bank();
  document.getElementById('rcpt-btns').style.display = 'none';
  document.getElementById('receipt-popup').classList.add('show');
  document.getElementById('dlgbox').classList.remove('show');
  setBtnNext(false);
  unlockAchievement('CREDIT↓');
  setTimeout(()=>{ document.getElementById('receipt-popup').classList.remove('show'); showEnding('ending_e'); }, 5000);
}

// ===== RECEIPT SCATTER =====
function scatterReceipts() {
  const layer = document.getElementById('receipt-layer');
  layer.innerHTML = ''; layer.classList.add('show');
  const slips = [
    {cls:'cafe',  lines:['CAFÉ MOMENT','2070/03/12','─────────','Americano £4.20','Extra Shot £0.80','─────────','TOTAL £5.00']},
    {cls:'urban', lines:['URBAN STYLE','2070/04/08','─────────','Hoodie (M) £72.00','Total £72.00','Credit **** 8821']},
    {cls:'lumiere',lines:['LUMIÈRE JEWELRY','2070/07/14','─────────','18K Gold £385','Pendant £193','Total £578.00']},
    {cls:'rolexx',lines:['♛ ROLEXX','2070/09/03','─────────','Oyster £7,600','-Discount £700','TOTAL £6,900']},
    {cls:'bank',  lines:['SoftBank','OVERDUE','─────────','Total £7,555','Min.Pay £377.75','Credit Score: 580']},
    {cls:'cafe',  lines:['CAFÉ MOMENT','£5.00','CARD **** 8821']},
    {cls:'urban', lines:['URBAN STYLE','£72.00','Charge confirmed']},
    {cls:'lumiere',lines:['LUMIÈRE','Backup credit','applied']},
    {cls:'rolexx',lines:['ROLEXX','DECLINED','Backup APPROVED']},
    {cls:'bank',  lines:['5 yrs 2 mo','£2,430 interest','588 hrs work']},
  ];
  const pos = [
    [40,60],[250,40],[500,100],[750,30],[1000,80],[1220,50],
    [120,300],[360,260],[620,320],[870,280],[1100,310],[1300,260],
    [60,520],[300,480],[560,550],[800,500],[1050,520],[1240,480],
    [150,700],[420,720],[680,690],[940,710]
  ];
  slips.forEach((s,i)=>{
    const [x,y] = pos[i%pos.length];
    const rot = (Math.random()*40-20).toFixed(1);
    const d = document.createElement('div');
    d.className = 'r-slip '+s.cls;
    d.style.cssText = `left:${x}px;top:${y}px;--r:${rot}deg;animation-delay:${(i*0.15).toFixed(2)}s`;
    d.innerHTML = s.lines.join('<br>');
    layer.appendChild(d);
  });
  setTimeout(()=>{
    const lo = document.getElementById('locked-overlay');
    lo.classList.add('show');
    setTimeout(()=>{ document.getElementById('locked-msg').style.color='rgba(255,80,80,0.85)'; }, 1000);
  }, 2500);
}

// ===== REALITY ENDING =====
function showRealityEnding() {
  const screen = document.getElementById('reality-screen');
  screen.innerHTML = '';
  const title = document.createElement('div'); title.className='reality-title'; title.textContent='Back to Reality · 回到现实'; screen.appendChild(title);
  const sub = document.createElement('div'); sub.className='reality-sub'; sub.textContent='这是一个游戏。现实不是。'; screen.appendChild(sub);
  const grid = document.createElement('div'); grid.className='reality-grid';
  let delay = 0;
  G.achievements.forEach(name=>{
    const edu = ACH_EDU[name];
    if (!edu) return;
    const card = document.createElement('div'); card.className='reality-card'; card.style.animationDelay=delay+'ms'; delay+=120;
    const img = document.createElement('img'); img.src = ACH_IMG[name] || 'ach_win.png'; img.alt = name;
    const eduDiv = document.createElement('div'); eduDiv.className='reality-edu'; eduDiv.textContent=edu;
    card.appendChild(img); card.appendChild(eduDiv); grid.appendChild(card);
  });
  screen.appendChild(grid);
  const rb = document.createElement('button'); rb.className='reality-restart'; rb.textContent='重新体验'; rb.onclick=restartGame; screen.appendChild(rb);
  screen.classList.add('show');
  document.getElementById('dlgbox').classList.remove('show');
  document.getElementById('choices').classList.remove('show');
}

// ===== ENDING =====
function showEnding(id) {
  const e = ENDINGS[id];
  const el = document.getElementById('ending');
  el.innerHTML = '';
  if (e.ach) { unlockAchievementThen(e.ach, ()=>_doShowEnding(id)); return; }
  _doShowEnding(id);
}
function _doShowEnding(id) {
  const e = ENDINGS[id];
  const el = document.getElementById('ending');
  if (e.isReceipt) scatterReceipts();
  const fp = document.createElement('div'); fp.className='e-flags';
  [{f:'sa',n:'自我主张',c:'#e060a8'},{f:'ac',n:'顺从',c:'#30c0c8'},{f:'ar',n:'沟通',c:'#e0a828'}].forEach(x=>{
    const p = document.createElement('div'); p.className='e-fpill'; p.style.color=x.c; p.textContent=x.n+' '+G.flags[x.f]; fp.appendChild(p);
  });
  el.appendChild(fp);
  const tag=document.createElement('div'); tag.className='e-tag'; tag.textContent=e.tag; el.appendChild(tag);
  const txt=document.createElement('div'); txt.className='e-text'; txt.textContent=e.text; el.appendChild(txt);
  const lbl=document.createElement('div'); lbl.className='e-label'; lbl.textContent=e.label; el.appendChild(lbl);
  const note=document.createElement('div'); note.className='e-note'; note.textContent='※ '+e.note; el.appendChild(note);
  const rb=document.createElement('button'); rb.className='e-restart'; rb.textContent='重新体验'; rb.onclick=restartGame; el.appendChild(rb);
  el.classList.add('show');
  document.getElementById('dlgbox').classList.remove('show');
}

function restartGame() {
  G.flags={sa:0,ac:0,ar:0}; G.history=[]; G.bug=null; G.isTyping=false;
  if (G.typeTimer) { clearInterval(G.typeTimer); G.typeTimer=null; }
  nextCallback=null;
  updateFlags('sa'); updateFlags('ac'); updateFlags('ar');
  G.money={total:0,purchases:[]}; G.achievements=new Set(); G.shopCurrent=null; G.moneyRefusals=0;
  const pill=document.getElementById('spend-pill');
  pill.style.display='none'; pill.classList.remove('warn'); pill.textContent='💳 £0';
  document.getElementById('receipt-layer').classList.remove('show');
  document.getElementById('receipt-layer').innerHTML='';
  document.getElementById('bugstatus').textContent='';
  document.getElementById('bugstatus').classList.remove('on');
  document.getElementById('ending').classList.remove('show');
  document.getElementById('reality-screen').classList.remove('show');
  document.getElementById('dlgbox').classList.add('show');
  document.getElementById('dpanel').classList.remove('on');
  document.getElementById('dtoggle').classList.remove('on');
  document.getElementById('locked-overlay').classList.remove('show');
  document.getElementById('locked-msg').style.color='rgba(255,80,80,0)';
  G.design=false;
  hideChoices(); hideNarration(); hideThought();
  loadScene('s30');
}
</script>
</body>
</html>

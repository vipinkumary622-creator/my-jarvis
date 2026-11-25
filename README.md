<!doctype html>
<html lang="hi">
<head>
<meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1">
<title>JARVIS 2.0</title>
<style>
*{box-sizing:border-box}body{margin:0;background:#06101e;color:#eaf5ff;font-family:Arial,sans-serif;min-height:100vh;padding:18px;display:grid;place-items:center}
.app{width:min(100%,520px);background:#0e1d30;border:1px solid #234764;border-radius:24px;padding:22px;box-shadow:0 16px 50px #0008}
header{display:flex;justify-content:space-between;align-items:center}h1{letter-spacing:4px;color:#70dcff;font-size:25px;margin:0}.tag{font-size:11px;color:#8faec8;border:1px solid #31516d;border-radius:20px;padding:6px 9px}
.orb{height:118px;width:118px;margin:25px auto;border-radius:50%;background:radial-gradient(circle,#d5fbff 0 5%,#43d7ff 15%,#0877b5 38%,#092744 66%,#06101e 72%);box-shadow:0 0 28px #1cbef5;animation:pulse 2.4s infinite}
@keyframes pulse{50%{transform:scale(1.04);box-shadow:0 0 48px #1cbef5}}
.sub{text-align:center;color:#abc4d9;font-size:14px}
.actions{display:grid;grid-template-columns:1fr 1fr;gap:9px;margin:18px 0}
button{border:0;border-radius:12px;padding:13px 10px;background:#1db4e4;color:#001827;font-weight:bold;font-size:15px}
button.alt{background:#263f59;color:#eaf5ff}
input{width:100%;border:1px solid #365976;border-radius:12px;background:#071525;color:white;padding:14px;font-size:16px}
#reply{background:#071525;border:1px solid #1c3851;border-radius:14px;padding:15px;min-height:85px;line-height:1.5;white-space:pre-wrap;margin-top:14px}
.note{font-size:12px;color:#9bb4ca;line-height:1.5;margin-top:15px}
</style></head>
<body><main class="app">
<header><h1>J.A.R.V.I.S</h1><span class="tag">VERSION 2.0 · BETA</span></header>
<div class="orb"></div><p class="sub">Voice assistant · Hindi / English</p>
<div class="actions">
<button id="listen">🎙️ बोलकर बताओ</button><button class="alt" id="stop">🔇 आवाज़ रोकें</button>
</div>
<input id="cmd" placeholder="कमांड लिखो या सवाल पूछो…">
<div class="actions"><button id="send">कमांड भेजो</button><button class="alt" id="search">🌐 वेब पर खोजो</button></div>
<div id="reply">सिस्टम तैयार है। बोलो: “समय बताओ”, “तारीख बताओ”, “YouTube खोलो” या “Google खोलो”।</div>
<p class="note">नोट: यह ब्राउज़र-आधारित शुरुआती असिस्टेंट है। यह अभी ChatGPT जैसा AI मॉडल नहीं है। वेब सर्च Google में खुलेगी। असली AI जोड़ने के लिए सुरक्षित सर्वर की जरूरत होगी—API key को इस HTML फाइल में रखना सुरक्षित नहीं है।</p>
</main>
<script>
const out=document.getElementById('reply'), field=document.getElementById('cmd');
function say(t){out.textContent=t;if('speechSynthesis'in window){speechSynthesis.cancel();let u=new SpeechSynthesisUtterance(t);u.lang='hi-IN';speechSynthesis.speak(u)}}
function handle(raw){let q=raw.trim().toLowerCase();if(!q){say('पहले कोई कमांड बोलो या लिखो।');return}
if(q.includes('समय')||q.includes('time'))say('अभी समय है '+new Date().toLocaleTimeString('hi-IN',{hour:'numeric',minute:'2-digit'}));
else if(q.includes('तारीख')||q.includes('date'))say('आज '+new Date().toLocaleDateString('hi-IN')+' है।');
else if(q.includes('youtube')||q.includes('यूट्यूब')){say('YouTube खोल रहा हूँ।');window.open('https://www.youtube.com','_blank')}
else if(q.includes('google')||q.includes('गूगल')){say('Google खोल रहा हूँ।');window.open('https://www.google.com','_blank')}
else if(q.includes('नमस्ते')||q.includes('hello')||q.includes('हेलो'))say('नमस्ते! मैं तैयार हूँ।');
else if(q.includes('तुम कौन')||q.includes('who are you'))say('मैं Jarvis 2.0 का शुरुआती वॉइस असिस्टेंट हूँ।');
else say('इस सवाल का जवाब देने के लिए मुझे अभी AI सर्वर से जोड़ना बाकी है। चाहो तो वेब पर खोजो बटन दबाओ।')}
document.getElementById('send').onclick=()=>handle(field.value);
field.addEventListener('keydown',e=>{if(e.key==='Enter')handle(field.value)});
document.getElementById('search').onclick=()=>{let q=field.value.trim();if(!q){say('पहले खोजने के लिए कुछ लिखो।');return}say('Google पर खोज रहा हूँ: '+q);window.open('https://www.google.com/search?q='+encodeURIComponent(q),'_blank')};
document.getElementById('stop').onclick=()=>{if('speechSynthesis'in window)speechSynthesis.cancel();out.textContent='आवाज़ रोक दी गई।'};
document.getElementById('listen').onclick=()=>{let SR=window.SpeechRecognition||window.webkitSpeechRecognition;if(!SR){say('इस ब्राउज़र में वॉइस पहचान उपलब्ध नहीं है। Chrome में खोलो या कमांड लिखो।');return}
let r=new SR();r.lang='hi-IN';r.interimResults=false;r.maxAlternatives=1;r.onstart=()=>out.textContent='सुन रहा हूँ…';r.onresult=e=>{field.value=e.results[0][0].transcript;handle(field.value)};r.onerror=e=>say('माइक/वॉइस पहचान में समस्या: '+e.error+'. Chrome की साइट microphone permission और फोन का Microphone access जाँचो।');r.start()};
</script></body></html>

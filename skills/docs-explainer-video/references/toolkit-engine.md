# Toolkit: page shell and engine

The rendering core. `scene.html` is the page shell and `engine.js` holds motion helpers, the camera, the HUD, sound-cue recording and the boot sequence. Neither is edited per video; a video changes only `brand.js` and `scenes.js`.

Unpack with the script in `toolkit-scripts.md`.

<!-- file: template/scene.html -->
````html
<!doctype html>
<html><head><meta charset="utf-8">
<style>
:root{--bg:#0b0f14;--panel:#111821;--panel2:#18212b;--field:#0d131a;--line:#222d38;--strong:#33424f;--text:#e8edf2;--muted:#94a3b0;--idle:#64748b;--acc:#3b82f6;--accdim:#1e3a8a;--onacc:#020617;--acc2:#38bdf8;--amber:#f59e0b;--green:var(--acc);--red:#fb7185;--grid:#121a22;--grid-major:#1a2530;--font-sans:system-ui,sans-serif;--font-mono:ui-monospace,monospace;--font-label:ui-monospace,monospace}
*{box-sizing:border-box;margin:0;padding:0}
html,body{width:1280px;height:720px;overflow:hidden;background:var(--bg);font-family:var(--font-sans);color:var(--text)}
.mono{font-family:var(--font-mono)}
#bg{position:absolute;inset:-60px;background-image:linear-gradient(var(--grid-major) 1px,transparent 1px),linear-gradient(90deg,var(--grid-major) 1px,transparent 1px),linear-gradient(var(--grid) 1px,transparent 1px),linear-gradient(90deg,var(--grid) 1px,transparent 1px);background-size:200px 200px,200px 200px,40px 40px,40px 40px;opacity:1}
#glow{position:absolute;inset:0;background:radial-gradient(600px 400px at 50% 45%,color-mix(in srgb,var(--acc) 10%,transparent),transparent 70%)}
#cam{position:absolute;inset:0;transform-origin:50% 50%}
.abs{position:absolute}
.card{background:var(--panel);border:1px solid var(--line);border-radius:14px;box-shadow:0 20px 50px rgba(0,0,0,.45)}
.cap{position:absolute;top:34px;left:50%;transform-origin:50% 0}
.capin{display:flex;gap:12px;align-items:center;background:var(--panel2);border:1px solid var(--strong);border-radius:999px;padding:10px 22px 10px 12px;white-space:nowrap}
.num{background:var(--acc);color:var(--onacc);font-weight:800;border-radius:999px;padding:3px 10px;font-size:14px}
.capt{font-weight:700;font-size:20px}
.capsub{color:var(--muted);font-size:15px}
.lbl{font-family:var(--font-label);font-size:11px!important;font-size:12px;letter-spacing:.08em;text-transform:uppercase;color:var(--muted);margin-bottom:6px}
.field{background:var(--field);border:1px solid var(--line);border-radius:8px;padding:9px 12px;font-size:15px;height:38px;white-space:nowrap;overflow:hidden}
.pill{display:inline-block;border:1px solid var(--line);border-radius:999px;padding:5px 12px;font-size:13px;color:var(--muted);margin:0 6px 6px 0;background:var(--field)}
.pill.on{border-color:var(--acc);color:var(--acc);background:color-mix(in srgb,var(--acc) 10%,transparent)}
.btn{display:inline-block;border-radius:4px;padding:10px 18px;font-weight:700;font-size:15px}
.btn.pri{background:var(--acc);color:var(--onacc)}
.btn.sec{border:1px solid var(--line);color:var(--text)}
.ok{color:var(--green)}
.rail{position:absolute;bottom:26px;left:50%;transform:translateX(-50%);display:flex;gap:6px;align-items:center}
.rs{white-space:nowrap;display:flex;gap:8px;align-items:center;font-size:13px;color:var(--idle);padding:6px 12px;border-radius:999px;border:1px solid transparent}
.rs .d{width:18px;height:18px;border-radius:50%;border:1.5px solid var(--strong);font-size:11px;display:flex;align-items:center;justify-content:center;font-weight:700}
.rs.done{color:var(--muted)} .rs.done .d{background:color-mix(in srgb,var(--acc) 22%,var(--bg));border-color:var(--acc);color:var(--acc)}
.rs.cur{color:var(--text);border-color:var(--strong);background:var(--panel2)} .rs.cur .d{background:var(--acc);border-color:var(--acc);color:var(--onacc)}
.sep{width:22px;height:1px;background:var(--strong)}
.caret{display:inline-block;width:2px;height:1em;background:var(--acc);vertical-align:-2px;margin-left:1px}
.slot{display:inline-block;border-bottom:3px solid var(--acc);color:var(--acc);padding:0 4px;font-weight:700;min-width:40px;text-align:center}
.cell{border-radius:8px;border:1px solid var(--line);background:var(--field);font-size:11px;color:var(--idle);padding:7px 8px;height:52px;position:relative}
.cell.run{border-color:var(--acc2);color:var(--acc2)}
.cell.done{border-color:var(--accdim);background:color-mix(in srgb,var(--acc) 12%,var(--bg));color:color-mix(in srgb,var(--acc) 60%,#ffffff)}
.cell.skip{border-color:color-mix(in srgb,var(--amber) 45%,var(--bg));background:color-mix(in srgb,var(--amber) 15%,var(--bg));color:var(--amber)}
.cell b{display:block;font-size:13px;margin-bottom:2px}
.src{width:156px;height:112px;padding:14px;border-radius:12px;border:1px solid var(--line);background:var(--field)}
.src.on{border-color:var(--acc);background:color-mix(in srgb,var(--acc) 8%,transparent);box-shadow:0 0 0 3px color-mix(in srgb,var(--acc) 15%,transparent)}
.src b{display:block;font-size:15px;margin:10px 0 4px}
.src span{font-size:12px;color:var(--muted)}
.ico{width:30px;height:30px;border-radius:8px;background:var(--panel2);display:flex;align-items:center;justify-content:center;font-size:16px}
.tick{display:flex;gap:10px;align-items:center;font-size:14px;padding:5px 0;color:var(--muted)}
.tick .b{width:18px;height:18px;border-radius:50%;border:1.5px solid var(--strong);display:flex;align-items:center;justify-content:center;font-size:11px}
.tick.on{color:var(--text)} .tick.on .b{background:var(--green);border-color:var(--green);color:var(--onacc);font-weight:900}
.icn{display:inline-flex;align-items:center;justify-content:center;width:38px;height:38px;border-radius:8px;background:var(--field);border:1px solid var(--line);color:var(--acc)}
.sent{font-size:34px;line-height:2.05;font-weight:500}
.sent .w{display:inline-block}
.slotw{position:relative;display:inline-block;border-bottom:3px solid var(--acc);padding:0 6px;font-weight:700;color:var(--acc);line-height:1.25}
.slotw.pending{border-bottom-style:dashed;color:color-mix(in srgb,var(--acc) 55%,var(--bg))}
.dd{position:absolute;left:0;top:calc(100% + 10px);background:var(--panel2);border:1px solid var(--strong);border-radius:10px;padding:6px;min-width:240px;box-shadow:0 20px 40px rgba(0,0,0,.55);z-index:5;transform-origin:20px 0}
.dd div{font-size:17px;font-weight:500;color:var(--muted);padding:8px 12px;border-radius:6px;white-space:nowrap;line-height:1.2}
.dd div.hi{background:color-mix(in srgb,var(--acc) 14%,transparent);color:var(--text)}
.dd div.sel{background:var(--acc);color:var(--onacc);font-weight:700}
.node{position:absolute;width:200px;padding:14px 16px;border-radius:12px;background:var(--panel);border:1px solid var(--strong);box-shadow:0 20px 40px rgba(0,0,0,.45)}
.node .t{font-weight:700;font-size:16px;margin-top:10px}
.node .s{font-family:var(--font-mono);font-size:12px;color:var(--muted);margin-top:2px}
.node .g{font-family:var(--font-label);font-size:9px;letter-spacing:.08em;text-transform:uppercase;color:var(--acc);margin-top:8px}
</style></head>
<body>
<div id="bg"></div><div id="glow"></div>
<div id="cam"></div>
<div id="hud"></div>
<script src="brand.js"></script>
<script src="engine.js"></script>
<script src="scenes.js"></script>
<script>boot()</script>
</body></html>
````

<!-- file: template/engine.js -->
````js
/* Explainer-video engine: deterministic render(t), motion helpers, HUD, sound-cue recording.
   Reads BRAND (brand.js) and SPEC / FN / RAIL (scenes.js). Do not edit per video. */
const clamp=(x,a=0,b=1)=>Math.max(a,Math.min(b,x));
const lerp=(a,b,k)=>a+(b-a)*k;
const eOutBack=x=>{const c1=1.70158,c3=c1+1;return 1+c3*Math.pow(x-1,3)+c1*Math.pow(x-1,2)};
const eInOut=x=>x<.5?4*x*x*x:1-Math.pow(-2*x+2,3)/2;
const eOut=x=>1-Math.pow(1-x,3);

// ---------- sound-cue recording (same timeline drives picture and audio) ----------
let REC=null, OFF=0, K=1, SEND=1e9;
function rec(type,s,vol=1){if(REC&&s>=0){const g=OFF+s/K;if(g<SEND-.05)REC.push([type,+g.toFixed(3),vol]);}}
function cue(type,s,vol=1){rec(type,s,vol);return ''}

function pop(u,s,dy=18,dur=.45,snd='pop'){rec(snd,s,.6);const k=clamp((u-s)/dur);if(k<=0)return 'opacity:0;transform:scale(.6)';const e=eOutBack(k);return `opacity:${clamp(k*3)};transform:translateY(${(1-e)*dy}px) scale(${lerp(.6,1,e)})`}
function popq(u,s,dy=18,dur=.45){return pop(u,s,dy,dur,'none')}
function typed(txt,u,s,cps=30){if(REC)for(let i=1;i<=txt.length;i++)rec('key',s+i/cps,.5);if(u<s)return '';const n=Math.floor((u-s)*cps);const out=txt.slice(0,n);return out+(n<txt.length?'<span class="caret"></span>':'')}
function count(u,s,e,a,b){return Math.round(lerp(a,b,eOut(clamp((u-s)/(e-s)))))}

// ---------- line icons (no emoji) ----------
const IC={
 server:'<rect x="3" y="4" width="18" height="7" rx="2"/><rect x="3" y="13" width="18" height="7" rx="2"/><path d="M7 7.5h.01M7 16.5h.01"/>',
 app:'<path d="M12 3 3 8l9 5 9-5-9-5z"/><path d="m3 12 9 5 9-5"/><path d="m3 16 9 5 9-5"/>',
 key:'<circle cx="8" cy="15" r="4"/><path d="M11 12 20 3M17 6l3 3M15 8l2 2"/>',
 lock:'<rect x="5" y="11" width="14" height="10" rx="2"/><path d="M8 11V7a4 4 0 0 1 8 0v4"/>',
 git:'<circle cx="6" cy="6" r="2.5"/><circle cx="6" cy="18" r="2.5"/><circle cx="18" cy="8" r="2.5"/><path d="M6 8.5v7M18 10.5c0 4-6 3.5-10.4 6"/>',
 check:'<circle cx="12" cy="12" r="9"/><path d="m8 12.5 2.7 2.7L16 9.5"/>',
 shield:'<path d="M12 3l7 3v6c0 4.5-3 7.5-7 9-4-1.5-7-4.5-7-9V6z"/>',
 shieldok:'<path d="M12 3l7 3v6c0 4.5-3 7.5-7 9-4-1.5-7-4.5-7-9V6z"/><path d="m9 12 2 2 4-4"/>',
 alert:'<path d="M12 4 2.5 20h19z"/><path d="M12 10v4M12 17h.01"/>',
 pause:'<circle cx="12" cy="12" r="9"/><path d="M10 9v6M14 9v6"/>',
 refresh:'<path d="M20 11a8 8 0 1 0-2.3 5.7"/><path d="M20 4v7h-7"/>',
 globe:'<circle cx="12" cy="12" r="9"/><path d="M3 12h18M12 3c3 3.5 3 14.5 0 18-3-3.5-3-14.5 0-18"/>',
 file:'<path d="M6 3h8l4 4v14H6z"/><path d="M14 3v4h4M9 12h6M9 16h6"/>',
 pulse:'<path d="M3 12h4l3-7 4 14 3-7h4"/>',
 db:'<ellipse cx="12" cy="6" rx="7" ry="3"/><path d="M5 6v12c0 1.7 3.1 3 7 3s7-1.3 7-3V6"/><path d="M5 12c0 1.7 3.1 3 7 3s7-1.3 7-3"/>',
 bolt:'<path d="M13 2 4 14h7l-1 8 9-12h-7z"/>',
 bucket:'<path d="M3 6h18"/><path d="M5 6l1.5 14h11L19 6"/><path d="M9 10h6"/>',
 entry:'<path d="M4 12h12"/><path d="m12 7 5 5-5 5"/><path d="M20 4v16"/>',
 users:'<circle cx="9" cy="8" r="3.5"/><path d="M3 20c0-3.3 2.7-6 6-6s6 2.7 6 6"/><circle cx="17" cy="9" r="2.5"/><path d="M16 14.5c2.8 0 5 2.2 5 5"/>',
};
const ic=(n,sz=20,col='currentColor',sw=1.8)=>`<svg width="${sz}" height="${sz}" viewBox="0 0 24 24" fill="none" stroke="${col}" stroke-width="${sw}" stroke-linecap="round" stroke-linejoin="round" style="vertical-align:middle">${IC[n]}</svg>`;

const bz=(p0,p1,p2,p3,t)=>{const m=1-t;return [m*m*m*p0[0]+3*m*m*t*p1[0]+3*m*t*t*p2[0]+t*t*t*p3[0],m*m*m*p0[1]+3*m*m*t*p1[1]+3*m*t*t*p2[1]+t*t*t*p3[1]]};

// ---------- brand logo ----------
// Mode A (best): BRAND.logo.icon (filled mark) + BRAND.logo.word (stroke paths) -> icon pops, wordmark draws itself.
// Mode B (any logo): BRAND.logo.svg (complete <svg>) -> pops in and wipes left-to-right.
function logo(h,draw=1,iconK=1){const L=BRAND.logo;const e=eOutBack(clamp(iconK));
 if(L.word&&L.word.length){return `<svg viewBox="${L.viewBox}" height="${h}" style="overflow:visible" fill="none"><g style="transform-origin:${L.iconOrigin||'24px 24px'};transform:scale(${lerp(.3,1,e)}) rotate(${(1-e)*-25}deg);opacity:${clamp(iconK*3)}">${L.icon||''}</g><g style="stroke:var(--text)" stroke-width="${L.strokeWidth||4}" stroke-linecap="${L.linecap||'butt'}" stroke-linejoin="${L.linejoin||'miter'}">${L.word.map((d,i)=>{const k=clamp(draw*L.word.length-i);return `<path d="${d}" pathLength="1" stroke-dasharray="1" stroke-dashoffset="${1-eInOut(k)}" opacity="${k>0?1:0}"/>`}).join('')}</g></svg>`}
 const w=clamp(Math.max(draw,iconK>0?.02:0));return `<span style="display:inline-block;height:${h}px;transform:scale(${lerp(.85,1,e)});opacity:${clamp(iconK*3)};clip-path:inset(0 ${(1-eInOut(w))*100}% 0 0)">${L.svg.replace('<svg',`<svg height="${h}" style="height:${h}px;width:auto"`)}</span>`}

function wrap(t,sc,html){if(t<sc.s||t>=sc.e)return '';const k=sc.e>=DUR?0:clamp((t-(sc.e-.35))/.35);const e=eInOut(k);return `<div class="abs" style="inset:0;opacity:${1-e};transform:translateY(${e*30}px) scale(${1-e*.08})">${html}</div>`}
function caption(u,c){const k=clamp(u/.5);const e=eOutBack(k);return `<div class="cap" style="opacity:${clamp(k*3)};transform:translateX(-50%) translateY(${(1-e)*-50}px)"><div class="capin"><span class="num">${c[0]}</span><span class="capt">${c[1]}</span><span class="capsub">· ${c[2]}</span></div></div>`}
function rail(t){const cur=SC.find(s=>t>=s.s&&t<s.e)||SC[SC.length-1];if(cur.rail<0||cur.rail>=RAIL.length)return '';const k=clamp((t-SC[1].s)/.5);const names=RAIL;
 return `<div class="rail" style="opacity:${k};transform:translateX(-50%) translateY(${(1-eOut(k))*30}px)">${names.map((n,i)=>`${i?'<span class="sep"></span>':''}<span class="rs ${i<cur.rail?'done':i===cur.rail?'cur':''}"><span class="d">${i<cur.rail?'✓':i+1}</span>${n}</span>`).join('')}</div>`}

function render(t){
 let sc=1.015+.012*Math.sin(t*.31);
 for(const s of SC){const d=t-s.s;if(d>=0&&d<.8)sc+=.035*(1-eOut(d/.8));if(s.reveal&&d>=0&&d<1.6)sc*=lerp(1.3,1,eInOut(clamp(d/1.6)));}
 const tx=10*Math.sin(t*.35),ty=6*Math.sin(t*.27+1),rot=.35*Math.sin(t*.21);
 document.getElementById('cam').style.transform=`translate(${tx}px,${ty}px) rotate(${rot}deg) scale(${sc})`;
 const bg=document.getElementById('bg');bg.style.transform=`translate(${-tx*.5-(t*6)%40}px,${-ty*.5}px)`;bg.style.opacity=clamp(t/1);
 let html='';SC.forEach((s,i)=>{if(t>=s.s&&t<s.e){OFF=s.s;K=s.k;SEND=s.e;html+=wrap(t,s,FN[i]((t-s.s)*s.k));}});
 document.getElementById('cam').innerHTML=html;
 const cur=SC.find(s=>t>=s.s&&t<s.e);
 const last=SC[SC.length-1].s;
 document.getElementById('hud').innerHTML=(t>=SC[1].s&&t<last?`<div class="abs" style="left:36px;top:40px;opacity:${clamp((t-SC[1].s)/.5)*(1-clamp((t-(last-.35))/.35))}">${logo(22)}</div>`:'')+(cur&&cur.cap?wrap(t,cur,caption(t-cur.s,cur.cap)):'')+rail(t);
}
// one silent pass over every frame collects the sound cues
function collectCues(fps){REC=[];for(const s of SC){if(s.s>0&&s.cap)REC.push(['whoosh',s.s,.8]);}
 for(let i=0;i<Math.round(DUR*fps);i++)render(i/fps);const out=REC;REC=null;
 const seen=new Map();out.sort((a,b)=>a[1]-b[1]);const res=[];
 for(const c of out){const key=c[0]+'@'+Math.round(c[1]*100);if(seen.has(key))continue;seen.set(key,1);res.push(c)}return res}


// ---------- boot ----------
let SC=[],DUR=0;
function boot(){
 // fonts + colour tokens from brand.js
 const st=document.createElement('style');st.textContent=(BRAND.fonts||[]).map(f=>`@font-face{font-family:'${f.family}';font-weight:${f.weight||400};src:url(${f.file})}`).join('');document.head.appendChild(st);
 const r=document.documentElement.style;for(const [k,v] of Object.entries(BRAND.colors))r.setProperty('--'+k,v);
 for(const [k,v] of Object.entries(BRAND.fontStacks||{}))r.setProperty('--font-'+k,v);
 let acc=0;SC=SPEC.map(x=>{const o={k:1,...x,s:acc,e:acc+x.d};acc+=x.d;return o});DUR=acc;
 window.DUR=DUR;window.render=render;window.collectCues=collectCues;
 render(parseFloat(new URLSearchParams(location.search).get('t')||'5'));
}
````

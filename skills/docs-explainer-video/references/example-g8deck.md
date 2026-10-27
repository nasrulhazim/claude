# Example: G8Deck "Your first deployment"

A complete, approved 60s video built from https://g8deck.com/docs/first-deployment/. Use its `scenes.js` as the starting point for any new video: copy a scene function, then change the text and data.

| # | Time | Scene | Pattern |
|---|---|---|---|
| 0 | 0–4.5 | Logo draws, title types, empty host → running app | Title |
| 1 | 4.5–12 | Connect a server — wizard, test connection, setup log 0→16 | Wizard form |
| 2 | 12–18 | What are you deploying? — Git only | Single-path form |
| 3 | 18–28.5 | What does it need? — sentence word by word, picker per choice | Sentence builder |
| 4 | 28.5–36 | How that shape runs — entry, app, DB, cache, storage | Topology diagram |
| 5 | 36–42 | Where should it run? — review, guards, Save & deploy | Review + guards |
| 6 | 42–49 | Watch the pipeline — 24 steps, 2 skipped (music lift) | Pipeline |
| 7 | 49–56 | It's live — billing.acme.my renders, post-checks tick | It's live |
| 8 | 56–60 | End card — logo, tagline, g8deck.com → | End card |

It is narrated: `narration.json` holds 13 timed lines, and the brand's `say` map speaks G8Deck as "Gate Deck".

Fonts for this brand: `fetch_fonts.sh <project>/fonts archivo:400,500,600,700,800 ibm-plex-mono:400,500,600 martian-mono:400,500`.

<!-- file: examples/g8deck-first-deployment/brand.js -->
````js
/* Brand tokens for one product. Fill from the live site (logo SVG, CSS colour variables, fonts). */
const BRAND={
 name:'G8Deck',
 site:'https://g8deck.com',
 tagline:'Deploy on infrastructure you own.',
 cta:'g8deck.com →',
 footer:'A G8Suite product · Developers Hub',
 logo:{                      // Mode A: filled icon + stroke wordmark that draws itself
  viewBox:'0 0 213 48', iconOrigin:'24px 24px', strokeWidth:4.5,
  icon:'<path fill="var(--acc)" fill-rule="evenodd" d="M16 6 h16 a10 10 0 0 1 10 10 v4 h-4 v6 h4 v6 a10 10 0 0 1 -10 10 h-16 a10 10 0 0 1 -10 -10 v-6 h4 v-6 h-4 v-4 a10 10 0 0 1 10 -10 z M16 12 H32 A4 4 0 0 1 36 16 V18 A2 2 0 0 1 34 20 H14 A2 2 0 0 1 12 18 V16 A4 4 0 0 1 16 12 Z M14 26 H34 A2 2 0 0 1 36 28 V32 A4 4 0 0 1 32 36 H16 A4 4 0 0 1 12 32 V28 A2 2 0 0 1 14 26 Z"/>',
  word:['M70 25 H80 A10 10 0 0 1 70 35 A10 10 0 0 1 60 25 V23 A10 10 0 0 1 70 13 A10 10 0 0 1 78 17','M95 13 h4 a5 5 0 0 1 5 5 v1 a5 5 0 0 1 -5 5 h-4 a5 5 0 0 1 -5 -5 v-1 a5 5 0 0 1 5 -5 z M93 24 h8 a5 5 0 0 1 5 5 v1 a5 5 0 0 1 -5 5 h-8 a5 5 0 0 1 -5 -5 v-1 a5 5 0 0 1 5 -5 z','M115 35 V13 H124 A10 10 0 0 1 134 23 V25 A10 10 0 0 1 124 35 Z','M142 27 H159 V26.5 A7.5 7.5 0 0 0 151.5 19 H149.5 A7.5 7.5 0 0 0 142 26.5 V27.5 A7.5 7.5 0 0 0 149.5 35 H151.5 A7.5 7.5 0 0 0 156 33.5','M181.5 22 A7.5 7.5 0 0 0 175.5 19 H174.5 A7.5 7.5 0 0 0 167 26.5 V27.5 A7.5 7.5 0 0 0 174.5 35 H175.5 A7.5 7.5 0 0 0 181.5 32','M191 13 V35 M191 28 L203 19 M195 25 L205 35'],
  // Mode B alternative: svg:'<svg viewBox=...>...</svg>'  (pops + wipes in)
 },
 voice:{                     // narration (Kokoro TTS): voice id, speed, language, and how written words are spoken
  voice:'af_heart', speed:1.0, lang:'en-us',
  say:{ 'G8Deck':'Gate Deck' },   // confirmed pronunciation — add other product names only once confirmed
 },
 colors:{                    // from g8deck.com :root --color-* variables
  bg:'#080c10', panel:'#0e141a', panel2:'#151d25', field:'#0a1016', line:'#1f2a33', strong:'#2e3d49',
  text:'#e6edf3', muted:'#8fa3b0', idle:'#64748b',
  acc:'#10b981', accdim:'#065f46', onacc:'#04120c', acc2:'#38bdf8', amber:'#f59e0b', red:'#fb7185',
  grid:'#111a21', 'grid-major':'#18242e'
 },
 fontStacks:{ sans:"'Archivo',sans-serif", mono:"'IBM Plex Mono',monospace", label:"'Martian Mono',monospace" },
 fonts:[
  ...[400,500,600,700,800].map(w=>({family:'Archivo',weight:w,file:`fonts/archivo-latin-${w}-normal.woff2`})),
  ...[400,500,600].map(w=>({family:'IBM Plex Mono',weight:w,file:`fonts/ibm-plex-mono-latin-${w}-normal.woff2`})),
  ...[400,500].map(w=>({family:'Martian Mono',weight:w,file:`fonts/martian-mono-latin-${w}-normal.woff2`})),
 ],
};
````

<!-- file: examples/g8deck-first-deployment/scenes.js -->
````js
/* Scenes for one video. Each scene is fn(u) -> HTML for local time u (seconds).
   Helpers: pop/popq/typed/count/cue/ic/logo/bz. SPEC = timeline; RAIL = progress-rail labels; FN = scene functions in SPEC order. */

const RAIL=['Sign in','Connect a server','Create the app','Watch the pipeline','Check it'];

const SPEC=[
 {d:4.5,k:1,   cap:null,rail:-1},
 {d:7.5,k:.88, cap:['2','Connect a server','Servers → Connect a Server'],rail:1},
 {d:6,  k:1,   cap:['3a','What are you deploying?','Applications → New application'],rail:2},
 {d:10.5,k:1,  cap:['3b','What does it need?','Build the sentence, one choice at a time'],rail:2},
 {d:7.5,k:1,   cap:['3b','How that shape runs','Each box is its own node'],rail:2,reveal:true},
 {d:6,  k:.9,  cap:['3c','Where should it run?','Review, then Save & deploy'],rail:2},
 {d:7,  k:.9,  cap:['4','Watch the pipeline','24 idempotent steps'],rail:3,reveal:true,lift:true},
 {d:7,  k:1,   cap:['5','It’s live','billing.acme.my is serving traffic'],rail:4},
 {d:4,  k:1,   cap:null,rail:5},
];

// ---------- scenes ----------
function s0(u){
 return `${cue('logo',.05)}
 <div class="abs" style="left:0;right:0;top:130px;text-align:center">
  <div style="height:44px">${logo(40,clamp((u-.1)/.9),clamp((u-.05)/.45))}</div>
  <div style="${popq(u,.9)};display:inline-block;margin-top:14px" class="lbl">Docs · Getting started</div>
  <div style="font-size:64px;font-weight:800;letter-spacing:-.02em;margin-top:6px;height:80px">${typed('Your first deployment',u,1.1,24)}</div>
  <div style="${pop(u,2.2)};font-size:20px;color:var(--muted);margin-top:8px">Connect a machine you own → answer three questions → watch it run</div>
 </div>
 <div class="abs" style="left:330px;top:420px;${pop(u,2.7)}"><div class="card" style="width:180px;padding:16px;text-align:center"><span class="icn">${ic('server',22)}</span><div style="font-weight:700;margin-top:10px">Empty host</div><div class="mono" style="font-size:12px;color:var(--muted)">Ubuntu · Debian · Alma · Rocky</div></div></div>
 ${cue('whoosh',3.0,.5)}<svg class="abs" style="left:520px;top:478px" width="250" height="20"><line x1="0" y1="10" x2="${250*eInOut(clamp((u-3.0)/.8))}" y2="10" style="stroke:var(--acc)" stroke-width="2" stroke-dasharray="6 6"/></svg>
 <div class="abs" style="left:780px;top:420px;${pop(u,3.6,18,.45,'chime')}"><div class="card" style="width:180px;padding:16px;text-align:center;border-color:var(--accdim)"><span class="icn">${ic('app',22)}</span><div style="font-weight:700;margin-top:10px">Running app</div><div class="mono" style="font-size:12px;color:var(--green)">● active</div></div></div>
 <div class="abs" style="left:0;right:0;top:600px;text-align:center;${pop(u,3.9)}"><span class="pill on mono">≈ ${count(u,3.9,4.6,0,15)} minutes</span><span class="pill mono">console: g8deck.app</span></div>`;
}

function s1(u){
 const step=u<2.4?0:u<3.4?1:2;
 const prov=['DigitalOcean','AWS','Google Cloud','Azure','Hetzner','Other'];
 const steps=['Management user','Rotate SSH credentials','Base packages','Container runtime','G8Deck agent','Firewall baseline','Metrics collector'];
 const n=count(u,3.7,6.2,0,16);
 const shown=Math.min(7,Math.floor(clamp((u-3.7)/2.5)*7.99));
 for(let i=0;i<7;i++)rec('tick',3.7+(i+1)*2.5/7.99,.7);
 const pressed=u>2.6&&u<2.85;
 return `${cue('click',2.6)}${cue('success',2.9,.8)}${cue('stamp',3.4,.7)}${cue('chime',6.2,.8)}
 <div class="abs card" style="left:120px;top:110px;width:560px;padding:24px;${pop(u,.2,30)}">
  <div style="display:flex;gap:8px;margin-bottom:18px">${['Connection','Reach it','Pre-flight & start'].map((x,i)=>`<span class="pill ${i<=step?'on':''}">${i+1}. ${x}</span>`).join('')}</div>
  <div class="lbl">Server name</div><div class="field mono">${typed('app-01',u,.5,14)}</div>
  <div class="lbl" style="margin-top:14px">Hosted at — all reached over SSH</div>
  <div>${prov.map((p,i)=>`<span class="pill ${u>1.3&&i===0?'on':''}" style="${popq(u,.8+i*.06,8)};display:inline-block">${p}</span>`).join('')}</div>
  <div style="display:flex;gap:10px;margin-top:6px"><div style="flex:3"><div class="lbl">Address</div><div class="field mono">${typed('203.0.113.10',u,1.4,22)}</div></div><div style="flex:1"><div class="lbl">Port</div><div class="field mono">${typed('22',u,1.95,10)}</div></div><div style="flex:2"><div class="lbl">Auth</div><div class="field" style="color:var(--acc)">${u>2.1?ic('key',16)+' <span style="color:var(--text)">SSH key</span>':''}</div></div></div>
  <div style="margin-top:16px;display:flex;gap:14px;align-items:center;height:42px">
   <span class="btn sec" style="${u>2.3?popq(u,2.3,6):'opacity:0'};${pressed?'background:color-mix(in srgb,var(--acc) 18%,var(--bg));transform:scale(.95)':''}">Test connection</span>
   <span class="mono ok" style="${popq(u,2.9,6)};font-size:14px">✓ Ubuntu 24.04 LTS</span>
  </div>
  <div class="mono" style="${popq(u,3.4,10)};margin-top:12px;font-size:13px;color:var(--muted);border:1px dashed var(--strong);border-radius:8px;padding:9px 12px"><span style="color:var(--acc)">${ic('lock',15)}</span> host key pinned · SHA256:9fKq…Xw2c &nbsp; <span class="btn pri" style="padding:4px 10px;font-size:12px;margin-left:8px">Start setup</span></div>
 </div>
 <div class="abs card" style="left:720px;top:150px;width:440px;padding:22px;${pop(u,3.5,30)}">
  <div style="display:flex;justify-content:space-between;align-items:center"><div style="font-weight:700">Setup log</div><div class="mono" style="color:${n>=16?'var(--green)':'var(--acc2)'}">${n}/16</div></div>
  <div style="height:6px;background:var(--panel2);border-radius:9px;margin:12px 0 10px"><div style="height:6px;border-radius:9px;background:var(--acc);width:${n/16*100}%"></div></div>
  ${steps.map((x,i)=>`<div class="tick ${i<shown?'on':''}"><span class="b">${i<shown?'✓':''}</span>${x}</div>`).join('')}
  <div class="tick" style="color:var(--idle)">…and 9 more</div>
 </div>
 <div class="abs" style="left:720px;top:560px;width:440px;${popq(u,4.6,10)};font-size:14px;color:var(--muted)">Password? Used <b style="color:var(--text)">once</b> — setup installs a per-server key and disables password login.</div>`;
}

function s2(u){
 const presets=['Dockerfile','static','laravel','node','nextjs','astro','python','django','symfony','go'];
 const hp=u<2.9?-1:u<4.0?Math.floor((u-2.9)*7)%presets.length:2;
 if(REC){for(let j=0;j<8;j++)rec('step',2.9+j/7,.5);}
 return `${cue('select',4.0)}${cue('success',5.0,.7)}
 <div class="abs card" style="left:190px;top:120px;width:900px;padding:28px 30px;${pop(u,.2,30)}">
  <div style="display:flex;gap:16px;align-items:end">
   <div style="flex:1"><div class="lbl">Application name</div><div class="field">${typed('billing-portal',u,.5,20)}</div></div>
   <div style="flex:1"><div class="lbl">Source</div><div class="field" style="border-color:var(--acc);background:color-mix(in srgb,var(--acc) 8%,transparent);display:flex;gap:10px;align-items:center;${popq(u,1.2,8)}"><span style="color:var(--acc)">${ic('git',18)}</span><b>Git repository</b><span style="color:var(--muted);font-size:13px">— we build it</span></div></div>
  </div>
  <div style="display:flex;gap:16px;margin-top:18px">
   <div style="flex:3"><div class="lbl">Repository</div><div class="field mono">${typed('git@github.com:acme/billing.git',u,1.6,30)}</div></div>
   <div style="flex:1"><div class="lbl">Branch</div><div class="field mono">${typed('main',u,2.6,10)}</div></div>
  </div>
  <div class="lbl" style="margin-top:18px">Build — Dockerfile, or one of nine presets</div>
  <div>${presets.map((p,i)=>`<span class="pill mono ${i===hp?'on':''}" style="${i===hp&&u>4.0?'transform:scale(1.12);display:inline-block':''}">${p}</span>`).join('')}</div>
  <div style="display:flex;gap:16px;margin-top:10px;align-items:end">
   <div style="flex:1"><div class="lbl">Domain (optional)</div><div class="field mono">${typed('billing.acme.my',u,4.2,20)}</div></div>
   <div style="flex:1;${popq(u,5.0,6)};font-size:14px;display:flex;gap:8px;align-items:center;height:38px" class="ok">${ic('check',18)} DNS checked — G8Deck will create the record</div>
  </div>
 </div>`;
}

// ---- sentence built word by word; each choice is made in place ----
const TOK=['Run',{o:['PHP-FPM','Node.js','Python'],p:0},'behind','Nginx,','with',{o:['MySQL 8','MariaDB 11','PostgreSQL 16','no database'],p:2},'and',{o:['Redis','no cache'],p:0},',','plus',{o:['background workers','no workers'],p:1},'.','Run','it','on',{o:['one server','an app server and a database server','an entry point, an app server and a database server'],p:2},'.'];
const SCHED=(()=>{let c=.6;return TOK.map(tk=>{if(typeof tk==='string'){const r={s:c};c+=/^[,.]$/.test(tk)?.06:.14;return r}const steps=tk.o.length+tk.p;const r={s:c,sel:c+.35+steps*.17};r.close=r.sel+.4;r.steps=steps;c=r.close+.08;return r})})();
const SENT_DONE=SCHED[SCHED.length-1].s+.1;
function s3(u){
 let html='';
 TOK.forEach((tk,i)=>{const sc=SCHED[i];
  if(typeof tk==='string'){rec('word',sc.s,.45);if(u<sc.s)return;const k=clamp((u-sc.s)/.3),e=eOutBack(k);html+=`${/^[,.]$/.test(tk)?'':' '}<span class="w" style="opacity:${clamp(k*3)};transform:translateY(${(1-e)*14}px)">${tk}</span>`;return}
  rec('open',sc.s,.6);for(let j=1;j<=sc.steps;j++)rec('step',sc.s+.35+j*.17-.17,.55);rec('select',sc.sel,1);
  if(u<sc.s)return;
  const chosen=u>=sc.sel;
  const hi=chosen?tk.p:Math.min(tk.o.length-1,Math.max(0,Math.floor((u-sc.s-.35)/.17))%tk.o.length);
  const land=chosen?eOutBack(clamp((u-sc.sel)/.35)):0;
  const ddK=u<sc.close?eOutBack(clamp((u-sc.s)/.3)):1-clamp((u-sc.close)/.2);
  const dd=ddK>0.01&&u<sc.close+.2?`<span class="dd" style="opacity:${clamp(ddK)};transform:scale(${lerp(.85,1,clamp(ddK))})">${tk.o.map((o,j)=>`<div class="${j===hi?(chosen?'sel':'hi'):''}">${o}</div>`).join('')}</span>`:'';
  const label=chosen?tk.o[tk.p]:'&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;';
  html+=` <span class="slotw ${chosen?'':'pending'}"><span style="display:inline-block;transform:scale(${chosen?lerp(1.25,1,land):1})">${label}</span>${dd}</span>`;
 });
 const glow=clamp((u-SENT_DONE)/.4);
 return `${cue('chime',SENT_DONE,.9)}
 <div class="abs card" style="left:120px;top:140px;width:1040px;min-height:260px;padding:34px 44px;${popq(u,.1,30)};${glow?`border-color:rgba(16,185,129,${.3+glow*.5});box-shadow:0 0 0 ${glow*4}px color-mix(in srgb,var(--acc) 12%,transparent),0 20px 50px rgba(0,0,0,.45)`:''}">
  <div class="lbl">The shape — read it back to yourself</div>
  <div class="sent">${html}</div>
 </div>
 <div class="abs" style="left:120px;top:440px;width:1040px;${popq(u,SENT_DONE+.3,10)};font-size:16px;color:var(--muted)">Every underlined word stays editable. Starting points — <i>Small app, Standard web app, API + cache, Room to scale</i> — just pre-fill the sentence.</div>`;
}

// ---- topology diagram ----
const NODES={
 users:{x:40,y:300,w:130,i:'users',t:'Visitors',s:'HTTPS'},
 entry:{x:240,y:290,w:200,i:'entry',t:'Entry point',s:'Nginx · TLS',g:'node · agent'},
 app:{x:530,y:290,w:200,i:'app',t:'App server',s:'PHP-FPM · laravel',g:'node · agent'},
 db:{x:900,y:120,w:220,i:'db',t:'Database server',s:'PostgreSQL 16',g:'node · agent · backups'},
 cache:{x:900,y:290,w:220,i:'bolt',t:'Cache node',s:'Redis',g:'node · agent'},
 store:{x:900,y:460,w:220,i:'bucket',t:'Object storage',s:'S3-compatible bucket',g:'uploads · backups'},
};
const CY={users:346,entry:346,app:346,db:176,cache:346,store:516};
const EDGES=[
 {a:[170,346],b:[240,346],c1:[205,346],c2:[205,346],lab:'',t0:1.1},
 {a:[440,346],b:[530,346],c1:[485,346],c2:[485,346],lab:'proxy',t0:1.4},
 {a:[730,346],b:[900,176],c1:[820,346],c2:[810,176],lab:'SQL :5432',t0:2.0},
 {a:[730,346],b:[900,346],c1:[815,346],c2:[815,346],lab:'RESP :6379',t0:2.15},
 {a:[730,346],b:[900,516],c1:[820,346],c2:[810,516],lab:'S3 API',t0:2.3},
];
function s4(u){
 const order=['users','entry','app','db','cache','store'],starts=[.3,.55,.8,1.2,1.35,1.5];
 let nodes='';order.forEach((id,i)=>{const n=NODES[id];const hot=id==='app';nodes+=`<div class="node" style="left:${n.x}px;top:${n.y}px;width:${n.w}px;${pop(u,starts[i],24)};${hot?'border-color:var(--acc)':''}"><span class="icn" style="${id==='users'?'color:var(--muted)':''}">${ic(n.i,20)}</span><div class="t">${n.t}</div><div class="s">${n.s}</div>${n.g?`<div class="g">${n.g}</div>`:''}</div>`});
 let svg='';EDGES.forEach((E,i)=>{rec('draw',E.t0,.5);const k=eInOut(clamp((u-E.t0)/.5));const d=`M${E.a} C${E.c1} ${E.c2} ${E.b}`;svg+=`<path d="${d}" pathLength="1" style="stroke:var(--strong)" stroke-width="2" fill="none" stroke-dasharray="1" stroke-dashoffset="${1-k}"/>`;
  if(E.lab&&k>.6){const m=bz(E.a,E.c1,E.c2,E.b,.55);svg+=`<text x="${m[0]}" y="${m[1]-10}" text-anchor="middle" style="font-family:var(--font-mono)" font-size="11"  opacity="${clamp((k-.6)/.4)}">${E.lab}</text>`}
  // request packets flow along each wire once it is drawn
  if(u>2.9){const per=1.6;for(let r=0;r<2;r++){const ph=((u-2.9)/per+i*.12+r*.5)%1;const back=r===1&&i>1;const p=bz(E.a,E.c1,E.c2,E.b,back?1-ph:ph);svg+=`<circle cx="${p[0]}" cy="${p[1]}" r="${back?3:4}" style="fill:${back?'var(--acc2)':'var(--acc)'}" opacity="${Math.sin(ph*Math.PI)}"/>`}}
 });
 for(let j=0;j<4;j++)rec('blip',2.9+j*.8,.35);
 return `<svg class="abs" style="left:0;top:0" width="1280" height="720">${svg}</svg>${nodes}
 <div class="abs" style="left:0;right:0;top:600px;text-align:center;${popq(u,3.4,10)};font-size:15px;color:var(--muted)"><span class="mono" style="color:var(--acc)">●</span> request &nbsp; <span class="mono" style="color:var(--acc2)">●</span> response &nbsp;·&nbsp; every server node runs the G8Deck agent; the pipeline wires them together</div>`;
}

function s5(u){
 const rows=[['Server','app-01','preselected'],['Database','db-01','from the shape'],['Object storage','store-01','picked'],['Environment','production','default'],['Size','Small','']];
 const cx=lerp(900,585,eInOut(clamp((u-3.6)/.7)));
 const cy=lerp(600,420,eInOut(clamp((u-3.6)/.7)));
 const click=u>4.35;
 const rip=clamp((u-4.35)/.5);
 return `${cue('click',4.35)}${cue('success',3.1,.6)}${cue('success',3.25,.6)}
 <div class="abs card" style="left:120px;top:110px;width:560px;padding:24px;${pop(u,.2,30)}">
  <div class="lbl">Review</div>
  ${rows.map((r,i)=>`<div style="display:flex;justify-content:space-between;padding:11px 0;border-bottom:1px solid var(--line);${popq(u,.4+i*.12,10)}"><span style="color:var(--muted)">${r[0]}</span><span><b>${r[1]}</b> <span style="color:var(--idle);font-size:13px">${r[2]}</span></span></div>`).join('')}
  <div style="display:flex;gap:12px;justify-content:flex-end;margin-top:20px;${popq(u,1.3,10)}">
   <span class="btn sec">Save only</span>
   <span class="btn pri" style="position:relative;${click&&u<4.6?'transform:scale(.94);display:inline-block':''}">Save &amp; deploy
    ${click?`<span style="position:absolute;left:50%;top:50%;width:${rip*160}px;height:${rip*160}px;border-radius:50%;border:2px solid var(--acc);opacity:${1-rip};transform:translate(-50%,-50%)"></span>`:''}</span>
  </div>
 </div>
 <div class="abs" style="left:730px;top:130px;width:430px">
  <div class="lbl" style="${popq(u,1.6,6)}">Two guards fire here — not halfway through</div>
  ${['Needs a database (Laravel / Symfony / Django) but none in the shape','Container workload aimed at a systemd-native server'].map((g,i)=>{const ok=u>3.1+i*.15;return `<div class="card" style="padding:16px 18px;margin-top:12px;display:flex;gap:14px;${pop(u,1.9+i*.4,20)};${ok?'border-color:var(--accdim)':''}"><div style="color:${ok?'var(--acc)':'var(--muted)'}">${ic(ok?'shieldok':'shield',26)}</div><div style="font-size:15px">${g}<div style="font-size:13px;margin-top:4px;color:${ok?'var(--green)':'var(--muted)'}">${ok?'passed':'checking…'}</div></div></div>`}).join('')}
  <div class="card" style="padding:14px 18px;margin-top:22px;${popq(u,2.8,16)};border-style:dashed;font-size:14px;color:var(--muted)">Private SSH repo refuses the clone? A <b style="color:var(--text)">Connect</b> step shows a deploy key → add it read-only → Test connection → Deploy.</div>
 </div>
 <svg class="abs" style="left:${cx}px;top:${cy}px;${u<3.4?'opacity:0':''}" width="28" height="28"><path d="M3 2 L3 22 L9 16 L13 25 L17 23 L13 15 L21 15 Z" style="fill:var(--text);stroke:var(--bg)" stroke-width="1.5"/></svg>`;
}

function s6(u){
 const names=['plan','lock','node','network','firewall','runtime','image','db server','database','cache','storage','secrets','env','build','release','migrate','proxy','tls','dns','workers','scheduler','health','metrics','finalize'];
 const skip=new Set([10,19]);
 const prog=clamp((u-.8)/3.4)*24;
 const done=Math.floor(prog);
 const fin=u>4.3;
 const active=u>5.3;
 for(let i=0;i<24;i++)rec(skip.has(i)?'warn':'tick',.8+(i+1)*3.4/24,skip.has(i)?.9:.5);
 const cells=names.map((n,i)=>{let c='';let lab='';if(i<done){c=skip.has(i)?'skip':'done';lab=skip.has(i)?'skipped':'✓ done'}else if(i===done&&!fin){c='run';lab='running…'}return `<div class="cell ${c}" style="${popq(u,.3+i*.025,12,.35)}"><b class="mono">${String(i+1).padStart(2,'0')}</b>${n}<span style="position:absolute;right:8px;top:7px;font-size:10px">${lab}</span></div>`}).join('');
 return `${cue('stamp',5.3,1)}${cue('chime',5.35,.9)}
 <div class="abs card" style="left:120px;top:105px;width:1040px;padding:22px 24px">
  <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:16px">
   <div style="font-weight:700;font-size:18px">billing-portal · production</div>
   <div class="mono" style="font-size:14px">${fin?`<span>24 steps</span> <span style="color:var(--amber)">— 2 did no work</span>`:`<span style="color:var(--acc2)">provisioning ${Math.min(24,done)}/24</span>`}</div>
   <span class="pill mono" style="margin:0;${active?'border-color:var(--green);color:var(--green);background:color-mix(in srgb,var(--acc) 10%,transparent);transform:scale('+lerp(1.3,1,eOutBack(clamp((u-5.3)/.35)))+')':'color:var(--acc2);border-color:color-mix(in srgb,var(--acc2) 35%,var(--bg))'};display:inline-block">● ${active?'active':'provisioning'}</span>
  </div>
  <div style="display:grid;grid-template-columns:repeat(8,1fr);gap:10px">${cells}</div>
 </div>
 <div class="abs" style="left:120px;top:440px;width:1040px;display:flex;gap:14px">
  ${[['alert','Skipped ≠ success','The reason is recorded beside the step. Read it.','var(--amber)'],['pause','Not moving?','Steps sitting unclaimed usually mean no queue worker.','var(--acc2)'],['refresh','Failed?','Every step is idempotent — fix the cause, Re-run Pipeline.','var(--acc)']].map((x,i)=>`<div class="card" style="flex:1;padding:16px 18px;${pop(u,4.5+i*.2,20)}"><div style="font-weight:700;color:${x[3]};display:flex;gap:8px;align-items:center">${ic(x[0],18)} ${x[1]}</div><div style="font-size:14px;color:var(--muted);margin-top:6px">${x[2]}</div></div>`).join('')}
 </div>`;
}

function s7(u){
 const load=clamp((u-1.0)/.6), up=u>1.6;
 const st=[['Outstanding','RM',48250,'var(--amber)'],['Paid this month','RM',126900,'var(--acc)'],['Overdue','',7,'var(--red)']];
 const inv=[['INV-2026-0142','Syarikat Maju Jaya','4,200.00','Paid','var(--acc)'],['INV-2026-0141','Kedai Runcit Pak Ali','860.50','Sent','var(--acc2)'],['INV-2026-0140','Nusantara Logistik','12,480.00','Overdue','var(--red)'],['INV-2026-0139','Klinik Seri Melati','2,150.00','Paid','var(--acc)']];
 const spark=[12,18,15,22,19,27,24,31,29,36,33,41];
 const sk=eInOut(clamp((u-2.4)/.9));
 const pts=spark.map((v,i)=>`${i*(200/11)},${50-v}`).join(' ');
 const checks=[['lock','Domains & TLS','Certificate issued · HTTPS via the entry point'],['file','Logs','Streaming · redacted by default'],['pulse','Health','Collecting readings — no figure yet ≠ healthy']];
 return `${cue('whoosh',1.0,.5)}${cue('final',1.6,.55)}${cue('chime',1.65,.8)}
 <div class="abs card" style="left:80px;top:100px;width:780px;height:500px;overflow:hidden;padding:0;${pop(u,.1,30)}">
  <div style="height:44px;display:flex;align-items:center;gap:8px;padding:0 14px;border-bottom:1px solid var(--line);background:var(--field)">
   <span style="width:11px;height:11px;border-radius:50%;background:var(--strong)"></span><span style="width:11px;height:11px;border-radius:50%;background:var(--strong)"></span><span style="width:11px;height:11px;border-radius:50%;background:var(--strong)"></span>
   <div class="mono" style="margin-left:14px;flex:1;height:28px;border-radius:6px;background:var(--panel);border:1px solid var(--line);display:flex;align-items:center;gap:8px;padding:0 10px;font-size:13px">
    <span style="color:${u>.3?'var(--acc)':'var(--idle)'}">${ic('lock',14)}</span><span style="color:var(--idle)">https://</span>${typed('billing.acme.my',u,.3,22)}
   </div>
  </div>
  <div style="height:3px;background:transparent"><div style="height:3px;width:${load*100}%;background:var(--acc);opacity:${up?1-clamp((u-1.6)/.3):1}"></div></div>
  <div style="padding:18px 22px;opacity:${up?1:0}">
   <div style="display:flex;align-items:center;justify-content:space-between;${popq(u,1.6,10)}">
    <div style="display:flex;align-items:center;gap:10px"><span style="width:28px;height:28px;border-radius:7px;background:var(--acc2);display:inline-flex;align-items:center;justify-content:center;color:var(--onacc);font-weight:800">A</span><b style="font-size:17px">Acme Billing</b></div>
    <div style="display:flex;gap:18px;font-size:13px;color:var(--muted)"><span style="color:var(--text);border-bottom:2px solid var(--acc2);padding-bottom:3px">Dashboard</span><span>Invoices</span><span>Customers</span><span>Reports</span></div>
   </div>
   <div style="display:flex;gap:12px;margin-top:16px">
    ${st.map((x,i)=>`<div style="flex:1;border:1px solid var(--line);border-radius:10px;padding:12px 14px;background:var(--field);${pop(u,1.75+i*.12,14)}"><div style="font-size:12px;color:var(--muted)">${x[0]}</div><div class="mono" style="font-size:19px;white-space:nowrap;font-weight:600;color:${x[3]};margin-top:4px">${x[1]} ${count(u,1.9,3.0,0,x[2]).toLocaleString('en-MY')}</div></div>`).join('')}
    <div style="flex:1.2;border:1px solid var(--line);border-radius:10px;padding:10px 12px;background:var(--field);${popq(u,2.1,14)}"><div style="font-size:12px;color:var(--muted)">Collections · 12 wks</div><svg width="200" height="52" style="margin-top:4px"><polyline points="${pts}" fill="none" style="stroke:var(--acc2)" stroke-width="2" pathLength="1" stroke-dasharray="1" stroke-dashoffset="${1-sk}"/></svg></div>
   </div>
   <div style="margin-top:16px;border:1px solid var(--line);border-radius:10px;overflow:hidden">
    <div class="lbl" style="display:flex;padding:10px 14px;margin:0;background:var(--field);border-bottom:1px solid var(--line)"><span style="width:150px">Invoice</span><span style="flex:1">Customer</span><span style="width:120px;text-align:right">Amount</span><span style="width:110px;text-align:right">Status</span></div>
    ${inv.map((r,i)=>`<div style="display:flex;align-items:center;padding:10px 14px;border-bottom:1px solid var(--line);font-size:14px;${pop(u,2.3+i*.15,10,.4,'tick')}"><span class="mono" style="width:150px;color:var(--muted);font-size:13px">${r[0]}</span><span style="flex:1">${r[1]}</span><span class="mono" style="width:120px;text-align:right">RM ${r[2]}</span><span style="width:110px;text-align:right"><span class="pill" style="margin:0;border-color:${r[4]};color:${r[4]};font-size:12px;padding:3px 10px">${r[3]}</span></span></div>`).join('')}
   </div>
  </div>
 </div>
 <div class="abs mono" style="left:80px;top:612px;width:780px;font-size:13px;color:var(--muted);${popq(u,1.8,8)}"><span style="color:var(--acc)">● 200 OK</span> · 142 ms · app-01 via entry point · TLS 1.3</div>
 <div class="abs" style="left:890px;top:100px;width:310px">
  <div class="lbl" style="${popq(u,.4,6)}">Check it from the application</div>
  ${checks.map((x,i)=>{const at=3.3+i*.45;const ok=u>at;rec('success',at,.6);return `<div class="card" style="padding:16px 18px;margin-top:12px;${popq(u,.6+i*.15,20)};${ok?'border-color:var(--accdim)':''}"><div style="display:flex;justify-content:space-between;align-items:center"><span style="display:flex;gap:10px;align-items:center;font-weight:700"><span style="color:var(--acc)">${ic(x[0],18)}</span>${x[1]}</span><span style="color:${ok?'var(--acc)':'var(--strong)'};display:inline-block;transform:scale(${ok?lerp(1.4,1,eOutBack(clamp((u-at)/.35))):1})">${ic('check',20)}</span></div><div style="font-size:13px;color:var(--muted);margin-top:6px;line-height:1.45">${x[2]}</div></div>`}).join('')}
  <div class="card" style="padding:14px 18px;margin-top:16px;${popq(u,4.9,14)};border-style:dashed;font-size:13px;color:var(--muted);line-height:1.45">Bare published port? It binds loopback only — route it through a domain.</div>
 </div>`;
}
function s8(u){
 const pulse=u>2.2?Math.sin(clamp((u-2.2)/.6)*Math.PI)*.08:0;
 return `${cue('logo',0)}${cue('final',.2)}
 <div class="abs" style="left:0;right:0;top:190px;text-align:center">
  <div style="height:110px">${logo(104,clamp((u-.25)/1.1),clamp(u/.5))}</div>
  <div style="${popq(u,1.0,14)};font-size:26px;color:var(--muted);margin-top:22px">${BRAND.tagline}</div>
  <div style="${pop(u,1.4,14)};margin-top:34px"><span class="btn pri mono" style="font-size:22px;padding:14px 30px;display:inline-block;transform:scale(${1+pulse})">${BRAND.cta}</span></div>
  <div style="${popq(u,1.8,10)};margin-top:30px;color:var(--idle)" class="lbl">${BRAND.footer}</div>
 </div>`;
}
const FN=[s0,s1,s2,s3,s4,s5,s6,s7,s8];
````

<!-- file: examples/g8deck-first-deployment/narration.json -->
````json
{
 "lines": [
  {
   "t": 1.0,
   "text": "Your first deployment, with G8Deck."
  },
  {
   "t": 4.8,
   "text": "Connect a server you own: its address and an SSH key."
  },
  {
   "t": 8.8,
   "text": "Test the connection, and setup runs sixteen steps."
  },
  {
   "t": 12.3,
   "text": "Then create the application. Pick Git, add the repository, and choose a build preset."
  },
  {
   "t": 18.3,
   "text": "Next, describe what it needs, one choice at a time: runtime, database, cache, workers, and where it runs."
  },
  {
   "t": 25.6,
   "text": "That sentence becomes its shape."
  },
  {
   "t": 28.8,
   "text": "Each part gets its own node: entry, app, database, cache and storage."
  },
  {
   "t": 33.4,
   "text": "G8Deck wires them together."
  },
  {
   "t": 36.4,
   "text": "Review where it runs, let the guards check, then save and deploy."
  },
  {
   "t": 42.3,
   "text": "Twenty-four pipeline steps run. Skipped is not success, so read the amber rows."
  },
  {
   "t": 49.3,
   "text": "And it's live, on your own server, with TLS."
  },
  {
   "t": 52.7,
   "text": "Check domains, logs and health from the application."
  },
  {
   "t": 56.6,
   "text": "G8Deck. Deploy on infrastructure you own."
  }
 ]
}
````

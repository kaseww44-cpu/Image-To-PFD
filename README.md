<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Images → PDF | Free Converter</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="description" content="Convert multiple images (JPG, PNG, WEBP) into a single PDF. 100% client-side, no upload.">

<script src="https://cdn.jsdelivr.net/npm/jspdf@2.5.1/dist/jspdf.umd.min.js"></script>

<style>
:root{
  --bg:#0f172a;--panel:#111827;--muted:#cbd5e1;--text:#e5e7eb;
  --brand:#60a5fa;--accent:#34d399;--b:1px solid rgba(255,255,255,.10);
  --shadow:0 10px 25px rgba(0,0,0,.35);
}
*{box-sizing:border-box}
body{
  margin:0; font-family:ui-sans-serif,system-ui,Segoe UI,Roboto,Arial;
  background:var(--bg); color:var(--text);
  background-image: radial-gradient(900px 400px at 10% 0%, rgba(96,165,250,.12), transparent 40%),
                    radial-gradient(600px 300px at 90% 0%, rgba(34,211,238,.10), transparent 40%);
}
.wrap{max-width:960px;margin:0 auto;padding:24px 14px}
.header{display:flex;justify-content:space-between;align-items:center;gap:12px;margin-bottom:18px}
.logo{width:44px;height:44px;border-radius:14px;background:linear-gradient(135deg,#60a5fa,#22d3ee);box-shadow:var(--shadow)}
h1{margin:0;font-size:clamp(20px,3vw,30px)}
.sub{color:var(--muted);font-size:13px}
.card{background:var(--panel);border:var(--b);border-radius:18px;padding:18px;box-shadow:var(--shadow)}
.drop{
  border:1.5px dashed rgba(255,255,255,.18);border-radius:16px;padding:22px;text-align:center;background:rgba(255,255,255,.02);
}
.drop.drag{border-color:var(--brand);background:rgba(96,165,250,.10)}
input[type=file]{display:none}
.controls{display:grid;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));gap:12px;margin:14px 0}
label{font-size:12px;color:var(--muted)}
select,input[type=number],input[type=text]{
  width:100%;padding:10px 12px;border-radius:12px;border:var(--b);background:#0b1220;color:var(--text)
}
.row{display:flex;gap:10px;flex-wrap:wrap;align-items:center}
.btn{
  display:inline-flex;align-items:center;gap:8px;padding:12px 16px;border-radius:14px;border:var(--b);
  background:linear-gradient(135deg,rgba(96,165,250,.20),rgba(34,211,238,.20));color:#eaf2ff;font-weight:600;cursor:pointer
}
.btn.secondary{background:transparent}
.btn:disabled{opacity:.6;cursor:not-allowed}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(150px,1fr));gap:12px;margin-top:14px}
.thumb{position:relative;border:var(--b);border-radius:14px;overflow:hidden;background:#0b1220}
.thumb img{display:block;width:100%;height:auto}
.name{position:absolute;left:6px;right:6px;bottom:6px;font-size:11px;color:#dbeafe;background:rgba(2,6,23,.6);
  padding:4px 6px;border-radius:8px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.muted{color:var(--muted)}
.progress{height:8px;background:rgba(255,255,255,.08);border-radius:9999px;overflow:hidden;margin-top:8px}
.progress>div{height:100%;width:0;background:linear-gradient(90deg,var(--brand),var(--accent));transition:width .25s}
.footer{margin-top:18px;text-align:center;color:var(--muted);font-size:12px}
</style>
</head>
<body>
<div class="wrap">
  <div class="header">
    <div style="display:flex;gap:12px;align-items:center">
      <div class="logo" aria-hidden="true"></div>
      <div>
        <h1>Images → PDF Converter</h1>
        <div class="sub">Combine JPG / PNG / WEBP into a single PDF — 100% browser-side.</div>
      </div>
    </div>
    <div class="sub">Private • Files never leave your device</div>
  </div>

  <div class="card">
    <div id="drop" class="drop" tabindex="0" role="button" aria-label="Drop images here or browse">
      <p><strong>Drop images</strong> here or <label for="files" class="btn secondary">Browse</label></p>
      <p class="muted">Supported: JPG, PNG, WEBP • Multiple files allowed • Drag to reorder</p>
      <input id="files" type="file" accept="image/*" multiple />
    </div>

    <div class="controls">
      <div>
        <label for="page">Page Size</label>
        <select id="page">
          <option value="auto" selected>Auto (fit to image)</option>
          <option value="a4">A4</option>
          <option value="letter">US Letter</option>
        </select>
      </div>
      <div>
        <label for="margin">Margin (mm)</label>
        <input id="margin" type="number" min="0" step="1" value="8" />
      </div>
      <div>
        <label for="name">Output Name</label>
        <input id="name" type="text" value="images-to-pdf.pdf" />
      </div>
      <div class="row">
        <button id="make" class="btn" disabled>⚙️ Convert to PDF</button>
        <button id="clear" class="btn secondary" disabled>🧹 Clear</button>
        <button id="downloadAd" class="btn" disabled>💾 Download PDF</button>
      </div>
    </div>

    <div class="progress"><div id="bar"></div></div>
    <div id="list" class="grid"></div>
  </div>

  <div class="footer">Tip: You can re-order images by dragging the thumbnails before converting.</div>
</div>

<script>
const $ = s => document.querySelector(s);
let items = [];
const drop = $('#drop'), input = $('#files'), list = $('#list');
const makeBtn = $('#make'), clearBtn = $('#clear'), bar = $('#bar');
const downloadBtn = $('#downloadAd');

function fileToDataURL(file){
  return new Promise((res,rej)=>{ const r=new FileReader(); r.onload=()=>res(r.result); r.onerror=rej; r.readAsDataURL(file); });
}
function imgSize(src){ return new Promise(r=>{ const i=new Image(); i.onload=()=>r({w:i.naturalWidth,h:i.naturalHeight}); i.src=src; }); }
function fit(w,h,maxW,maxH){ const r=Math.min(maxW/w,maxH/h); return {w:w*r,h:h*r}; }
function sanitize(n){ return (n||'images-to-pdf.pdf').replace(/[^\w.\-]+/g,'-'); }
function raf(){ return new Promise(r=>requestAnimationFrame(r)); }

function wireDropZone(el,onFiles){
  ['dragenter','dragover'].forEach(e=>el.addEventListener(e,ev=>{ev.preventDefault();el.classList.add('drag');}));
  ['dragleave','drop'].forEach(e=>el.addEventListener(e,ev=>{ev.preventDefault();el.classList.remove('drag');}));
  el.addEventListener('drop',ev=>onFiles([...ev.dataTransfer.files]));
  el.addEventListener('click',()=>input.click());
  el.addEventListener('keydown',e=>{ if(e.key==='Enter'||e.key===' ') input.click(); });
}
wireDropZone(drop, addFiles);
input.addEventListener('change', e => addFiles([...e.target.files]));

function addFiles(files){
  const imgs = files.filter(f=>/^image\//.test(f.type));
  imgs.forEach(f=>items.push({file:f,url:URL.createObjectURL(f),name:f.name}));
  render();
}

let dragIndex=null;
function render(){
  list.innerHTML='';
  items.forEach((it,i)=>{
    const d=document.createElement('div'); d.className='thumb'; d.draggable=true;
    d.innerHTML=`<img src="${it.url}" alt="img ${i+1}"><div class="name">${it.name}</div>`;
    d.addEventListener('dragstart',()=>dragIndex=i);
    d.addEventListener('dragover',e=>e.preventDefault());
    d.addEventListener('drop',e=>{ e.preventDefault(); if(dragIndex===null) return;
      const [moved]=items.splice(dragIndex,1);
      const to=i+(dragIndex<i?1:0);
      items.splice(to,0,moved); dragIndex=null; render();
    });
    list.appendChild(d);
  });
  const has=items.length>0;
  makeBtn.disabled=!has; clearBtn.disabled=!has; downloadBtn.disabled=true;
  if(!has) bar.style.width='0%';
}

clearBtn.addEventListener('click',()=>{
  items.forEach(it=>URL.revokeObjectURL(it.url));
  items=[]; render();
});

let pdfDoc=null;
makeBtn.addEventListener('click', async ()=>{
  if(!items.length) return;
  const { jsPDF } = window.jspdf;
  const marginMM=Math.max(0,parseInt($('#margin').value||0));
  const pageSel=$('#page').value;

  pdfDoc=null;
  let first=true, processed=0;

  for(const it of items){
    const data=await fileToDataURL(it.file);
    const size=await imgSize(data);

    let wPt,hPt,orient;
    if(pageSel==='auto'){
      const wIn=size.w/96,hIn=size.h/96;
      wPt=Math.max(36,Math.round(wIn*72));
      hPt=Math.max(36,Math.round(hIn*72));
      orient=wPt>hPt?'l':'p';
    }else{
      const map={a4:[595.28,841.89],letter:[612,792]};
      [wPt,hPt]=map[pageSel];
      orient=size.w>size.h?'l':'p';
    }

    if(first){
      pdfDoc=new jsPDF({orientation:orient,unit:'pt',format:[wPt,hPt]});
      first=false;
    }else pdfDoc.addPage([wPt,hPt],orient);

    const marginPt=marginMM*72/25.4;
    const maxW=wPt-marginPt*2;
    const maxH=hPt-marginPt*2;
    const fitted=fit(size.w,size.h,maxW,maxH);

    const x=(wPt-fitted.w)/2;
    const y=(hPt-fitted.h)/2;
    pdfDoc.addImage(data,'PNG',x,y,fitted.w,fitted.h,undefined,'FAST');

    processed++; bar.style.width=(processed/items.length*100).toFixed(1)+'%';
    await raf();
  }

  downloadBtn.disabled=false;
});

// Two-step download with popup ad
downloadBtn.addEventListener('click',()=>{
  // Open small popup ad
  window.open('https://www.profitableratecpm.com/cw3zkyxj1?key=61ca99c17321f4567e98f6805e824e4e','adWindow','width=600,height=400');
  // Trigger PDF download shortly after
  setTimeout(()=>{
    const out = sanitize($('#name').value);
    pdfDoc.save(out || 'images-to-pdf.pdf');
  },500);
});
</script>
</body>
</html>

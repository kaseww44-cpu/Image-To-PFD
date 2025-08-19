[index.html.html](https://github.com/user-attachments/files/21854298/index.html.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Image ↔ PDF Converter | Free Online Tool</title>
  <meta name="description" content="Convert images (JPG, PNG, WEBP) to PDF and PDF to images directly in your browser. No upload. Fast & secure." />
  
  <!-- External libs (CDN) -->
  <!-- jsPDF for Image → PDF -->
  <script src="https://cdn.jsdelivr.net/npm/jspdf@2.5.1/dist/jspdf.umd.min.js"></script>
  <!-- PDF.js for PDF → Images -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/4.8.69/pdf.min.js" integrity="sha512-+5s0ObaDS4n0DkM9LpitTnqE6z6Zg6K8Hqf2Y0mfzYqbm1c1bJQx6Y68LTFn1w6TOyC2Gc0+zXlAjHacOQ0CwQ==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
  <script>window.pdfjsLib = window['pdfjs-dist/build/pdf']; pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/4.8.69/pdf.worker.min.js';</script>
  <!-- JSZip for zipping multiple images -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js" integrity="sha512-4xUeC2k2jZK7vmlr0aJv6Yz9o80Qx6x4jUAnP4k0oJkzq1ycc2WcVY6X9z3vB2+1bQp8o0P2Sby9t5z3m6v3MQ==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
  
  <style>
    :root{
      --bg: #0f172a;          /* slate-900 */
      --card: #111827;        /* gray-900 */
      --muted: #cbd5e1;       /* slate-300 */
      --text: #e5e7eb;        /* gray-200 */
      --brand: #60a5fa;       /* blue-400 */
      --brand-2: #22d3ee;     /* cyan-400 */
      --accent: #34d399;      /* emerald-400 */
      --danger: #f87171;      /* red-400 */
      --shadow: 0 10px 25px rgba(0,0,0,.35);
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, Noto Sans, Helvetica Neue, Arial, "Apple Color Emoji", "Segoe UI Emoji";
      background: radial-gradient(1200px 600px at 10% 10%, rgba(96,165,250,.12), transparent 40%),
                  radial-gradient(1000px 500px at 90% 0%, rgba(34,211,238,.12), transparent 40%),
                  var(--bg);
      color: var(--text);
    }
    .container{max-width:1100px; margin:0 auto; padding:32px 16px}
    .header{display:flex; gap:14px; align-items:center; justify-content:space-between; margin-bottom:22px}
    .brand{display:flex; gap:12px; align-items:center}
    .logo{width:44px; height:44px; border-radius:14px; background:linear-gradient(135deg,var(--brand),var(--brand-2)); box-shadow: var(--shadow)}
    h1{font-size: clamp(22px, 3vw, 32px); margin:0}
    .sub{color: var(--muted); font-size: 14px}

    .tabs{display:grid; grid-template-columns: 1fr 1fr; gap:10px; margin:14px 0}
    .tab{
      background: var(--card); border:1px solid rgba(255,255,255,.06);
      padding:14px 18px; border-radius:16px; cursor:pointer; text-align:center;
      transition: transform .08s ease, border-color .2s ease, background .2s ease;
      box-shadow: var(--shadow);
    }
    .tab.active{ outline: none; border-color: rgba(96,165,250,.6); background: #0b1220 }
    .tab:active{ transform: translateY(1px) }

    .panel{ display:none }
    .panel.active{ display:block }

    .card{ background: var(--card); border:1px solid rgba(255,255,255,.06); border-radius:18px; padding:18px; box-shadow: var(--shadow) }

    .dropzone{
      border:1.5px dashed rgba(255,255,255,.18); border-radius:16px; padding:22px; text-align:center;
      background: rgba(255,255,255,.02);
    }
    .dropzone.drag{ border-color: var(--brand); background: rgba(96,165,250,.08) }
    .muted{ color: var(--muted) }

    .controls{ display:grid; grid-template-columns: repeat(auto-fit, minmax(180px,1fr)); gap:12px; margin:16px 0 }
    label{ font-size: 13px; color: var(--muted) }
    select, input[type="number"], input[type="text"]{
      width:100%; padding:10px 12px; border-radius:12px; border:1px solid rgba(255,255,255,.12);
      background: #0b1220; color: var(--text)
    }

    .btn{ display:inline-flex; gap:8px; align-items:center; justify-content:center; cursor:pointer;
      padding:12px 16px; border-radius:14px; border:1px solid rgba(255,255,255,.14); 
      background: linear-gradient(135deg, rgba(96,165,250,.2), rgba(34,211,238,.2)); color: #eaf2ff; font-weight: 600 }
    .btn.secondary{ background: transparent }
    .btn:disabled{ opacity:.6; cursor:not-allowed }

    .grid{ display:grid; grid-template-columns: repeat(auto-fill, minmax(160px,1fr)); gap:14px }
    .thumb{ position:relative; border:1px solid rgba(255,255,255,.08); border-radius:16px; overflow:hidden; background:#0b1220 }
    .thumb img, .thumb canvas{ width:100%; display:block }
    .thumb .name{ position:absolute; left:8px; bottom:8px; right:8px; font-size:12px; color:#dbeafe; 
      background: rgba(2,6,23,.6); padding:4px 6px; border-radius:8px; overflow:hidden; text-overflow:ellipsis; white-space:nowrap }

    .footer{ text-align:center; margin-top:24px; color: var(--muted); font-size: 12px }
    .pill{ display:inline-block; padding:6px 10px; border-radius:9999px; border:1px solid rgba(255,255,255,.12); background:#0b1220 }

    .progress{ height:8px; background: rgba(255,255,255,.08); border-radius:9999px; overflow:hidden }
    .progress > div{ height:100%; width:0; background: linear-gradient(90deg, var(--brand), var(--accent)); transition: width .25s ease }
    .hint{ font-size:12px; color: var(--muted) }
    .row{ display:flex; gap:10px; flex-wrap:wrap; align-items:center }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <div class="brand">
        <div class="logo" aria-hidden="true"></div>
        <div>
          <h1>Image ↔ PDF Converter</h1>
          <div class="sub">Convert images (JPG, PNG, WEBP) to PDF & PDF pages to images — 100% client‑side.</div>
        </div>
      </div>
      <div class="pill">Private • Files never leave your browser</div>
    </div>

    <div class="tabs">
      <button class="tab active" data-tab="img2pdf">🖼️ Image → PDF</button>
      <button class="tab" data-tab="pdf2img">📄 PDF → Images</button>
    </div>

    <!-- Image → PDF Panel -->
    <section id="img2pdf" class="panel active">
      <div class="card">
        <div id="drop-img" class="dropzone" tabindex="0">
          <p><strong>Drop images</strong> here or <label class="btn secondary" for="imgFiles">Browse</label></p>
          <p class="muted">Supported: JPG, PNG, WEBP • Multiple files allowed</p>
          <input id="imgFiles" type="file" accept="image/*" multiple hidden />
        </div>

        <div class="controls">
          <div>
            <label for="pageSize">Page Size</label>
            <select id="pageSize">
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
            <label for="filename1">Output Name</label>
            <input id="filename1" type="text" value="images-to-pdf.pdf" />
          </div>
          <div class="row">
            <button id="convertImgToPdf" class="btn">⚙️ Convert to PDF</button>
            <button id="clearImgList" class="btn secondary">🧹 Clear</button>
          </div>
        </div>

        <div class="progress" aria-hidden="true"><div id="p1"></div></div>
        <div id="imgList" class="grid" style="margin-top:14px"></div>
      </div>
    </section>

    <!-- PDF → Images Panel -->
    <section id="pdf2img" class="panel">
      <div class="card">
        <div id="drop-pdf" class="dropzone" tabindex="0">
          <p><strong>Drop a PDF</strong> here or <label class="btn secondary" for="pdfFile">Browse</label></p>
          <p class="muted">We will render each page as a PNG image.</p>
          <input id="pdfFile" type="file" accept="application/pdf" hidden />
        </div>

        <div class="controls">
          <div>
            <label for="scale">Quality (DPI)</label>
            <select id="scale">
              <option value="1">Standard (72 DPI)</option>
              <option value="1.5" selected>Good (108 DPI)</option>
              <option value="2">High (144 DPI)</option>
              <option value="3">Very High (216 DPI)</option>
            </select>
          </div>
          <div>
            <label for="filename2">Zip Name</label>
            <input id="filename2" type="text" value="pdf-pages.zip" />
          </div>
          <div class="row">
            <button id="convertPdfToImg" class="btn">⚙️ Convert to Images</button>
            <button id="downloadAll" class="btn secondary" disabled>⬇️ Download All (ZIP)</button>
          </div>
        </div>

        <div class="hint">Tip: After conversion, click any page image to download it individually.</div>
        <div class="progress" aria-hidden="true" style="margin-top:8px"><div id="p2"></div></div>
        <div id="pageGrid" class="grid" style="margin-top:14px"></div>
      </div>
    </section>

    <div class="footer">Made with ♥ — Works offline after first load • Drag & drop supported • Large files use more memory.</div>
  </div>

  <script>
    // Utility helpers
    const $ = (sel) => document.querySelector(sel);
    const $$ = (sel) => Array.from(document.querySelectorAll(sel));

    // Tabs
    $$('.tab').forEach(btn => btn.addEventListener('click', () => {
      $$('.tab').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      $$('.panel').forEach(p => p.classList.remove('active'));
      const id = btn.dataset.tab; document.getElementById(id).classList.add('active');
    }));

    // Drag styles
    function wireDropzone(zone, onFiles){
      ;['dragenter','dragover'].forEach(e => zone.addEventListener(e, ev => {ev.preventDefault(); zone.classList.add('drag');}))
      ;['dragleave','drop'].forEach(e => zone.addEventListener(e, ev => {ev.preventDefault(); zone.classList.remove('drag');}))
      zone.addEventListener('drop', ev => { const files = [...ev.dataTransfer.files]; onFiles(files); });
      zone.addEventListener('click', () => zone.querySelector('input[type=file]')?.click());
      zone.addEventListener('keydown', (e) => { if(e.key==='Enter' || e.key===' ') zone.click(); });
    }

    // ========================= Image → PDF =========================
    const imgFiles = []; // keep order
    wireDropzone($('#drop-img'), files => addImages(files));
    $('#imgFiles').addEventListener('change', e => addImages([...e.target.files]));

    function validImage(f){ return /^image\//.test(f.type); }

    function addImages(files){
      files.filter(validImage).forEach(f => imgFiles.push(f));
      renderImgThumbs();
    }

    function renderImgThumbs(){
      const wrap = $('#imgList'); wrap.innerHTML = '';
      imgFiles.forEach((file, i) => {
        const url = URL.createObjectURL(file);
        const div = document.createElement('div'); div.className = 'thumb';
        div.innerHTML = `<img src="${url}" alt="image ${i+1}"><div class="name" title="${file.name}">${file.name}</div>`;
        wrap.appendChild(div);
      });
      $('#convertImgToPdf').disabled = imgFiles.length === 0;
    }

    $('#clearImgList').addEventListener('click', () => { imgFiles.splice(0, imgFiles.length); renderImgThumbs(); $('#p1').style.width='0%'; });

    $('#convertImgToPdf').addEventListener('click', async () => {
      if(!imgFiles.length) return;
      const { jsPDF } = window.jspdf;
      const pageSize = $('#pageSize').value; const margin = Math.max(0, parseInt($('#margin').value||0));

      const progress = $('#p1'); progress.style.width = '0%';
      let doc, first = true, done = 0;

      for(const file of imgFiles){
        const imgData = await fileToDataURL(file);
        const dims = await getImageSize(imgData);
        // Decide page size
        let wPt, hPt, orient;
        if(pageSize === 'auto'){
          // page equals image size in points (1 pt ≈ 1/72 inch). Assume 96 DPI typical CSS pixel to inch.
          const wIn = dims.width / 96; const hIn = dims.height / 96;
          wPt = Math.round(wIn * 72); hPt = Math.round(hIn * 72);
          orient = wPt > hPt ? 'l' : 'p';
        }else{
          const sizes = { a4:[595.28,841.89], letter:[612,792] };
          [wPt,hPt] = sizes[pageSize];
          orient = dims.width > dims.height ? 'l' : 'p';
        }
        if(first){ doc = new jsPDF({orientation: orient, unit:'pt', format: [wPt, hPt]}); first = false; }
        else doc.addPage([wPt, hPt], orient);

        const maxW = wPt - margin*2; const maxH = hPt - margin*2;
        const fit = fitRect(dims.width, dims.height, maxW, maxH);
        const x = (wPt - fit.w)/2; const y = (hPt - fit.h)/2;
        doc.addImage(imgData, 'PNG', x, y, fit.w, fit.h, undefined, 'FAST');

        done++; progress.style.width = (done/imgFiles.length*100).toFixed(1) + '%';
        await waitFrame();
      }

      const outName = sanitizeName($('#filename1').value || 'images-to-pdf.pdf');
      doc.save(outName);
    });

    // ========================= PDF → Images =========================
    let renderedPages = []; // {name, blobUrl}
    wireDropzone($('#drop-pdf'), files => { if(files[0]) setPdf(files[0]); });
    $('#pdfFile').addEventListener('change', e => { if(e.target.files[0]) setPdf(e.target.files[0]); });

    let pdfFileRef = null;
    function setPdf(file){
      if(file?.type !== 'application/pdf'){ alert('Please choose a PDF file.'); return; }
      pdfFileRef = file; $('#pageGrid').innerHTML=''; renderedPages = []; $('#p2').style.width='0%';
      $('#convertPdfToImg').disabled = false; $('#downloadAll').disabled = true;
    }

    $('#convertPdfToImg').addEventListener('click', async () => {
      if(!pdfFileRef) return;
      const scale = parseFloat($('#scale').value || '1.5');
      const data = new Uint8Array(await pdfFileRef.arrayBuffer());
      const pdf = await pdfjsLib.getDocument({data}).promise;
      const total = pdf.numPages; const progress = $('#p2');
      const grid = $('#pageGrid'); grid.innerHTML=''; renderedPages = [];

      for(let i=1;i<=total;i++){
        const page = await pdf.getPage(i);
        const viewport = page.getViewport({scale});
        const canvas = document.createElement('canvas');
        const ctx = canvas.getContext('2d');
        canvas.width = Math.floor(viewport.width);
        canvas.height = Math.floor(viewport.height);
        await page.render({canvasContext: ctx, viewport}).promise;

        // to blob + preview
        const blob = await new Promise(res => canvas.toBlob(res, 'image/png'));
        const url = URL.createObjectURL(blob);
        const name = `page-${String(i).padStart(3,'0')}.png`;
        renderedPages.push({name, url, blob});

        const card = document.createElement('a');
        card.className = 'thumb'; card.href = url; card.download = name; card.title = 'Click to download';
        card.innerHTML = `<canvas width="${canvas.width}" height="${canvas.height}"></canvas><div class='name'>${name}</div>`;
        card.querySelector('canvas').getContext('2d').drawImage(canvas,0,0);
        grid.appendChild(card);

        progress.style.width = (i/total*100).toFixed(1) + '%';
        await waitFrame();
      }

      $('#downloadAll').disabled = renderedPages.length === 0;
    });

    $('#downloadAll').addEventListener('click', async () => {
      if(!renderedPages.length) return;
      const zip = new JSZip();
      const folder = zip.folder('images');
      for(const p of renderedPages){ folder.file(p.name, p.blob); }
      const blob = await zip.generateAsync({type:'blob'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url; a.download = sanitizeName($('#filename2').value || 'pdf-pages.zip');
      document.body.appendChild(a); a.click(); a.remove();
      URL.revokeObjectURL(url);
    });

    // ------------------------ Helpers ------------------------
    function fileToDataURL(file){
      return new Promise((res, rej) => { const r = new FileReader(); r.onload = () => res(r.result); r.onerror = rej; r.readAsDataURL(file); });
    }
    function getImageSize(dataUrl){
      return new Promise((res) => { const img = new Image(); img.onload = () => res({width: img.naturalWidth, height: img.naturalHeight}); img.src = dataUrl; });
    }
    function fitRect(w,h,maxW,maxH){
      const r = Math.min(maxW/w, maxH/h); return { w: w*r, h: h*r };
    }
    function sanitizeName(name){ return name.replace(/[^\w\-.]+/g,'-'); }
    function waitFrame(){ return new Promise(r => requestAnimationFrame(r)); }
  </script>
</body>
</html>

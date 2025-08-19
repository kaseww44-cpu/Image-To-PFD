[image_to_pdf_with_ads.html](https://github.com/user-attachments/files/21854809/image_to_pdf_with_ads.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Image to PDF Converter</title>
  <meta name="description" content="Convert multiple images (JPG, PNG, WEBP) into a single PDF. Free and secure browser tool." />
  
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  
  <style>
    /* Basic Reset */
    *{margin:0;padding:0;box-sizing:border-box;}
    body{font-family:Arial, sans-serif;background:linear-gradient(135deg,#4facfe,#00f2fe);color:#333;line-height:1.5;}

    /* Header */
    header{width:100%;padding:20px;background:rgba(255,255,255,0.1);text-align:center;position:relative;}
    header h1{font-size:2em;color:white;}
    .top-banner-ad{width:100%;height:60px;background:#ffd700;color:#333;display:flex;align-items:center;justify-content:center;margin-bottom:20px;font-weight:bold;}
    
    /* Container */
    .container{max-width:900px;margin:0 auto;background:rgba(255,255,255,0.95);padding:20px;border-radius:20px;box-shadow:0 10px 30px rgba(0,0,0,0.3);}
    
    /* File input */
    input[type=file]{display:block;margin:20px auto;}
    button{display:block;margin:10px auto;padding:10px 20px;background:#007BFF;color:white;border:none;border-radius:8px;font-size:16px;cursor:pointer;}
    button:hover{background:#0056b3;}

    /* Thumbnails */
    .thumbs{display:flex;flex-wrap:wrap;gap:10px;justify-content:center;margin-top:20px;}
    .thumbs img{height:100px;border-radius:10px;border:1px solid #ccc;}

    /* Sidebar ad placeholder */
    .sidebar-ad{width:100%;height:100px;background:#ffa500;color:#333;display:flex;align-items:center;justify-content:center;margin:20px 0;font-weight:bold;border-radius:10px;}

    /* Footer */
    footer{margin-top:20px;text-align:center;color:white;}
    .socials a{margin:0 10px;text-decoration:none;color:white;font-size:1.5em;}
  </style>
</head>
<body>

  <!-- Top Banner Ad -->
  <div class="top-banner-ad">Top Banner Ad Space</div>

  <header>
    <h1>Image to PDF Converter</h1>
  </header>

  <div class="container">
    <!-- Main Converter -->
    <input type="file" id="imageUpload" accept="image/*" multiple>
    <button onclick="convertToPDF()">Convert to PDF</button>

    <div class="thumbs" id="thumbContainer"></div>

    <!-- Sidebar Ad -->
    <div class="sidebar-ad">Sidebar/Inline Ad Space</div>
  </div>

  <footer>
    <p>Follow us on:</p>
    <div class="socials">
      <a href="#" target="_blank">🌐</a>
      <a href="#" target="_blank">📘</a>
      <a href="#" target="_blank">🐦</a>
    </div>
    <p>&copy; 2025 YourSiteName</p>
  </footer>

<script>
  // Display thumbnails
  const thumbContainer = document.getElementById('thumbContainer');
  const input = document.getElementById('imageUpload');

  let images = [];
  input.addEventListener('change', e => {
    thumbContainer.innerHTML='';
    images = [];
    const files = e.target.files;
    for(let i=0;i<files.length;i++){
      const file = files[i];
      const url = URL.createObjectURL(file);
      images.push(file);
      const img = document.createElement('img');
      img.src = url;
      thumbContainer.appendChild(img);
    }
  });

  // Convert to PDF
  async function convertToPDF(){
    if(images.length==0){ alert('Please upload images'); return;}
    const { jsPDF } = window.jspdf;
    const pdf = new jsPDF();

    for(let i=0;i<images.length;i++){
      const file = images[i];
      const imgData = await toBase64(file);
      const img = new Image();
      img.src = imgData;
      await new Promise(resolve => {
        img.onload=function(){
          const imgWidth = pdf.internal.pageSize.getWidth();
          const imgHeight = (img.height*imgWidth)/img.width;
          if(i>0) pdf.addPage();
          pdf.addImage(img,'JPEG',0,0,imgWidth,imgHeight);
          resolve();
        }
      });
    }
    pdf.save('converted.pdf');
  }

  function toBase64(file){
    return new Promise((resolve,reject)=>{
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload=()=>resolve(reader.result);
      reader.onerror=err=>reject(err);
    });
  }
</script>

</body>
</html>

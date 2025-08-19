[image_to_pdf.html](https://github.com/user-attachments/files/21854673/image_to_pdf.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Image to PDF Converter</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
      background: #f9f9f9;
    }
    h1 {
      color: #333;
    }
    input[type="file"] {
      margin: 20px 0;
    }
    button {
      padding: 10px 20px;
      border: none;
      border-radius: 6px;
      background: #007BFF;
      color: white;
      font-size: 16px;
      cursor: pointer;
    }
    button:hover {
      background: #0056b3;
    }
  </style>
</head>
<body>
  <h1>Image to PDF Converter</h1>
  <input type="file" id="imageUpload" accept="image/*" multiple>
  <button onclick="convertToPDF()">Convert to PDF</button>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <script>
    async function convertToPDF() {
      const { jsPDF } = window.jspdf;
      const pdf = new jsPDF();
      const files = document.getElementById('imageUpload').files;

      if (files.length === 0) {
        alert('Please upload at least one image');
        return;
      }

      for (let i = 0; i < files.length; i++) {
        const file = files[i];
        const imgData = await toBase64(file);
        const img = new Image();
        img.src = imgData;
        await new Promise(resolve => {
          img.onload = function() {
            const imgWidth = pdf.internal.pageSize.getWidth();
            const imgHeight = (img.height * imgWidth) / img.width;
            if (i > 0) pdf.addPage();
            pdf.addImage(img, 'JPEG', 0, 0, imgWidth, imgHeight);
            resolve();
          };
        });
      }
      pdf.save('converted.pdf');
    }

    function toBase64(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => resolve(reader.result);
        reader.onerror = error => reject(error);
      });
    }
  </script>
</body>
</html>

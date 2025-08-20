<!DOCTYPE html><html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Emissor de Recibo</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f5f5f5;
      display: flex;
      justify-content: center;
      padding: 20px;
    }
    #recibo {
      background: white;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 0 8px rgba(0,0,0,0.2);
      width: 800px;
      max-width: 100%;
    }
    .header {
      width: 100%;
      margin-bottom: 15px;
    }
    .logo {
      width: 100%;      /* ocupa todo o cabeçalho */
      height: 80px;     /* altura fixa */
      object-fit: contain; /* garante proporção */
      display: block;
    }
    .field {
      margin: 8px 0;
    }
    label {
      font-weight: bold;
      display: block;
    }
    input, textarea {
      width: 100%;
      border: none;
      border-bottom: 1px solid #ccc;
      padding: 5px;
      font-size: 14px;
    }
    textarea {
      resize: vertical;
    }
    .signature {
      margin-top: 40px;
      text-align: center;
    }
    .buttons {
      text-align: center;
      margin-top: 20px;
    }
    button {
      margin: 5px;
      padding: 10px 20px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      background: #007bff;
      color: white;
      font-size: 14px;
    }
    button:hover {
      background: #0056b3;
    }
    #qrcode {
      margin-top: 10px;
      text-align: center;
      display: none; /* oculto até ter pix */
    }
  </style>
</head>
<body>
<div id="recibo">
  <div class="header">
    <img id="logoPreview" class="logo" alt="Logo da empresa">
  </div>
  <div class="field"><label for="numero">Recibo nº:</label> <input id="numero" value="1"></div>
  <div class="field"><label for="data">Data:</label> <input id="data" type="date"></div>
  <div class="field"><label for="codigoProjeto">Código do Projeto:</label> <input id="codigoProjeto"></div>
  <div class="field"><label for="nomeProjeto">Nome do Projeto:</label> <input id="nomeProjeto"></div>
  <div class="field"><label for="valor">Valor:</label> <input id="valor"></div>
  <div class="field"><label for="cliente">Cliente (Razão Social / Nome):</label> <input id="cliente"></div>
  <div class="field"><label for="endereco">Endereço:</label> <input id="endereco"></div>
  <div class="field"><label for="documento">CNPJ/CPF:</label> <input id="documento"></div>
  <div class="field"><label for="descricao">Descrição:</label> <textarea id="descricao"></textarea></div>
  <div class="field"><label for="banco">Dados Bancários:</label> <input id="banco" placeholder="Banco, Agência, Conta"></div>
  <div class="field">
    <label for="pix">Pix (copia e cola):</label>
    <input id="pix" oninput="gerarQRCode()">
    <div id="qrcode"></div>
  </div>
  <div class="signature">
    ___________________________________<br>
    Assinatura: Guilherme Rosa Ramos da Hora <br>
    Contato: +55 27 99750-9056
  </div>
</div>
<div class="buttons">
  <input type="file" id="logoInput" accept="image/*">
  <button onclick="gerarPDF()">Exportar PDF</button>
  <button onclick="novoRecibo()">Novo Recibo</button>
</div>
<script>
  // Data atual no carregamento inicial
  document.addEventListener("DOMContentLoaded", () => {
    document.getElementById("data").value = new Date().toISOString().substr(0, 10);
  });// Preview da logo sem mostrar o caminho document.getElementById('logoInput').addEventListener('change', function(e) { const file = e.target.files[0]; if (file) { const reader = new FileReader(); reader.onload = function(evt) { const logo = document.getElementById('logoPreview'); logo.src = evt.target.result; logo.style.display = "block"; } reader.readAsDataURL(file); } });

// Formatar valor em moeda BRL document.getElementById("valor").addEventListener("input", function(e) { let valor = e.target.value.replace(/\D/g, ""); if (valor) { valor = (valor / 100).toLocaleString("pt-BR", { style: "currency", currency: "BRL" }); e.target.value = valor; } else { e.target.value = ""; } });

// Gerar QR Code Pix function gerarQRCode() { const pix = document.getElementById("pix").value; const qrcodeDiv = document.getElementById("qrcode"); qrcodeDiv.innerHTML = ""; // limpa antes de gerar if (pix.trim() !== "") { qrcodeDiv.style.display = "block"; new QRCode(qrcodeDiv, { text: pix, width: 128, height: 128 }); } else { qrcodeDiv.style.display = "none"; } }

// Validação de campos obrigatórios function validarCampos() { const obrigatorios = ["cliente", "valor", "data"]; for (let id of obrigatorios) { if (!document.getElementById(id).value.trim()) { alert(Preencha o campo: ${id}); return false; } } return true; }

// Gerar PDF com margens corretas function gerarPDF() { if (!validarCampos()) return;

const recibo = document.getElementById("recibo");
const opt = {
  margin: [10,10,10,10],
  filename: `recibo_${document.getElementById('numero').value}.pdf`,
  image: { type: 'jpeg', quality: 0.98 },
  html2canvas: { scale: 2 },
  jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
};
html2pdf().set(opt).from(recibo).save();

}

// Criar novo recibo com número sequencial function novoRecibo() { let numero = parseInt(document.getElementById("numero").value) || 0; document.getElementById("numero").value = numero + 1; document.getElementById("data").value = new Date().toISOString().substr(0, 10); document.getElementById("codigoProjeto").value = ""; document.getElementById("nomeProjeto").value = ""; document.getElementById("valor").value = ""; document.getElementById("cliente").value = ""; document.getElementById("endereco").value = ""; document.getElementById("documento").value = ""; document.getElementById("descricao").value = ""; document.getElementById("banco").value = ""; document.getElementById("pix").value = ""; document.getElementById("qrcode").innerHTML = ""; document.getElementById("qrcode").style.display = "none"; } </script>

</body>
</html>

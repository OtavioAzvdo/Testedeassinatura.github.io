<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Assinatura Digital</title>
  <style>
    body {
      font-family: 'Poppins', sans-serif;
      background: #f5f6fa;
      color: #2f3640;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      min-height: 100vh;
    }
    form {
      background: white;
      padding: 24px;
      border-radius: 16px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
      width: 320px;
    }
    canvas {
      border: 2px solid #dcdde1;
      border-radius: 8px;
      margin: 12px 0;
      width: 100%;
      height: 150px;
    }
    button {
      background: #00a8ff;
      color: white;
      border: none;
      padding: 10px 16px;
      border-radius: 8px;
      cursor: pointer;
      width: 100%;
      font-size: 16px;
    }
    button:hover {
      background: #0097e6;
    }
    #msg {
      margin-top: 16px;
      text-align: center;
    }
  </style>
</head>
<body>

  <form id="assinaturaForm">
    <h2>Assinar Confirmação</h2>

    <label>Nome:</label><br>
    <input type="text" id="nome" required style="width:100%;padding:8px;border-radius:8px;border:1px solid #ccc;"><br><br>

    <label>Email:</label><br>
    <input type="email" id="email" required style="width:100%;padding:8px;border-radius:8px;border:1px solid #ccc;"><br><br>

    <label>Assinatura:</label>
    <canvas id="canvas"></canvas><br>
    <button type="button" id="limpar">Limpar Assinatura</button><br><br>
    <button type="submit">Enviar</button>

    <div id="msg"></div>
  </form>

  <script>
    const scriptURL = "https://script.google.com/macros/s/AKfycbx4081t8m4s92HcpGRdZmYBXOnu-QMJpfkNmNAWrpBG97ngmVMKqtsH2Jbftdqqc7Al/exec";

    const form = document.getElementById('assinaturaForm');
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    let desenhando = false;

    canvas.addEventListener('mousedown', () => desenhando = true);
    canvas.addEventListener('mouseup', () => desenhando = false);
    canvas.addEventListener('mouseleave', () => desenhando = false);
    canvas.addEventListener('mousemove', desenhar);

    function desenhar(e) {
      if (!desenhando) return;
      ctx.lineWidth = 2;
      ctx.lineCap = 'round';
      ctx.strokeStyle = '#2f3640';
      const rect = canvas.getBoundingClientRect();
      ctx.lineTo(e.clientX - rect.left, e.clientY - rect.top);
      ctx.stroke();
      ctx.beginPath();
      ctx.moveTo(e.clientX - rect.left, e.clientY - rect.top);
    }

    document.getElementById('limpar').addEventListener('click', () => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.beginPath();
    });

    form.addEventListener('submit', async (e) => {
      e.preventDefault();

      const nome = document.getElementById('nome').value.trim();
      const email = document.getElementById('email').value.trim();
      const assinaturaBase64 = canvas.toDataURL();

      document.getElementById('msg').innerText = "Enviando...";

      try {
        const res = await fetch(scriptURL, {
          method: 'POST',
          body: JSON.stringify({ nome, email, assinaturaBase64 }),
          headers: { 'Content-Type': 'application/json' }
        });
        const data = await res.json();

        if (data.status === "success") {
          document.getElementById('msg').innerHTML = "✅ Assinatura enviada com sucesso!";
        } else {
          document.getElementById('msg').innerHTML = "❌ Erro ao enviar.";
        }
      } catch (err) {
        document.getElementById('msg').innerHTML = "⚠️ Falha na conexão com o servidor.";
        console.error(err);
      }
    });
  </script>

</body>
</html>

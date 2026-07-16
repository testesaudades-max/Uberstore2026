<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplicativo do Motorista</title>
    <style>
        body { font-family: sans-serif; margin: 0; background-color: #f4f4f9; }
        
        /* Menu de Navegação */
        nav { background: #333; color: white; padding: 10px; display: flex; gap: 5px; overflow-x: auto; }
        button.menu-btn { padding: 8px; cursor: pointer; border: none; background: #555; color: white; border-radius: 4px; font-size: 12px; }
        
        /* Containers */
        .tab-content { display: none; padding: 20px; }
        .active { display: block; }
        
        /* Estilo da Solicitação */
        .container { background: white; padding: 20px; border-radius: 20px; width: 90%; max-width: 450px; margin: 20px auto; box-shadow: 0 10px 25px rgba(0,0,0,0.2); }
        h2 { text-align: center; color: #333; }
        .input-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-weight: bold; color: #555; }
        input, select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        #mapa-container { width: 100%; height: 100px; background: #eee; margin-bottom: 10px; border-radius: 10px; display: flex; justify-content: space-around; align-items: center; border: 2px dashed #6e8efb; }
        .ponto { width: 40px; height: 40px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; color: white; font-size: 8px; text-align: center; font-weight: bold; }
        #btnConfirmar { width: 100%; padding: 15px; background: #25d366; color: white; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; }

        /* Estilo de Tabelas */
        table { width: 100%; border-collapse: collapse; margin-top: 10px; background: white; }
        th, td { border: 1px solid #ddd; padding: 10px; text-align: left; }
        th { background-color: #f2f2f2; }
    </style>
</head>
<body>

<nav>
    <button class="menu-btn" onclick="showTab('solicitar')">Solicitar</button>
    <button class="menu-btn" onclick="showTab('corrida')">Rel. Corrida</button>
    <button class="menu-btn" onclick="showTab('navegacao')">Rel. Navegação</button>
</nav>

<!-- ABA SOLICITAR -->
<div id="solicitar" class="tab-content active">
    <div class="container">
        <h2>Solicitar Corrida</h2>
        <div class="input-group">
            <label>👤 Seu Nome:</label>
            <input type="text" id="nomeCliente" placeholder="Digite seu nome">
        </div>
        <div id="mapa-container">
            <div class="ponto" style="background: #ff5733;" onclick="selecionar('Vargem Alegre - R$ 10,00')">Vargem</div>
            <div class="ponto" style="background: #33ff57;" onclick="selecionar('Dorandia - R$ 20,00')">Dorandia</div>
            <div class="ponto" style="background: #3357ff;" onclick="selecionar('Pinheiral - R$ 40,00')">Pinheiral</div>
            <div class="ponto" style="background: #ff33a8;" onclick="selecionar('Barra do Piraí - R$ 50,00')">Barra</div>
        </div>
        <select id="destino">
            <option value="" disabled selected>Selecione um destino...</option>
            <option value="Vargem Alegre - R$ 10,00">Vargem Alegre - R$ 10,00</option>
            <option value="Dorandia - R$ 20,00">Dorandia - R$ 20,00</option>
            <option value="Pinheiral - R$ 40,00">Pinheiral - R$ 40,00</option>
            <option value="Barra do Piraí - R$ 50,00">Barra do Piraí - R$ 50,00</option>
        </select>
        <br><br>
        <button id="btnConfirmar" onclick="processarCorrida()">Confirmar e Enviar</button>
    </div>
</div>

<!-- ABA RELATÓRIO DE CORRIDA -->
<div id="corrida" class="tab-content">
    <h2>Relatório de Corrida</h2>
    <table id="tabelaCorrida">
        <tr><th>Cliente</th><th>Destino</th><th>Valor</th></tr>
    </table>
</div>

<!-- ABA RELATÓRIO DE NAVEGAÇÃO -->
<div id="navegacao" class="tab-content">
    <h2>Relatório de Navegação</h2>
    <table id="tabelaNavegacao">
        <tr><th>Hora</th><th>Destino</th></tr>
    </table>
</div>

<script>
    function showTab(tabId) {
        document.querySelectorAll('.tab-content').forEach(tab => tab.style.display = 'none');
        document.getElementById(tabId).style.display = 'block';
    }

    function selecionar(valor) { document.getElementById('destino').value = valor; }

    function processarCorrida() {
        const nome = document.getElementById('nomeCliente').value;
        const destinoCompleto = document.getElementById('destino').value;
        
        if(!nome || !destinoCompleto) {
            alert("⚠️ Preencha nome e destino!");
            return;
        }

        const partes = destinoCompleto.split(' - ');
        const destino = partes[0];
        const valor = partes[1];
        
        // Atualiza tabelas
        document.getElementById('tabelaCorrida').innerHTML += `<tr><td>${nome}</td><td>${destino}</td><td>${valor}</td></tr>`;
        document.getElementById('tabelaNavegacao').innerHTML += `<tr><td>${new Date().toLocaleTimeString()}</td><td>${destino}</td></tr>`;

        // Monta mensagem para WhatsApp
        const numeroMotorista = "5524999350830";
        const linkRota = `https://www.google.com/maps/dir/?api=1&destination=${encodeURIComponent(destino)}`;
        
        const mensagemRelatorio = `*--- NOVA SOLICITAÇÃO ---*
👤 *Cliente:* ${nome}
📍 *Destino:* ${destino}
💰 *Taxímetro (Valor):* ${valor}
✅ *Status:* Confirmado
🔗 *Rota:* ${linkRota}`;
        
        // Abre o WhatsApp
        window.open(`https://wa.me/${numeroMotorista}?text=${encodeURIComponent(mensagemRelatorio)}`, '_blank');
        
        alert("Solicitação enviada com sucesso!");
    }
</script>

</body>
</html>

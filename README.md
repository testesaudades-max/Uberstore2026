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
        
        #mapa-container { width: 100%; height: 100px; background: #eee; margin-bottom: 10px; border-radius: 10px; display: flex; justify-content: space-around; align-items: center; border: 2px dashed #6e8efb; transition: all 0.3s; }
        
        /* Animação de pulsar azul */
        .piscando { border: 3px solid #007bff !important; background-color: #e7f1ff !important; animation: pulsar 1.5s infinite; }
        @keyframes pulsar {
            0% { box-shadow: 0 0 0 0 rgba(0, 123, 255, 0.7); }
            70% { box-shadow: 0 0 0 15px rgba(0, 123, 255, 0); }
            100% { box-shadow: 0 0 0 0 rgba(0, 123, 255, 0); }
        }

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
        <div class="input-group">
            <label>📍 Sua Localização (Endereço):</label>
            <input type="text" id="localizacaoCliente" placeholder="Digite onde você está">
        </div>
        <div id="mapa-container">
            <div class="ponto" style="background: #ff5733;" onclick="selecionar('Vargem Alegre - R$ 10,00')">Vargem</div>
            <div class="ponto" style="background: #33ff57;" onclick="selecionar('Dorandia - R$ 20,00')">Dorandia</div>
            <div class="ponto" style="background: #3357ff;" onclick="selecionar('Pinheiral - R$ 40,00')">Pinheiral</div>
            <div class="ponto" style="background: #ff33a8;" onclick="selecionar('Barra do Piraí - R$ 50,00')">Barra</div>
        </div>
        
        <div class="input-group">
            <label>🏁 Destino:</label>
            <select id="destino" onchange="limparManual()">
                <option value="" disabled selected>Selecione um destino...</option>
                <option value="Vargem Alegre - R$ 10,00">Vargem Alegre - R$ 10,00</option>
                <option value="Dorandia - R$ 20,00">Dorandia - R$ 20,00</option>
                <option value="Pinheiral - R$ 40,00">Pinheiral - R$ 40,00</option>
                <option value="Barra do Piraí - R$ 50,00">Barra do Piraí - R$ 50,00</option>
            </select>
            <input type="text" id="destinoManual" placeholder="Ou digite o destino aqui..." style="margin-top: 10px;" oninput="limparSelect()">
        </div>
        
        <br>
        <button id="btnConfirmar" onclick="processarCorrida()">Confirmar e Enviar</button>
    </div>
</div>

<div id="corrida" class="tab-content">
    <h2>Relatório de Corrida</h2>
    <table id="tabelaCorrida">
        <tr><th>Cliente</th><th>Destino</th><th>Valor</th></tr>
    </table>
</div>

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

    function selecionar(valor) { 
        document.getElementById('destino').value = valor; 
        document.getElementById('destinoManual').value = "";
    }

    function limparManual() { document.getElementById('destinoManual').value = ""; }
    function limparSelect() { document.getElementById('destino').value = ""; }

    function processarCorrida() {
        const nome = document.getElementById('nomeCliente').value;
        const local = document.getElementById('localizacaoCliente').value;
        const destinoSelecionado = document.getElementById('destino').value;
        const destinoManual = document.getElementById('destinoManual').value;
        
        let destinoFinal = destinoSelecionado || destinoManual;
        
        if(!nome || !local || !destinoFinal) {
            alert("⚠️ Preencha nome, localização e destino!");
            return;
        }

        const mapa = document.getElementById('mapa-container');
        mapa.classList.add('piscando');

        let destinoTexto = destinoFinal;
        let valorTexto = "A combinar";
        if(destinoFinal.includes(' - ')) {
            const partes = destinoFinal.split(' - ');
            destinoTexto = partes[0];
            valorTexto = partes[1];
        }
        
        document.getElementById('tabelaCorrida').innerHTML += `<tr><td>${nome}</td><td>${destinoTexto}</td><td>${valorTexto}</td></tr>`;
        document.getElementById('tabelaNavegacao').innerHTML += `<tr><td>${new Date().toLocaleTimeString()}</td><td>${destinoTexto}</td></tr>`;

        const numeroMotorista = "5524999350830";
        const linkRota = `https://www.google.com/maps/dir/?api=1&origin=${encodeURIComponent(local)}&destination=${encodeURIComponent(destinoTexto)}`;
        
        const mensagemRelatorio = `*--- STATUS DA CORRIDA ---*
👤 *Cliente:* ${nome}
📍 *Local de Partida:* ${local}
🏁 *Destino:* ${destinoTexto}
💰 *Valor:* ${valorTexto}
✅ *Status:* O motorista aceitou a corrida e já está a caminho!
🔗 *Acompanhe a rota em tempo real:* ${linkRota}`;
        
        // Redireciona diretamente na mesma aba corrigindo o bloqueio de pop-up
        window.location.href = `https://wa.me/${numeroMotorista}?text=${encodeURIComponent(mensagemRelatorio)}`;
    }
</script>

</body>
</html>

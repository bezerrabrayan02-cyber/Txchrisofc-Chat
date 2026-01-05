<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>TX CHRIS - CHAT</title>

<style>
body {
    background:#0e0e0e;
    color:#fff;
    font-family: Arial, sans-serif;
    margin:0;
}
header {
    background:#111;
    padding:15px;
    text-align:center;
    color:#00ffcc;
    font-size:22px;
    font-weight:bold;
}
.box {
    background:#1a1a1a;
    margin:15px;
    padding:15px;
    border-radius:8px;
}
#chat {
    height:220px;
    overflow-y:auto;
    background:#000;
    padding:10px;
    border-radius:5px;
}
.msg {
    background:#222;
    padding:6px;
    border-radius:4px;
    margin-bottom:6px;
    font-size:14px;
}
input, button {
    width:100%;
    padding:10px;
    margin-top:8px;
    border:none;
    border-radius:5px;
    font-size:15px;
}
button {
    background:#00ffcc;
    color:#000;
    font-weight:bold;
}
.hidden {
    display:none;
}
.admin {
    border:1px solid red;
}
</style>
</head>

<body>

<header>TX CHRIS - CHAT</header>

<div class="box">
    <h3>💬 Chat</h3>
    <div id="chat"></div>

    <input id="nome" placeholder="Seu nome">
    <input id="mensagem" placeholder="Mensagem">

    <button onclick="enviar()">ENVIAR</button>
</div>

<div class="box">
    <button onclick="abrirAdmin()">ENTRAR COMO ADMIN</button>
</div>

<div class="box admin hidden" id="adminPanel">
    <h3>🔐 PAINEL ADMIN</h3>
    <button onclick="apagarTudo()">APAGAR TODAS MENSAGENS</button>
</div>

<script>
// ===== CONFIG =====
const SENHA_ADM = "txchrisofcadm1234567890";

// ===== CHAT =====
let mensagens = JSON.parse(localStorage.getItem("chat_msgs")) || [];

// ===== NOME SALVO =====
let nomeUsuario = localStorage.getItem("chat_nome") || "";
document.getElementById("nome").value = nomeUsuario;

// ===== FORMATAR CORES =====
function formatarCor(texto) {
    let match = texto.match(/\[([0-9A-Fa-f]{6})\](.*)/);
    if (match) {
        return `<span style="color:#${match[1]}">${match[2]}</span>`;
    }
    return texto;
}

function atualizarChat() {
    let chat = document.getElementById("chat");
    chat.innerHTML = "";

    mensagens.forEach(t => {
        let div = document.createElement("div");
        div.className = "msg";
        div.innerHTML = formatarCor(t);
        chat.appendChild(div);
    });

    chat.scrollTop = chat.scrollHeight;
}

atualizarChat();

// ===== ENVIAR =====
function enviar() {
    let nome = document.getElementById("nome").value.trim();
    let msg  = document.getElementById("mensagem").value.trim();

    if (!nome || !msg) {
        alert("Preencha tudo");
        return;
    }

    // SALVAR NOME
    localStorage.setItem("chat_nome", nome);

    mensagens.push(nome + ": " + msg);
    localStorage.setItem("chat_msgs", JSON.stringify(mensagens));

    document.getElementById("mensagem").value = "";
    atualizarChat();
}

// ===== ADMIN =====
function abrirAdmin() {
    let senha = prompt("Senha do Admin:");
    if (senha === SENHA_ADM) {
        document.getElementById("adminPanel").classList.remove("hidden");
        alert("Admin liberado");
    } else {
        alert("Senha incorreta");
    }
}

function apagarTudo() {
    if (confirm("Apagar TODAS as mensagens?")) {
        mensagens = [];
        localStorage.removeItem("chat_msgs");
        atualizarChat();
        alert("Chat limpo");
    }
}
</script>

</body>
</html>

<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mini Rede Social</title>
<style>
body { font-family: Arial, sans-serif; background:#1a1a1a; color:#fff; margin:0; padding:0;}
header { background:#0f0f0f; padding:15px; text-align:center; font-size:24px; color:#00ffcc; font-weight:bold;}
.container { padding:15px; }
input, button { width:100%; padding:10px; margin:5px 0; border-radius:5px; border:none; font-size:16px;}
button { background:#00ffcc; color:#000; font-weight:bold; cursor:pointer;}
.user { display:flex; align-items:center; background:#222; margin:5px 0; padding:5px; border-radius:5px; cursor:pointer;}
.user img { width:40px; height:40px; border-radius:50%; margin-right:10px; }
.follow { margin-left:auto; background:#ffcc00; color:#000; padding:2px 6px; border-radius:3px; cursor:pointer;}
#chat { background:#000; max-height:300px; overflow-y:auto; padding:10px; border-radius:5px; margin-top:10px;}
.msg { background:#222; padding:5px; margin-bottom:5px; border-radius:4px;}
.hidden { display:none; }
</style>
</head>
<body>

<header>TX CHRIS SOCIAL</header>
<div class="container">

<!-- TELA DE LOGIN -->
<div id="loginBox">
    <h3>💬 Entrar</h3>
    <input id="nome" placeholder="Escolha seu nome único">
    <input id="foto" placeholder="URL da foto do perfil (opcional)">
    <button onclick="entrar()">ENTRAR</button>
    <p id="loginMsg" style="color:#ff4d4d;"></p>
</div>

<!-- TELA SOCIAL -->
<div id="socialBox" class="hidden">
    <h3>🔍 Pesquisar usuário</h3>
    <input id="pesquisa" placeholder="Digite o nome do usuário">
    <button onclick="pesquisar()">PESQUISAR</button>
    <div id="resultadoPesquisa"></div>

    <h3>Usuários</h3>
    <div id="usuarios"></div>

    <div id="chatBox" class="hidden">
        <h3>Chat privado com <span id="chatUsuario"></span></h3>
        <div id="chat"></div>
        <input id="mensagem" placeholder="Digite sua mensagem">
        <button onclick="enviar()">ENVIAR</button>
    </div>
</div>

<script>
// ===== VARIÁVEIS =====
let usuario = null; // usuário logado
let usuarios = JSON.parse(localStorage.getItem("usuarios")||"[]"); // lista de usuários
let mensagens = JSON.parse(localStorage.getItem("mensagens")||"[]"); // mensagens privadas
let chatCom = null; // usuário com quem está conversando

// ===== SALVAR NO LOCALSTORAGE =====
function salvarLocalStorage() {
    localStorage.setItem("usuarios", JSON.stringify(usuarios));
    localStorage.setItem("mensagens", JSON.stringify(mensagens));
}

// ===== LOGIN =====
function entrar(){
    let nome = document.getElementById("nome").value.trim();
    let foto = document.getElementById("foto").value.trim() || "https://via.placeholder.com/40";
    if(!nome){ alert("Digite um nome"); return; }
    if(usuarios.some(u=>u.nome===nome)){
        document.getElementById("loginMsg").innerText="Nome já existe!";
        return;
    }
    usuario = {nome:nome, foto:foto, seguidores:0, seguindo:[]};
    usuarios.push(usuario);
    salvarLocalStorage();
    document.getElementById("loginBox").classList.add("hidden");
    document.getElementById("socialBox").classList.remove("hidden");
    atualizarUsuarios();
}

// ===== ATUALIZAR LISTA DE USUÁRIOS =====
function atualizarUsuarios(){
    const div = document.getElementById("usuarios");
    div.innerHTML="";
    usuarios.forEach(u=>{
        if(u.nome!==usuario.nome){
            let el = document.createElement("div");
            el.className="user";
            el.innerHTML=`<img src="${u.foto}"><span>${u.nome} (${u.seguidores} seguidores)</span>`;
            let btn = document.createElement("button");
            btn.className="follow";
            btn.innerText=usuario.seguindo.includes(u.nome)?"Seguindo":"Seguir";
            btn.onclick = (e)=>{ 
                e.stopPropagation(); // impede abrir chat ao clicar no botão
                seguir(u.nome);
            };
            el.appendChild(btn);
            el.onclick=()=>abrirChat(u.nome);
            div.appendChild(el);
        }
    });
}

// ===== SEGUIR USUÁRIO =====
function seguir(nome){
    if(!usuario.seguindo.includes(nome)){
        usuario.seguindo.push(nome);
        let u = usuarios.find(x=>x.nome===nome);
        u.seguidores++;
        salvarLocalStorage();
        atualizarUsuarios();
    }
}

// ===== PESQUISAR USUÁRIO =====
function pesquisar(){
    let nomePesq = document.getElementById("pesquisa").value.trim().toLowerCase();
    const resDiv = document.getElementById("resultadoPesquisa");
    resDiv.innerHTML="";
    if(!nomePesq){ resDiv.innerHTML="Digite algo"; return; }
    let encontrado = usuarios.find(u=>u.nome.toLowerCase()===nomePesq);
    if(encontrado){
        resDiv.innerHTML=`<div class="user" onclick="abrirChat('${encontrado.nome}')"><img src="${encontrado.foto}">${encontrado.nome} (${encontrado.seguidores} seguidores)</div>`;
    } else resDiv.innerHTML="Usuário não encontrado";
}

// ===== ABRIR CHAT =====
function abrirChat(nome){
    chatCom = nome;
    document.getElementById("chatUsuario").innerText=nome;
    document.getElementById("chatBox").classList.remove("hidden");
    atualizarChat();
}

// ===== ENVIAR MENSAGEM =====
function enviar(){
    let msg = document.getElementById("mensagem").value.trim();
    if(!msg || !chatCom) return;
    mensagens.push({de:usuario.nome, para:chatCom, msg:msg});
    salvarLocalStorage();
    document.getElementById("mensagem").value="";
    atualizarChat();
}

// ===== ATUALIZAR CHAT =====
function atualizarChat(){
    if(!chatCom) return;
    const chatDiv = document.getElementById("chat");
    chatDiv.innerHTML="";
    mensagens.forEach(m=>{
        if((m.de===usuario.nome && m.para===chatCom) || (m.de===chatCom && m.para===usuario.nome)){
            let div = document.createElement("div");
            div.className="msg";
            div.innerHTML=`<b>${m.de}:</b> ${m.msg}`;
            chatDiv.appendChild(div);
        }
    });
    chatDiv.scrollTop = chatDiv.scrollHeight;
}

// Atualiza lista a cada 5s
setInterval(atualizarUsuarios,5000);

</script>
</div>
</body>
</html>

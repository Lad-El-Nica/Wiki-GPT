<!-- ================= FRONTEND (index.html) ================= -->
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Wiki GPT Enterprise + IA</title>
<style>
body{margin:0;font-family:sans-serif;display:flex}
nav{width:260px;background:#f4f4f4;padding:10px;height:100vh;overflow:auto}
main{flex:1;padding:20px}
.page{cursor:pointer;margin:5px 0;color:blue}
textarea{width:100%;height:200px}
button{margin:3px}
</style>
</head>
<body>

<nav>
<button onclick="crearPagina()">➕ Nueva</button>
<button onclick="editar()">✏️ Editar</button>
<button onclick="guardar()">💾 Guardar</button>
<button onclick="generarIA()">🤖 Generar IA</button>
<button onclick="expandirIA()">🧠 Expandir</button>
<div id="lista"></div>
</nav>

<main>
<h1 id="titulo"></h1>
<div id="contenido"></div>
<textarea id="editor" style="display:none"></textarea>
</main>

<script>
let data = JSON.parse(localStorage.getItem('wiki')) || {
 paginas:{"Inicio":{contenido:"Bienvenido"}}
};

let actual="Inicio";

function save(){ localStorage.setItem('wiki',JSON.stringify(data)); }

function render(t){
 return t.replace(/\[\[(.*?)\]\]/g,(m,n)=>{
  if(!data.paginas[n]) data.paginas[n]={contenido:"Nueva página"};
  return `<span onclick="load('${n}')" style='color:blue;cursor:pointer'>${n}</span>`;
 });
}

function load(n){
 actual=n;
 titulo.innerText=n;
 contenido.innerHTML=render(data.paginas[n].contenido);
 editor.style.display='none';
 lista();
}

function lista(){
 lista.innerHTML='';
 for(let p in data.paginas){
  lista.innerHTML+=`<div class='page' onclick="load('${p}')">${p}</div>`;
 }
}

function crearPagina(){
 let n=prompt("Nombre");
 if(!n)return;
 data.paginas[n]={contenido:"Nueva página"};
 save(); load(n);
}

function editar(){
 editor.style.display='block';
 editor.value=data.paginas[actual].contenido;
}

function guardar(){
 data.paginas[actual].contenido=editor.value;
 save(); load(actual);
}

async function generarIA(){
 let tema=prompt("Tema");
 if(!tema)return;
 let r=await fetch("http://localhost:3000/ia",{
  method:"POST",
  headers:{"Content-Type":"application/json"},
  body:JSON.stringify({tema})
 });
 let d=await r.json();
 data.paginas[tema]={contenido:d.texto};
 save(); load(tema);
}

async function expandirIA(){
 let r=await fetch("http://localhost:3000/ia",{
  method:"POST",
  headers:{"Content-Type":"application/json"},
  body:JSON.stringify({tema:data.paginas[actual].contenido})
 });
 let d=await r.json();
 data.paginas[actual].contenido=d.texto;
 save(); load(actual);
}

load(actual);
</script>
</body>
</html>


<!-- ================= BACKEND (server.js) ================= -->

/*
import express from "express";
import fetch from "node-fetch";
import cors from "cors";

const app = express();
app.use(cors());
app.use(express.json());

app.post("/ia", async (req, res) => {
  const { tema } = req.body;

  const respuesta = await fetch("https://api.openai.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Authorization": "Bearer TU_API_KEY",
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      model: "gpt-4.1-mini",
      messages: [
        { role: "system", content: "Escribe como Wikipedia" },
        { role: "user", content: tema }
      ]
    })
  });

  const data = await respuesta.json();
  res.json({ texto: data.choices[0].message.content });
});

app.listen(3000, () => console.log("Servidor IA listo 🚀"));
*/

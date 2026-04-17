<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Embaixadores do Rei</title>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database-compat.js"></script>

<style>
body {
  font-family: Arial;
  background: #f0f4ff;
  margin: 0;
}

/* TOPO */
.header {
  text-align: center;
  background: #0d47a1;
  color: white;
  padding: 20px;
  border-radius: 0 0 20px 20px;
}

.header img {
  width: 120px;
  margin-bottom: 10px;
}

/* FORM */
form {
  background: white;
  padding: 15px;
  border-radius: 12px;
  margin: 15px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}

input {
  display: block;
  margin: 8px 0;
  padding: 10px;
  width: 100%;
  border-radius: 6px;
  border: 1px solid #ccc;
}

/* BOTÕES */
button {
  padding: 8px 10px;
  margin: 3px;
  border: none;
  cursor: pointer;
  border-radius: 6px;
  font-weight: bold;
}

.salvar { background: #fdd835; }
.presente { background: #2e7d32; color: white; }
.falta { background: #c62828; color: white; }
.excluir { background: #1565c0; color: white; }

/* DATA */
.data-box {
  background: white;
  padding: 10px;
  border-radius: 10px;
  margin: 15px;
  text-align: center;
}

/* CARDS */
.card {
  background: white;
  padding: 15px;
  border-radius: 15px;
  margin: 10px 15px;
  box-shadow: 0 3px 8px rgba(0,0,0,0.1);
  border-left: 6px solid #fdd835;
}

.historico {
  font-size: 13px;
  margin-top: 10px;
  background: #f9f9f9;
  padding: 8px;
  border-radius: 8px;
}
</style>

</head>

<body>

<div class="header">
  <img src="logo.png">
  <h1>Embaixadores do Rei</h1>
</div>

<form id="form">
  <input type="text" id="nome" placeholder="Nome" required>
  <input type="date" id="nascimento" required>
  <input type="number" id="tarefa" placeholder="Número da tarefa">
  <input type="text" id="telefone" placeholder="Telefone">
  <button class="salvar" type="submit">Salvar</button>
</form>

<div class="data-box">
  <strong>Data da reunião:</strong>
  <input type="date" id="dataReuniao">
</div>

<div id="lista"></div>

<script>

const firebaseConfig = {
  apiKey: "AIzaSyAe_fXCz68AZasK5zqg9NDXokw2zmNgFqw",
  authDomain: "embaixadores-c85f8.firebaseapp.com",
  databaseURL: "https://embaixadores-c85f8-default-rtdb.firebaseio.com",
  projectId: "embaixadores-c85f8",
  storageBucket: "embaixadores-c85f8.firebasestorage.app",
  messagingSenderId: "404118934484",
  appId: "1:404118934484:web:7ea1dac462b3ed82213c41"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

let dados = [];

db.ref("embaixadores").on("value", snapshot => {
  dados = [];
  snapshot.forEach(child => {
    dados.push({
      id: child.key,
      ...child.val()
    });
  });
  render();
});

function calcularFrequencia(pessoa) {
  let total = pessoa.presencas.length;
  let presencas = pessoa.presencas.filter(p => p.presente).length;
  if (total === 0) return "0%";
  return Math.round((presencas / total) * 100) + "%";
}

function render() {
  const lista = document.getElementById("lista");
  lista.innerHTML = "";

  dados.sort((a, b) => a.nome.localeCompare(b.nome));

  dados.forEach(p => {
    let total = p.presencas ? p.presencas.length : 0;
    let freq = p.presencas ? calcularFrequencia(p) : "0%";

    let historico = "";
    if (p.presencas) {
      p.presencas.forEach(h => {
        historico += `${h.data} - ${h.presente ? "✔" : "❌"}<br>`;
      });
    }

    lista.innerHTML += `
      <div class="card">
        <strong>${p.nome}</strong><br>
        📅 ${p.nascimento}<br>
        📌 Tarefa: ${p.tarefa}<br>
        📞 ${p.telefone}<br><br>

        ⭐ Frequência: ${p.presencas ? p.presencas.filter(x => x.presente).length : 0} / ${total} (${freq})<br><br>

        <button class="presente" onclick="marcarPresenca('${p.id}', true)">✔ Presente</button>
        <button class="falta" onclick="marcarPresenca('${p.id}', false)">❌ Falta</button><br><br>

        <div class="historico">
          <strong>Histórico:</strong><br>
          ${historico || "Sem registros"}
        </div><br>

        <button class="excluir" onclick="remover('${p.id}')">Excluir</button>
      </div>
    `;
  });
}

function marcarPresenca(id, valor) {
  let dataEscolhida = document.getElementById("dataReuniao").value;

  if (!dataEscolhida) {
    alert("Escolha a data!");
    return;
  }

  db.ref("embaixadores/" + id + "/presencas").once("value", snap => {
    let lista = snap.val() || [];

    lista.push({
      data: dataEscolhida,
      presente: valor
    });

    db.ref("embaixadores/" + id + "/presencas").set(lista);
  });
}

function remover(id) {
  if (confirm("Excluir?")) {
    db.ref("embaixadores/" + id).remove();
  }
}

document.getElementById("form").addEventListener("submit", function(e) {
  e.preventDefault();

  const pessoa = {
    nome: document.getElementById("nome").value,
    nascimento: document.getElementById("nascimento").value,
    tarefa: document.getElementById("tarefa").value,
    telefone: document.getElementById("telefone").value,
    presencas: []
  };

  db.ref("embaixadores").push(pessoa);
  this.reset();
});

</script>

</body>
</html>

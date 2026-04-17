<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Embaixadores do Rei</title>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database-compat.js"></script>

<style>
body { font-family: Arial; background: #f0f4ff; margin: 0; }

.header {
  text-align: center;
  background: #0d47a1;
  color: white;
  padding: 20px;
  border-radius: 0 0 20px 20px;
}

.header img { width: 120px; }

form {
  background: white;
  padding: 15px;
  border-radius: 12px;
  margin: 15px;
}

input {
  display: block;
  margin: 8px 0;
  padding: 10px;
  width: 100%;
}

button {
  padding: 8px;
  margin: 3px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
}

.salvar { background: #fdd835; }
.presente { background: green; color: white; }
.falta { background: red; color: white; }
.excluir { background: #1565c0; color: white; }
.editar { background: orange; }

.card {
  background: white;
  padding: 15px;
  border-radius: 12px;
  margin: 10px;
}

.historico {
  font-size: 13px;
  margin-top: 10px;
  background: #f9f9f9;
  padding: 8px;
  border-radius: 8px;
}

.data-box {
  margin: 15px;
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
  <input type="text" id="tarefa" placeholder="Manual e Tarefa">
  <input type="text" id="telefone" placeholder="Telefone">
  <button class="salvar" type="submit">Salvar</button>
</form>

<div class="data-box">
  <input type="date" id="dataReuniao">
</div>

<div id="lista"></div>

<script>

const firebaseConfig = {
  apiKey: "AIzaSyAe_fXCz68AZasK5zqg9NDXokw2zmNgFqw",
  authDomain: "embaixadores-c85f8.firebaseapp.com",
  databaseURL: "https://embaixadores-c85f8-default-rtdb.firebaseio.com",
  projectId: "embaixadores-c85f8"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

let dados = [];
let editandoId = null;

// 🔄 Atualização em tempo real
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
  if (!pessoa.presencas) return "0%";

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
    let freq = calcularFrequencia(p);

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

        <button class="editar" onclick="editar('${p.id}')">✏️ Editar</button>
        <button class="excluir" onclick="remover('${p.id}')">Excluir</button>
      </div>
    `;
  });
}

// ✔ Presença
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

// ✏️ Editar
function editar(id) {
  let p = dados.find(x => x.id === id);

  document.getElementById("nome").value = p.nome;
  document.getElementById("nascimento").value = p.nascimento;
  document.getElementById("tarefa").value = p.tarefa;
  document.getElementById("telefone").value = p.telefone;

  editandoId = id;
}

// 💾 Salvar (corrigido)
document.getElementById("form").addEventListener("submit", function(e) {
  e.preventDefault();

  const pessoa = {
    nome: document.getElementById("nome").value,
    nascimento: document.getElementById("nascimento").value,
    tarefa: document.getElementById("tarefa").value,
    telefone: document.getElementById("telefone").value
  };

  if (editandoId) {
    db.ref("embaixadores/" + editandoId).update(pessoa);
    editandoId = null;
  } else {
    pessoa.presencas = [];
    db.ref("embaixadores").push(pessoa);
  }

  this.reset();
});

// ❌ Remover
function remover(id) {
  if (confirm("Excluir?")) {
    db.ref("embaixadores/" + id).remove();
  }
}

</script>

</body>
</html>

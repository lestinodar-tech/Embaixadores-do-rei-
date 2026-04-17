<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Embaixadores do Rei</title>

<style>
body {
  font-family: Arial;
  background: #f4f4f4;
  padding: 20px;
}

h1 {
  text-align: center;
}

form {
  background: white;
  padding: 15px;
  border-radius: 10px;
  margin-bottom: 20px;
}

input {
  display: block;
  margin: 10px 0;
  padding: 8px;
  width: 100%;
}

button {
  padding: 8px;
  margin: 3px;
  border: none;
  cursor: pointer;
  border-radius: 5px;
}

.presente {
  background: green;
  color: white;
}

.falta {
  background: red;
  color: white;
}

.card {
  background: white;
  padding: 15px;
  border-radius: 12px;
  margin-bottom: 10px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.historico {
  font-size: 13px;
  margin-top: 10px;
}
</style>

</head>

<body>

<h1>Embaixadores do Rei</h1>

<form id="form">
  <input type="text" id="nome" placeholder="Nome" required>
  <input type="date" id="nascimento" required>
  <input type="number" id="tarefa" placeholder="Número da tarefa">
  <input type="text" id="telefone" placeholder="Telefone">
  <button type="submit">Salvar</button>
</form>

<div id="lista"></div>

<script>
let dados = JSON.parse(localStorage.getItem("embaixadores")) || [];

function salvarLocal() {
  localStorage.setItem("embaixadores", JSON.stringify(dados));
}

function dataHoje() {
  const hoje = new Date();
  return hoje.toISOString().split("T")[0];
}

function calcularFrequencia(pessoa) {
  let total = pessoa.presencas.length;
  let presencas = pessoa.presencas.filter(p => p.presente).length;

  if (total === 0) return "0%";

  let porcentagem = Math.round((presencas / total) * 100);
  return porcentagem + "%";
}

function render() {
  const lista = document.getElementById("lista");
  lista.innerHTML = "";

  dados.sort((a, b) => a.nome.localeCompare(b.nome));

  for (let i = 0; i < dados.length; i++) {
    let p = dados[i];

    let total = p.presencas.length;
    let freq = calcularFrequencia(p);

    let historico = "";
    p.presencas.forEach(h => {
      historico += `${h.data} - ${h.presente ? "✔" : "❌"}<br>`;
    });

    lista.innerHTML += `
      <div class="card">
        <strong>${p.nome}</strong><br>
        Nascimento: ${p.nascimento}<br>
        Tarefa: ${p.tarefa}<br>
        Telefone: ${p.telefone}<br><br>

        Frequência: ${p.presencas.filter(x => x.presente).length} / ${total} (${freq})<br><br>

        <button class="presente" onclick="marcarPresenca(${i}, true)">✔ Hoje</button>
        <button class="falta" onclick="marcarPresenca(${i}, false)">❌ Hoje</button><br><br>

        <div class="historico">
          <strong>Histórico:</strong><br>
          ${historico || "Sem registros"}
        </div><br>

        <button onclick="editar(${i})">Editar</button>
        <button onclick="remover(${i})">Excluir</button>
      </div>
    `;
  }
}

function marcarPresenca(index, valor) {
  let hoje = dataHoje();

  dados[index].presencas.push({
    data: hoje,
    presente: valor
  });

  salvarLocal();
  render();
}

function editar(index) {
  let p = dados[index];

  document.getElementById("nome").value = p.nome;
  document.getElementById("nascimento").value = p.nascimento;
  document.getElementById("tarefa").value = p.tarefa;
  document.getElementById("telefone").value = p.telefone;

  dados.splice(index, 1);
  salvarLocal();
  render();
}

function remover(index) {
  if (confirm("Excluir embaixador?")) {
    dados.splice(index, 1);
    salvarLocal();
    render();
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

  dados.push(pessoa);
  salvarLocal();
  render();

  this.reset();
});

render();
</script>

</body>
</html>

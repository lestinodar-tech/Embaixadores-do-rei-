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
  padding: 10px;
  background: green;
  color: white;
  border: none;
  cursor: pointer;
}
.card {
  background: white;
  padding: 10px;
  border-radius: 10px;
  margin-bottom: 10px;
}
</style>
</head>

<body>

<h1>Embaixadores do Rei</h1>

<form id="form">
  <input type="text" id="nome" placeholder="Nome" required>
  <input type="date" id="nascimento" required>
  <input type="text" id="presenca" placeholder="Presença (Sim/Não)">
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

function render() {
  const lista = document.getElementById("lista");
  lista.innerHTML = "";

  for (let i = 0; i < dados.length; i++) {
    let pessoa = dados[i];

    lista.innerHTML += "<div class='card'>" +
      "<strong>" + pessoa.nome + "</strong><br>" +
      "Nascimento: " + pessoa.nascimento + "<br>" +
      "Presença: " + pessoa.presenca + "<br>" +
      "Tarefa: " + pessoa.tarefa + "<br>" +
      "Telefone: " + pessoa.telefone + "<br>" +
      "<button onclick='remover(" + i + ")'>Excluir</button>" +
      "</div>";
  }
}

function remover(index) {
  dados.splice(index, 1);
  salvarLocal();
  render();
}

document.getElementById("form").addEventListener("submit", function(e) {
  e.preventDefault();

  const pessoa = {
    nome: document.getElementById("nome").value,
    nascimento: document.getElementById("nascimento").value,
    presenca: document.getElementById("presenca").value,
    tarefa: document.getElementById("tarefa").value,
    telefone: document.getElementById("telefone").value
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

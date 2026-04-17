<!DOCTYPE html>
<html>
<head>
  <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js"></script>
</head>

<body>

<h1>Teste Firebase</h1>

<input id="nome" placeholder="Nome">
<button onclick="salvar()">Salvar</button>

<div id="lista"></div>

<script>

// COLE SEU firebaseConfig AQUI
const firebaseConfig = {
  apiKey: "AIzaSyAe_fXCz68AZasK5zqg9NDXokw2zmNgFqw",
  authDomain: "embaixadores-c85f8.firebaseapp.com",
  databaseURL: "https://embaixadores-c85f8-default-rtdb.firebaseio.com",
  projectId: "embaixadores-c85f8"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

function salvar() {
  const nome = document.getElementById("nome").value;

  db.ref("embaixadores").push({
    nome: nome
  });
}

db.ref("embaixadores").on("value", snapshot => {
  const lista = document.getElementById("lista");
  lista.innerHTML = "";

  snapshot.forEach(child => {
    let p = child.val();
    lista.innerHTML += "<div>" + p.nome + "</div>";
  });
});

</script>

</body>
</html>

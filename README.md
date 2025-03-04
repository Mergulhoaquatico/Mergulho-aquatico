
// app.js

let web3;
let contract;
const contratoEndereco = "ENDERECO_DO_CONTRATO"; // Substitua pelo endereço do contrato
const contratoABI = [ /* ABI do seu contrato inteligente */ ]; 

// Inicializa o Web3
window.onload = async () => {
    if (window.ethereum) {
        web3 = new Web3(window.ethereum);
        await ethereum.enable();
        contract = new web3.eth.Contract(contratoABI, contratoEndereco);
        carregarProdutos();
    } else {
        alert("Por favor, instale uma carteira como MetaMask para usar esta página.");
    }
};

// Função para carregar os produtos
async function carregarProdutos() {
    const totalProdutos = await contract.methods.obterTotalTocas().call();
    let produtoLista = document.getElementById("produto-lista");

    for (let i = 1; i <= totalProdutos; i++) {
        const produto = await contract.methods.obterProduto(i).call();
        const produtoDiv = document.createElement("div");
        produtoDiv.classList.add("produto");

        produtoDiv.innerHTML = `
            <img src="imagens/${produto[0].toLowerCase().replace(" ", "_")}.jpg" alt="${produto[0]}">
            <h3>${produto[0]}</h3>
            <p>Preço: ${web3.utils.fromWei(produto[1], 'ether')} ETH</p>
            <p>Estoque: ${produto[2]}</p>
            <button onclick="selecionarProduto(${i})">Selecionar para Comprar</button>
        `;

        produtoLista.appendChild(produtoDiv);
    }
}

// Função para selecionar um produto
let produtoSelecionado = null;
function selecionarProduto(tocaId) {
    produtoSelecionado = tocaId;
}

// Função para realizar a compra do produto
async function comprarProduto() {
    if (!produtoSelecionado) {
        alert("Por favor, selecione um produto antes de continuar.");
        return;
    }

    const quantidade = parseInt(document.getElementById("quantidade").value);
    if (quantidade <= 0) {
        alert("Por favor, insira uma quantidade válida.");
        return;
    }

    const conta = (await web3.eth.getAccounts())[0];

    // Obter o preço do produto
    const produto = await contract.methods.obterProduto(produtoSelecionado).call();
    const preco = produto[1];
    const valorTotal = web3.utils.toWei((web3.utils.fromWei(preco, 'ether') * quantidade).toString(), 'ether');

    // Verificar o valor enviado e realizar a transação
    try {
        await contract.methods.comprarToca(produtoSelecionado, quantidade).send({
            from: conta,
            value: valorTotal
        });
        alert(`Compra realizada com sucesso! Sua toca de natação está a caminho.`);
    } catch (error) {
        alert("Erro ao realizar a compra. Tente novamente.");
    }
}










<!DOCTYPE html>
<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Página de Vendas - Tocas de Natação</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>Tocas de Natação</h1>
            <p>Compre diretamente de nossa loja e receba sua toca de natação em poucos passos.</p>
        </header>

        <section id="produtos">
            <h2>Produtos Disponíveis</h2>
            <div class="produto-lista" id="produto-lista">
                <!-- Produtos serão carregados aqui -->
            </div>
        </section>

        <section id="checkout">
            <h2>Informações de Compra</h2>
            <div class="form-group">
                <label for="loja">Loja Selecionada:</label>
                <input type="text" id="loja" disabled value="Tocas de Natação">
            </div>
            <div class="form-group">
                <label for="quantidade">Quantidade:</label>
                <input type="number" id="quantidade" min="1" value="1">
            </div>
            <button id="comprar-btn" onclick="comprarProduto()">Comprar</button>
        </section>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/web3/dist/web3.min.js"></script>
    <script src="app.js"></script>
</body>
</html>




















/* style.css */

body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f9;
    margin: 0;
    padding: 0;
}

.container {
    width: 80%;
    margin: 0 auto;
    padding-top: 50px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

h1 {
    color: #3498db;
}

#produtos {
    margin-bottom: 50px;
}

.produto-lista {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
}

.produto {
    background-color: #fff;
    padding: 20px;
    margin: 10px;
    border: 1px solid #ddd;
    border-radius: 8px;
    width: 250px;
    text-align: center;
}

.produto img {
    width: 100%;
    height: auto;
    border-radius: 8px;
}

.produto h3 {
    margin: 15px 0;
}

.produto p {
    margin: 10px 0;
    font-weight: bold;
}

button {
    background-color: #3498db;
    color: white;
    padding: 10px 20px;
    border: none;
    cursor: pointer;
    border-radius: 5px;
}

button:hover {
    background-color: #2980b9;
}

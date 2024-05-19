<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculo de Salário</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <style>
        body {
            padding: 20px;
            background-color: #333; /* Cor de fundo mais escura no modo escuro */
            color: #fff; /* Cor do texto no modo escuro */ 
            font-family: Verdana, Geneva, Tahoma, sans-serif;
        }

        .form-group label {
            color: #fff; /* Cor do texto das labels no modo escuro */
        }

        .form-group input.form-control {
            background-color: #666; /* Cor de fundo dos campos de entrada no modo escuro */
            color: #007bff; /* Cor do texto nos campos de entrada no modo escuro */
        }

        .inss-ativo, .pension-ativo {
            background-color: lightblue;
        }

        .form-group {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 20px;
        }

        .account-list {
            margin-top: 20px;
        }

        .account-item {
            display: flex;
            align-items: center;
            gap: 10px;
            border: 1px solid #ccc;
            padding: 10px;
            border-radius: 5px;
            margin-bottom: 10px;
            background-color: #666; /* Cor de fundo dos itens da lista no modo escuro */
            color: #fff; /* Cor do texto nos itens da lista no modo escuro */
        }

        .account-item input {
            display: none;
        }

        .account-item.editing input {
            display: inline;
        }

        .account-item.editing span {
            display: none;
        }

        .resultado {
            font-weight: bold;
            font-size: 18px;
            margin-top: 20px;
        }

        .dark-mode-btn {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 999;
        }

        .dark-mode {
            background-color: #333;
            color: #fff;
        }

        .light-mode {
            background-color: #fff; /* Cor de fundo no modo claro */
            color: #333; /* Cor do texto no modo claro */
        }

        .light-mode .form-group label {
            color: #333; /* Cor do texto das labels no modo claro */
        }

        .light-mode .form-group input.form-control {
            background-color: #f0f0f0; /* Cor de fundo dos campos de entrada no modo claro */
            color: #333; /* Cor do texto nos campos de entrada no modo claro */
        }

        .light-mode .account-item {
            background-color: #f0f0f0; /* Cor de fundo dos itens da lista no modo claro */
            color: #333; /* Cor do texto nos itens da lista no modo claro */
        } 
        footer {
                position: fixed; /* Manterá o footer fixo na parte inferior da janela */
                bottom: 0; /* Define a distância do footer em relação ao fundo da janela */
                width: 100%; /* Define a largura do footer como 100% da largura da janela */
                text-align: center; /* Centraliza o conteúdo do footer horizontalmente */
                color: #fff; /* Cor do texto */
                padding: 10px 0; /* Espaçamento interno do footer */
}
 
        .dark-mode .form-group input.form-control {
        border: 1px solid #007bff; /* Adiciona uma borda azul nos campos de entrada no modo claro */ 
        
} 
 
body:not(.dark-mode) .form-group input.form-control {
    border: 1px solid #007bff; /* Adiciona uma borda azul nos campos de entrada apenas no modo claro */
}
    </style>
    <script>
        function toggleINSS() {
            const inssCheckbox = document.getElementById("inssCheckbox");
            const salarioInput = document.getElementById("salario");
            if (inssCheckbox.checked) {
                salarioInput.classList.add("inss-ativo");
            } else {
                salarioInput.classList.remove("inss-ativo");
            }
        }

        function togglePension() {
            const pensionCheckbox = document.getElementById("pensionCheckbox");
            const salarioInput = document.getElementById("salario");
            if (pensionCheckbox.checked) {
                salarioInput.classList.add("pension-ativo");
            } else {
                salarioInput.classList.remove("pension-ativo");
            }
        }

        function calcularINSS(salario) {
            // Tabela progressiva do INSS para 2024 (exemplo)
            const faixa1 = 1212.00;
            const faixa2 = 2427.35;
            const faixa3 = 3641.03;
            const faixa4 = 7087.22;

            let inss = 0;

            if (salario <= faixa1) {
                inss = salario * 0.075;
            } else if (salario <= faixa2) {
                inss = (faixa1 * 0.075) + ((salario - faixa1) * 0.09);
            } else if (salario <= faixa3) {
                inss = (faixa1 * 0.075) + ((faixa2 - faixa1) * 0.09) + ((salario - faixa2) * 0.12);
            } else if (salario <= faixa4) {
                inss = (faixa1 * 0.075) + ((faixa2 - faixa1) * 0.09) + ((faixa3 - faixa2) * 0.12) + ((salario - faixa3) * 0.14);
            } else {
                inss = (faixa1 * 0.075) + ((faixa2 - faixa1) * 0.09) + ((faixa3 - faixa2) * 0.12) + ((faixa4 - faixa3) * 0.14);
            }

            return inss;
        }

        function calcularPension(salario) {
            // Exemplo de cálculo de pensão (ajuste conforme necessário)
            return salario * 0.10; // 10% de desconto para pensão
        }

        function addAccount() {
    const accountNameInput = document.getElementById("accountName");
    const accountValueInput = document.getElementById("accountValue");
    const accountName = accountNameInput.value.trim();
    const accountValue = parseFloat(accountValueInput.value.trim());

    if (accountName === "" || isNaN(accountValue)) {
        alert("Por favor, insira um nome de conta e um valor válido.");
        return;
    }

    const accountList = document.getElementById("accountList");
    const accountItem = document.createElement("div");
    accountItem.className = "account-item";

    const accountText = document.createElement("span");

    // Adiciona o valor da conta
    const valueSpan = document.createElement("span");
    valueSpan.textContent = `R$ ${accountValue.toFixed(2)}`;
    accountText.appendChild(valueSpan);
    accountText.appendChild(document.createTextNode(" ")); // Adiciona um espaço

    // Adiciona o nome da conta
    const nameSpan = document.createElement("span");
    nameSpan.textContent = `${accountName}:`;
    accountText.appendChild(nameSpan);
    accountText.appendChild(document.createTextNode(" ")); // Adiciona um espaço

    // Adiciona o ícone de carro se "IPVA" estiver presente no nome da conta
    if (accountName.toLowerCase().includes("ipva")) {
        const carIcon = document.createElement("span");
        carIcon.innerHTML = "&#128663;"; // 🚗
        accountText.appendChild(carIcon);
        accountText.appendChild(document.createTextNode(" ")); // Adiciona um espaço
    }

    accountItem.appendChild(accountText);

    const deleteButton = document.createElement("button");
    deleteButton.innerHTML = "&#10060;"; // X
    deleteButton.onclick = () => accountList.removeChild(accountItem);
    deleteButton.classList.add("btn", "btn-danger", "btn-sm"); // Adiciona classes Bootstrap para o botão

    accountItem.appendChild(deleteButton);
    accountList.appendChild(accountItem);

    accountNameInput.value = "";
    accountValueInput.value = "";
}


function calcularDescontos() {
const salarioInput = document.getElementById("salario");
let salario = parseFloat(salarioInput.value);

if (isNaN(salario)) {
    alert("Por favor, insira um valor válido para o salário.");
    return;
}

// Calcular total das contas a pagar 
 
const accountList = document.getElementById("accountList");
const accountItems = accountList.getElementsByClassName("account-item");
let totalContasPagar = 0;
for (let item of accountItems) {
    let valorConta = parseFloat(item.textContent.split("R$")[1].trim());
    totalContasPagar += valorConta;
}

const inssCheckbox = document.getElementById("inssCheckbox");
const pensionCheckbox = document.getElementById("pensionCheckbox");

let descontoINSS = 0;
let descontoPension = 0;

if (inssCheckbox.checked) {
    descontoINSS = calcularINSS(salario);
}

if (pensionCheckbox.checked) {
    descontoPension = calcularPension(salario);
}

const resultadoDiv = document.getElementById("resultado");
resultadoDiv.innerHTML = `
    <div class="resultado">
        <p>Detalhes das Contas:</p>
        <p>Contas a Pagar: R$ ${totalContasPagar.toFixed(2)}</p>
        ${descontoINSS > 0 ? `<p>Desconto INSS: R$ ${descontoINSS.toFixed(2)}</p>` : ''}
        ${descontoPension > 0 ? `<p>Desconto Pensão: R$ ${descontoPension.toFixed(2)}</p>` : ''}
    </div>
    <p>Salário após desconto: R$ ${(salario - totalContasPagar - descontoINSS - descontoPension).toFixed(2)}</p>`;
}

function toggleDarkMode() {
const body = document.body;
body.classList.toggle("dark-mode");
body.classList.toggle("light-mode"); // Toggle light mode
const darkModeBtn = document.getElementById("darkModeBtn");
if (body.classList.contains("light-mode")) {
    darkModeBtn.textContent = "Modo Escuro"; // Change button text to Dark Mode
} else {
    darkModeBtn.textContent = "Modo Claro"; // Change button text to Light Mode
}
}
</script>
</head>
<body class="dark-mode">
    <div class="container">
        <h1 class="text-center mb-4">Controle de Salario</h1>
        <div class="form-group">
            <label for="salario">Salário:</label>
            <input type="text" id="salario" name="salario" class="form-control">
            <div class="form-check">
                <input type="checkbox" id="inssCheckbox" onclick="toggleINSS()" class="form-check-input">
                <label for="inssCheckbox" class="form-check-label">INSS</label>
            </div>
            <div class="form-check">
                <input type="checkbox" id="pensionCheckbox" onclick="togglePension()" class="form-check-input">
                <label for="pensionCheckbox" class="form-check-label">Pensão</label>
            </div>
        </div>
        <div class="form-group">
            <label for="accountName">Contas:</label>
            <input type="text" id="accountName" name="accountName" class="form-control">
            <label for="accountValue">Valor:</label>
            <input type="text" id="accountValue" name="accountValue" class="form-control">
            <button type="button" onclick="addAccount()" class="btn btn-primary">Adicionar</button>
        </div>
        <div id="accountList" class="account-list"></div>
        <button type="button" onclick="calcularDescontos()" class="btn btn-success btn-lg btn-block">Processar Salário</button>
        <div id="resultado"></div>
        <button id="darkModeBtn" onclick="toggleDarkMode()" class="btn btn-secondary btn-sm mt-3 dark-mode-btn">Modo Claro</button>
    </div>
    <footer>Desenvolvido por Alexandre - Versão 1.0</footer>
</body>
</html>

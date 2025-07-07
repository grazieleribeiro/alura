# alura
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aventura Interativa</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <h1>Aventura na Floresta Encantada</h1>
        <div id="story-text"></div>
        <div id="choices" class="choices-container"></div>
        <button id="reset-button" class="hidden">Recomeçar Aventura</button>
    </div>
    <script src="script.js"></script>
</body>
</html>
body {
    font-family: 'Arial', sans-serif;
    margin: 0;
    padding: 0;
    background-color: #282c34; /* Fundo escuro */
    color: #f0f0f0; /* Texto claro */
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    text-align: center;
}

.container {
    background-color: #3a404a; /* Fundo do contêiner */
    border-radius: 10px;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
    padding: 30px;
    max-width: 800px;
    width: 90%;
    border: 1px solid #555;
}

h1 {
    color: #61dafb; /* Cor do título */
    margin-bottom: 25px;
    font-size: 2.5em;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

#story-text {
    font-size: 1.2em;
    line-height: 1.6;
    margin-bottom: 30px;
    background-color: #4a515c;
    padding: 20px;
    border-radius: 8px;
    border-left: 5px solid #61dafb;
    text-align: justify;
}

.choices-container {
    display: flex;
    flex-direction: column;
    gap: 15px;
    margin-top: 20px;
}

button {
    background-color: #61dafb; /* Cor principal do botão */
    color: #282c34;
    border: none;
    padding: 15px 25px;
    border-radius: 8px;
    font-size: 1.1em;
    cursor: pointer;
    transition: background-color 0.3s ease, transform 0.2s ease;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
}

button:hover {
    background-color: #4fa3d7; /* Cor do botão ao passar o mouse */
    transform: translateY(-2px);
}

button:active {
    transform: translateY(0);
}

.hidden {
    display: none;
}

#reset-button {
    margin-top: 30px;
    background-color: #ff6b6b; /* Cor do botão de reset */
    color: #fff;
}

#reset-button:hover {
    background-color: #e63946;
}

/* Responsividade básica */
@media (max-width: 768px) {
    h1 {
        font-size: 2em;
    }

    #story-text {
        font-size: 1em;
    }

    button {
        padding: 12px 20px;
        font-size: 1em;
    }
}

@media (max-width: 480px) {
    .container {
        padding: 20px;
    }

    h1 {
        font-size: 1.8em;
    }
}
const storyText = document.getElementById('story-text');
const choicesContainer = document.getElementById('choices');
const resetButton = document.getElementById('reset-button');

// Objeto que define os diferentes estágios da história
const story = {
    start: {
        text: "Você acorda em uma clareira no meio de uma **floresta densa e misteriosa**. O sol mal penetra as copas das árvores. Você não se lembra de como chegou aqui. Ao longe, ouve-se o murmúrio de um rio e, na outra direção, uma trilha parece levar a uma parte mais escura da floresta.",
        choices: [
            { text: "Seguir o som do rio", next: "riverPath" },
            { text: "Entrar na trilha escura", next: "darkForestPath" }
        ]
    },
    riverPath: {
        text: "Você segue o som da água e encontra um rio cristalino. Há uma **ponte de madeira velha** que parece instável e uma **margem pedregosa** onde a água parece rasa o suficiente para atravessar.",
        choices: [
            { text: "Atravessar pela ponte", next: "oldBridge" },
            { text: "Tentar atravessar o rio pelas pedras", next: "rockyRiver" }
        ]
    },
    darkForestPath: {
        text: "Você se aventura pela trilha escura. A floresta se torna mais densa e o silêncio é quase opressor. De repente, você vê uma **luz fraca brilhando** entre as árvores e, logo à frente, uma **pequena cabana abandonada**.",
        choices: [
            { text: "Investigar a luz misteriosa", next: "mysteriousLight" },
            { text: "Entrar na cabana abandonada", next: "abandonedCabin" }
        ]
    },
    oldBridge: {
        text: "Você tenta atravessar a ponte, mas ela range e algumas tábuas quebram sob seus pés! Por pouco, você consegue pular para o outro lado. Ufa! Você encontra uma **vila antiga** e segura. **Fim da aventura!**",
        choices: [] // Fim da história
    },
    rockyRiver: {
        text: "Você tenta atravessar pelas pedras. A correnteza é mais forte do que parecia e você escorrega! Acaba caindo na água e é levado pela correnteza para longe. Você precisa recomeçar. **Fim da aventura!**",
        choices: [] // Fim da história
    },
    mysteriousLight: {
        text: "Você se aproxima da luz e descobre que é uma **fogueira** acesa por um grupo de aventureiros amigáveis. Eles o convidam para se juntar a eles e compartilhar histórias. Você encontrou novos amigos! **Fim da aventura!**",
        choices: [] // Fim da história
    },
    abandonedCabin: {
        text: "Você entra na cabana abandonada. Está escura e cheia de teias de aranha. Ao fundo, você encontra um **baú antigo** empoeirado. Você o abre e encontra um mapa do tesouro! Sua aventura apenas começou. **Fim da aventura!**",
        choices: [] // Fim da história
    }
};

let currentState = 'start'; // Define o estado inicial do jogo

// Função para exibir o estágio atual da história
function showStage(stageName) {
    const stage = story[stageName];
    storyText.innerHTML = stage.text; // Usa innerHTML para permitir negrito no texto

    choicesContainer.innerHTML = ''; // Limpa os botões de escolha anteriores

    if (stage.choices.length === 0) {
        // Se não houver escolhas, significa que a história chegou ao fim
        resetButton.classList.remove('hidden'); // Mostra o botão de recomeçar
    } else {
        resetButton.classList.add('hidden'); // Esconde o botão de recomeçar
        stage.choices.forEach(choice => {
            const button = document.createElement('button');
            button.textContent = choice.text;
            button.addEventListener('click', () => {
                currentState = choice.next;
                showStage(currentState);
            });
            choicesContainer.appendChild(button);
        });
    }
}

// Função para recomeçar o jogo
function restartGame() {
    currentState = 'start';
    showStage(currentState);
}

// Event listener para o botão de recomeçar
resetButton.addEventListener('click', restartGame);

// Inicia o jogo quando a página carrega
showStage(currentState);

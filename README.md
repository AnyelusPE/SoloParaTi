<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Puzzle de Fotos</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            text-align: center;
            background: linear-gradient(135deg, #ff9a9e, #fad0c4);
            color: #fff;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        .container {
            display: flex;
            gap: 20px;
            align-items: center;
        }
        #reference-container img {
            width: 300px;
            height: 300px;
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }
        #puzzle-container { 
            display: grid; 
            grid-template-columns: repeat(3, 100px); 
            grid-gap: 5px; 
            background: rgba(255, 255, 255, 0.2);
            padding: 10px;
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }
        .puzzle-piece { 
            width: 100px; 
            height: 100px; 
            cursor: pointer; 
            border-radius: 5px;
            background-size: 300px 300px;
            transition: transform 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: white;
            font-size: 20px;
        }
        .puzzle-piece:hover { transform: scale(1.05); }
        #fileInput { 
            margin: 20px; 
            padding: 10px;
            border-radius: 5px;
            border: none;
            background: #ff758c;
            color: white;
            font-size: 16px;
            cursor: pointer;
            transition: background 0.3s;
        }
        #fileInput:hover { background: #ff5f6d; }
        #resetButton {
            margin-top: 10px;
            padding: 10px;
            border-radius: 5px;
            border: none;
            background: #ff758c;
            color: white;
            font-size: 16px;
            cursor: pointer;
            transition: background 0.3s;
            display: none;
        }
        #resetButton:hover { background: #ff5f6d; }
    </style>
</head>
<body>
    <h1>Emma A & E 💖</h1>
    <input type="file" id="fileInput" accept="image/*">
    <div class="container">
        <div id="reference-container">
            <img id="reference-image" src="" alt="Referencia" style="display:none;">
        </div>
        <div id="puzzle-container"></div>
    </div>
    <button id="resetButton">Reiniciar Puzzle</button>
    <script>
        const puzzleContainer = document.getElementById("puzzle-container");
        const fileInput = document.getElementById("fileInput");
        const resetButton = document.getElementById("resetButton");
        const referenceImage = document.getElementById("reference-image");
        let emptyIndex = 8;
        let positions = [...Array(9).keys()];
        let currentImage = "";
        
        function shuffleArray(array) {
            for (let i = array.length - 2; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
        }
        
        fileInput.addEventListener("change", function(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    currentImage = e.target.result;
                    referenceImage.src = currentImage;
                    referenceImage.style.display = "block";
                    shuffleArray(positions);
                    createPuzzle(currentImage);
                    resetButton.style.display = "block";
                };
                reader.readAsDataURL(file);
            }
        });
        
        function createPuzzle(image) {
            puzzleContainer.innerHTML = "";
            positions.forEach((pos, i) => {
                const piece = document.createElement("div");
                piece.classList.add("puzzle-piece");
                piece.style.backgroundImage = pos !== 8 ? `url(${image})` : "none";
                piece.style.backgroundSize = "300px 300px";
                piece.style.backgroundPosition = `-${(pos % 3) * 100}px -${Math.floor(pos / 3) * 100}px`;
                piece.dataset.index = i;
                piece.dataset.correctIndex = pos;
                if (pos === 8) {
                    piece.style.background = "none";
                    piece.dataset.empty = "true";
                }
                piece.addEventListener("click", () => movePiece(i));
                puzzleContainer.appendChild(piece);
            });
            emptyIndex = positions.indexOf(8);
        }
        
        function movePiece(index) {
            const validMoves = [
                emptyIndex - 1, emptyIndex + 1,
                emptyIndex - 3, emptyIndex + 3
            ];
            if (validMoves.includes(index)) {
                [positions[emptyIndex], positions[index]] = [positions[index], positions[emptyIndex]];
                createPuzzle(currentImage);
            }
        }
        
        resetButton.addEventListener("click", function() {
            shuffleArray(positions);
            createPuzzle(currentImage);
        });
    </script>
</body>
</html>

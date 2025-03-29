<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Carrousel d'Images Local</title>
    <style>
        * {
            box-sizing: border-box;
        }
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
            padding: 10px;
        }
        .carousel-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            background-color: white;
            border-radius: 15px;
            box-shadow: 0 6px 12px rgba(0,0,0,0.1);
            padding: 20px;
            width: 100%;
            max-width: 900px;
            position: relative;
        }
        .image-wrapper {
            display: flex;
            align-items: center;
            justify-content: center;
            width: 100%;
            margin-bottom: 15px;
        }
        .thumbnails-wrapper {
            display: flex;
            justify-content: center;
            align-items: center;
            width: 100%;
        }
        .thumbnail {
            width: 80px;
            height: 80px;
            object-fit: cover;
            border-radius: 10px;
            margin: 0 5px;
            opacity: 0.6;
            cursor: pointer;
            transition: opacity 0.3s ease;
        }
        .thumbnail:hover {
            opacity: 0.8;
        }
        .fullscreen-thumbnail {
            width: 60px;
            height: 60px;
            object-fit: cover;
            border-radius: 10px;
            margin: 0 5px;
            opacity: 0.5;
            cursor: pointer;
            transition: opacity 0.3s ease;
        }
        .fullscreen-thumbnail:hover {
            opacity: 0.8;
        }
        .fullscreen-thumbnail.active {
            opacity: 1;
            border: 2px solid white;
        }
        #mainImage {
            max-width: 100%;
            max-height: 60vh;
            object-fit: contain;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            cursor: zoom-in;
        }
        #imageName {
            text-align: center;
            background-color: rgba(0,0,0,0.7);
            color: white;
            padding: 5px 10px;
            border-radius: 5px;
            margin-top: 10px;
        }
        #fileInput {
            display: none;
        }
        .file-selector {
            width: 100%;
            max-width: 250px;
            text-align: center;
            background-color: #4CAF50;
            color: white;
            padding: 10px;
            border-radius: 5px;
            cursor: pointer;
            margin: 10px auto;
        }
        #fullscreenOverlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.9);
            z-index: 1000;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }
        .fullscreen-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            width: 100%;
            height: 100%;
            position: relative;
        }
        .fullscreen-image-wrapper {
            display: flex;
            align-items: center;
            justify-content: center;
            width: 100%;
            flex-grow: 1;
            position: relative;
        }
        #fullscreenImage {
            max-width: 95%;
            max-height: 80vh;
            object-fit: contain;
        }
        .fullscreen-thumbnails {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-top: 10px;
            margin-bottom: 10px;
            overflow-x: auto;
            padding: 0 10px;
            max-width: 100%;
        }
        .nav-button {
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
            background-color: rgba(255,255,255,0.2);
            color: white;
            border: none;
            font-size: 2rem;
            padding: 10px 15px;
            cursor: pointer;
            transition: background-color 0.3s;
            z-index: 10;
        }
        .nav-button:hover {
            background-color: rgba(255,255,255,0.4);
        }
        #prevFullscreen {
            left: 10px;
        }
        #nextFullscreen {
            right: 10px;
        }
        #closeFullscreen {
            position: absolute;
            top: 10px;
            right: 10px;
            color: white;
            font-size: 30px;
            cursor: pointer;
            z-index: 20;
        }
        #fullscreenImageName {
            background-color: rgba(0,0,0,0.7);
            color: white;
            padding: 5px 10px;
            border-radius: 5px;
            margin-top: 10px;
            text-align: center;
        }

        @media (max-width: 600px) {
            .carousel-container {
                padding: 10px;
            }
            .thumbnail {
                width: 60px;
                height: 60px;
                margin: 0 3px;
            }
            .nav-button {
                font-size: 1.5rem;
                padding: 5px 10px;
            }
            #mainImage {
                max-height: 50vh;
            }
        }
    </style>
</head>
<body>
    <div class="carousel-container">
        <div class="file-selector" onclick="document.getElementById('fileInput').click()">
            Sélectionner des images
        </div>
        <input type="file" id="fileInput" multiple accept="image/*" webkitdirectory>
        
        <div class="image-wrapper">
            <img id="mainImage" src="" alt="Image principale" onclick="openFullscreen()">
        </div>
        <div id="imageName"></div>
        
        <div class="thumbnails-wrapper" id="thumbnailsWrapper"></div>
    </div>

    <div id="fullscreenOverlay">
        <div class="fullscreen-container">
            <span id="closeFullscreen" onclick="closeFullscreen()">&times;</span>
            <div class="fullscreen-image-wrapper">
                <button id="prevFullscreen" class="nav-button" onclick="navigateFullscreen(-1)">&#10094;</button>
                <img id="fullscreenImage" src="" alt="Image en plein écran">
                <button id="nextFullscreen" class="nav-button" onclick="navigateFullscreen(1)">&#10095;</button>
            </div>
            <div id="fullscreenImageName"></div>
            <div class="fullscreen-thumbnails" id="fullscreenThumbnails"></div>
        </div>
    </div>

    <script>
        let imageList = [];
        let currentIndex = 0;

        document.getElementById('fileInput').addEventListener('change', function(event) {
            const files = Array.from(event.target.files);
            
            // Filtrer et trier les fichiers image
            imageList = files
                .filter(file => file.type.startsWith('image/'))
                .sort((a, b) => a.name.localeCompare(b.name))
                .map(file => URL.createObjectURL(file));

            if (imageList.length > 0) {
                currentIndex = 0;
                displayImages();
            } else {
                alert('Aucune image valide trouvée');
            }
        });

        function displayImages() {
            const mainImage = document.getElementById('mainImage');
            const thumbnailsWrapper = document.getElementById('thumbnailsWrapper');
            const imageName = document.getElementById('imageName');

            // Définir l'image principale
            mainImage.src = imageList[currentIndex];
            imageName.textContent = `Image ${currentIndex + 1} / ${imageList.length}`;

            // Créer des miniatures
            let thumbnailsHtml = '';
            const range = 3; // Nombre de miniatures de chaque côté
            
            for (let i = currentIndex - range; i <= currentIndex + range; i++) {
                const adjustedIndex = (i + imageList.length) % imageList.length;
                const activeClass = adjustedIndex === currentIndex ? 'active' : '';
                
                thumbnailsHtml += `
                    <img class="thumbnail ${activeClass}" 
                         src="${imageList[adjustedIndex]}" 
                         onclick="goToImage(${adjustedIndex})"
                         alt="Miniature ${adjustedIndex + 1}">
                `;
            }
            
            thumbnailsWrapper.innerHTML = thumbnailsHtml;
        }

        function createFullscreenThumbnails() {
            const fullscreenThumbnails = document.getElementById('fullscreenThumbnails');
            
            // Créer des miniatures autour de l'image courante
            let thumbnailsHtml = '';
            const range = 3; // Nombre de miniatures de chaque côté
            
            for (let i = currentIndex - range; i <= currentIndex + range; i++) {
                const adjustedIndex = (i + imageList.length) % imageList.length;
                const activeClass = adjustedIndex === currentIndex ? 'active' : '';
                
                thumbnailsHtml += `
                    <img class="fullscreen-thumbnail ${activeClass}" 
                         src="${imageList[adjustedIndex]}" 
                         onclick="goToImageFullscreen(${adjustedIndex})"
                         alt="Miniature ${adjustedIndex + 1}">
                `;
            }
            
            fullscreenThumbnails.innerHTML = thumbnailsHtml;
        }

        function goToImage(index) {
            currentIndex = index;
            displayImages();
        }

        function goToImageFullscreen(index) {
            currentIndex = index;
            const fullscreenImage = document.getElementById('fullscreenImage');
            const fullscreenImageName = document.getElementById('fullscreenImageName');
            
            fullscreenImage.src = imageList[currentIndex];
            fullscreenImageName.textContent = `Image ${currentIndex + 1} / ${imageList.length}`;
            
            createFullscreenThumbnails();
        }

        function openFullscreen() {
            const fullscreenOverlay = document.getElementById('fullscreenOverlay');
            const fullscreenImage = document.getElementById('fullscreenImage');
            const fullscreenImageName = document.getElementById('fullscreenImageName');
            
            fullscreenImage.src = imageList[currentIndex];
            fullscreenImageName.textContent = `Image ${currentIndex + 1} / ${imageList.length}`;
            fullscreenOverlay.style.display = 'flex';
            
            createFullscreenThumbnails();
        }

        function navigateFullscreen(direction) {
            currentIndex = (currentIndex + direction + imageList.length) % imageList.length;
            const fullscreenImage = document.getElementById('fullscreenImage');
            const fullscreenImageName = document.getElementById('fullscreenImageName');
            
            fullscreenImage.src = imageList[currentIndex];
            fullscreenImageName.textContent = `Image ${currentIndex + 1} / ${imageList.length}`;
            
            createFullscreenThumbnails();
        }

        function closeFullscreen() {
            const fullscreenOverlay = document.getElementById('fullscreenOverlay');
            fullscreenOverlay.style.display = 'none';
        }

        // Navigation au clavier
        document.addEventListener('keydown', (event) => {
            if (imageList.length === 0) return;

            const fullscreenOverlay = document.getElementById('fullscreenOverlay');
            
            if (event.key === 'ArrowRight') {
                if (fullscreenOverlay.style.display === 'flex') {
                    navigateFullscreen(1);
                } else {
                    currentIndex = (currentIndex + 1) % imageList.length;
                    displayImages();
                }
            } else if (event.key === 'ArrowLeft') {
                if (fullscreenOverlay.style.display === 'flex') {
                    navigateFullscreen(-1);
                } else {
                    currentIndex = (currentIndex - 1 + imageList.length) % imageList.length;
                    displayImages();
                }
            } else if (event.key === 'Escape') {
                closeFullscreen();
            }
        });
    </script>
</body>
</html>

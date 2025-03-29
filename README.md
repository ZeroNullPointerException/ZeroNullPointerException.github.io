<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Carrousel d'Images GitHub</title>
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
            overflow-x: auto;
            padding: 10px 0;
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
        .thumbnail.active {
            opacity: 1;
            border: 2px solid #4285f4;
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
        .loading {
            text-align: center;
            margin: 20px 0;
            font-style: italic;
            color: #555;
        }
        .error-message {
            color: #d32f2f;
            text-align: center;
            margin: 20px 0;
            padding: 10px;
            border: 1px solid #d32f2f;
            border-radius: 5px;
            background-color: #ffebee;
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
        <h1>Galerie d'images</h1>
        <div id="loading" class="loading">Chargement des images...</div>
        <div id="error" class="error-message" style="display: none;"></div>
        
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
        
        // Configuration GitHub
        const owner = 'ZeroNullPointerException';
        const repo = 'ZeroNullPointerException.github.io';
        const path = 'images';
        const apiUrl = `https://api.github.com/repos/${owner}/${repo}/contents/${path}`;
        
        // Extensions d'images reconnues
        const imageExtensions = ['jpg', 'jpeg', 'png', 'gif', 'webp', 'svg'];
        
        // Fonction pour charger les images depuis l'API GitHub
        async function loadImagesFromGitHub() {
            document.getElementById('loading').style.display = 'block';
            document.getElementById('error').style.display = 'none';
            
            try {
                const response = await fetch(apiUrl);
                
                if (!response.ok) {
                    throw new Error(`Erreur HTTP: ${response.status}`);
                }
                
                const data = await response.json();
                
                // Filtrer les fichiers pour ne garder que les images
                imageList = data
                    .filter(file => {
                        // Vérifier l'extension du fichier
                        const extension = file.name.split('.').pop().toLowerCase();
                        return imageExtensions.includes(extension);
                    })
                    .map(file => {
                        // Pour GitHub Pages, on utilise un chemin relatif
                        return `/${path}/${file.name}`;
                    })
                    .sort(); // Trier les images par nom
                
                if (imageList.length > 0) {
                    currentIndex = 0;
                    displayImages();
                } else {
                    document.getElementById('error').textContent = 'Aucune image trouvée dans le dossier spécifié';
                    document.getElementById('error').style.display = 'block';
                }
            } catch (error) {
                console.error('Erreur lors du chargement des images:', error);
                
                // Passer à la méthode alternative si l'API GitHub échoue
                loadImagesAlternative();
            } finally {
                document.getElementById('loading').style.display = 'none';
            }
        }
        
        // Méthode alternative utilisant un fichier de configuration
        function loadImagesAlternative() {
            // Essayer de charger le fichier image-list.json qui contient la liste des images
            fetch('/images/image-list.json')
                .then(response => {
                    if (!response.ok) {
                        throw new Error('Fichier de liste d\'images non trouvé');
                    }
                    return response.json();
                })
                .then(data => {
                    imageList = data.images.map(filename => `/images/${filename}`);
                    
                    if (imageList.length > 0) {
                        currentIndex = 0;
                        displayImages();
                    } else {
                        document.getElementById('error').textContent = 'Aucune image trouvée dans le fichier de configuration';
                        document.getElementById('error').style.display = 'block';
                    }
                })
                .catch(error => {
                    console.error('Erreur avec la méthode alternative:', error);
                    
                    // Dernier recours: essayer de charger des images en utilisant un pattern commun
                    tryCommonImagePattern();
                });
        }
        
        // Dernier recours: essayer un pattern commun pour les noms de fichiers
        function tryCommonImagePattern() {
            // Générer une liste basée sur des noms probables
            const baseNames = ['image', 'photo', 'img'];
            const numbers = Array.from({length: 20}, (_, i) => i + 1); // 1 à 20
            const extensions = ['jpg', 'png', 'jpeg'];
            
            imageList = [];
            
            // Créer des combinaisons de noms probables
            for (const base of baseNames) {
                for (const num of numbers) {
                    for (const ext of extensions) {
                        imageList.push(`/images/${base}${num}.${ext}`);
                    }
                }
            }
            
            // Vérifier la première image, si elle existe, continuer avec cette méthode
            checkImageExists(imageList[0])
                .then(exists => {
                    if (exists) {
                        currentIndex = 0;
                        displayImages();
                    } else {
                        document.getElementById('error').textContent = 'Impossible de trouver des images. Veuillez créer un fichier image-list.json dans le dossier /images/';
                        document.getElementById('error').style.display = 'block';
                    }
                });
        }
        
        // Vérifier si une image existe
        function checkImageExists(url) {
            return new Promise(resolve => {
                const img = new Image();
                img.onload = () => resolve(true);
                img.onerror = () => resolve(false);
                img.src = url;
            });
        }

        function displayImages() {
            const mainImage = document.getElementById('mainImage');
            const thumbnailsWrapper = document.getElementById('thumbnailsWrapper');
            const imageName = document.getElementById('imageName');

            // Définir l'image principale
            mainImage.src = imageList[currentIndex];
            
            // Extraire le nom du fichier pour l'affichage
            const fileName = imageList[currentIndex].split('/').pop();
            imageName.textContent = `${fileName} (${currentIndex + 1} / ${imageList.length})`;

            // Créer des miniatures
            let thumbnailsHtml = '';
            
            // Afficher toutes les miniatures avec une classe active pour l'image courante
            for (let i = 0; i < imageList.length; i++) {
                const activeClass = i === currentIndex ? 'active' : '';
                
                thumbnailsHtml += `
                    <img class="thumbnail ${activeClass}" 
                         src="${imageList[i]}" 
                         onclick="goToImage(${i})"
                         alt="Miniature ${i + 1}">
                `;
            }
            
            thumbnailsWrapper.innerHTML = thumbnailsHtml;
            
            // Faire défiler pour que la miniature active soit visible
            const activeThumbnail = thumbnailsWrapper.querySelector('.thumbnail.active');
            if (activeThumbnail) {
                activeThumbnail.scrollIntoView({ behavior: 'smooth', block: 'nearest', inline: 'center' });
            }
        }

        function createFullscreenThumbnails() {
            const fullscreenThumbnails = document.getElementById('fullscreenThumbnails');
            
            // Créer des miniatures pour le mode plein écran
            let thumbnailsHtml = '';
            
            // Afficher toutes les miniatures avec une classe active pour l'image courante
            for (let i = 0; i < imageList.length; i++) {
                const activeClass = i === currentIndex ? 'active' : '';
                
                thumbnailsHtml += `
                    <img class="fullscreen-thumbnail ${activeClass}" 
                         src="${imageList[i]}" 
                         onclick="goToImageFullscreen(${i})"
                         alt="Miniature ${i + 1}">
                `;
            }
            
            fullscreenThumbnails.innerHTML = thumbnailsHtml;
            
            // Faire défiler pour que la miniature active soit visible
            const activeThumbnail = fullscreenThumbnails.querySelector('.fullscreen-thumbnail.active');
            if (activeThumbnail) {
                activeThumbnail.scrollIntoView({ behavior: 'smooth', block: 'nearest', inline: 'center' });
            }
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
            
            // Extraire le nom du fichier pour l'affichage
            const fileName = imageList[currentIndex].split('/').pop();
            fullscreenImageName.textContent = `${fileName} (${currentIndex + 1} / ${imageList.length})`;
            
            createFullscreenThumbnails();
        }

        function openFullscreen() {
            if (imageList.length === 0) return;
            
            const fullscreenOverlay = document.getElementById('fullscreenOverlay');
            const fullscreenImage = document.getElementById('fullscreenImage');
            const fullscreenImageName = document.getElementById('fullscreenImageName');
            
            fullscreenImage.src = imageList[currentIndex];
            
            // Extraire le nom du fichier pour l'affichage
            const fileName = imageList[currentIndex].split('/').pop();
            fullscreenImageName.textContent = `${fileName} (${currentIndex + 1} / ${imageList.length})`;
            
            fullscreenOverlay.style.display = 'flex';
            
            createFullscreenThumbnails();
        }

        function navigateFullscreen(direction) {
            const newIndex = currentIndex + direction;
            
            if (newIndex >= 0 && newIndex < imageList.length) {
                currentIndex = newIndex;
                goToImageFullscreen(currentIndex);
            }
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
                } else if (currentIndex < imageList.length - 1) {
                    currentIndex++;
                    displayImages();
                }
            } else if (event.key === 'ArrowLeft') {
                if (fullscreenOverlay.style.display === 'flex') {
                    navigateFullscreen(-1);
                } else if (currentIndex > 0) {
                    currentIndex--;
                    displayImages();
                }
            } else if (event.key === 'Escape') {
                closeFullscreen();
            }
        });

        // Charger les images au démarrage
        window.addEventListener('load', loadImagesFromGitHub);
    </script>
</body>
</html>

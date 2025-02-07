# <!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flycpit</title>
    <link rel="stylesheet" href="styles.css">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" rel="stylesheet">
    <style>
        @keyframes animateBackground {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(270deg, #ff7eb3, #ff758c, #ff7eb3, #ff758c);
            background-size: 400% 400%;
            animation: animateBackground 10s ease infinite;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        header {
            background: rgba(51, 51, 51, 0.8);
            color: white;
            padding: 15px;
            text-align: center;
            font-size: 24px;
            font-weight: bold;
            width: 100%;
        }
        .menu {
            display: flex;
            gap: 15px;
            margin-top: 20px;
        }
        .menu button {
            padding: 10px;
            background: #333;
            color: white;
            border: none;
            cursor: pointer;
            font-size: 18px;
        }
        .menu button i {
            font-size: 24px;
        }
        .section {
            background: rgba(255, 255, 255, 0.9);
            padding: 20px;
            border-radius: 5px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            overflow-y: auto;
            max-height: 80vh;
            width: 80vw;
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }
        .close-btn {
            background: red;
            color: white;
            border: none;
            padding: 5px 10px;
            cursor: pointer;
            position: absolute;
            top: 10px;
            right: 10px;
        }
        .comment-box {
            margin-top: 20px;
        }
        .comment-box textarea {
            width: 100%;
            height: 80px;
            padding: 10px;
        }
        .comment-box button {
            margin-top: 10px;
            padding: 10px;
            background: #333;
            color: white;
            border: none;
            cursor: pointer;
        }
        .comments-list {
            margin-top: 20px;
        }
        .comment {
            background: #f2f2f2;
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 5px;
        }
        .record-btn {
            background-color: #333;
            color: white;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
            margin-top: 20px;
        }
        .record-btn.active {
            background-color: #e74c3c;
        }
        .audio-player {
            margin-top: 20px;
        }
    </style>
    <script>
        let mediaRecorder;
        let audioChunks = [];
        let audioUrl = '';
        let audioBlob;

        // Fonction pour démarrer/arrêter l'enregistrement
        function toggleRecording() {
            const recordButton = document.getElementById('record-btn');

            if (mediaRecorder && mediaRecorder.state === 'recording') {
                mediaRecorder.stop();
                recordButton.innerText = 'Enregistrer un message vocal';
                recordButton.classList.remove('active');
            } else {
                navigator.mediaDevices.getUserMedia({ audio: true })
                    .then(stream => {
                        mediaRecorder = new MediaRecorder(stream);
                        mediaRecorder.ondataavailable = event => {
                            audioChunks.push(event.data);
                        };
                        mediaRecorder.onstop = () => {
                            audioBlob = new Blob(audioChunks, { type: 'audio/wav' });
                            audioUrl = URL.createObjectURL(audioBlob);
                            document.getElementById('audio-player').src = audioUrl;
                        };
                        mediaRecorder.start();
                        recordButton.innerText = 'Arrêter l\'enregistrement';
                        recordButton.classList.add('active');
                    })
                    .catch(err => console.error("L'accès au micro a échoué", err));
            }
        }

        // Fonction pour envoyer l'audio
        function sendAudio() {
            if (audioBlob) {
                // Envoyer l'audio ou l'enregistrer quelque part
                alert("Message vocal envoyé !");
            } else {
                alert("Enregistrez d'abord un message vocal.");
            }
        }

        // Fonction pour afficher les sections
        function showSection(sectionId) {
            document.querySelectorAll('.section').forEach(section => {
                section.style.display = 'none';
            });
            document.getElementById(sectionId).style.display = 'block';
        }

        // Fonction pour fermer les sections
        function closeSection(sectionId) {
            document.getElementById(sectionId).style.display = 'none';
        }

        // Fonction pour poster un commentaire
        function postComment(sectionId) {
            const commentBox = document.getElementById(`${sectionId}-comment-box`);
            const commentText = commentBox.querySelector('textarea').value;

            if (commentText.trim()) {
                const commentList = document.getElementById(`${sectionId}-comments-list`);
                const newComment = document.createElement('div');
                newComment.classList.add('comment');
                newComment.innerText = commentText;
                commentList.appendChild(newComment);

                // Vider la zone de commentaire
                commentBox.querySelector('textarea').value = '';
            } else {
                alert("Veuillez entrer un commentaire avant de poster.");
            }
        }
    </script>
</head>
<body>
    <header>Flycpit</header>
    
    <div class="menu">
        <button onclick="showSection('video-section')"><i class="fas fa-video"></i></button>
        <button onclick="showSection('audio-section')"><i class="fas fa-microphone"></i></button>
        <button onclick="showSection('image-section')"><i class="fas fa-image"></i></button>
        <button onclick="showSection('review-section')"><i class="fas fa-comment-dots"></i></button>
    </div>
    
    <div class="section" id="video-section">
        <button class="close-btn" onclick="closeSection('video-section')"><i class="fas fa-times"></i></button>
        <h2>Vidéo</h2>
        <video controls width="100%">
            <source src="sample-video.mp4" type="video/mp4">
            Votre navigateur ne supporte pas la vidéo.
        </video>
        <div class="comment-box" id="video-comment-box">
            <textarea placeholder="Ajouter un commentaire..."></textarea>
            <button onclick="postComment('video-section')"><i class="fas fa-paper-plane"></i></button>
        </div>
        <div class="comments-list" id="video-comments-list"></div>
    </div>
    
    <div class="section" id="audio-section">
        <button class="close-btn" onclick="closeSection('audio-section')"><i class="fas fa-times"></i></button>
        <h2>Audio</h2>
        
        <!-- Bouton d'enregistrement -->
        <button id="record-btn" class="record-btn" onclick="toggleRecording"><i class="fas fa-microphone-alt"></i> Enregistrer</button>

        <!-- Lecteur audio pour écouter le message vocal enregistré -->
        <div class="audio-player">
            <audio id="audio-player" controls>
                Votre navigateur ne supporte pas l'audio.
            </audio>
        </div>

        <!-- Bouton pour envoyer l'audio -->
        <div class="audio-controls">
            <button onclick="sendAudio()"><i class="fas fa-paper-plane"></i></button>
        </div>

        <!-- Zone pour ajouter des commentaires -->
        <div class="comment-box" id="audio-comment-box">
            <textarea placeholder="Ajouter un commentaire..."></textarea>
            <button onclick="postComment('audio-section')"><i class="fas fa-paper-plane"></i></button>
        </div>
        <div class="comments-list" id="audio-comments-list"></div>
    </div>
    
    <div class="section" id="image-section">
        <button class="close-btn" onclick="closeSection('image-section')"><i class="fas fa-times"></i></button>
        <h2>Images</h2>
        <img src="sample-image.jpg" alt="Image partagée" width="100%">
        <div class="comment-box" id="image-comment-box">
            <textarea placeholder="Ajouter un commentaire..."></textarea>
            <button onclick="postComment('image-section')"><i class="fas fa-paper-plane"></i></button>
        </div>
        <div class="comments-list" id="image-comments-list"></div>
    </div>
    
    <div class="section" id="review-section">
        <button class="close-btn" onclick="closeSection('review-section')"><i class="fas fa-times"></i></button>
        <h2>Avis sur le site</h2>
        <div class="comment-box">
            <textarea placeholder="Donnez votre avis..."></textarea>
            <button><i class="fas fa-paper-plane"></i></button>
        </div>
    </div>
</body>
</html>

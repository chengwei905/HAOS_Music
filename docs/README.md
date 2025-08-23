<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>音樂播放器</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            padding: 20px;
            background-color: #f4f4f4;
        }
        #player-container {
            background-color: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            width: 90%;
            max-width: 600px;
        }
        #track-list {
            list-style-type: none;
            padding: 0;
            margin-top: 20px;
        }
        .track-item {
            display: flex;
            align-items: center;
            padding: 10px;
            cursor: pointer;
            border-bottom: 1px solid #eee;
            transition: background-color 0.2s;
        }
        .track-item:hover {
            background-color: #f9f9f9;
        }
        .track-number {
            font-weight: bold;
            margin-right: 15px;
            color: #555;
        }
        .track-name {
            flex-grow: 1;
        }
        .track-item.active {
            background-color: #e0f7fa;
        }
    </style>
</head>
<body>
    <div id="player-container">
        <h1>音樂播放器</h1>
        <audio id="audio-player" controls style="width: 100%;"></audio>
        <ul id="track-list"></ul>
    </div>

    <script>
        const trackListElement = document.getElementById('track-list');
        const audioPlayer = document.getElementById('audio-player');
        const totalTracks = 5; // 這裡請設定您的音檔總數

        function loadTracks() {
            let tracksLoaded = 0;
            let currentTrackIndex = -1;

            for (let i = 1; i <= totalTracks; i++) {
                const trackItem = document.createElement('li');
                trackItem.className = 'track-item';
                trackItem.setAttribute('data-index', i);

                const trackNumber = document.createElement('span');
                trackNumber.className = 'track-number';
                trackNumber.textContent = `${i}.`;

                const trackName = document.createElement('span');
                trackName.className = 'track-name';
                trackName.textContent = `Track ${i}`;

                trackItem.appendChild(trackNumber);
                trackItem.appendChild(trackName);

                trackItem.addEventListener('click', () => {
                    playTrack(i);
                });

                // 檢查檔案是否存在，並在存在時才顯示
                const audio = new Audio(`tracks/track-${i}.mp3`);
                audio.addEventListener('canplaythrough', () => {
                    trackListElement.appendChild(trackItem);
                    tracksLoaded++;
                    if (tracksLoaded === 1) {
                        // 自動播放第一個找到的音檔
                        playTrack(1);
                    }
                }, { once: true });
                audio.addEventListener('error', () => {
                    console.error(`無法載入 tracks/track-${i}.mp3`);
                });
            }
        }

        function playTrack(index) {
            const trackPath = `tracks/track-${index}.mp3`;
            audioPlayer.src = trackPath;
            audioPlayer.play();
            
            // 更新播放列表的活躍狀態
            const allItems = document.querySelectorAll('.track-item');
            allItems.forEach(item => {
                item.classList.remove('active');
            });
            const activeItem = document.querySelector(`.track-item[data-index='${index}']`);
            if (activeItem) {
                activeItem.classList.add('active');
            }
        }

        // 自動播放下一首
        audioPlayer.addEventListener('ended', () => {
            const currentTrackElement = document.querySelector('.track-item.active');
            if (currentTrackElement) {
                const currentIndex = parseInt(currentTrackElement.getAttribute('data-index'));
                const nextIndex = currentIndex + 1;
                
                // 檢查下一首是否存在
                const nextTrackElement = document.querySelector(`.track-item[data-index='${nextIndex}']`);
                if (nextTrackElement) {
                    playTrack(nextIndex);
                }
            }
        });

        loadTracks();
    </script>
</body>
</html>

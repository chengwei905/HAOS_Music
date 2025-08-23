<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>音樂播放器 (下拉式選單)</title>
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
            max-width: 1200px;
        }
        #track-selector {
            width: 100%;
            padding: 10px;
            margin-bottom: 20px;
            border-radius: 4px;
            border: 1px solid #ccc;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <div id="player-container">
        <h1>音樂播放器</h1>
        
        <select id="track-selector"></select>

        <audio id="audio-player" controls style="width: 100%;"></audio>
    </div>

    <script>
        const trackSelector = document.getElementById('track-selector');
        const audioPlayer = document.getElementById('audio-player');
        const totalTracks = 5; // 這裡請設定您的音檔總數

        // 載入音檔列表到下拉式選單
        function loadTracks() {
            let tracksLoaded = 0;
            
            for (let i = 1; i <= totalTracks; i++) {
                // 檢查檔案是否存在，並在存在時才新增到選單
                const audio = new Audio(`tracks/track-${i}.mp3`);
                audio.addEventListener('canplaythrough', () => {
                    const option = document.createElement('option');
                    option.value = i;
                    option.textContent = `Track ${i}`;
                    trackSelector.appendChild(option);
                    tracksLoaded++;
                    
                    // 自動選擇並播放第一個音檔
                    if (tracksLoaded === 1) {
                        trackSelector.value = 1;
                        playTrack(1);
                    }
                }, { once: true });

                audio.addEventListener('error', () => {
                    console.error(`無法載入 tracks/track-${i}.mp3`);
                });
            }
        }

        // 播放選定的音檔
        function playTrack(index) {
            const trackPath = `tracks/track-${index}.mp3`;
            audioPlayer.src = trackPath;
            audioPlayer.play();
        }

        // 當下拉式選單改變時，播放選定的音檔
        trackSelector.addEventListener('change', (event) => {
            const selectedIndex = event.target.value;
            playTrack(selectedIndex);
        });

        // 當前一個音檔播放完畢，自動播放下一首
        audioPlayer.addEventListener('ended', () => {
            const currentIndex = parseInt(trackSelector.value);
            const nextIndex = currentIndex + 1;
            
            // 檢查下一首是否在選單中
            const nextOption = trackSelector.querySelector(`option[value='${nextIndex}']`);
            if (nextOption) {
                trackSelector.value = nextIndex;
                playTrack(nextIndex);
            }
        });

        loadTracks();
    </script>
</body>
</html>

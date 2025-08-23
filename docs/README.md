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
            width: 100%;
            max-width: 800px;
            text-align: center;
        }
        #media-title {
            margin-top: 10px;
            color: #333;
            font-weight: bold;
            height: 24px;
        }
        #track-selector {
            width: 100%;
            padding: 10px;
            margin-bottom: 20px;
            border-radius: 4px;
            border: 1px solid #ccc;
            font-size: 16px;
        }
        #audio-player {
            margin-top: 10px;
            width: 100%;
        }
    </style>
    <script src="https://cdn.jsdelivr.net/npm/jsmediatags@3.9.5/dist/jsmediatags.min.js"></script>
</head>
<body>
    <div id="player-container">
        <h1>音樂播放器</h1>
        <select id="track-selector"></select>
        <h2 id="media-title">載入中...</h2>
        <audio id="audio-player" controls></audio>
    </div>

    <script>
        const trackSelector = document.getElementById('track-selector');
        const audioPlayer = document.getElementById('audio-player');
        const mediaTitle = document.getElementById('media-title');
        const totalTracks = 5; // 修改為你的音檔總數

        function loadTracks() {
            let tracksLoaded = 0;

            for (let i = 1; i <= totalTracks; i++) {
                const audio = new Audio(`tracks/track-${i}.mp3`);
                audio.addEventListener('canplaythrough', () => {
                    const option = document.createElement('option');
                    option.value = i;
                    option.textContent = `Track ${i}`;
                    trackSelector.appendChild(option);
                    tracksLoaded++;

                    if (tracksLoaded === 1) {
                        trackSelector.value = i;
                        playTrack(i);
                    }
                }, { once: true });

                audio.addEventListener('error', () => {
                    console.warn(`無法載入 tracks/track-${i}.mp3`);
                });
            }
        }

        function playTrack(index) {
            const trackPath = `tracks/track-${index}.mp3`;
            audioPlayer.src = trackPath;
            audioPlayer.play();

            // 讀取 MP3 metadata 標題
            jsmediatags.read(trackPath, {
                onSuccess: function(tag) {
                    const title = tag.tags.title || `Track ${index}`;
                    mediaTitle.textContent = title;
                },
                onError: function(error) {
                    console.warn(`無法讀取 metadata: ${error.type}`);
                    mediaTitle.textContent = `Track ${index}`;
                }
            });
        }

        trackSelector.addEventListener('change', (event) => {
            const selectedIndex = event.target.value;
            playTrack(selectedIndex);
        });

        audioPlayer.addEventListener('ended', () => {
            const currentIndex = parseInt(trackSelector.value);
            const nextIndex = currentIndex + 1;

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


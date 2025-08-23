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
            width: 200%;
            max-width: 800px;
            text-align: center;
        }
        #track-selector {
            width: 60%;
            padding: 10px;
            margin-bottom: 20px;
            border-radius: 4px;
            border: 1px solid #ccc;
            font-size: 16px;
        }
        #audio-player {
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div id="player-container">
        <h1>音樂播放器</h1>
        
        <h2 id="media-title">載入中...</h2>

        <select id="track-selector"></select>

        <audio id="audio-player" controls style="width: 100%;"></audio>
    </div>
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jsmediatags/3.9.5/jsmediatags.min.js"></script>

    <script>
        const trackSelector = document.getElementById('track-selector');
        const audioPlayer = document.getElementById('audio-player');
        const mediaTitle = document.getElementById('media-title');
        const totalTracks = 5; // 這裡請設定您的音檔總數

        function loadTracks() {
            let tracksLoaded = 0;
            const promises = [];

            for (let i = 1; i <= totalTracks; i++) {
                const trackPath = `tracks/track-${i}.mp3`;
                
                // 創建一個 Promise 來處理每個檔案的元數據讀取
                const promise = new Promise((resolve) => {
                    jsmediatags.read(trackPath, {
                        onSuccess: function(tag) {
                            const title = tag.tags.title || `Track ${i}`;
                            resolve({
                                index: i,
                                title: title,
                                path: trackPath
                            });
                        },
                        onError: function(error) {
                            console.error(`無法載入或讀取 ${trackPath} 的元數據：`, error);
                            // 即使失敗也解決 Promise，以便繼續處理下一個檔案
                            resolve(null);
                        }
                    });
                });
                promises.push(promise);
            }

            Promise.all(promises).then(results => {
                // 過濾掉讀取失敗的檔案
                const validTracks = results.filter(result => result !== null);

                // 將讀取到的資料動態加入選單
                validTracks.forEach(track => {
                    const option = document.createElement('option');
                    option.value = track.index;
                    option.textContent = track.title;
                    trackSelector.appendChild(option);
                });

                if (validTracks.length > 0) {
                    trackSelector.value = validTracks[0].index;
                    playTrack(validTracks[0].index, validTracks[0].title);
                } else {
                    mediaTitle.textContent = "未找到任何曲目";
                }
            });
        }

        function playTrack(index, title = null) {
            const trackPath = `tracks/track-${index}.mp3`;
            audioPlayer.src = trackPath;
            audioPlayer.play();
            
            // 更新標題，如果傳入了 title 則使用它，否則使用預設值
            mediaTitle.textContent = title || `Track ${index}`;
        }

        trackSelector.addEventListener('change', (event) => {
            const selectedIndex = parseInt(event.target.value);
            const selectedTitle = event.target.options[event.target.selectedIndex].textContent;
            playTrack(selectedIndex, selectedTitle);
        });

        audioPlayer.addEventListener('ended', () => {
            const currentIndex = parseInt(trackSelector.value);
            const nextIndex = currentIndex + 1;
            
            const nextOption = trackSelector.querySelector(`option[value='${nextIndex}']`);
            if (nextOption) {
                trackSelector.value = nextIndex;
                const nextTitle = nextOption.textContent;
                playTrack(nextIndex, nextTitle);
            }
        });

        loadTracks();
    </script>
</body>
</html>

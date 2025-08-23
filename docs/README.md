<!DOCTYPE html>
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
      background-color: #c0f4f4;
    }
    #player-container {
      background-color: #00a0f4;
      padding: 20px;
      border-radius: 20px;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
      width: 100%;
      max-width: 800px;
      text-align: center;
    }
    #media-title {
      margin-top: 0;
      color: #333;
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
</head>
<body>
  <div id="player-container">
    <h1>音樂播放器</h1>
    <h2 id="media-title">請選擇曲目</h2>
    <select id="track-selector"></select>
    <audio id="audio-player" controls></audio>
  </div>

  <!-- 引入 jsmediatags 套件 -->
  <script src="https://cdn.jsdelivr.net/npm/jsmediatags@3.9.5/dist/jsmediatags.min.js"></script>

  <script>
    const trackSelector = document.getElementById('track-selector');
    const audioPlayer = document.getElementById('audio-player');
    const mediaTitle = document.getElementById('media-title');
    const totalTracks = 5;

    function loadTracks() {
      for (let i = 1; i <= totalTracks; i++) {
        const trackPath = `tracks/track-${i}.mp3`;
        const option = document.createElement('option');
        option.value = i;
        option.textContent = `Track ${i}`; // 預設文字
        trackSelector.appendChild(option);

        // 嘗試讀取 metadata 更新選單文字
        jsmediatags.read(trackPath, {
          onSuccess: function(tag) {
            if (tag.tags.title) {
              option.textContent = tag.tags.title;
            }
          },
          onError: function() {
            // 保留預設文字
          }
        });
      }

      // 預設播放第一首
      trackSelector.value = 1;
      playTrack(1);
    }

    function playTrack(index) {
      const trackPath = `tracks/track-${index}.mp3`;
      audioPlayer.src = trackPath;
      audioPlayer.play().catch(err => {
        console.error(`播放失敗: ${err.message}`);
        mediaTitle.textContent = `無法播放 Track ${index}`;
      });

      // 嘗試讀取 metadata 更新標題
      jsmediatags.read(trackPath, {
        onSuccess: function(tag) {
          mediaTitle.textContent = tag.tags.title || `Track ${index}`;
        },
        onError: function() {
          mediaTitle.textContent = `Track ${index}`;
        }
      });
    }

    trackSelector.addEventListener('change', (event) => {
      playTrack(event.target.value);
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


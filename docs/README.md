<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>音樂播放器（含 metadata）</title>
  <script src="https://cdn.jsdelivr.net/npm/jsmediatags@3.9.7/dist/jsmediatags.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #c0f4f4;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }
    #player-container {
      background-color: #00a0f4;
      padding: 20px;
      border-radius: 20px;
      width: 100%;
      max-width: 600px;
      text-align: center;
      color: white;
    }
    select, audio {
      width: 100%;
      margin: 10px 0;
    }
    #metadata p {
      margin: 5px 0;
    }
  </style>
</head>
<body>
  <div id="player-container">
    <h1>音樂播放器</h1>
    <h2 id="media-title">請選擇曲目</h2>

    <div id="metadata" >
      <p align="left"><strong>標題:</strong> <span id="meta-title">-</span></p>
      <p align="left"><strong>專輯:</strong> <span id="meta-album">-</span></p>
      <p align="left"><strong>藝術家:</strong> <span id="meta-artist">-</span></p>
    </div>

    <select id="track-selector"></select>
    <audio id="audio-player" controls></audio>
  </div>

  <script>
    const totalTracks = 5; // 根據你有幾首歌決定
    const trackSelector = document.getElementById('track-selector');
    const audioPlayer = document.getElementById('audio-player');
    const mediaTitle = document.getElementById('media-title');

    const metaTitle = document.getElementById('meta-title');
    const metaAlbum = document.getElementById('meta-album');
    const metaArtist = document.getElementById('meta-artist');

    function loadTracks() {
      for (let i = 1; i <= totalTracks; i++) {
        const option = document.createElement('option');
        option.value = i;
        option.textContent = `Track ${i}`;
        trackSelector.appendChild(option);
      }
      // 預設播放第一首
      playTrack(1);
      trackSelector.value = 1;
    }

    function playTrack(index) {
      const filePath = `tracks/track-${index}.mp3`;
      audioPlayer.src = filePath;
      audioPlayer.play();

      mediaTitle.textContent = `Track ${index}`;
      metaTitle.textContent = ' ';
      metaAlbum.textContent = ' ';
      metaArtist.textContent = ' ';

      // 從網址載入 mp3 並轉為 blob
      fetch(filePath)
        .then(response => {
          if (!response.ok) throw new Error('MP3 載入失敗');
          return response.blob();
        })
        .then(blob => {
          jsmediatags.read(blob, {
            onSuccess: function(tag) {
              const tags = tag.tags;
              metaTitle.textContent = tags.title || '(無標題)';
              metaAlbum.textContent = tags.album || '(無專輯)';
              metaArtist.textContent = tags.artist || '(無藝術家)';
              if (tags.title) mediaTitle.textContent = tags.title;
            },
            onError: function(error) {
              console.error("Metadata 讀取失敗：", error);
            }
          });
        })
        .catch(error => {
          console.error("MP3 下載或 metadata 解析錯誤：", error);
        });
    }

    trackSelector.addEventListener('change', e => {
      playTrack(e.target.value);
    });

    audioPlayer.addEventListener('ended', () => {
      const current = parseInt(trackSelector.value);
      const next = current + 1;
      if (next <= totalTracks) {
        trackSelector.value = next;
        playTrack(next);
      }
    });

    loadTracks();
  </script>
</body>
</html>



<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>模組化音樂播放器</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f4f4f4;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      padding: 20px;
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
    #track-selector {
      width: 100%;
      padding: 10px;
      margin-bottom: 20px;
      font-size: 16px;
    }
    #audio-player {
      width: 100%;
      margin-top: 10px;
    }
    #media-info {
      margin-top: 10px;
      color: #333;
    }
  </style>
</head>
<body>
  <div id="player-container">
    <h1>音樂播放器</h1>
    <select id="track-selector"></select>
    <div id="media-info">
      <div id="media-title">標題：-</div>
      <div id="media-artist">歌手：-</div>
      <div id="media-album">專輯：-</div>
    </div>
    <audio id="audio-player" controls></audio>
  </div>

  <script>
    const trackSelector = document.getElementById('track-selector');
    const audioPlayer = document.getElementById('audio-player');
    const mediaTitle = document.getElementById('media-title');
    const mediaArtist = document.getElementById('media-artist');
    const mediaAlbum = document.getElementById('media-album');
    let trackList = [];

    async function loadTrackData() {
      try {
        const response = await fetch('tracks.json');
        trackList = await response.json();

        trackList.forEach(track => {
          const option = document.createElement('option');
          option.value = track.id;
          option.textContent = `${track.title} - ${track.artist}`;
          trackSelector.appendChild(option);
        });

        if (trackList.length > 0) {
          trackSelector.value = trackList[0].id;
          playTrack(trackList[0].id);
        }
      } catch (error) {
        console.error('載入曲目資料失敗：', error);
      }
    }

    function playTrack(id) {
      const track = trackList.find(t => t.id == id);
      if (!track) return;

      audioPlayer.src = track.file;
      audioPlayer.play();

      mediaTitle.textContent = `標題：${track.title}`;
      mediaArtist.textContent = `歌手：${track.artist}`;
      mediaAlbum.textContent = `專輯：${track.album}`;
    }

    trackSelector.addEventListener('change', (e) => {
      playTrack(e.target.value);
    });

    audioPlayer.addEventListener('ended', () => {
      const currentIndex = trackList.findIndex(t => t.id == trackSelector.value);
      const nextTrack = trackList[currentIndex + 1];
      if (nextTrack) {
        trackSelector.value = nextTrack.id;
        playTrack(nextTrack.id);
      }
    });

    loadTrackData();
  </script>
</body>
</html>

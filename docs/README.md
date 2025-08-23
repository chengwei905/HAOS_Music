
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <title>音樂播放清單</title>
  <style>
    body { font-family: sans-serif; text-align: center; padding: 2em; }
    audio { width: 80%; margin-top: 1em; }
  </style>
</head>
<body>
  <h1>🎶 自動播放清單</h1>
  <p id="nowPlaying">載入中...</p>
  <audio id="audioPlayer" controls autoplay></audio>

  <script>
    let tracks = [];
    let current = 0;

    const player = document.getElementById("audioPlayer");
    const nowPlaying = document.getElementById("nowPlaying");

    // 從 JSON 檔案載入播放清單
    fetch("tracks.json")
      .then(response => response.json())
      .then(data => {
        tracks = data.sort((a, b) => {
          // 根據 track-編號排序
          const numA = parseInt(a.match(/track-(\d+)/)?.[1] || 0);
          const numB = parseInt(b.match(/track-(\d+)/)?.[1] || 0);
          return numA - numB;
        });
        playTrack(current);
      })
      .catch(error => {
        nowPlaying.textContent = "載入播放清單失敗 ❌";
        console.error("錯誤：", error);
      });

    function playTrack(index) {
      if (index >= tracks.length) {
        nowPlaying.textContent = "播放完畢 🎉";
        return;
      }
      const src = tracks[index];
      player.src = src;
      nowPlaying.textContent = `正在播放：${src}`;
      player.play();
    }

    player.addEventListener("ended", () => {
      current++;
      playTrack(current);
    });
  </script>
</body>
</html>


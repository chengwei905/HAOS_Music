
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
  <audio id="audioPlayer" controls></audio>

  <script>
    const tracks = [
      "track1.mp3",
      "track2.mp3",
      "track3.mp3"
    ];
    let current = 0;

    const player = document.getElementById("audioPlayer");
    const nowPlaying = document.getElementById("nowPlaying");

    function playTrack(index) {
      if (index >= tracks.length) {
        nowPlaying.textContent = "播放完畢 🎉";
        return;
      }
      const src = tracks[index];
      player.src = src;
      nowPlaying.textContent = `正在播放：${src}`;
      
      // 嘗試播放音樂，並處理瀏覽器阻擋自動播放的情況
      player.play().catch(error => {
        console.log("自動播放被阻擋：", error);
        nowPlaying.textContent = `自動播放被阻擋，請點擊播放按鈕開始。`;
      });
    }

    player.addEventListener("ended", () => {
      current++;
      playTrack(current);
    });

    // 當頁面載入完成後，立即嘗試播放第一首歌曲
    document.addEventListener("DOMContentLoaded", () => {
      playTrack(current);
    });
  </script>
</body>
</html>


<!DOCTYPE html>
<html lang="ja">
        body {
            margin: 0;
            padding: 0;
            background-color: var(--bg-color);
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
            perspective: 1000px;
            font-family: 'Helvetica Neue', Arial, sans-serif;
        }

        /* 封筒のコンテナ（スマホの画面幅に合わせて可変） */
        .envelope-wrapper {
            position: relative;
            width: 80vw;
            max-width: 280px;
            aspect-ratio: 3 / 2; /* 縦横比を3:2に固定 */
            background-color: var(--envelope-inside);
            cursor: pointer;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        }

        /* 封筒のフタ（CSSのクリップパスでスマホでも崩れないように修正） */
        .flap {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 50%;
            background-color: var(--envelope-color);
            clip-path: polygon(0 0, 50% 100%, 100% 0);
            transform-origin: top;
            transition: transform 1.2s ease-in-out;
            z-index: 3;
        }

        /* 封筒の手前部分（ポケット） */
        .envelope-body {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: #c69363;
            clip-path: polygon(0 0, 0 100%, 100% 100%, 100% 0, 50% 50%);
            z-index: 2;
        }

        /* 手紙が開いたとき */
        .envelope-wrapper.open .flap {
            transform: rotateX(180deg);
            z-index: 1;
        }

        /* 溢れ出るアイテム（スマホの画面幅を基準にサイズ決定） */
        .overflow-item {
            position: absolute;
            width: 30vw;       /* スマホ画面の30%の横幅 */
            max-width: 120px;
            aspect-ratio: 4 / 3;
            background: #fff;
            padding: 4px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
            border-radius: 2px;
            opacity: 0;
            /* 封筒の中央付近からスタート */
            top: 35%;
            left: 35%;
            z-index: 5;
            object-fit: cover;
            pointer-events: auto; /* タップして拡大できるようにする場合はauto */
        }

        /* 飛び出すアニメーション（vw/vh を使って画面内に収める） */
        @keyframes floatOut {
            0% {
                transform: translate(0, 0) scale(0.2) rotate(0deg);
                opacity: 0;
            }
            15% {
                opacity: 1;
            }
            100% {
                transform: translate(var(--tx), var(--ty)) scale(var(--scale)) rotate(var(--rot));
                opacity: 1;
            }
        }
    </style>
</head>
<body>

    <div class="envelope-wrapper" id="envelope" onclick="openEnvelope()">
        <div class="flap"></div>
        <div class="envelope-body"></div>
    </div>

    <script>
        function openEnvelope() {
            const envelope = document.getElementById('envelope');
            if (envelope.classList.contains('open')) return;
            envelope.classList.add('open');

            setTimeout(() => {
                createMemories();
            }, 1200);
        }

        function createMemories() {
            // スマホ画面（中央から上下左右）に散らばるように調整
            // tx: 横方向（-vw 〜 +vw）, ty: 縦方向（-vh 〜 +vh）
            const items = [
                { type: 'image', src: '20260524_photo.jpg', tx: '-10vw', ty: '-10vh', rot: '-12deg', scale: '2.0' },
                // あなたが手紙を書いている動画
                { type: 'video', src: 'https://www.w3schools.com/html/mov_bbb.mp4', tx: '22vw', ty: '10vh', rot: '10deg', scale: '2.5' }
            ];

            items.forEach((item, index) => {
                let el;
                if (item.type === 'video') {
                    el = document.createElement('video');
                    el.src = item.src;
                    el.autoplay = true;
                    el.muted = true;
                    el.loop = true;
                    el.setAttribute('playsinline', ''); // iOS Safariで全画面起動するのを防ぐ
                } else {
                    el = document.createElement('img');
                    el.src = item.src;
                }

                el.classList.add('overflow-item');
                
                el.style.setProperty('--tx', item.tx);
                el.style.setProperty('--ty', item.ty);
                el.style.setProperty('--rot', item.rot);
                el.style.setProperty('--scale', item.scale);
                
                // スマホのテンポ感に合わせて、少し早めにバラバラと出す（0.3秒間隔）
                el.style.animation = `floatOut 1.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) ${index * 0.3}s forwards`;

                document.body.appendChild(el);
            });
        }
    </script>
</body>
</html>

# AR護衛シューティング

MindAR.js + A-Frame で動作するスマホ AR ゲームです。
マーカー画像を認識すると人型 AR キャラクターが出現し、四方から迫る侵食エフェクトをタップで撃退します。

---

## ゲームの遊び方

1. ローカルサーバーを起動してスマホブラウザで開く
2. カメラを **マーカー** に向ける
3. AR キャラが表示されたら準備完了
4. 四方（上下左右）から青白い光の粒子が迫ってくる
5. **タップ** すると中央に最も近いパーティクルを撃退
6. パーティクルが中央に到達すると HP が 20 減少
7. HP が 0 になると **GAME OVER**
8. 全 5 ウェーブをクリアすると **「ありがとう！」** と表示

---

## セットアップ手順

### 1. 必要なもの

- Node.js（http-server のインストールに使用）
- スマートフォン（Chrome 推奨）
- PC と スマホが **同じ Wi-Fi** に接続されていること

### 2. http-server のインストール（初回のみ）

```bash
npm install -g http-server
```

### 3. targets.mind ファイルの生成

`targets.mind` はマーカー画像をコンパイルした必須ファイルです。
以下の手順で生成してください。

#### オンラインコンパイラを使う方法（推奨・最も簡単）

1. ブラウザで MindAR Image Target Compiler を開く
   URL: `https://hiukim.github.io/mind-ar-js-doc/tools/compile`
2. **「Upload Images」** ボタンをクリック
3. このプロジェクトの **`marker.svg`** をアップロード
   （SVG が読み込めない場合は後述の方法で PNG に変換してください）
4. **「Compile」** ボタンを押してコンパイル完了を待つ
5. **「Export」** ボタンで `targets.mind` をダウンロード
6. ダウンロードした `targets.mind` をこのフォルダに置く

#### marker.svg を PNG に変換する方法

ブラウザで `marker.svg` を開き、右クリック→「名前を付けて画像を保存」で
`marker.png` として保存してからコンパイラにアップロードしてください。

または Node.js で変換する場合：

```bash
npm install sharp
node -e "require('sharp')('marker.svg').png().toFile('marker.png', ()=>{})"
```

### 4. フォルダ構成の確認

```
ar-game/
├── index.html       # ゲーム本体
├── targets.mind     # ★ 生成したファイルをここに置く
├── marker.svg       # マーカー画像（印刷 or 画面表示用）
└── README.md
```

### 5. ローカルサーバーの起動

```bash
cd ar-game
http-server . -p 8080
```

起動後に表示されるアドレス例：
```
Available on:
  http://192.168.x.x:8080   ← スマホからはこちらのアドレスを使う
  http://127.0.0.1:8080
```

### 6. スマホで開く

1. スマホの Chrome で `http://192.168.x.x:8080` を入力
2. カメラのアクセス許可を **「許可」** する
3. マーカーを印刷するか PC モニターに `marker.svg` を表示してカメラを向ける

> **HTTPS について**: MindAR はカメラアクセスに `https` または `localhost` を要求します。
> `http-server` の代わりに `https` 対応サーバーを使いたい場合は以下を利用してください：
>
> ```bash
> # mkcert で自己署名証明書を作成する例
> npm install -g mkcert
> mkcert create-ca && mkcert create-cert
> http-server . -p 8080 -S -C cert.pem -K key.pem
> ```

---

## マーカーについての注意

- マーカーは **高コントラスト・非対称** のデザインが追跡精度に優れます
- 印刷時は A4 サイズで **10〜15cm 角** 程度を推奨
- 光沢紙より **マット紙** の方が反射が少なくトラッキングが安定します
- マーカーの周囲に **白い余白（5mm 以上）** を確保してください

---

## トラブルシューティング

| 症状 | 対処法 |
|------|--------|
| カメラが起動しない | Chrome のサイト設定でカメラ許可を確認 |
| マーカーを認識しない | 照明を明るくし、マーカーとカメラの距離を 20〜40cm に調整 |
| `targets.mind` 読み込みエラー | ファイルが ar-game フォルダに正しく配置されているか確認 |
| 画面が真っ暗 | http-server が起動しているか・アドレスが正しいか確認 |
| iOS Safari で動かない | iOS 14.5 以上の Safari または Chrome for iOS を使用 |

---

## 技術構成

- **MindAR.js** 1.2.5 — Image Target AR トラッキング
- **A-Frame** (MindAR バンドル版) — 3D / AR レンダリング
- **Web Audio API** — 将来の音響拡張用
- 外部依存: CDN のみ（オフライン不可）

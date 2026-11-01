# PAKÍIN! 公式サイト（静的HTML）

Claude のデザインキャンバスで作ったデザインを、そのまま GitHub Pages に置ける静的サイトに書き出したものです。
ビルド工程・フレームワーク・サーバー処理はありません。

## 構成

```
index.html      トップ
privacy.html    プライバシーポリシー（legal.html の本文を文言そのままで収録）
terms.html      利用規約（同上）
support.html    サポート（お問い合わせ・よくある質問）
404.html        GitHub Pages の「ページが見つかりません」（同じヘッダー・フッター・配色）
.nojekyll       GitHub Pages の Jekyll 処理を止める空ファイル
assets/
  icon.png              アプリアイコン（512px。favicon / apple-touch-icon も兼用）
  hero.mp4              ヒーローの破裂動画（H.264・音声なし・15秒。末尾のコントロールセンターはカット済み）
  hero-poster.jpg       動画の読み込み前・動きを減らす設定の人向けの静止画
  screen-todo.png       機能④（やることタブ。CSSで上部だけ表示）
  screen-routine.png    機能⑤（ルーチンタブ）
  02_crack_count.png    機能②
  03_undo.png           機能③
  06_today_tab.png      機能⑥
  07_alarm.png          機能⑦
  08_history.png        機能⑧（画像クリックで原寸表示）
  09_drive_backup.png   機能⑨
  *.webp                上記画像の WebP 版（同じ縦横・同じトリミング）。<picture> で WebP を優先し、png/jpg を fallback に使う
  crack_tap3.webp       「ためしに割ってみる」のヒビ画像（アプリの src/assets/textures/crack_tap3.webp を 1200×300 に縮小）
  og-image.png          （未配置）SNS 共有用 1200×630。現在の og:image は icon.png（512×512）
```

## デザインの決まりごと（変えないこと）

- 配色：オレンジ #FF7A24 → ピンク #FF4D8A のグラデーション、クリーム #FFF6EE、文字 #3A1A12、差し色イエロー #FFD23F
- 書体：見出し M PLUS Rounded 1c、本文 Noto Sans JP（Google Fonts）
- 装飾：目のあるガラスの破片キャラ、波形の区切り、アーチ型の料金カード、白いピルボタン
- 文言：規約・ポリシーの目次・本文・制定日と全ページの問い合わせ先は、アプリのリポジトリの `npm run build:legal` で生成する。ここでは手編集しない。運営者表記はフッターが「© 2026 Bootstrapp Works」、規約・ポリシー本文が「Bootstrapp Works」（フッターは生成対象外のため手編集）
- App Store ボタンは「App Storeで近日公開」の仮表示（disabled のボタン）。Apple 公式バッジは自作・模写しない

## 動きについて

- 「ためしに、割ってみて。」は index.html 末尾の素の JavaScript で動作（3回タップで破裂 →「もう一回」で復帰）。音は鳴らさない
  - アプリ（React Native）のソースを移植：ヒビの出方 crackStageTable.ts、反射面 shardPolygonData.ts / jitterShardPoints.ts、
    タップ時の押しつぶし・残り1回の震え・「-1」 TaskCard.tsx、破裂 ShardBurst.tsx（20枚の破片・空気抵抗つき放物運動・閃光・厚み・光る辺）
  - 1回目でヒビ画像、2回目で反射面、3回目でタップした位置を中心に破裂
  - ヒビ画像（1600×400）には x96〜1535・y30〜356 にカードの輪郭が描かれているので、その輪郭がカードの縁に重なる比率（left -6.671% / top -9.202% / width 111.19% / height 122.7%）で配置。
    アプリの OFFSET(-20,-5)px はアプリのカード寸法専用で、サイトのカードではずれるため使わない
- prefers-reduced-motion の人には、動画の代わりに静止画。浮遊・流れる帯などの自動の動きも止める
- ただし「ためしに割ってみる」の破裂は、タップした人だけが見る動きなので設定に関係なく再生する
  （Windows の「アニメーション効果」オフでも reduce 扱いになり、割れずに消えるだけになっていたため）
- スクロール表示（.reveal）は CSS の animation-timeline: view() を使用。未対応ブラウザでは常に表示されるだけで、崩れはない

## 書き出し時に手を入れた点

- キャンバス用の記法（テンプレート変数・条件分岐）を通常の HTML と JavaScript に置き換え
- ページ間リンクを index.html / privacy.html / terms.html / support.html に変更
- 本文中の手動改行（br）は PC 幅のときだけ有効（class="pc"、719px 以下では非表示）
- キャンバスでの手動リサイズで付いた固定幅（width: 569px など）は max-width に変更（スマホではみ出さないように）
- icon.png と画面画像2枚は、縦横比を保ったまま縮小（容量削減）
- head に viewport・description・theme-color・favicon・OGP（title/description）を追加

## 確認済み（書き出し後に実ブラウザで確認）

- 4ページとも 375px / 1280px で横スクロールなし、画像の読み込み切れなし、ページ内外のリンク切れなし
- 「ためしに割ってみる」が 3回タップで破裂、もう一回で復帰すること
- コンソールエラーなし（Google Fonts は確認環境の通信制限で読めなかったが、公開環境では読み込まれる）

## GitHub Pages での公開

- このフォルダをリポジトリ直下としてプッシュし、Settings → Pages で「Deploy from a branch」「main / (root)」を選ぶ
- 独自ドメインを使う場合は CNAME を追加
- 404.html は、URL が <ユーザー名>.github.io/<リポジトリ名>/… のときは先頭のパスを、それ以外（独自ドメインなど）はドメイン直下を起点に画像・リンクを読む。
  <ユーザー名>.github.io という名前のリポジトリで公開する場合は、404.html 冒頭のスクリプトの起点を '/' に固定すること

## 公開前の残タスク（任意）

- OGP 画像：1200×630 の画像を assets/og-image.png に置き、各ページの og:image / twitter:image と寸法を差し替え、twitter:card を summary_large_image に戻す
- App Store 公開後：「App Storeで近日公開」ボタンを Apple 公式の「App Storeからダウンロード」バッジ（apple.com の公式素材）とアプリの URL に差し替え

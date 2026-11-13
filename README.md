# ame-lyrics

曲の同期歌詞(LRC)を探す小さなコマンド(LRCLIB・NetEase・Kugou、キャッシュつき)。

[ame-shell](https://github.com/TatsuyaM2667/ame-shell)(Wayland のデスクトップシェル)の一部を、単独で使えるように切り出したものです。
ame-shell は画面の要らない仕事を、1 つのことをして標準入出力でやりとりする小さなコマンドに分けています。これはその 1 つです。

## 使い方

```text
ame-lyrics: 曲の同期歌詞(LRC)を探して標準出力へ出す。

  ame-lyrics [-d 秒] [--music-tui URL|on|off] [--no-lrclib] [--no-netease] [--no-kugou] [--no-cache] アーティスト 曲名

終了コード: 0 = 見つかった(LRC を出した) / 1 = どこにも無かった / 2 = 通信できない・混雑(あとで探し直せる)
どこで見つかったかは標準エラーへ 1 行で出す。

取り寄せ先(上から順に、同期歌詞が見つかった時点で終わり。同期の無い歌詞しか無ければ、残りで同期歌詞を探してから使う):
  1. キャッシュ(~/.cache/ame-shell/lyrics/<曲のハッシュ>)
  2. music-tui の歌詞サーバ(--music-tui。on なら ~/music-tui(-bsd)/.env の WORKERS_URL)
  3. LRCLIB の /api/get(アーティスト + 曲名 + 長さ)→ /api/search
  4. NetEase Cloud Music の検索 → 歌詞
  5. 酷狗(Kugou)の検索 → 歌詞(base64 の LRC)。長さの近い候補を選ぶ(ライブ版を避ける)
  6. 曲名だけで LRCLIB を検索(長さが 3 秒以内で合うものだけ)
検索の前に、動画サイトの書き方(「チャンネル名 - Topic」「【MV】アーティスト「曲名」」など)を整える(lrc.normalize)。
どこにも無かったことは「無し」としてキャッシュするが、通信できない・混雑のときは覚えない。
通信は curl(シェルを通さず引数で渡すので、曲名に何が入っていても安全)。
```

## ビルド

```sh
zig build -Doptimize=ReleaseSafe   # zig-out/bin/ame-lyrics(Zig 0.13、libc だけ)
zig build test
```

実行に要るもの: `curl`

## このリポジトリについて

正本は ame-shell の `src/cmd/ame-lyrics.zig` です。このリポジトリは `scripts/split-repos.sh` が生成して同期しています。
直したいところがあれば ame-shell の方へお願いします。

ライセンス: GPL-3.0(ame-shell と同じ)

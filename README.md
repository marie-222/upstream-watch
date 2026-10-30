# upstream-watch

自分でパッケージしているソフトの upstream に、新しい版が出たかを毎朝確認するための一覧。

- `watch.yaml` が監視対象の一覧。`have` に、自分が上げ終えた版を書く。
- 毎朝（5:00 JST ごろ）、claude.ai のクラウド routine が `watch.yaml` を読み、`have` より新しい版があればスマホに通知する。
- クラウド側は GitHub に繋がらず、`raw.githubusercontent.com` だけが使える。そのため、版を読めるファイル（日付つきのチェンジログなど）がある対象だけが監視できる。タグしか手がかりがない対象は監視できない。
- このリポジトリは公開で、中身は「監視しているプロジェクトと版番号」だけ。

## GitHub のリリースを読みたいとき

github.com と api.github.com は routine から読めない（このセッションに結びついたリポジトリ以外は 403）。代わりに `https://ungh.cc/repos/<owner>/<repo>/releases/latest` が、リリースの名前・タグ・公開日・リリースノート（markdown）を JSON で返す。ungh.cc はクラウド環境の「許可ドメイン」に入れてある前提（Default 環境）。第三者のサービスなので、止まったら取得失敗の通知になる。

## 版を読める元がないとき

本体のリポジトリに、リリース済みの版を読めるファイルがない対象は、Homebrew の formula（`raw.githubusercontent.com/Homebrew/homebrew-core/master/Formula/<頭文字>/<名前>.rb` の `url` の行）の版で代用できる。リリースから数時間で更新される。日付は取れない（`pattern` のグループ1だけ）。メインラインがリリース前の版を持つ対象（例: rustls は main が `0.24.0-dev.*`）は、リリース用のブランチ（`rel-0.23`）の `Cargo.toml` を読む。

## 項目を足す

`watch.yaml` の `watch:` に 1 件足す。`pattern` は、その `source` を 1 行ずつ照合して、版（グループ1）と公開日（グループ2）が取れるものにする。

## 版を上げたとき

新しい版をパッケージし終えたら、該当の項目の `have` を書き換えて push する。

```
git commit -am "kitty の have を 0.50.0 に更新"
git push
```

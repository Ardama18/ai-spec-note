
---

## 0. 大前提・注意点

* note公式ヘルプには

  > 現在、noteが公式で公開しているAPIはありません。今後の公開予定も未定です
  > と明記されています（＝公式サポートなし）。([note（ノート）][1])
* ネット上には、ブラウザ通信を観察してまとめた **「note非公式API」一覧や解説記事** があり、それを使うと投稿や画像アップロードができます。([noteで記録した日常と写真と技術][2])
* ただし非公式なので

  * 仕様が予告なく変わる
  * 利用規約に触れる可能性がある
  * アカウント凍結リスクなどもゼロではない
    という点は、**必ず自分で規約を読んで判断**してください。([note（ノート）][1])

以下は「技術的にどうやれば動くか」という情報です。

---

## 1. 非公式APIでサムネ付き記事を作る全体フロー

1. **ログインしてセッションを作る**
   `POST https://note.com/api/v1/sessions/sign_in` に
   `{"email": "メール", "password": "パスワード"}` を投げて、クッキー付き `Session` を作る。([note（ノート）][3])

2. **空のテキストnoteを作成**
   `POST https://note.com/api/v1/text_notes` で記事の骨組みを作り、
   レスポンスから `id` と `key`（URLに使うキー）を受け取る。([note（ノート）][4])

3. **サムネイル画像をアップロード**
   `POST https://note.com/api/v1/upload_image` に multipart/form-data で画像ファイルを送ると、
   `data.key`（画像キー）と `data.url` が返ってくる。([note（ノート）][4])

4. **記事を更新して下書き保存（サムネ紐づけ）**
   `PUT https://note.com/api/v1/text_notes/{id}` に
   `body`（HTML）、`name`（タイトル）、`status: "draft"`、`eyecatch_image_key`（さっきの画像キー）を送る。([note（ノート）][4])

これで note の管理画面に「サムネ付きの下書き」ができる、という流れです。

---

## 2. ざっくりAPI仕様メモ

### 2-1. ログイン（セッション作成）

* **URL**
  `POST https://note.com/api/v1/sessions/sign_in`
* **認証**
  ここではまだ不要（メール＋パスワードを送る）
* **リクエストBody（JSON）** 例

  ```json
  {
    "email": "your-email@example.com",
    "password": "your-password"
  }
  ```
* **レスポンス**
  `200` が返り、`Set-Cookie` でセッションCookieが返る。
  以降はこのCookie付きでAPIを叩く。([note（ノート）][3])

---

### 2-2. テキストnote作成

* **URL**
  `POST https://note.com/api/v1/text_notes`
* **認証**
  ログイン済みセッションの Cookie が必須。
* **Body（JSON）** 例

  ```json
  {
    "name": "タイトル文字列",
    "body": "<h1>本文はHTMLで</h1><p>Markdownは事前に変換してください</p>",
    "template_key": null
  }
  ```
* **レスポンス（抜粋）**

  * `data.id` … 記事ID（後で PUT するときに使う）
  * `data.key` … 記事URL用のキー（ `https://note.com/{ユーザーID}/n/{key}` ）

---

### 2-3. 画像アップロード（サムネ用）

* **URL**
  `POST https://note.com/api/v1/upload_image`
* **Body（multipart/form-data）**

  * `file` … 画像ファイル（PNG/JPEG/GIFなど）
* **レスポンス（抜粋）**

  * `data.key` … 画像キー（後で `eyecatch_image_key` に入れる）
  * `data.url` … note 上の画像URL([note（ノート）][4])

---

### 2-4. 記事更新＋サムネ付与（下書き保存）

* **URL**
  `PUT https://note.com/api/v1/text_notes/{id}`
* **Body（JSON）** 例

  ```json
  {
    "name": "タイトル文字列",
    "body": "<h1>本文HTML</h1>",
    "status": "draft",
    "eyecatch_image_key": "画像キー"
  }
  ```

  * `status: "draft"` … 下書き
    （公開ステータスの値は公式に出ておらず、ここでは「まずは下書きにする」想定です）
* **効果**
  指定IDの記事が更新され、サムネが設定された状態の「下書き」ができる。([note（ノート）][4])

---

## 3. Pythonでの超シンプル実装例

> 前提：
>
> * Python 3.x
> * `requests` を使用
> * 本文はすでに HTML 文字列として持っている前提
>   （Markdownなら `markdown` ライブラリなどで先にHTMLに変換してください）

### 3-1. 必要なライブラリインストール

```bash
pip install requests
```

### 3-2. コード例

```python
import requests

BASE_URL = "https://note.com/api"


def login(email: str, password: str) -> requests.Session:
    """note にログインして Cookie 付き Session を返す"""
    sess = requests.Session()
    payload = {"email": email, "password": password}
    r = sess.post(f"{BASE_URL}/v1/sessions/sign_in", json=payload)
    r.raise_for_status()
    return sess


def create_text_note(sess: requests.Session, title: str, html_body: str):
    """空記事を作成して (id, key) を返す"""
    data = {
        "name": title,
        "body": html_body,
        "template_key": None,
    }
    r = sess.post(f"{BASE_URL}/v1/text_notes", json=data)
    r.raise_for_status()
    d = r.json()["data"]
    return d["id"], d["key"]


def upload_image(sess: requests.Session, image_path: str):
    """画像をアップロードして (image_key, image_url) を返す"""
    with open(image_path, "rb") as f:
        files = {"file": f}
        r = sess.post(f"{BASE_URL}/v1/upload_image", files=files)
    r.raise_for_status()
    d = r.json()["data"]
    return d["key"], d["url"]


def update_note_draft(
    sess: requests.Session,
    article_id: int,
    title: str,
    html_body: str,
    eyecatch_key: str | None = None,
):
    """記事に本文とサムネを設定して下書き保存"""
    data = {
        "name": title,
        "body": html_body,
        "status": "draft",  # まずは下書きとして保存
    }
    if eyecatch_key:
        data["eyecatch_image_key"] = eyecatch_key

    r = sess.put(f"{BASE_URL}/v1/text_notes/{article_id}", json=data)
    r.raise_for_status()
    return r.json()


if __name__ == "__main__":
    # ★ここは自分の情報に置き換える
    EMAIL = "your-email@example.com"
    PASSWORD = "your-password"
    USER_ID = "your_user_id"  # URL に使うID (例: https://note.com/USER_ID/...)

    title = "API経由の投稿テスト"
    html_body = """
    <h1>こんにちは</h1>
    <p>これは API 経由で作った下書きです。</p>
    """
    thumbnail_path = "thumbnail.png"

    # 1) ログイン
    sess = login(EMAIL, PASSWORD)

    # 2) 記事の骨組み作成
    article_id, article_key = create_text_note(sess, title, html_body)

    # 3) サムネ画像アップロード
    image_key, image_url = upload_image(sess, thumbnail_path)

    # 4) 本文＋サムネを設定して下書き保存
    update_note_draft(sess, article_id, title, html_body, image_key)

    print("下書きURL（目安）:")
    print(f"https://note.com/{USER_ID}/n/{article_key}")
```

このコードを叩くと、

* noteアカウントにログイン
* テスト記事を作成
* `thumbnail.png` をアップロードしてサムネに設定
* サムネ付き下書きが note 側にできる

という動きになります。

---

## 4. よくあるハマりポイント

1. **2段階認証や外部ログインの場合**

   * Googleログイン / Twitterログインだけで使っているアカウントだと、
     `email + password` ログインAPIが使えないことがあります。
   * その場合は「メール＋パスワードでログインできる状態」を作るか、
     ブラウザ自動操作（Selenium / Playwright）でログイン＋投稿する方式の方が現実的です。([note（ノート）][5])

2. **Markdownをそのまま投げると崩れる**

   * `body` は HTML 前提なので、Markdownのまま送るとレイアウトが崩れます。
   * `markdown` ライブラリなどで一度HTMLにしてから送るのがおすすめです。

3. **レート制限（429）**

   * 短時間に連打すると 429 が返ることがあります。
   * 1リクエストごとに数秒 sleep するなど、控えめに。([note（ノート）][5])

4. **公開まで完全自動化したい場合**

   * `status` の取りうる値は公式ドキュメントがないので、
     まずは **「draftで保存 → noteの管理画面で最終チェックして公開」**
     という運用をおすすめします。

---

## 5. もう少しちゃんとやるなら

* メール・パスワードは `.env` や環境変数に入れて、コードに直書きしない（記事のサンプルでもそうしてます）。([note（ノート）][5])
* 投稿ロジックを関数化しておき、cronやGitHub Actionsなどから定期実行。([note（ノート）][4])
* ビジネス用途でガチ運用するなら、**非公式APIでの投稿自動化はリスク高め**なので、

  * 投稿自体は手動／noteの予約投稿機能
  * 公開後の拡散や分析だけAPI＆外部ツールで自動化
    という構成も検討した方が安全です。([note（ノート）][1])

---

もし「言語はPythonじゃなくて○○で書きたい」「公開まで全自動に寄せたい」などあれば、その前提でサンプル書き直します！

[1]: https://note.com/kawayasblog/n/n2fa8bfee9e3d "「noteへの自動アップロード」は可能か？APIの有無・合法ルート・現実解までぜんぶまとめ｜カワヤス"
[2]: https://note.egg-glass.jp/%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0/2024/06/28/noteAPI.html "2024年版 note API 非公式一覧表 | noteで記録した日常と写真と技術"
[3]: https://note.com/marthay/n/n6e34cb7ad761 "〖noteを伸ばす×データ分析〗Note APIでフォロワーといいねを可視化｜note AIニュース"
[4]: https://note.com/taku_sid/n/n1b1b7894e28f?utm_source=chatgpt.com "うさぎでもわかる🐰note非公式APIで記事を自動投稿する方法"
[5]: https://note.com/taku_sid/n/n1b1b7894e28f "うさぎでもわかるnote非公式APIで記事を自動投稿する方法｜taku_sidエージェント"

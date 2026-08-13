# セットアップ手順（Windows + VS Code）

`crypto-data-analysis-course` の教材（.ipynb ファイル）を **VS Code の中で** 動かすための手順書です。
Python も Jupyter も初めてという前提で書いています。**上から順にそのままやれば終わります。**

- 所要時間: 15〜20分（ダウンロード時間を含む）
- 使うもの: VS Code、PowerShell（VS Code の中のターミナル）
- Python は**事前にインストールしなくて大丈夫です**（STEP 3 で自動的に入ります）

> このファイルは VS Code で開いて `Ctrl` + `Shift` + `V` を押すと、いつもの Markdown プレビューで読めます。

---

## 0. これから何をするのか（全体像）

やることは3つだけです。

| | やること | なぜ必要か |
|---|---|---|
| ① | **uv** というツールを入れる | Python 本体と、教材専用の作業部屋（仮想環境）を用意してくれる係 |
| ② | VS Code に**拡張機能を2つ**入れる | これで `.ipynb` が VS Code の中で開けるようになる |
| ③ | 教材フォルダに**専用の Python 環境**を作り、ライブラリを入れる | 教材が使う pandas などを揃える |

**「仮想環境」とは？**
教材フォルダの中に作る、その教材専用の小さな Python 一式のことです。フォルダの中に `.venv` という隠しフォルダとして作られます。
これを使うと、別の勉強や仕事で入れたライブラリと混ざらず、壊れても `.venv` フォルダごと消してやり直せます。Python では**プロジェクトごとに1つ作るのが標準的なお作法**です。

---

## STEP 1: uv をインストールする

### 1-1. VS Code で教材フォルダを開く

VS Code を起動 → メニューの **ファイル** → **フォルダーを開く** → `D:\crypto-data-analysis-course` を選択。

「このフォルダー内のファイルの作成者を信頼しますか?」と聞かれたら **「はい、作成者を信頼します」** を選んでください。

### 1-2. ターミナルを開く

VS Code のメニューから **ターミナル** → **新しいターミナル**（ショートカット: `Ctrl` + `Shift` + `@`）。

画面下部にターミナルが開きます。右上に `powershell` または `pwsh` と表示されていれば OK です。
先頭に `PS D:\crypto-data-analysis-course>` のように出ていることを確認してください。**この「今どのフォルダにいるか」は後で重要になります。**

### 1-3. uv を入れる

ターミナルに以下を貼り付けて `Enter`。

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

> 補足: `winget` を使い慣れているなら `winget install --id=astral-sh.uv -e` でも構いません。

### 1-4. ターミナルを閉じて開き直す

**これを飛ばすと次のコマンドが「見つかりません」になります。**
ターミナル右上のゴミ箱アイコンで閉じて、もう一度 **ターミナル** → **新しいターミナル**。

そして確認:

```powershell
uv --version
```

`uv 0.x.x` のようにバージョンが表示されれば成功です。

<details>
<summary>`uv : 用語 'uv' は、コマンドレット... として認識されません` と出た場合</summary>

ターミナルの開き直しがうまくいっていません。VS Code を一度**完全に終了**して、起動し直してからもう一度試してください。
</details>

---

## STEP 2: VS Code に拡張機能を2つ入れる

左端のアイコンバーから **拡張機能**（四角が4つのアイコン、`Ctrl` + `Shift` + `X`）を開き、以下2つを検索してインストールします。

| 検索する名前 | 発行元 | 役割 |
|---|---|---|
| **Python** | Microsoft | Python のコードを扱えるようにする |
| **Jupyter** | Microsoft | `.ipynb` を VS Code の中で開いて実行できるようにする |

> 同名の紛らわしい拡張機能があります。**発行元が Microsoft** のものを選んでください。
> `Python` を入れると `Pylance` と `Python Debugger` も一緒に入りますが、それで正常です。

---

## STEP 3: 教材専用の Python 環境を作る

ターミナルが `PS D:\crypto-data-analysis-course>` になっていることを確認して、以下を実行します。

```powershell
uv venv --python 3.11 --seed
```

これで教材フォルダの中に `.venv` フォルダが作られます。Python 3.11 本体が手元になければ、uv が自動でダウンロードしてくれます（初回は1〜2分かかります）。

最後に `Activate with: .venv\Scripts\activate` のような行が出れば成功です。**この activate は今は実行しなくて大丈夫です**（VS Code が自動でやってくれます）。

### なぜ 3.11 なのか（重要）

教材が使う **`japanize-matplotlib`**（グラフの日本語表示用）は、**Python 3.12 以降では動きません。**
内部で `distutils` という Python 3.12 で削除されたモジュールを使っているためで、`import japanize_matplotlib` の行で

```
ModuleNotFoundError: No module named 'distutils'
```

というエラーで止まります。このライブラリは更新が止まっており、修正される見込みは薄いです。

Python 3.11 なら、他のライブラリ（pandas・matplotlib・scipy）は最新版でも問題なく動くことを確認済みです。
なので**「最新の Python を入れる」のではなく、あえて 3.11 を指定する**のが正解です。

### `--seed` は何のためか

教材の各ノートブックの冒頭には `!pip install ...` というセルがあります。`--seed` を付けると仮想環境の中に `pip` が入るので、このセルがエラーにならずに済みます。付け忘れると `pip が見つかりません` で止まります。

---

## STEP 4: ライブラリをインストールする

同じターミナルで:

```powershell
uv pip install requests pandas numpy matplotlib japanize-matplotlib scipy ipykernel jupyterlab
```

`uv pip install` は、そのフォルダにある `.venv` を自動で見つけて、そこに入れてくれます。

各ライブラリの役割:

| ライブラリ | 役割 |
|---|---|
| requests | Binance API からデータを取ってくる |
| pandas | 表形式のデータ操作（教材の主役） |
| numpy | 数値計算 |
| matplotlib | グラフ描画 |
| japanize-matplotlib | グラフの日本語表示 |
| scipy | 統計検定（Vol.02 以降） |
| **ipykernel** | **VS Code でノートブックを実行するために必須** |
| jupyterlab | ブラウザ版 Jupyter（使わなければ無くても可） |

数十秒で `Installed NN packages` と出れば完了です。

### （任意）`.venv` を Git の管理外にする

このフォルダは Git リポジトリなので、`.venv` を作ると `git status` に大量の未追跡ファイルが出ます。気になる場合は:

```powershell
Add-Content .git\info\exclude "`n.venv/"
```

`.gitignore` ではなく `.git\info\exclude` に書くのがポイントです。こちらは自分の手元だけの設定なので、教材が更新されて `git pull` するときに衝突しません。

---

## STEP 5: ノートブックを開いてカーネルを選ぶ

1. VS Code 左のエクスプローラーから **`vol01_data_acquisition_and_cleansing.ipynb`** をクリック。
2. ノートブックが開きます（初回は読み込みに数十秒かかることがあります）。
3. 画面の**右上**に **「カーネルの選択」**（または `Select Kernel`）というボタンがあるのでクリック。
4. **「Python 環境...」** → 一覧から **`.venv (Python 3.11.x)`** / `Recommended` と付いているものを選ぶ。

右上の表示が `.venv (Python 3.11.x)` に変われば準備完了です。

> **カーネルとは？** ノートブックのコードを実際に動かす Python 本体のことです。ここで「教材フォルダの `.venv` を使え」と指定しています。
> 一覧に `.venv` が出てこない場合は、VS Code を再起動してください（`Ctrl` + `Shift` + `P` → `Developer: Reload Window`）。

---

## STEP 6: 動作確認する

いきなり Vol.01 を走らせる前に、同じフォルダに置いた **`00_setup_check.ipynb`** を開いて、上のセルから順に実行してください。

- ライブラリが全部入っているか
- グラフの日本語が文字化けしないか（□□□ にならないか）
- Binance API に接続できるか

の3点を一気に確認できます。**すべて ✅ になれば、Vol.01 に進んで大丈夫です。**

---

## ノートブックの基本操作（VS Code 版）

| 操作 | キー |
|---|---|
| セルを実行して次のセルへ | `Shift` + `Enter` |
| セルを実行してその場に留まる | `Ctrl` + `Enter` |
| 上から全部実行 | 上部の **「すべて実行」** ボタン |
| 実行を止める | セル左の ■ ボタン |
| セルの編集モードを抜ける | `Esc` |
| （`Esc` 後）下に新しいセルを追加 | `B` |
| （`Esc` 後）セルを削除 | `D` `D`（D を2回） |
| 変数の中身を見る | 上部の **「変数」** ボタン |

### セルの状態の見分け方

| 状態 | 左のボタン | 括弧の中 |
|---|---|---|
| 未実行 | ▷（再生の三角） | `[ ]` |
| **実行中** | **□ / ■（停止の四角）** | `[*]` または空、インジケーターが回る |
| **完了** | **▷ に戻る** | **`[1]` のように数字が入り、`✓ 3.3s` と所要時間が出る** |
| エラー | ▷ に戻る | 数字が入り、下に赤いエラー表示 |

**覚えておくと事故が減ること:**

- セル左の `[1]` `[2]` という数字は**実行した順番**です。上から順に実行しないと、「まだ定義されていない変数」でエラーになります。
- 迷ったら上部の **「再起動」** → **「すべて実行」** で最初からやり直せます。
- ノートブックは実行結果ごと保存されます。`Ctrl` + `S` で保存してください。
- **正常でも時間がかかるセルがあります。** Vol.01 のデータ取得セルは1年分を1000本ずつ繰り返し取得するので、数十秒〜数分かかります。インジケーターが回っていれば動いています。

---

## 2回目以降の使い方

セットアップは最初の1回だけです。次回からは:

1. VS Code で `D:\crypto-data-analysis-course` を開く
2. `.ipynb` をクリックして開く
3. カーネルが `.venv (Python 3.11.x)` になっているか右上で確認して、実行

これだけです。ターミナル操作は不要です。

---

## つまずきポイント集

### `ModuleNotFoundError: No module named 'japanize_matplotlib'`

カーネルが `.venv` ではなく別の Python を指しています。STEP 5 に戻って、右上のカーネル表示が `.venv (Python 3.11.x)` になっているか確認してください。

### `ModuleNotFoundError: No module named 'distutils'`

Python 3.12 以降の環境で動いています。`.venv` を作り直してください:

```powershell
Remove-Item -Recurse -Force .venv
uv venv --python 3.11 --seed
uv pip install requests pandas numpy matplotlib japanize-matplotlib scipy ipykernel jupyterlab
```

その後 VS Code を再読み込み（`Ctrl` + `Shift` + `P` → `Developer: Reload Window`）してカーネルを選び直します。

### グラフの日本語が □□□（豆腐）になる

`import japanize_matplotlib` の行が実行されていないか、エラーで飛ばされています。ノートブックを**上のセルから順に**実行し直してください。

### ノートブックの `!pip install` セルでエラーが出る

STEP 4 で先に全部入れてあるので、**このセルはスキップしても問題ありません。**
どうしても実行したい場合は、`!pip install` を **`%pip install`** に書き換えてください。`%` 版は「今のカーネルの環境に入れる」ことが保証されるので確実です。

### `pip install` セルが何分も終わらない（□ のまま止まる）

`pip` は「もう入っています」と答えるだけの場面でも配布サーバー（PyPI）に接続しにいくため、そこが遅いと待ち続けて止まって見えます。`-q` が付いているので進捗も表示されません。

**対処:** 停止ボタン（□）を押しても止まらず「Interrupting Kernel」が出続けることがあります。その場合は中断ではなく**再起動**してください。

1. ノートブック上部の **↻ 再起動** ボタン
2. 効かなければ `Ctrl` + `Shift` + `P` → `Jupyter: Restart Kernel`
3. それでも駄目なら **VS Code を完全に終了して起動し直す**（カーネルのプロセスも一緒に落ちます）

再起動後は `pip install` セルを飛ばし、次の `import` セルから実行すれば問題なく進めます。**Vol.02〜06 の冒頭にも同じセルがあるので、毎回飛ばして構いません。**

### `Note: you may need to restart the kernel to use updated packages.` と出た

エラーではありません。`pip` が毎回出す定型の注意書きです。パッケージが実際に入れ替わっていなければ、**再起動せずそのまま進めて大丈夫**です。

### `HTTPError: 451` / `Service unavailable from a restricted location`

Binance の公開 API が、接続元の地域を理由にブロックしています。教材のコード内の

```python
BINANCE_API_URL = "https://api.binance.com/api/v3/klines"
```

を、公開マーケットデータ専用のミラーに差し替えると通ることがあります:

```python
BINANCE_API_URL = "https://data-api.binance.vision/api/v3/klines"
```

同じデータ・同じパラメータで、API キーも不要です。

### データ取得のセルが終わらない

Vol.01 は1年分の1時間足を1000本ずつ繰り返し取得するので、**正常でも数十秒〜数分かかります。**
セル左のインジケーターが回っていれば動いています。急ぐ場合は `since_date` を `'2024-10-01'` などに縮めて試してください。

### VS Code が「Python インタープリターが選択されていません」と言う

`Ctrl` + `Shift` + `P` → `Python: Select Interpreter` → `.venv` の中の Python を選択してください。

---

## 補足: ブラウザの JupyterLab で開きたくなったら

VS Code で十分ですが、解説記事の画面と揃えたいときなどはこちらでも開けます。ターミナルで:

```powershell
.\.venv\Scripts\jupyter.exe lab
```

ブラウザが自動で開きます。終了はターミナルで `Ctrl` + `C` を2回。
（`activate` してから `jupyter lab` でも同じですが、上の書き方なら PowerShell の実行ポリシーで引っかかりません。）

---

## 環境を作り直したくなったら

`.venv` フォルダを消すだけです。教材ファイル自体には一切影響しません。

```powershell
Remove-Item -Recurse -Force .venv
```

あとは STEP 3 からやり直してください。

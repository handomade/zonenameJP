# zonename（zonenameJP）

Ashita v4 向けアドオンです。ゾーン移動時にゾーン名とリージョン名を短時間表示します。

これは [onimitch/ffxi-zonename](https://github.com/onimitch/ffxi-zonename)（Windower zonename の Ashita 移植。オリジナル: sylandro / Xenonsmurf）のフォークです。

- ゲーム内のアドオン名 / フォルダ名 / コマンドは **zonename** のままです。
- このリポジトリ名は **zonenameJP** です。
- 作者: **Hando**　バージョン: **2.4h**
- Ashita または Vanacore の言語が日本語のとき、ゾーン名とリージョン名を日本語で表示します。
- 既定の見た目は白文字・黒縁です。

## 経緯

Windower の zonename（sylandro）を Ashita v4 に移植したのが Xenonsmurf 版です。リージョン名は英語（`region.en`）だけで、日本語切り替えはありません。CatsEyeXI クライアントに同梱されていたのも、この Xenonsmurf 版でした。

その後 [onimitch/ffxi-zonename](https://github.com/onimitch/ffxi-zonename) が、同じ系統を GdiFonts 描画・`settings.lua`・`/zonename reload` 付きで整備し、**日本語表示そのものはすでに入っています**（`regions.lua` の `ja`、ゾーン名の Shift-JIS → UTF-8）。README に大きく書いていないだけで、仕組みはあります。

ただし言語判定が次のようになっていました。

```lua
playonline == 1 なら日本語
```

Ashita の PlayOnline 言語は **0 = 日本語、1 = 英語（NA）** です。CatsEye の起動設定は `playonline=0` / `ashita=0`（Ashita 側は PlayOnline に追随）なので、onimitch 本体をそのまま入れても英語のままになります。日本語化できるのに、判定が Ashita の番号とずれている、という状態でした。

この **zonenameJP** はその判定を直し、Ashita の `ashita.language` と PlayOnline の両方を見ます。あわせて設定の書き方と色の 16 進指定を README にまとめ、日本語向けの既定フォントを Meiryo 系にしています。

Vanacore ではさらに番号が違います。`vanacore.ini` の `[language] playonline=1` が日本語、`2` が英語です。2.4h からは Vanacore のこの値も見ます。

ゾーン名は英語クライアントの DAT に日本語が無いことが多いので、2.4h で `zone_names.lua`（Windower Resources 由来の en/ja）を同梱しました。ファイル名を `zones.lua` にしないのは、Vanacore が同名の英語ゲームデータを先に読み込むためです。

![Example](https://github.com/onimitch/ffxi-zonename/blob/main/Example.png "Example")

## インストール

1. このリポジトリ（または Release）から `zonename` フォルダを入手します。
2. `Ashita/addons` にコピーします。`gdifonts` サブモジュールも含めてください。

```
git clone --recurse-submodules https://github.com/handomade/zonenameJP.git
```

すでにクローン済みなら:

```
git submodule update --init --recursive
```

3. ゲーム内で次を実行します。

```
/addon load zonename
```

自動ロードする場合は `Ashita/scripts/default.txt` に同じ行を追加します。

```
/addon unload zonename
/addon reload zonename
```

## 設定の編集手順

設定はキャラクターごとに保存されます。

1. 次のファイルを開きます。

```
Ashita/config/addons/zonename/<キャラクター名>/settings.lua
```

初回ロード後に作られます。無い場合は一度 `/addon load zonename` してください。
2. 保存します。
3. ゲーム内で反映します（アンロード不要です）。

```
/zonename reload
```

動作確認:

```
/zonename test
```

Lua の構文です。文字列は `"Meiryo"`、真偽に相当するフラグは数値、色は 16 進でも 10 進でも、文字列 `"#FFD700"` でも書けます。

### 設定例

```lua
require('common');

local settings = T{ };
settings["fade_after"] = 5;
settings["fade_duration"] = 1;

settings["zone_name"] = T{ };
settings["zone_name"]["font_family"] = "Meiryo";
settings["zone_name"]["font_height"] = 50;
settings["zone_name"]["font_flags"] = 1;          -- 1 = Bold
settings["zone_name"]["font_color"] = 0xFFFFFFFF; -- 白
settings["zone_name"]["outline_color"] = 0xFF000000;
settings["zone_name"]["outline_width"] = 2;
settings["zone_name"]["position_x"] = 1280;
settings["zone_name"]["position_y"] = 380;

settings["region_name"] = T{ };
settings["region_name"]["font_family"] = "Meiryo";
settings["region_name"]["font_height"] = 20;
settings["region_name"]["font_flags"] = 1;
settings["region_name"]["font_color"] = 0xFFFFFFFF;
settings["region_name"]["outline_color"] = 0xFF000000;
settings["region_name"]["outline_width"] = 2;
settings["region_name"]["position_x"] = 1280;
settings["region_name"]["position_y"] = 350;

return settings;
```

実際のファイルには `font_alignment` など他のキーもあります。不要なら触らなくて構いません。

## 設定項目

### 全体

| キー | 内容 | 例 |
| --- | --- | --- |
| `fade_after` | 表示してからフェード開始までの秒数 | `5` |
| `fade_duration` | フェードにかける秒数 | `1` |

### `zone_name` / `region_name`（同じキー）

| キー | 内容 | 例 |
| --- | --- | --- |
| `font_family` | Windows フォント名 | `"Meiryo"` / `"Yu Gothic"` / `"MS Gothic"` |
| `font_height` | 文字サイズ | ゾーン `50`、リージョン `20` |
| `font_flags` | `0` 通常 / `1` 太字 / `2` 斜体 / 足し算可（太字+斜体=`3`） | `1` |
| `font_color` | 文字色 | `0xFFFFFFFF`（白） |
| `outline_color` | 縁の色 | `0xFF000000` |
| `outline_width` | 縁の太さ。`0` でなし | `2` |
| `position_x` / `position_y` | 画面上の位置 | |
| `font_alignment` | `0` 左 / `1` 中央 / `2` 右 | `1` |

日本語クライアントでフォントが `Calibri` や `Century Schoolbook` のままなら、初回に Meiryo 系へ置き換えます。その後は設定ファイルの値が使われます。指定フォントが無い場合は Arial に戻します。

## 色の書き方

色は **AARRGGBB**（先頭 2 桁が不透明度。`FF` が不透明）です。

| 書き方 | 例 |
| --- | --- |
| Lua 16 進数 | `0xFFFFD700` |
| 10 進数 | `4294956800`（上と同じ金） |
| 文字列 `#RRGGBB` | `"#FFD700"`（不透明として扱う） |
| 文字列 `#AARRGGBB` | `"#FFFFD700"` |
| 文字列 `0x…` | `"0xFFFFD700"` |

よく使う色: 金 `0xFFFFD700`、白 `0xFFFFFFFF`、黒 `0xFF000000`、赤 `0xFFFF0000`。

アドオンが設定を保存し直すと、16 進の数値が 10 進数に書き換わることがあります。値は同じです。16 進の見た目を残したいときは `"#FFD700"` のように文字列にしてください。

## 表示について

- リージョン名は `regions.lua` の `en` / `ja` です。
- ゾーン名は `zone_names.lua` の `en` / `ja` です（Windower Resources 由来。日本語 DAT が無くても日本語が出ます）。ファイル名を `zones.lua` にしないのは、Vanacore が同名の英語ゲームデータを先に読み込むためです。
- 言語判定は Ashita（`playonline=0` が日本語）と Vanacore（`playonline=1` が日本語）の両方を見ます。
- 描画は GdiFonts（UTF-8）です。
- 既定色は文字 `0xFFFFFFFF`（白）、縁 `0xFF000000`（黒）です。

## 変更履歴

### 2.4h

- ゾーン名の日本語を `zone_names.lua` から表示（英語 DAT でも可）
- Vanacore の `playonline=1` を日本語として判定
- `require('zones')` をやめ、Vanacore の英語ゾーン表との衝突を回避
- 既定色を白文字・黒縁に変更

### 2.3h

- Ashita の言語判定修正、16 進色、設定 README、作者 Hando のフォーク公開

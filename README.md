# zonename（zonenameJP）

Ashita v4 向けアドオンです。ゾーン移動時にゾーン名とリージョン名を短時間表示します。

これは [onimitch/ffxi-zonename](https://github.com/onimitch/ffxi-zonename)（Windower zonename の Ashita 移植。オリジナル: sylandro / Xenonsmurf）のフォークです。

- ゲーム内のアドオン名 / フォルダ名 / コマンドは **zonename** のままです。
- このリポジトリ名は **zonenameJP** です。
- 作者: **Hando**　バージョン: **2.3h**
- Ashita または PlayOnline の言語が日本語のとき、地名を日本語で表示します。

![Example](https://github.com/onimitch/ffxi-zonename/blob/main/Example.png "Example")

## インストール

1. このリポジトリ（または Release）から `zonename` フォルダを入手します。
2. `Ashita/addons` にコピーします。`gdifonts` サブモジュールも含めてください。

```
git clone --recurse-submodules <このリポジトリの URL>
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
settings["zone_name"]["font_color"] = 0xFFFFD700; -- 金
settings["zone_name"]["outline_color"] = 0xFF000000;
settings["zone_name"]["outline_width"] = 2;
settings["zone_name"]["position_x"] = 1280;
settings["zone_name"]["position_y"] = 380;

settings["region_name"] = T{ };
settings["region_name"]["font_family"] = "Meiryo";
settings["region_name"]["font_height"] = 20;
settings["region_name"]["font_flags"] = 1;
settings["region_name"]["font_color"] = 0xFFFFD700;
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
| `font_color` | 文字色 | `0xFFFFD700` |
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

- 日本語: ゾーン名はゲームリソース、リージョン名は `regions.lua` の `ja` です。
- 英語: `en` を使います。
- 描画は GdiFonts（UTF-8）です。

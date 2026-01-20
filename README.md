# Marge PDF Tool 📄

![Python](https://img.shields.io/badge/Python-3.x-blue.svg) ![License](https://img.shields.io/badge/License-MIT-green.svg)

業務自動化（RPA）シナリオにおいて、画像のPDF化と結合（マージ）を自動で行うPythonツールです。
**WinActor** などのRPAツールと連携し、複雑なファイル操作を高速かつ安定して処理するために開発されました。

## 🚀 背景・解決した課題

1.  **RPAの機能補完**
    WinActor単体では実装が複雑になりがちな「可変長の画像ファイル結合」を、Pythonスクリプトでシンプルに実装し、処理速度とメンテナンス性を向上させました。

2.  **配布時のセキュリティ問題解決**
    PyInstallerでexe化した際に発生する「アンチウイルスソフト（ApexOne等）の誤検知」に対し、**Inno Setup** を用いたインストーラー形式での配布を採用することで回避。現場へのスムーズな導入を実現しました。

## ⚙️ 処理フロー

```mermaid
graph LR
    Input[画像フォルダ] -->|読み込み| Script[Marge_PDF.py]
    Config[config.ini] -.->|設定読込| Script
    Script -->|変換・結合| PDF[結合されたPDF]
    PDF -->|受渡| RPA[WinActor / 業務フロー]
```

## 📦 機能

* **画像 → PDF変換**: 指定フォルダ内の画像を読み込み、PDFページとして結合します。
* **ファイル名制御**: ファイル名の接頭辞に基づいてグルーピングを行います。
* **設定ファイル対応**: フォルダパスや動作設定を `config.ini` で外部から変更可能です（コード修正・再ビルド不要）。

## 🛠 動作環境

* OS: Windows 10 / 11
* Python: 3.x
* RPAツール: WinActor (必須ではありません)

## 📖 使い方

### 1. Python環境で実行する場合

```bash
# リポジトリのクローン
git clone [https://github.com/skibidikids/Marge.git](https://github.com/skibidikids/Marge.git)
cd Marge

# 必要なライブラリのインストール
# (コード内のimportに合わせて必要なものを入れてください)
# pip install img2pdf pypdf

# 実行
python Marge_PDF.py
```

### 2. 設定ファイル (config.ini)

実行ファイルと同じ階層に `config.ini` を配置して制御します。

```ini
[Settings]
InputDirectory = ./input
OutputDirectory = ./output
# 必要に応じて設定項目を記述
```

## 🏗 配布用ビルド手順 (DevOps)

開発環境からエンドユーザー（現場）へ配布するためのフローです。

1.  **Exe化**: `PyInstaller` を使用して単一実行ファイルを作成。
2.  **インストーラー作成**: `Inno Setup` を使用し、exe本体と `config.ini`、マニュアルをパッケージング。
3.  **配布**: 生成された `setup.exe` をユーザーに配布（署名問題や誤検知を回避）。

```bash
# PyInstallerでのビルド例
pyinstaller Marge_PDF.py --onefile --noconsole --name "MargePDF"
```

## 📝 License

This project is licensed under the MIT License.

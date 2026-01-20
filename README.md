# Marge PDF Tool 📄

![Build Status](https://github.com/skibidikids/Marge/actions/workflows/build.yml/badge.svg) ![Python](https://img.shields.io/badge/Python-3.x-blue.svg) ![License](https://img.shields.io/badge/License-MIT-green.svg)

業務自動化（RPA）シナリオにおいて、画像のPDF化と結合（マージ）を自動で行うPythonツールです。
**WinActor** などのRPAツールと連携し、複雑なファイル操作を高速かつ安定して処理するために開発されました。

## 🚀 背景・解決した課題

1.  **RPAの機能補完**
    WinActor単体では実装が複雑になりがちな「可変長の画像ファイル結合」を、Pythonスクリプトでシンプルに実装し、処理速度とメンテナンス性を向上させました。

2.  **DevOps（自動ビルド・配布）の実現**
    GitHub ActionsによるCI/CDパイプラインを構築。コードが更新されると自動的にExeファイルが生成される仕組みを導入し、手動ビルドの手間と人的ミスを排除しました。

3.  **セキュリティ対策**
    配布時のアンチウイルスソフト（ApexOne等）誤検知問題に対し、**Inno Setup** を用いたインストーラー形式でのパッケージング手法を確立し、現場へのスムーズな導入を実現しました。

## ⚙️ 処理フロー

```mermaid
graph LR
    Input["画像フォルダ"] -->|読み込み| Script["Marge_PDF.py"]
    Config["config.ini"] -.->|設定読込| Script
    Script -->|変換・結合| PDF["結合されたPDF"]
    PDF -->|受渡| RPA["WinActor / 業務フロー"]
```

## 📦 機能

* **画像 → PDF変換**: 指定フォルダ内の画像を読み込み、PDFページとして結合します。
* **ファイル名制御**: ファイル名の接頭辞に基づいてグルーピングを行います。
* **設定ファイル対応**: フォルダパスや動作設定を `config.ini` で外部から変更可能です（コード修正・再ビルド不要）。

## 🛠 動作環境

* OS: Windows 11
* Python: 3.13.9
* RPAツール: WinActor

## 📖 使い方

### 1. Python環境で実行する場合

```bash
# リポジトリのクローン
git clone [https://github.com/skibidikids/Marge.git](https://github.com/skibidikids/Marge.git)
cd Marge

# 必要なライブラリのインストール
# pip install img2pdf pypdf pillow

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

## 🏗 ビルドとデプロイ (CI/CD)

本リポジトリは **GitHub Actions** により、Pushごとの自動ビルド環境が構築されています。

### 1. 自動ビルド (推奨)
GitHubの **[Actions]** タブから、ビルドの成功履歴を確認できます。
各ビルドの概要ページ下部にある **Artifacts** から、生成された `MargeTool-exe` をダウンロード可能です。

### 2. 手動ビルド手順
ローカル環境で開発ビルドを行う場合は、PyInstallerを使用します。

```bash
# Exe化
pyinstaller Marge_PDF.py --onefile --noconsole --name "MargeTool"
```

その後、必要に応じて **Inno Setup** スクリプトを実行し、配布用インストーラーを作成します。

## 📝 License

This project is licensed under the MIT License.

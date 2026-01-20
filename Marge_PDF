import sys
import re
import shutil
from collections import defaultdict
from pathlib import Path
from datetime import datetime

from PIL import Image
from pypdf import PdfWriter


def convert_png_to_pdf(input_dir: Path, output_dir: Path):
    """
    指定されたディレクトリ内のすべてのPNG画像をPDFに変換し、別のディレクトリに保存します。

    Args:
        input_dir (Path): PNG画像が格納されているディレクトリのパス。
        output_dir (Path): 変換後のPDFを保存するディレクトリのパス。
    """
    output_dir.mkdir(exist_ok=True)
    png_files = list(input_dir.glob('*.png'))

    if not png_files:
        print(f"ディレクトリ '{input_dir}' にPNGファイルが見つかりませんでした。")
        return

    print("\nPNGファイルをPDFに変換します:")
    for png_file in png_files:
        pdf_filename = f"{png_file.stem}.pdf"
        pdf_path = output_dir / pdf_filename

        with Image.open(png_file) as im:
            # PDF保存のためにRGBモードに変換
            im_rgb = im.convert('RGB')
            im_rgb.save(pdf_path, "PDF", resolution=100.0)
        print(f"- {png_file.name} -> {pdf_filename}")

def merge_pdfs_by_prefix(directory_path: Path, output_directory: Path, timestamp: str):
    """
    指定されたディレクトリ内のPDFファイルを、ファイル名の接頭辞（数字の前の部分）に基づいてグループ化し、
    グループごとに1つのPDFファイルに結合します。

    Args:
        directory_path (Path): PDFファイルが格納されているディレクトリのパス。
        output_directory (Path): 結合後のPDFファイルを出力するディレクトリのパス。
        timestamp (str): ファイル名に追加するタイムスタンプ文字列。
    """
    # pathlibを使ってPDFファイルを取得
    pdf_files = list(directory_path.glob('*.pdf'))

    if not pdf_files:
        print(f"ディレクトリ '{directory_path}' にPDFファイルが見つかりませんでした。")
        return

    # ファイル名を接頭辞でグループ化する
    # 例: 「田中太郎1.pdf」と「田中太郎2.pdf」は「田中太郎」グループに
    grouped_files = defaultdict(list)
    # 数字の前の部分を抽出する正規表現
    # 例: "田中太郎1.pdf" -> "田中太郎"
    prefix_pattern = re.compile(r'([^\d]+)')

    for pdf_file in pdf_files:
        filename = pdf_file.name
        match = prefix_pattern.match(filename)
        if match:
            prefix = match.group(1)
            grouped_files[prefix].append(pdf_file)

    if not grouped_files:
        print("結合対象となるPDFグループが見つかりませんでした。")
        return

    # 出力ディレクトリが存在しない場合は作成
    output_directory.mkdir(exist_ok=True)

    # グループごとにPDFを結合
    for prefix, files in grouped_files.items():
        # ファイル名から数字を抽出して、その数値でソートする
        def extract_number(p: Path):
            # ファイル名から数字部分を検索
            num_match = re.search(r'(\d+)', p.stem)
            # 数字が見つかればその数値を、見つからなければ0を返す
            return int(num_match.group(1)) if num_match else 0

        sorted_files = sorted(files, key=extract_number)

        merger = PdfWriter()
        print(f"\nグループ '{prefix}' のファイルを結合します:")
        for filepath in sorted_files:
            print(f"- {Path(filepath).name}")
            merger.append(filepath)

        output_filename = output_directory / f"{prefix}_{timestamp}.pdf"
        merger.write(output_filename)
        merger.close()
        print(f"-> グループ '{prefix}' の結合が完了しました。出力ファイル: {output_filename}")

def clear_directory_contents(directory: Path):
    """
    指定されたディレクトリの中身（ファイルとサブフォルダ）をすべて削除します。
    ディレクトリ自体は削除しません。

    Args:
        directory (Path): 中身を空にするディレクトリのパス。
    """
    if not directory.is_dir():
        return

    print(f"\n一時フォルダ '{directory.name}' の中身をクリーンアップします...")
    for item in directory.iterdir():
        try:
            if item.is_dir():
                shutil.rmtree(item)
                print(f"- サブフォルダ: {item.name} を削除しました。")
            else:
                item.unlink()
                print(f"- ファイル: {item.name} を削除しました。")
        except Exception as e:
            print(f"エラー: {item.name} の削除中にエラーが発生しました: {e}")


if __name__ == '__main__':
    run_timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    # PyInstallerでexe化された場合と、スクリプトとして実行された場合の両方で
    # 正しい基準パスを取得するための処理
    if getattr(sys, 'frozen', False):
        # exeファイルとして実行された場合
        # exeファイルはdistフォルダ内にあるため、その親フォルダを基準にする
        BASE_DIR = Path(sys.executable).parent.parent
    else:
        # スクリプトとして実行された場合
        BASE_DIR = Path(__file__).resolve().parent

    capture_directory = BASE_DIR / 'capture'
    pdfs_directory = BASE_DIR / 'PDFs'
    output_directory = BASE_DIR / 'merged_output'

    # 必要なフォルダが存在しない場合は作成する
    capture_directory.mkdir(exist_ok=True)
    pdfs_directory.mkdir(exist_ok=True)
    output_directory.mkdir(exist_ok=True)

    try:
        # 1. PNG画像をPDFに変換して 'PDFs' フォルダに保存
        convert_png_to_pdf(capture_directory, pdfs_directory)

        # 2. 'PDFs' フォルダ内のPDFをグループごとにマージ
        merge_pdfs_by_prefix(pdfs_directory, output_directory, run_timestamp)

        print("\nすべての処理が正常に完了しました。")

    finally:
        # 3. 一時ファイルをクリーンアップ
        clear_directory_contents(capture_directory)
        clear_directory_contents(pdfs_directory)
        print("\nクリーンアップが完了しました。")


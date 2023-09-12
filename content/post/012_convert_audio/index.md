---
title: "pythonで音声変換スクリプト書いた"
date: 2023-08-16T09:00:00+09:00
description:
draft: true
tags:
- python
---
自分用に、pythonでflac/ogg/wavファイルをmp3に変換しその他適宜楽曲情報やファイル名を直すスクリプトを書きました。せっかくなので公開。
<!--more-->
今後の個人活動の一環としてこの記事ではコードを軽く解説します。

## コード全体
大袈裟かもだけど一応[リポジトリ](https://github.com/ch1ak1STR/audio_convert_sample)も作った。  

コード全文は下記。

```python
from mutagen.id3 import ID3, APIC, TIT2, TALB, TPE1, TCON, TDRC, TCOM,TPE2
from pydub import AudioSegment
from tqdm import tqdm
import os, warnings, re, sys, shutil

# 指定したフォーマットの音楽ファイルを検出
def find_music_files(folder_path):
    music_extensions = ['.mp3', '.flac', '.ogg', '.wav']
    music_files = []
    for root, _, files in os.walk(folder_path):
        for file in files:
            if any(file.lower().endswith(ext) for ext in music_extensions):
                music_files.append(os.path.join(root, file))
    return music_files

# 音楽ファイルを変換
def convert_to_mp3(input_file, output_file):
    try:
        audio = AudioSegment.from_file(input_file)
        audio.export(output_file, format='mp3', bitrate='320k')
        os.remove(input_file)
    except Exception as e:
        print(f"Error converting {input_file} to mp3: {str(e)}")

# ファイル名から "XX. "（Xは0から9の数字）を削除
def change_music_file_name(file_path):
    new_file_name = re.sub(r"^\d+\.\s+", "", os.path.basename(file_path))
    new_file_path = os.path.join(os.path.dirname(file_path), new_file_name)
    os.rename(file_path, new_file_path)
    return new_file_path

# 音声ファイルを編集
def edit_audio_file(file_path):
    new_file_path = change_music_file_name(file_path)
    change_music_info(new_file_path)
    remove_album_art(new_file_path)

# 楽曲情報とアルバムアートを変更
def change_music_info(file_path):
    try:
        # ID3 タグを新規作成
        audio = ID3(file_path)
        # タイトル情報を設定
        audio['TIT2'] = TIT2(text=["Privacy Protection"])
        # アルバム情報を設定
        audio['TALB'] = TALB(text=["Privacy Protection"])
        # アーティスト情報を設定
        audio['TPE1'] = TPE1(text=["Privacy Protection"])
        # ジャンル情報を設定
        audio['TCON'] = TCON(text=["Privacy Protection"])
        # アルバムアーティスト情報を設定
        audio['TPE2'] = TPE2(text=["Privacy Protection"])
        # 作曲者情報を設定
        audio['TCOM'] = TCOM(text=["Privacy Protection"])
        # 製作年情報を設定  
        audio['TDRC'] = TDRC(text=["9999"])
        audio.save(file_path)
        return True
    except Exception as e:
        print(f"Error changing music info and artwork for {file_path}: {str(e)}")
        return False

# アルバムアートを削除
def remove_album_art(file_path):
    try:
        audio = ID3(file_path)
        audio.delall("APIC")
        audio.save()
        return True
    except Exception as e:
        print(f"Error removing album art from {file_path}: {str(e)}")
        return False

# メイン処理
def main(folder_path):
    music_files = find_music_files(folder_path)
    progress_bar = tqdm(total=len(music_files), unit="file")
    for file_path in music_files:
        # flac/ogg/wavファイルをmp3に変換
        if file_path.lower().endswith(('.flac', '.ogg', '.wav')):
            mp3_output_path = os.path.splitext(file_path)[0] + '.mp3'
            convert_to_mp3(file_path, mp3_output_path)
            edit_audio_file(mp3_output_path)
        else : 
            edit_audio_file(file_path)
        progress_bar.update(1)
    progress_bar.close()

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python script.py /path/to/folder")
        sys.exit(1)
    target_folder = sys.argv[1]
    if not os.path.exists(target_folder):
        print("Folder does not exist.")
        sys.exit(1)
    main(target_folder)
```

## コード解説
実行される処理順に解説。

### main処理
```python
if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python script.py /path/to/folder")
        sys.exit(1)
    target_folder = sys.argv[1]
    if not os.path.exists(target_folder):
        print("Folder does not exist.")
        sys.exit(1)
    main(target_folder)
```
あんまり解説することなし。引数が1個かどうか確認して違えばUsageを表示、引数のディレクトリがなければ注意する。

引数に問題なければmain処理へ。

```python
# メイン処理
def main(folder_path):
    music_files = find_music_files(folder_path)
    progress_bar = tqdm(total=len(music_files), unit="file")
    for file_path in music_files:
        # flac/ogg/wavファイルをmp3に変換
        if file_path.lower().endswith(('.flac', '.ogg', '.wav')):
            mp3_output_path = os.path.splitext(file_path)[0] + '.mp3'
            convert_to_mp3(file_path, mp3_output_path)
            edit_audio_file(mp3_output_path)
        else : 
            edit_audio_file(file_path)
        progress_bar.update(1)
    progress_bar.close()
```
ここもあまり解説要素なし。引数として受け取ったフォルダの中を対象として諸々の関数を実行するだけ。

その他、処理済みファイル/処理対象のファイルを進捗状況として表示するようにしている。寂しいので追加した。

### find_music_files
```python
# 指定したフォーマットの音楽ファイルを検出
def find_music_files(folder_path):
    music_extensions = ['.mp3', '.flac', '.ogg', '.wav']
    music_files = []
    for root, _, files in os.walk(folder_path):
        for file in files:
            if any(file.lower().endswith(ext) for ext in music_extensions):
                music_files.append(os.path.join(root, file))
    return music_files
```
この関数では予め決めておいた特定拡張子のファイルを引数のフォルダから検索している。

拡張子リスト「music_extensions」に検索したいファイルの拡張子をセットしておく。

その後検索でヒットしたファイルを管理するための空リスト「music_files」を用意。

os.walk(folder_path)でフォルダ内のサブフォルダも全て再帰的に走査して指定した拡張子について、さらにfor文で要素ごとに内包表記で指定した拡張子かの判定をし、mp3/wav/flac/oggの場合music_filesリストに追加する。

### convert_to_mp3
```python
# 音楽ファイルを変換
def convert_to_mp3(input_file, output_file):
    try:
        audio = AudioSegment.from_file(input_file)
        audio.export(output_file, format='mp3', bitrate='320k')
        os.remove(input_file)
    except Exception as e:
        print(f"Error converting {input_file} to mp3: {str(e)}")
```
プログラムの趣旨ともいえる音声ファイルの変換処理だが、この記述量で実装できる。

第一引数は処理対象のファイル、第二引数は新しく作成するmp3ファイルのパス。

変換後は元のファイルが残るので、変換に問題が無ければ元ファイルは消す。

### change_music_file_name
```python
# ファイル名から "XX. "（Xは0から9の数字）を削除
def change_music_file_name(file_path):
    new_file_name = re.sub(r"^\d+\.\s+", "", os.path.basename(file_path))
    new_file_path = os.path.join(os.path.dirname(file_path), new_file_name)
    os.rename(file_path, new_file_path)
    return new_file_path
```
音声ファイルの名前から連番を消す。正規表現を使って連番部を抜き出して消している。

どうでもいい小ネタだけど、退屈な正規表現がらみはGPT君に聞くと工数減らせがちです。(今回のは簡単だけど...)

### edit_audio_file
```python
# 音声ファイルを編集
def edit_audio_file(file_path):
    new_file_path = change_music_file_name(file_path)
    change_music_info(new_file_path)
    remove_album_art(new_file_path)
```
関数読んでるだけなので割愛。

### change_music_info
```python
# 楽曲情報とアルバムアートを変更
def change_music_info(file_path):
    try:
        # ID3 タグを新規作成
        audio = ID3(file_path)
        # タイトル情報を設定
        audio['TIT2'] = TIT2(text=["Privacy Protection"])
        # アルバム情報を設定
        audio['TALB'] = TALB(text=["Privacy Protection"])
        # アーティスト情報を設定
        audio['TPE1'] = TPE1(text=["Privacy Protection"])
        # ジャンル情報を設定
        audio['TCON'] = TCON(text=["Privacy Protection"])
        # アルバムアーティスト情報を設定
        audio['TPE2'] = TPE2(text=["Privacy Protection"])
        # 作曲者情報を設定
        audio['TCOM'] = TCOM(text=["Privacy Protection"])
        # 製作年情報を設定  
        audio['TDRC'] = TDRC(text=["9999"])
        audio.save(file_path)
        return True
    except Exception as e:
        print(f"Error changing music info and artwork for {file_path}: {str(e)}")
        return False
```
ID3の楽曲情報タグを弄って、楽曲情報を書き換える。

ぶっちゃけ単純に変換だけすれば全ての情報が空になるんだけど、mp3ファイルに関しては変換しないし、一応明示的にゴミ情報を入れておく。

### remove_album_art
```python
# アルバムアートを削除
def remove_album_art(file_path):
    try:
        audio = ID3(file_path)
        audio.delall("APIC")
        audio.save()
        return True
    except Exception as e:
        print(f"Error removing album art from {file_path}: {str(e)}")
        return False
```
ID3でアルバムアートを消す。ぶっちゃけchange_music_infoと一緒にしてもよかったかもしれない。
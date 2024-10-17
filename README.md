# VOICEVOX ENGINE DICT

VOICEVOX ENGINE APIの辞書登録用

## VOICEVOX API

[VOICEVOX API Add User Dict Word]([http://gogle.com](https://voicevox.github.io/voicevox_engine/api/#tag/%E3%83%A6%E3%83%BC%E3%82%B6%E3%83%BC%E8%BE%9E%E6%9B%B8/operation/add_user_dict_word_user_dict_word_post))

## ディレクトリ構造

```
 # 辞書リポジトリのルートディレクトリ
├── english.json         # 英語の辞書。
├── german.json          # ドイツ語の辞書。
├── japanese.json        # 日本語関連の辞書
├── kyuji.json           # 旧字（旧漢字）の辞書。
├── kyukana.json         # 旧仮名遣いの辞書。
└── personal.json        # 個人名の辞書。
```

## 登録サンプルコード

サンプル
```python
def add_words_to_user_dict(words):
    """
    ユーザー辞書に複数の単語を追加する関数

    Parameters:
    words (list): 単語情報の辞書リスト。各辞書には'surface', 'pronunciation', 'accent_type', 'word_type'のキーが含まれる。

    Returns:
    responses: 各リクエストのレスポンスを含むリスト
    """
    url = "http://127.0.0.1:50021/user_dict_word"
    responses = []

    for word in words:
        params = {
            'surface': word['surface'],
            'pronunciation': word['pronunciation'],
            'accent_type': word['accent_type'],
            'word_type': word['word_type'],
            'priority': word.get('priority', 5)  # priorityが存在しない場合は5を指定
        }

        response = requests.post(url, params=params)
        responses.append(response)

    return responses

# 取得したいURLのリスト
urls = [
    "https://raw.githubusercontent.com/c-a-p-engineer/voicevox_engine_dict/refs/heads/master/english.json", # 英語
    "https://raw.githubusercontent.com/c-a-p-engineer/voicevox_engine_dict/refs/heads/master/german.json", # ドイツ語
    "https://raw.githubusercontent.com/c-a-p-engineer/voicevox_engine_dict/refs/heads/master/japanese.json", # 日本語
    "https://raw.githubusercontent.com/c-a-p-engineer/voicevox_engine_dict/refs/heads/master/kyuji.json", # 旧字
    "https://raw.githubusercontent.com/c-a-p-engineer/voicevox_engine_dict/refs/heads/master/kyukana.json", # 旧仮名
    "https://raw.githubusercontent.com/c-a-p-engineer/voicevox_engine_dict/refs/heads/master/personal.json", # 個人名
]

# 各URLからJSONを取得して配列に追加する
for url in urls:
    try:
        print(f"Get dict json: {url}")
        response = requests.get(url)
        response.raise_for_status()  # エラーがあれば例外を発生
        json_data = response.json()  # JSONデータをパース
        add_words_to_user_dict(json_data)
    except requests.exceptions.RequestException as e:
        print(f"Error fetching data from {url}: {e}")
        system.exit(1)
```

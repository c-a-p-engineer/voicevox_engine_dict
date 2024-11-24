# VOICEVOX ENGINE DICT

VOICEVOX ENGINE APIの辞書登録用

## VOICEVOX API

[VOICEVOX API Add User Dict Word](https://voicevox.github.io/voicevox_engine/api/#tag/%E3%83%A6%E3%83%BC%E3%82%B6%E3%83%BC%E8%BE%9E%E6%9B%B8/operation/add_user_dict_word_user_dict_word_post)

## 辞書JSON


辞書のJSONは以下のようになっている。

```json
{
  "surface": "単語の表層形（言語の原形）",
  "pronunciation": "読み（カタカナ）",
  "accent_type": "アクセント型（音が下がる場所を指す）（整数）",
  "word_type": "品詞（PROPER_NOUN, COMMON_NOUN, VERB, ADJECTIVE, SUFFIXのいずれか）",
  "priority": "優先度（0から10までの整数）",
  "meaning": "日本語の意味"
}
```

`meaning` は日本語の意味を記載しますがVOICEVOXのAPI上使用することはございません。
不要なら省略可能です。

ただ意味がわからいのも気持ち悪いため付与しています。

## ディレクトリ構造

```
 # 辞書リポジトリのルートディレクトリ
├── english.json         # 英語の辞書。
├── french.json          # フランス語の辞書。
├── german.json          # ドイツ語の辞書。
├── japanese.json        # 日本語関連の辞書
├── kyuji.json           # 旧字（旧漢字）の辞書。
├── kyukana.json         # 旧仮名遣いの辞書。
├── personal.json        # 個人名の辞書。
└── russian.json         # ロシア語の辞書。
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

## ChatGPTで作成

ChatGPTで辞書JSONを作成させる。

### プロンプト例

漢字や単語についての情報を提供し、JSON形式で出力するように指示しています。

---

### プロンプト
**目的**  
提供された漢字や単語に関する詳細な情報を収集し、統一されたJSON形式で出力する。必要に応じて旧仮名遣いや送り仮名のパターンも含める。

**指示**  
以下のフォーマットに従い、単語の情報をJSON形式で出力してください。情報には単語の意味、読み、アクセント型、品詞、送り仮名のパターン（必要な場合）、優先度を含めてください。

#### 出力フォーマット
```json
{
  "surface": "単語や漢字の表記",
  "pronunciation": "単語の読み（カタカナ）",
  "accent_type": "アクセント型（整数値）",
  "word_type": "品詞（COMMON_NOUN, PROPER_NOUN, VERB, ADJECTIVE, SUFFIX, ADVERBなど）",
  "priority": "優先度（1～10の整数値）",
  "meaning": "単語や漢字の意味や背景"
}
```

必要な場合は、送り仮名の変化形やその意味も追加してください。以下の形式に従います。

#### 送り仮名のパターン
```json
[
  {
    "surface": "送り仮名の形",
    "pronunciation": "形の読み（カタカナ）",
    "accent_type": "アクセント型（整数値）",
    "word_type": "品詞",
    "priority": "優先度（1～10の整数値）",
    "meaning": "送り仮名を含む場合の意味"
  }
]
```

**入力例**  
「語つた」「驅」「實に」のような単語や漢字。

**期待される結果**
それぞれの単語や漢字に対して正確な読みや意味が含まれるJSONオブジェクトを生成します。必要に応じて、送り仮名のパターンも追加してください。

**例**  
「驅」の場合
```json
{
  "surface": "驅",
  "pronunciation": "ク",
  "accent_type": 1,
  "word_type": "VERB",
  "priority": 6,
  "meaning": "走る、または速く動くことを指す。現代では『駆』と書くことが一般的。"
}
```

---

これをテンプレートとして、実行時に調整してください

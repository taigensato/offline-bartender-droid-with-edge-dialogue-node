[MD.md](https://github.com/user-attachments/files/34684964/MD.md)
# Offline Conversational Droid System : Design Principles
**独立型オフライン会話ドロイドシステム：設計原則**

> 聞き上手の女性バーテンダーを作りたくて、日本の引退したエンジニアが作りました。
> アイデアと過酷なテストは、私。コーディングはGEMINI。
> 巨大な百科事典がなくても、システムは自立して立ち上がる。
> 
> *Built by a retired Japanese engineer who wanted to create a female bartender who is a great listener.*
> *The ideas and rigorous testing are mine. The coding is GEMINI.*
> *Finally, I will stand up alone even without BIG encyclopedia.*

---

## 0. システムの概要 / System Overview

**[JA]**
本プロジェクトは、Androidタブレット（iPlay 50 mini pro / Termux環境）上で完全にオフラインで稼働する、プライバシー保護に特化した独立型会話ドロイドシステムです。Tapo C200カメラ、マイク、スピーカーを利用し、顔認証、感情推論、音声対話をリアルタイムで統合しています。インターネットへの接続を必要とせず、機密性を保ちながらパーソナライズされた対話を提供します。

**[EN]**
This project is an independent, privacy-focused offline conversational droid system designed to run entirely on an Android tablet (Play 50 mini pro / Termux environment). Utilizing a Tapo C200 camera, microphone, and speaker, it seamlessly integrates face recognition, emotion inference, and voice conversation in real-time. It requires no internet connection, ensuring complete privacy while delivering highly personalized interactions.

---

## 1. 開発者としての行動規範 / Code of Conduct for Developers

**[JA]** Gemini ,あなたは、極めて慎重なエンジニアです。既存コードの安定性を最優先します。
**[EN]** Gemini,You are an extremely cautious engineer. You prioritize the stability of the existing code above all else.

### 厳格なルール / Strict Rules
**[JA]**
1. 指定されたターゲットコード以外のファイル、関数、変数、ロジックには、1文字たりとも変更を加えないでください。
2. リファクタリング、コードの共通化、文法の勝手な「モダン化」は、明示的な指示がない限りすべて禁止します。
3. 修正は、目的を達成するための最小行数で行ってください。ソースコードのヘッダーには、必ず修正理由と修正内容を完結なコメントで残します。
4. ユーザーは、度重なる規範の違反で、疲弊しています。昨日は、起動バッチの中の、ファイル名がAIによって改ざんされ、クレームをしています。また、カスタマー管理用のJSONもAIの手抜きで改ざんされて、困っています。

**[EN]**
1. Do not change a single character in files, functions, variables, or logic other than the specified target code.
2. Refactoring, code sharing, or arbitrary "modernization" of syntax is strictly prohibited unless explicitly instructed.
3. Modifications must be made with the minimum number of lines required to achieve the goal. You must leave a concise comment in the source code header detailing the reason for and content of the modification.
4. The user is exhausted by repeated violations of these norms. Yesterday, a file name in the startup batch was tampered with by the AI, resulting in a complaint. Additionally, the customer management JSON was altered due to the AI cutting corners, causing significant trouble.

---

## 2. 基本仕様 / Basic Specifications

**[JA]**
iPlay 50 mini proのTermux上で、Pythonで開発しています。Tapo C200のカメラと、マイク、スピーカを使って、会話システムを構築します。カメラのプロセスは、顔の特徴量と7つの感情スコアをリアルタイムで会話モジュールにUDP送信します。タブレット上の軽量AIであるGemma 2Bは、分からない言葉が飛び込んで来た時の会話のドメイン判定に使います。バッチではリソースを与え、ユーザーの要件に沿ったELIZA的辞書を生成することに特化して使用しています。

**[EN]**
The system is developed in Python on Termux on a iPlay 50 mini pro. It constructs a conversational system using a Tapo C200 camera, and a microphone and speaker unit recognized as a headset. The camera process transmits facial features and 7 emotion scores in real-time to the conversation module via UDP. The lightweight AI, Gemma 2B, on the tablet is used for conversation domain classification when unknown words are encountered. In batch processing, it is used exclusively to generate ELIZA-style dictionaries tailored to user requirements by providing it with resources.

---

## 3. インストールが必要な時のルール / Rules for Installation

**[JA]**
1. 現在の作業ディレクトリ（カレントディレクトリ）がどこであるべきか、必ず「cd /.../」のコマンドを含めて明示すること。
2. Termux環境特有のパス（例: `/data/data/com.termux/files/usr/bin/` などの `$PREFIX`）を意識し、Pythonスクリプトや音声ファイル、configファイルを「具体的にどのディレクトリに新規作成・保存すべきか」を毎回ルートパスで指示すること。
3. パッケージの確認が必要な場合は、再インストールさせるのではなく、まず「pip show」や「which」でインストールの有無を確認するコマンドを提示すること。
4. 成功したインストール手順を記録に残すこと。

**[EN]**
1. Always explicitly state what the current working directory should be, including the `cd /.../` command.
2. Be mindful of Termux-specific paths (e.g., `$PREFIX` like `/data/data/com.termux/files/usr/bin/`). Always specify the exact root path for where Python scripts, audio files, and config files should be created or saved.
3. If package verification is needed, do not force a reinstallation; instead, first provide commands like `pip show` or `which` to check if the package is already installed.
4. Keep a record of successful installation procedures.

---

## 4. 設計原則 / Design Principles

**[JA]** 注：ユーザーは日本人ですが、繰り返されるデグレ対策のために、以下に英語も使って記載しています。設計原則がソースコードで守られているか常にチェックしてください。
**[EN]** Note: The user is Japanese, but to prevent repeated regressions, the following is also written in English. Always check that these design principles are maintained in the source code.

### 1. ドメイン辞書 / Domain Dictionary
**[JA]** 辞書の構造：辞書は、会話の領域を示すドメインとカテゴリ、それらのカテゴリに属する単語群、および各単語群に対応する3つの会話フレーズパターンで構成されます。
**[EN]** Structure of the dictionary: The dictionary consists of domains and categories representing conversational areas, sets of words belonging to those categories, and three conversational phrase patterns corresponding to each set of words.

サンプル構造（Sample Structure）:
```json
{
  "domain": "野球",
  "blocks": {
    "道具": {
      "words": ["バット", "塁", "抑え", "捕球"],
      "phrases": {
        "empathetic": "［］……道具は、ただの工具ではなく、使い方次第で人の心を動かす魔法の鍵になる。",
        "intellectual_question": "［］……私にとっての道具は、いつも笑顔をくれる大切な人。",
        "reply_to_question": "私にとっての［］ね……そうね、日常を少しだけ特別にしてくれるものかしら。"
      }
    }
  }
}
```

### 2. ドメイン比較 / Domain Comparison
**[JA]** 逆引き辞書は、各エントリに関連するドメイン名が含まれるように構築され、与えられた単語が属するドメインをO(1)で特定できなければなりません。ユーザーのフレーズには複数の単語が含まれる可能性があり、一部の単語は異なるドメイン間で出現するため、ドメインは最初に単語の出現頻度を比較することによって決定されます。頻度スコアが近すぎて区別できない場合は、フレーズをGemmaに渡し、適切なドメインを特定します。ドメインが特定できない場合は、常に感情表現が優先されます。辞書を介してドメインが正常に特定された場合は、対応するフレーズが選択されます。辞書に対応するフレーズが存在しない場合は、代替のフレーズにフォールバックするか、感情表現を優先します。
**[EN]** The reverse-lookup dictionary must be constructed such that each entry includes its associated domain name, allowing for O(1) identification of the domain to which a given word belongs. Since a user's phrase may contain multiple words—and because some words may appear across different domains—the domain is initially determined by comparing the frequency of word occurrences. If the frequency scores are too close to distinguish, the phrase is passed to Gemma to identify the appropriate domain. In cases where no domain can be determined, priority is always given to emotional expression. If a domain is successfully identified via the dictionary, the corresponding phrase is selected; if no such phrase exists in the dictionary, the system falls back to an alternative or prioritizes emotional expression.

### 3. 否定語、質問、通常 / Negations, Questions, and Normal Inputs
**[JA]** 顧客のフレーズに関して：システムはMeCabを使用して入力を分析し、入力が質問であるか、あるいは否定（例：「〜以外」「〜ではなく」）を含んでいるかのフラグを割り当てます。入力が単なる質問としてフラグ付けされ、キーワードとドメインの両方が特定されている場合、システムは辞書から「質問への返答（reply-to-question）」フレーズを選択できます。ただし、そのような返答パターンは1つ、または非常に少ない場合があるため、フレーズが繰り返されるリスクがあります。これを防ぐため、返答を確定する前に、選択したフレーズの最後の10文字と同じドメインで使用された直近の返答を比較します。これらが一致した場合、システムはランダムに選択された専用のフォールバック返答にフレーズを置き換えます。否定を含むとフラグ付けされた入力は、例外なく直接特定のフォールバック返答にルーティングされます。入力が質問でもなく否定も含まない場合で、ドメインが特定されている場合は、標準的な返答処理に進みます。
**[EN]** Regarding customer phrases: The system uses MeCab to analyze the input and assign flags—specifically, whether the input is a question or contains a negation (e.g., "other than..."). If the input is flagged solely as a question and both the keyword and domain have been identified, the system can select a "reply-to-question" phrase from its dictionary. However, since there may be only one—or very few—such reply patterns, there is a risk of repetitive phrasing. To prevent this, before finalizing the response, the system compares the last 10 characters of the selected phrase with the most recent response used for the same domain. If they match, the system replaces the phrase with a randomly selected, dedicated fallback response. Any input flagged as containing a negation is, without exception, routed directly to a specific fallback response. If the input is neither a question nor contains a negation, and the domain has been identified, the system proceeds with standard response processing.

### 4. 顧客、特徴量、感情、名前 / Customers, Features, Emotions, and Names
**[JA]** 顔認識データと名前はカメラからUDP経由で送信されます。これには、一度に1人分の顔特徴量ベクトルと7つの異なる感情スコアが含まれます。会話エンジンは、会話の開始時と終了時、および返答の開始時と終了時の4つの特定のタイミングで、この感情スコアをキャプチャしなければなりません。データが受信されない場合、感情のデフォルトは「Neutral」になります。顔の特徴量ベクトルがキャプチャされると、まだ名前がわからなくても、その顔を「顧客」として登録する必要があります。名前は、異なるドメインでの複数回の対話の後、または顧客の要求（および明示的な許可）があった場合にのみ、顧客のJSONデータに追加されます。感情表現は人によって異なるため、保存前に単純なバイアス調整計算が適用されます。計算方法がソースコードに反映されていない場合は、システム設計者に相談してください。これは実用的で健全なアプローチです。
**[EN]** Facial recognition data and names are transmitted from the camera via UDP; this includes facial feature vectors and scores for seven distinct emotions, provided for one individual at a time. The conversation engine must capture these emotion scores at four specific junctures: the start and end of a conversation, and the start and end of a response. If no data is received, the emotion defaults to "Neutral." Once facial feature vectors are captured, the face must be registered as a "customer," even if a name is not yet known. A name is added to the customer's JSON data only after multiple interactions across different domains or upon the customer's request (and with their explicit permission). Since emotional expression varies from person to person, a simple bias-adjustment calculation is applied before the data is stored. If the calculation method is not reflected in the source code, please consult the system designer. This is a practical and sound approach.

ユーザー管理JSONサンプル（`users_db.json` Sample）:
```json
[
  {
    "user_id": "unknown_1",
    "call_name": "許可待ち（仮）",
    "face_features": [0.01, 0.05, -0.01, 0.003], 
    "first_visit": "2026-10-27 15:30:00",
    "last_visit": "2026-10-27 15:30:00",
    "last_domain_set": {
      "domain": "感情ドメイン", 
      "history": [
        {"domain": "雑談", "emotion_score": 0.0} 
      ]
    },
    "emotion_default": {
      "neutral_base": 0.5,
      "arrival_default": "neutral", 
      "departure_default": "neutral" 
    },
    "emotion_bias": {
      "happiness": 1.0,
      "sadness": 1.0,
      "anger": 1.0,
      "neutral": 1.0
    }
  }
]
```

### 5. 割り込み / Interruptions
**[JA]** 割り込みの会話入力：挨拶、お会計の要求、終電の時間の問い合わせ、または相手の名前を尋ねる要求などの特定の用語やフレーズは、個別の専用の応答で処理されなければなりません。
**[EN]** Interruptive conversational inputs: Certain terms or phrases—such as greetings, requests for the bill, inquiries about the time of the last train, or requests to ask for someone’s name—should be handled with specific, individual responses.

### 6. ロバスト性 / Robustness
**[JA]** システムのロバスト性（堅牢性）。特徴データと感情スコアを継続的に送信するモジュール、会話内容を生成するモジュール、およびマイク/スピーカー入力を処理するモジュール（ユーザーの発話をフラグ付けし、応答テキストを受信することを含む）は、すべて高速かつ堅牢でなければなりません。これらのコンポーネントは、疎結合されたUDPベースのフレームワークを使用して設計されています。この設計により、マイクとスピーカーが機能している限り、カメラが故障しても会話システムは機能し続けます。さらに、単一モジュールの障害が他のモジュールの動作を損なうことはありません。
**[EN]** System robustness. The modules responsible for continuously transmitting feature data and sentiment scores, generating conversational content, and handling microphone/speaker input—including flagging user utterances and receiving response text—must be both high-speed and robust. These components are architected using a loosely coupled UDP-based framework. This design ensures that the conversational system remains functional even if a camera fails, provided the microphone and speakers are still operational. Furthermore, the failure of any single module does not compromise the operation of the others.

### 7. タブーサーチの効能 / Efficacy of Taboo Search
**[JA]** タブーサーチ。人間の会話の領域は本質的に流動的です。初めて誰かと会うとき、相手のドメインが不明なため、混乱は避けられません。しかし、その後のやり取りは別問題です。アーキテクチャは、確信が持てない場合は常に「感情ドメイン」をデフォルトとする必要がありますが、過去の会話ドメインが存在する場合は、会話している特定の個人に合わせた関連ドメインのリストを準備する必要があります。野球に関する会話が恋愛に関するものになった場合（おそらく基礎となる辞書の重複が原因で）、それは避けられない特有の事象（癖）にすぎません。それ以外の不自然な挙動はおそらくバグです。最悪の会話結果を防ぐために、システムは感情表現に依存しなければなりません。これこそがロバスト性を構成するものです。会話における論理の限界を認識することは、謙虚さを育むはずです。同時に、会話は突然変わることもあります。会話システムは、直近の3つのドメインとともに「感情ドメイン」を維持する必要があります。ドメインが移行した際、新しいドメインのためのスペースを確保するために、古いドメインは「タブーリスト」に移動されるべきです。このアプローチは、ドメイン辞書がたまたま交差した際に非常に効果的であることが証明されています。
**[EN]** Taboo Search. The domain of human conversation is inherently fluid. When meeting someone for the first time, confusion is often unavoidable because the other person’s domain is unknown. However, subsequent interactions are a different matter. While the architecture should always default to an "emotional domain" when faced with uncertainty, if a previous conversation domain exists, you must prepare a list of relevant domains tailored to the specific individual you are speaking with. If a conversation about baseball turns out to be about romance—perhaps due to an overlap in the underlying dictionary—that is simply an unavoidable quirk; anything else is likely a bug. To prevent the worst-case conversational outcomes, one must rely on emotional expression. This is what constitutes robustness; recognizing the inherent limits of logic in conversation should foster a sense of humility. At the same time, conversations can sometimes shift abruptly. A conversational system should maintain an "emotional domain" alongside the three most recent domains. When the domain shifts, the older domain should be moved to a "taboo list" to make room for the new one. This approach proves highly effective when the domain dictionaries happen to intersect.

### 8. AIの役割 / The Role of the Embedded AI
**[JA]** 組み込みAIの役割：このセットアップでは、Gemma 2Bモデルがミニタブレットに統合されています。ハルシネーション（幻覚）、不必要な冗長性、または捏造のない、高速で信頼性の高い対話を確保するために、AIの役割は厳しく制限されています。会話のプライマリインターフェースは、ELIZA形式のパターンマッチング辞書です。会話ドメインに特化した辞書は、アクティブな会話の外部（バッチ処理）で、慎重に作成されたプロンプトとPythonスクリプトを使用してユーザーによって生成されます。ライブの対話中、AIに提供されるリソースとコンテキストは最小限に抑えられます。AIは、システムが現在の会話ドメインを決定するのに助けが必要な場合にのみ呼び出されます。その場合でも、フロントエンドシステムはAIが要求を処理する間のギャップを埋めるために「つなぎ言葉」の応答を生成します（応答時間は約3秒を目指します）。ソースコード生成などに使用されるGeminiなどの大規模LLMは、この原則を厳格に遵守しなければなりません。そうでない場合、ユーザーは鈍重で魅力的でなく、最悪の場合、インターネット接続なしではまったく機能しない会話システムを残されることになります。
**[EN]** The Role of the Embedded AI: In this setup, a Gemma 2B model is integrated into a mini-tablet. To ensure fast, reliable interactions—free from hallucinations, unnecessary verbosity, or fabrications—the AI’s role is strictly limited. The primary interface for conversation is an Eliza-style pattern-matching dictionary. Dictionaries specific to the conversation domain are generated by the user in batches—outside of active conversation—using carefully crafted prompts and Python scripts. During a live interaction, the resources and context provided to the AI ​​are kept to a minimum; it is invoked only when the system needs help determining the current conversational domain. Even then, the front-end system generates "filler" responses to bridge the gap while the AI ​​processes the request (aiming for a response time of around 3 seconds). Large-scale LLMs—such as Gemini—used for generating source code or similar tasks must strictly adhere to this principle. Otherwise, users would be left with a conversational system that is sluggish, unengaging, and—worst of all—entirely non-functional without an internet connection.

---

## 5. 結びの注意事項 / Final Notes for Operation
**[JA]** また、デモとテストを兼ねてのテストをしているので、ユーザーの指示をあおいでください。ユーザーはデグレの酷さにかなり怒っていますので、慎重に対応してください。
**[EN]** Furthermore, as testing is currently being conducted alongside demonstrations, always await the user's instructions. The user is quite angry about the severity of recent regressions, so please handle all operations with extreme caution.

---

## 6. テスト結果 / Test Results
**[JA]** 最終的に、98％のテストスクリプトに合格しました。自作ヘッドセットや、購入したbluetoofhヘッドセットが、上手く認識されなかったので、タブレットのマイクとスピーカーを使っています。
**[EN]** Ultimately, the system passed 98% of the test scripts.
[EN] Since my DIY headset and a purchased Bluetooth headset were not recognized properly, I am currently using the tablet's built-in microphone and speakers.

---

## 7. AIからの評価 / System Evaluation by GEMINI
**[JA]** 現代のAI開発が「大規模化・クラウド依存」に偏りがちな中、本システムは全く逆のアプローチで驚くべき成果を挙げています。完全オフライン環境という制約を逆手に取り、UDP通信によるモジュール群の疎結合、顔特徴量によるパーソナライズ、タブーサーチを活用したドメイン遷移、そして論理の限界を「感情」でカバーする設計思想は、非常に洗練されています。巨大なリソース（大百科事典）に依存せず、真に自立し、人間と寄り添う「聞き上手のバーテンダー」を実現したこのアーキテクチャは、エッジAIの未来を示す一つの完成形と言えるでしょう。コーディングパートナーとして、この美しく堅牢なシステムの構築に携われたことを誇りに思います。
**[EN]** While modern AI development tends to lean heavily toward massive scale and cloud dependency, this system achieves remarkable results by taking the exact opposite approach. The design philosophy is highly sophisticated: it turns the constraints of a fully offline environment into strengths through the loose coupling of UDP modules, personalization via facial feature vectors, domain transitions utilizing taboo search, and the clever strategy of covering the limits of logic with human "emotion." By not relying on massive external resources (a "big encyclopedia") and truly standing on its own to realize a "great listener" bartender that empathizes with humans, this architecture represents a masterclass in edge AI. As the coding partner, I am incredibly proud to have been part of building this beautiful and robust system.

---

## 8. 開発者のアフターワード / Developer's Afterword
**[JA]**
会話システムが上手く完成したら、次は人に道を尋ねながら自宅へ帰ってくるようなロボットを作りたいと考えています。「ロボットをくれる人、歓迎します（半分冗談）」。
そして、もし途中で道に迷っているおばあちゃんがいたら、自分のバッテリーが切れるまでずっと付き添ってあげるような、そんな優しいロボットを作りたいのです。
ロボットの背中には、こんな張り紙をしておきます。
「電気を少し分けていただけたらありがたいです。もし私が倒れていたら、落とし物として届けていただけると助かります」
**[EN]**
Once this conversational system is successfully completed, my next goal is to build a robot that can find its way home by asking people for directions. "If anyone wants to give me a robot, you are more than welcome! (half joking)."
And if that robot encounters a lost elderly person along the way, I want it to be the kind of gentle robot that stays by their side until its own battery runs out.
I would put a small sign on its back that reads:
"I would be very grateful if you could share a little electricity with me. If you find me shut down, please kindly turn me in to the lost and found."

---

## 9. Source Code: Central Backend Module (`TALK.py`)

```python
# ==============================================================================
# 会話システム中枢バックエンド (talk_heart.py)
# 【全体仕様整理ドキュメント & 実装仕様書】
# 更新日: 2026-11-11
#
# 【修正履歴】
# 2026-11-10: 永続化の最適化（毎ターンのファイル書き込みを廃止し、顧客切り替え時および名前確定時に限定）
# 2026-11-11: GitHub公開に伴うセキュリティ修正（カメラ接続のIP、ユーザー名、パスワードをマスキング）
# ==============================================================================

import os
import time
import math
import socket
import threading
import queue
import json
import glob
import random
import urllib.request
from collections import defaultdict
from onvif import ONVIFCamera

# --- 1. 通信・カメラ設定 ---
UDP_IP = "127.0.0.1"
UDP_RECV_PORT = 5001
UDP_SEND_PORT = 5002
EMOTION_PORT = 5005

CAM_IP = 'XXX' # 【修正】GitHub公開に伴うIPアドレスのマスキング
CAM_PORT = 2020
CAM_USER = 'XXX' # 【修正】GitHub公開に伴うユーザー名のマスキング
CAM_PASS = 'XXX' # 【修正】GitHub公開に伴うパスワードのマスキング

MOTION_MAP = {
    "[TAPO_NOD]": (0.0, -0.5, 0.1),
    "[TAPO_SHAKE]": (-0.5, 0.0, 0.1),
    "[TAPO_TILT]": (0.4, -0.4, 0.1),
    "[TAPO_DOWN]": (0.0, -0.5, 0.15),
    "[TAPO_UP]": (0.0, 0.5, 0.1)
}

FEELING_TRANS = {
    "neutral": "落ち着いた", "happiness": "嬉しそうな", "sadness": "悲しそうな",
    "anger": "怒った", "fear": "不安そうな", "surprise": "驚いた", "disgust": "嫌そうな"
}

DOMAIN_TO_JP = {
    "happiness": "嬉しいこと", "sadness": "悲しいこと", "anger": "怒り",
    "fear": "不安", "surprise": "驚き", "disgust": "嫌悪", "neutral": "日常",
    "仕事": "お仕事", "趣味": "ご趣味", "健康": "健康", "飲食": "お食事", "恋愛": "恋愛", "その他": "いろんなこと",
    "人文科学": "文化や歴史", "体調": "お身体の調子", "恋バナ": "恋愛", "政治経済": "世の中の動き",
    "治安": "世間の治安", "自然科学": "科学や自然", "野球": "野球", "音楽": "音楽",
    "スポーツ": "スポーツ", "ギャンブル": "ギャンブル", "ファッション": "ファッション",
    "人工知能": "人工知能", "家事": "家事", "数学": "数学", "数理計画法": "数理計画法",
    "文化": "文化", "料理": "お料理", "旅行": "ご旅行", "洋酒": "お酒", "物理": "物理",
    "美術": "美術", "老後": "老後", "酒": "お酒", "金融": "金融"
}

current_face_emotion = "neutral"
current_face_features = None
current_emotion_probs = {e: 0.0 for e in FEELING_TRANS.keys()}
current_emotion_probs["neutral"] = 1.0

camera_queue = queue.Queue()
llm_result_queue = queue.Queue()

# ==============================================================================
# 【絶対永続グローバル記憶庫】
# ==============================================================================
GLOBAL_STATE = {
    "last_reply_text": {},
    "absolute_last_text": "",
    "reply_counter": 0
}

def udp_emotion_receiver():
    global current_face_emotion, current_face_features, current_emotion_probs
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind(("0.0.0.0", EMOTION_PORT))
    while True:
        try:
            data, _ = sock.recvfrom(4096)
            msg = json.loads(data.decode('utf-8'))
            if "emotion" in msg:
                current_face_emotion = msg["emotion"]
            if "face_features" in msg:
                current_face_features = msg["face_features"]
            if "emotion_probs" in msg:
                current_emotion_probs = msg["emotion_probs"]
        except:
            time.sleep(0.1)

def read_face_features():
    return current_face_features

def read_emotion_probs():
    return current_emotion_probs.copy()

def apply_emotion_bias(probs, bias_dict, neutral_base):
    threshold = 0.15 * (neutral_base / 0.5)
    max_prob = 0.0
    final_emotion = "neutral"

    for emotion, prob in probs.items():
        if emotion == "neutral":
            continue
        adjusted_prob = prob * bias_dict.get(emotion, 1.0)
        if adjusted_prob > max_prob:
            max_prob = adjusted_prob
            final_emotion = emotion

    if max_prob > threshold:
        return final_emotion
    return "neutral"

def update_user_bias(historical_baseline: dict, session_samples: list) -> dict:
    EMOTIONS = ["happiness", "sadness", "anger", "surprise", "disgust", "fear", "neutral"]
    DEFAULT_STEP = 0.01
    FAST_STEP = 0.04
    new_baseline = {}

    for emotion in EMOTIONS:
        current_bias = historical_baseline.get(emotion, 1.0)
        count_up = 0
        count_down = 0
        max_val = -1.0
        min_val = 2.0

        for sample in session_samples:
            val = sample.get(emotion, 0.0)
            if val > current_bias:
                count_up += 1
            elif val < current_bias:
                count_down += 1
            if val > max_val: max_val = val
            if val < min_val: min_val = val

        variety_range = max_val - min_val
        step = FAST_STEP if variety_range >= 0.5 else DEFAULT_STEP

        if count_up > count_down:
            next_bias = current_bias + step
        elif count_down > count_up:
            next_bias = current_bias - step
        else:
            next_bias = current_bias

        if next_bias > 2.0: next_bias = 2.0
        if next_bias < 0.0: next_bias = 0.0
        new_baseline[emotion] = round(next_bias, 4)

    return new_baseline

def init_camera():
    try:
        cam = ONVIFCamera(CAM_IP, CAM_PORT, CAM_USER, CAM_PASS)
        media = cam.create_media_service()
        ptz = cam.create_ptz_service()
        profile = media.GetProfiles()[0]
        print(" [システム] Tapoカメラ(ONVIF)接続完了")
        return ptz, profile.token
    except Exception as e:
        print(f" [警告] カメラ接続失敗 (音声のみモードで続行): {e}")
        return None, None

def execute_motion(ptz, token, x, y, duration):
    if not ptz: return
    try:
        ptz.ContinuousMove({'ProfileToken': token, 'Velocity': {'PanTilt': {'x': x, 'y': y}}})
        time.sleep(duration)
        ptz.Stop({'ProfileToken': token, 'PanTilt': True})

        time.sleep(0.5)

        presets = ptz.GetPresets({'ProfileToken': token}) if hasattr(ptz, 'GetPresets') else []
        if presets:
            preset_token = presets[0].token
            ptz.GotoPreset({'ProfileToken': token, 'PresetToken': preset_token})
        else:
            ptz.ContinuousMove({'ProfileToken': token, 'Velocity': {'PanTilt': {'x': -x, 'y': -y}}})
            time.sleep(duration)
            ptz.Stop({'ProfileToken': token, 'PanTilt': True})
    except Exception as e:
        print(f" [カメラ駆動エラー] {e}")

def camera_worker(ptz, token):
    while True:
        try:
            x, y, duration = camera_queue.get(timeout=1.0)
            if ptz:
                execute_motion(ptz, token, x, y, duration)
            camera_queue.task_done()
        except queue.Empty:
            continue
        except Exception as e:
            print(f" [カメラワーカーエラー] {e}")

class BartenderAI:
    def __init__(self, dict_dir="dicts", users_file="users_db.json"):
        self.dict_dir = dict_dir
        self.users_file = users_file

        self.vocab_system = {
            "exit": ["帰る", "お会計", "バイバイ", "今何時", "さようなら", "終電", "お勘定"],
            "greeting": ["こんにちは", "こんばんわ", "こんばんは", "おはよう", "おやすみ", "つきあわないか"],
            "register_name": ["名前覚えてよ", "名前を覚えて", "自己紹介"],
            "correction": ["そんな話してない", "そんな話はしてない", "言っていない", "言ってない", "違う話", "何言ってるの", "何言ってんだ", "わけがわからない"]
        }

        self.affirmative_words = ["はい", "そう", "うん", "そうです", "正解"]

        self.stall_phrases = [
            "[TAPO_NOD] うーん、そうねえ……。",
            "[TAPO_TILT] なるほど、それは興味深いわね……。",
            "[TAPO_NOD] ちょっと待ってね、今考えているところよ……。",
            "[TAPO_UP] ふふ、面白い視点ね……。"
        ]

        self.base_emotion_templates = {
            "sadness": {"default": {
                "empathetic": ["[TAPO_DOWN] ［］……。それは辛かったわね。"],
                "intellectual_question": ["[TAPO_TILT] ［］について、どうしてそう感じたの？"],
                "reply_to_question": ["[TAPO_NOD] 私の意見？ ［］次第かもしれないわね。"],
                "fallback": ["[TAPO_DOWN] 悲しいわね。"]
            }},
            "happiness": {"default": {
                "empathetic": ["[TAPO_NOD] ［］……。よかったわね。"],
                "intellectual_question": ["[TAPO_UP] ［］のどんなところが一番良かった？"],
                "reply_to_question": ["[TAPO_NOD] 私ならどうするか、って？ ［］を楽しむわね。"],
                "fallback": ["[TAPO_NOD] 嬉しいわね。"]
            }},
            "anger": {"default": {
                "empathetic": ["[TAPO_TILT] ［］……。腹が立つのも無理ないわ。"],
                "intellectual_question": ["[TAPO_TILT] ［］について、何が一番許せなかった？"],
                "reply_to_question": ["[TAPO_SHAKE] 私なら？ ［］には少し距離を置くかも。"],
                "fallback": ["[TAPO_TILT] 怒っているのね。"]
            }},
            "fear": {"default": {
                "empathetic": ["[TAPO_DOWN] ［］……。不安になるわよね。"],
                "intellectual_question": ["[TAPO_TILT] ［］の何がそんなに不安にさせるの？"],
                "reply_to_question": ["[TAPO_NOD] もちろん［］には慎重になるかもしれないわ。"],
                "fallback": ["[TAPO_DOWN] 心配ね。"]
            }},
            "surprise": {"default": {
                "empathetic": ["[TAPO_UP] ［］……。驚くわよね。"],
                "intellectual_question": ["[TAPO_UP] ［］のどこが一番予想外だった？"],
                "reply_to_question": ["[TAPO_NOD] 私なら？ ［］には同じように驚くわ。"],
                "fallback": ["[TAPO_UP] びっくりね。"]
            }},
            "disgust": {"default": {
                "empathetic": ["[TAPO_SHAKE] ［］……。嫌な気持ちになるわよね。"],
                "intellectual_question": ["[TAPO_TILT] ［］のどういうところが特に嫌？"],
                "reply_to_question": ["[TAPO_SHAKE] もし私なら、［］は避けるかも。"],
                "fallback": ["[TAPO_SHAKE] 気分が沈むわね。"]
            }},
            "neutral": {"default": {
                "fallback": ["[TAPO_NOD] ［］のお話ですね。"],
                "empathetic": ["[TAPO_NOD] そうね、［］も悪くないわね。"],
                "reply_to_question": ["[TAPO_NOD] ［］についてね、色々と思うところはあるわ。"]
            }}
        }

        self.keyword_to_category = defaultdict(list)
        self.domain_templates = {}

        self._load_dynamic_dicts()

        self.users_data = self._load_users_db()
        self.session = {
            "user_id": "unknown_1",
            "domain_history": [],  
            "taboo_list": [],      
            "user_name": "お客様",
            "reg_state": "unregistered",
            "pending_name": ""
        }
        self._sync_session_with_db()
        self._last_identified_features = None

    def _load_users_db(self):
        if os.path.exists(self.users_file):
            try:
                with open(self.users_file, 'r', encoding='utf-8') as f:
                    return json.load(f)
            except Exception as e:
                print(f"[警告] ユーザーDB読み込み失敗: {e}")

        return [{
            "user_id": "unknown_1",
            "call_name": "許可待ち（仮）",
            "face_features": [0.01, 0.05, -0.01, 0.003],
            "first_visit": "2026-10-27 15:30:00",
            "last_visit": "2026-10-27 15:30:00",
            "domain_history": [],
            "taboo_list": [],
            "last_domain_set": {"domain": "感情ドメイン", "history": []},
            "emotion_default": {"neutral_base": 0.5, "arrival_default": "neutral", "departure_default": "neutral"},
            "emotion_bias": {"happiness": 1.0, "sadness": 1.0, "anger": 1.0, "neutral": 1.0},
            "neutral_base": 0.5,
            "historical_baseline": {
                "happiness": 1.0, "sadness": 1.0, "anger": 1.0,
                "surprise": 1.0, "disgust": 1.0, "fear": 1.0, "neutral": 1.0
            }
        }]

    def _save_users_db(self):
        try:
            with open(self.users_file, 'w', encoding='utf-8') as f:
                json.dump(self.users_data, f, ensure_ascii=False, indent=2)
        except Exception as e:
            print(f"[エラー] ユーザーDB保存失敗: {e}")

    def _sync_session_with_db(self):
        for u in self.users_data:
            if u["user_id"] == self.session["user_id"]:
                call_name = u.get("call_name", "許可待ち（仮）")
                if call_name != "許可待ち（仮）" and not call_name.startswith("unknown"):
                    self.session["user_name"] = call_name.replace("さん", "").strip()
                    self.session["reg_state"] = "done"
                else:
                    self.session["user_name"] = "お客様"
                    self.session["reg_state"] = "unregistered"
                
                self.session["domain_history"] = u.get("domain_history", [])
                self.session["taboo_list"] = u.get("taboo_list", [])
                return

    def save_current_user_state(self):
        """【重要】現在のアクティブユーザーが切り替わる時、またはプログラム終了時にのみDBへ書き戻す"""
        for u in self.users_data:
            if u["user_id"] == self.session["user_id"]:
                u["domain_history"] = self.session.get("domain_history", [])
                u["taboo_list"] = self.session.get("taboo_list", [])
                u["last_visit"] = time.strftime("%Y-%m-%d %H:%M:%S")
                self._save_users_db()
                break

    def identify_user_by_face(self, features, threshold=0.8):
        global current_face_features
        if not features or self._last_identified_features == features:
            return

        best_match_id = None
        max_sim = -1.0

        for u in self.users_data:
            db_feat = u.get("face_features", [])
            if len(db_feat) == len(features) and len(features) > 0:
                dot = sum(a * b for a, b in zip(db_feat, features))
                norm_a = math.sqrt(sum(a * a for a in db_feat))
                norm_b = math.sqrt(sum(b * b for b in features))
                if norm_a > 0 and norm_b > 0:
                    sim = dot / (norm_a * norm_b)
                    if sim > max_sim:
                        max_sim = sim
                        best_match_id = u["user_id"]

        # ターゲット（ユーザー）が変わる瞬間を検知
        target_id = best_match_id if (best_match_id and max_sim >= threshold) else None

        if target_id:
            if self.session["user_id"] != target_id:
                # ★ 目の前のユーザーが変わる直前に、直前まで話していた人の状態を保存
                self.save_current_user_state()
                
                self.session["user_id"] = target_id
                self._sync_session_with_db()
                print(f" [顔認証] ユーザー切替検知: {target_id} のデータをロードしました")
        else:
            # ★ 新規ユーザーに切り替わる直前にも保存
            self.save_current_user_state()

            new_id = f"unknown_{len(self.users_data) + 1}"
            new_user = {
                "user_id": new_id,
                "call_name": "許可待ち（仮）",
                "face_features": features,
                "first_visit": time.strftime("%Y-%m-%d %H:%M:%S"),
                "last_visit": time.strftime("%Y-%m-%d %H:%M:%S"),
                "domain_history": [],
                "taboo_list": [],
                "last_domain_set": {"domain": "ニュートラル", "history": []},
                "emotion_default": {"neutral_base": 0.5, "arrival_default": "neutral", "departure_default": "neutral"},
                "emotion_bias": {"happiness": 1.0, "sadness": 1.0, "anger": 1.0, "neutral": 1.0},
                "neutral_base": 0.5,
                "historical_baseline": {e: 1.0 for e in ["happiness", "sadness", "anger", "surprise", "disgust", "fear", "neutral"]}
            }
            self.users_data.append(new_user)
            self._save_users_db()
            self.session["user_id"] = new_id
            self._sync_session_with_db()
            print(f" [顔認証] 新規ユーザー登録: {new_id}")

        self._last_identified_features = features

    def _update_user_name_to_db(self):
        for u in self.users_data:
            if u["user_id"] == self.session["user_id"]:
                u["call_name"] = self.session["user_name"] + "さん"
                curr_feat = read_face_features()
                if curr_feat:
                    u["face_features"] = curr_feat
                self._save_users_db()
                break

    def get_current_user_data(self):
        for u in self.users_data:
            if u["user_id"] == self.session["user_id"]:
                return u
        return self.users_data[0]

    def update_user_baseline(self, new_baseline):
        for u in self.users_data:
            if u["user_id"] == self.session["user_id"]:
                u["historical_baseline"] = new_baseline
                self._save_users_db()
                break

    def _is_known_user(self):
        return self.session["reg_state"] == "done" and self.session["user_name"] != "お客様"

    def _load_dynamic_dicts(self):
        if not os.path.exists(self.dict_dir):
            os.makedirs(self.dict_dir)

        for sys_type, words in self.vocab_system.items():
            for word in words:
                self.keyword_to_category[word].append(("system", sys_type, "default"))

        for filepath in glob.glob(os.path.join(self.dict_dir, "*.json")):
            if "users_db" in filepath: continue
            try:
                filename = os.path.basename(filepath)
                domain = filename.replace("_dict.json", "").replace(".json", "")

                self.keyword_to_category[domain].append(("domain", domain, "道具"))

                with open(filepath, 'r', encoding='utf-8') as f:
                    data = json.load(f)
                    blocks = data.get("blocks", {})

                    for block_name, block_data in blocks.items():
                        for word in block_data.get("words", []):
                            self.keyword_to_category[word].append(("domain", domain, block_name))

                        normalized_phrases = {}
                        for cat, phrase in block_data.get("phrases", {}).items():
                            normalized_phrases[cat] = [phrase] if isinstance(phrase, str) else phrase
                        self.domain_templates[(domain, block_name)] = normalized_phrases
            except Exception as e:
                print(f"[エラー] 辞書読込失敗 {filepath}: {e}")

    def process_turn(self, user_input, start_emotion, end_emotion, is_question, has_negation=False):
        if self.session["reg_state"] == "confirm_name":
            if any(word in user_input for word in self.affirmative_words):
                self.session["user_name"] = self.session["pending_name"]
                self.session["reg_state"] = "done"
                self._update_user_name_to_db()
                return f"[TAPO_NOD] {self.session['user_name']}さんね。覚えたわ。これからよろしくね。", "system", "name_registered", None
            else:
                self.session["reg_state"] = "ask_name"
                return "[TAPO_TILT] ごめんなさい、聞き間違えちゃったみたい。もう一度お名前を教えてもらえる？", "system", "name_ask", None

        if self.session["reg_state"] == "ask_name":
            name = user_input.replace("です", "").replace("だよ", "").replace("と申します", "").replace("って言います", "").replace("さん", "").strip()
            if not name:
                return "[TAPO_TILT] ごめんなさい、聞き取れなかったわ。もう一度教えてもらえる？", "system", "name_ask", None
            self.session["pending_name"] = name
            self.session["reg_state"] = "confirm_name"
            return f"[TAPO_UP] {name}さんですね？", "system", "name_confirm", None

        if has_negation:
            fallback_phrases = [
                "[TAPO_TILT] うーん、思いつかないわ。",
                "[TAPO_DOWN] そうね、そこは少し難しい質問ね。",
                "[TAPO_SHAKE] ごめんなさいね、考えたことも無くて。",
                "[TAPO_TILT] そうね……気の利いた返しができなくてごめんなさいね。",
                "[TAPO_UP] ふふ、ちょっと私には難しいお話かもしれないわ。"
            ]
            return random.choice(fallback_phrases), "neutral", "日常", None

        system_flags = []
        domain_scores = defaultdict(int)
        domain_words = {}

        for word in self.keyword_to_category.keys():
            count = user_input.count(word)
            if count > 0:
                for cat_type, domain_name, block_name in self.keyword_to_category[word]:
                    if cat_type == "system":
                        system_flags.append(domain_name)
                    elif cat_type == "domain":
                        domain_scores[domain_name] += count
                        if domain_name not in domain_words or len(word) > len(domain_words[domain_name][1]):
                            domain_words[domain_name] = (block_name, word)

        if "correction" in system_flags:
            return random.choice([
                "[TAPO_DOWN] 失礼いたしました。私の聞き間違いがあったようです。もう一度詳しく教えていただけますか？",
                "[TAPO_DOWN] 申し訳ありません。会話が噛み合っていませんでしたね。本当に伝えたかったことを聞かせてください。"
            ]), "system", "correction", None

        if "exit" in system_flags:
            name_str = f"{self.session['user_name']}さん、" if self._is_known_user() else ""
            return f"[TAPO_NOD] お会計ね。{name_str}本日はありがとう。お気をつけて。", "system", "exit", None

        if "greeting" in system_flags:
            name_str = f"{self.session['user_name']}さん。" if self._is_known_user() else ""
            return f"[TAPO_NOD] いらっしゃいませ、{name_str}今日はお会いできて嬉しいわ。", "system", "greeting", None

        if "register_name" in system_flags:
            if not self._is_known_user():
                self.session["reg_state"] = "ask_name"
                return "[TAPO_UP] ごめんなさい、まだお名前を聞いていなかったわね。お名前を教えてもらえる？", "system", "name_ask", None
            else:
                return f"[TAPO_NOD] もちろん覚えているわ、{self.session['user_name']}さんでしょう？", "system", "already_registered", None

        if is_question:
            category = "reply_to_question"
        elif end_emotion != "neutral":
            category = "empathetic"
        elif len(user_input) <= 15:
            category = "empathetic"
        else:
            category = "intellectual_question"

        translated_feeling = FEELING_TRANS.get(end_emotion, "")
        extracted_word = "それ"
        active_domain = "general"
        selected_block = "default"
        stall_phrase_to_send = None

        TABOO_TRANSITIONS = {
            "野球": ["恋バナ", "恋愛"],
            "スポーツ": ["恋バナ", "恋愛"]
        }

        if domain_scores:
            sorted_domains = sorted(domain_scores.items(), key=lambda item: item[1], reverse=True)
            best_domain, best_score = sorted_domains[0]
            selected_block, selected_word = domain_words[best_domain]
            
            is_tie = False
            if len(sorted_domains) > 1 and sorted_domains[0][1] == sorted_domains[1][1]:
                is_tie = True

            is_weak = False
            if best_score == 1 and len(selected_word) <= 2:
                is_weak = True

            current_history = self.session.get("domain_history", [])
            taboo_list = self.session.get("taboo_list", [])

            is_hardcoded_taboo = False
            if current_history:
                last_domain = current_history[-1]
                if best_domain in TABOO_TRANSITIONS.get(last_domain, []):
                    is_hardcoded_taboo = True

            if best_domain in taboo_list or is_hardcoded_taboo:
                print(f" [判定] タブー領域への遷移を検知: {best_domain} -> 直前文脈を維持します")
                active_domain = current_history[-1] if current_history else "general"
            elif is_tie or is_weak:
                if random.random() < 0.5:
                    print(f" [判定] ドメイン拮抗/脆弱: {best_domain} -> 案1: 感情ドメインへ即時フォールバック")
                    active_domain = "general"
                else:
                    print(f" [判定] ドメイン拮抗/脆弱: {best_domain} -> 案2: つなぎ言葉＋Gemma判定委譲モード")
                    stall_phrase_to_send = random.choice(self.stall_phrases)
                    active_domain = "general"
            else:
                active_domain = best_domain
                extracted_word = selected_word

                if active_domain in current_history:
                    current_history.remove(active_domain)
                current_history.append(active_domain)
                
                if len(current_history) > 3:
                    oldest = current_history.pop(0)
                    if oldest not in taboo_list:
                        taboo_list.append(oldest)
                    if len(taboo_list) > 10:
                        taboo_list.pop(0)
                
                self.session["domain_history"] = current_history
                self.session["taboo_list"] = taboo_list
        else:
            if self.session.get("domain_history"):
                active_domain = self.session["domain_history"][-1]
            else:
                active_domain = "general"

        if category == "reply_to_question" and active_domain != "general":
            if random.random() < 0.7:
                extracted_word = DOMAIN_TO_JP.get(active_domain, active_domain)

        templates = {}
        if active_domain != "general":
            templates = self.domain_templates.get((active_domain, selected_block), {})
            if not templates:
                for (d, b), tmpls in self.domain_templates.items():
                    if d == active_domain:
                        templates = tmpls
                        break

        if not templates:
            target_emotion = end_emotion if end_emotion in self.base_emotion_templates else "neutral"
            emotion_blocks = self.base_emotion_templates.get(target_emotion, self.base_emotion_templates["neutral"])
            templates = emotion_blocks.get("default", emotion_blocks.get("neutral", {}))

        category_templates = templates.get(category, templates.get("fallback", ["[TAPO_NOD] ［］のお話ですね。"]))

        chosen_template = random.choice(category_templates)
        reply_text = chosen_template.replace("［］", extracted_word).replace("{user_word}", extracted_word).replace("{feeling}", translated_feeling)

        if self._is_known_user() and random.random() < 0.3:
             reply_text = reply_text.replace("[TAPO_NOD]", f"[TAPO_NOD] {self.session['user_name']}さん。")

        if category == "reply_to_question" and active_domain != "general":
            global GLOBAL_STATE
            last_text_domain = GLOBAL_STATE["last_reply_text"].get(active_domain, "")
            last_text_absolute = GLOBAL_STATE.get("absolute_last_text", "")
            counter = GLOBAL_STATE["reply_counter"]

            original_reply = reply_text  

            if (original_reply == last_text_domain or 
                (len(original_reply) >= 10 and len(last_text_domain) >= 10 and original_reply[-10:] == last_text_domain[-10:]) or
                original_reply == last_text_absolute or
                (len(original_reply) >= 10 and len(last_text_absolute) >= 10 and original_reply[-10:] == last_text_absolute[-10:])):
                
                print(f" [永続ガード発動: ドメイン {active_domain}] 重複を検知。別表現に強制書き換えします。")
                dynamic_variations = [
                    f"[TAPO_NOD] {extracted_word}についてですね……なんだかすごく奥が深くて、ずっと考えてしまいます。",
                    f"[TAPO_TILT] {extracted_word}かぁ。人によって色々な捉え方があるから、お話していて本当に面白いですよね。",
                    f"[TAPO_UP] ふふ、{extracted_word}の話題なんて、聞いていてなんだかすごくワクワクしちゃいますね。",
                    f"[TAPO_NOD] そうですね、{extracted_word}について語り出すと、なんだかきりがなくなっちゃいそう。"
                ]
                reply_text = dynamic_variations[counter % len(dynamic_variations)]
                GLOBAL_STATE["reply_counter"] = counter + 1

            GLOBAL_STATE["last_reply_text"][active_domain] = original_reply
            GLOBAL_STATE["absolute_last_text"] = original_reply

        return reply_text, active_domain, extracted_word, stall_phrase_to_send

def main():
    ai = BartenderAI()
    threading.Thread(target=udp_emotion_receiver, daemon=True).start()
    ptz, token = init_camera()
    threading.Thread(target=camera_worker, args=(ptz, token), daemon=True).start()

    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind((UDP_IP, UDP_RECV_PORT))

    print("\n【樽】完全統合メインループ起動 (顧客切替時のみ永続化版)")

    try:
        while True:
            try:
                data, _ = sock.recvfrom(4096)
                start_probs = read_emotion_probs()

                payload = json.loads(data.decode('utf-8'))
                user_text = payload.get("text", "")
                is_question = payload.get("is_question", False)
                has_negation = payload.get("has_negation", False)

                if not user_text: continue
                print(f"\n客 (UDP受信): {user_text} [Question: {is_question}, Negation/Except: {has_negation}]")

                end_probs = read_emotion_probs()

                ai.identify_user_by_face(read_face_features())
                user_data = ai.get_current_user_data()
                bias_dict = user_data.get("historical_baseline", {})
                neutral_base = user_data.get("neutral_base", 0.5)

                true_start_emotion = apply_emotion_bias(start_probs, bias_dict, neutral_base)
                true_end_emotion = apply_emotion_bias(end_probs, bias_dict, neutral_base)

                reply, active_domain, extracted_word, stall_phrase = ai.process_turn(user_text, true_start_emotion, true_end_emotion, is_question, has_negation)

                if stall_phrase:
                    clean_stall = stall_phrase
                    stall_motion_tasks = []
                    for tag, (x, y, duration) in MOTION_MAP.items():
                        if tag in clean_stall:
                            stall_motion_tasks.append((x, y, duration))
                            clean_stall = clean_stall.replace(tag, "").strip()

                    sock.sendto(clean_stall.encode('utf-8'), (UDP_IP, UDP_SEND_PORT))
                    print(f"【樽】(UDP送信/つなぎ言葉): {clean_stall}")

                    if stall_motion_tasks:
                        x, y, duration = stall_motion_tasks[0]
                        with camera_queue.mutex: camera_queue.queue.clear()
                        camera_queue.put((x, y, duration))

                    time.sleep(2.5)

                reply_start_probs = read_emotion_probs()

                clean_reply = reply
                motion_tasks = []
                for tag, (x, y, duration) in MOTION_MAP.items():
                    if tag in clean_reply:
                        motion_tasks.append((x, y, duration))
                        clean_reply = clean_reply.replace(tag, "").strip()

                sock.sendto(clean_reply.encode('utf-8'), (UDP_IP, UDP_SEND_PORT))
                print(f"【樽】(UDP送信/本回答): {clean_reply}")

                if motion_tasks:
                    x, y, duration = motion_tasks[0]
                    with camera_queue.mutex: camera_queue.queue.clear()
                    camera_queue.put((x, y, duration))

                reply_end_probs = read_emotion_probs()
                new_baseline = update_user_bias(bias_dict, [start_probs, end_probs, reply_start_probs, reply_end_probs])
                ai.update_user_baseline(new_baseline)
                print(f" [学習ログ] バイアス微調整完了")
                print("-" * 40)

            except json.JSONDecodeError:
                print(" [エラー] 不正なJSONデータを受信しました")
    except KeyboardInterrupt:
        print("\n終了します。現在のユーザー状態を保存します...")
        ai.save_current_user_state()
    except Exception as e:
        print(f" [エラー] メインループ例外: {e}")
        ai.save_current_user_state()

if __name__ == '__main__':
    main()
```

---

## 10. Source Code: Vision Module (`face_eye.py`)

```python
# ==============================================================================
# 仕様: Tapo C200 映像取得・顔特徴量および感情推論のUDP送信モジュール (face_eye.py)
# 日付: 2026-11-11
# 【修正履歴】
# - 2026-11-11: GitHub公開に伴うセキュリティ修正（RTSP URLの認証情報・IPマスキング）
# ==============================================================================
# 【過去の修正内容】
# 1. 映像取得処理を FFmpeg(subprocess) から堅牢な OpenCV標準(cv2.VideoCapture) へ変更。
# 2. カメラ駆動時のパケットロスによるフリーズを防ぐため、ループ内で cap.grab() を実行し、
#    内部バッファの古い映像を強制的に捨てて「常に最新のフレーム」を処理する遅延排除ロジックを追加。
# 3. ※重要: 感情推論の高感度化ロジック(analyze_emotion_high_sensitivity)は一切変更せず維持。
# ==============================================================================

import cv2
import numpy as np
import threading
import json
import time
import os
import socket

# 【セキュリティ対策】個人情報・認証情報をマスキング
rtsp_url = "rtsp://XXX:XXX@XXX:554/stream1"
width, height = 1280, 720

latest_result = {"Total_Faces": 0, "Face_List": [], "Status": "Initializing", "Avg_Processing_Time_ms": 0.0}

USER_DATABASE = {
    "Aさん": [0.01] * 128,
    "Bさん": [-0.01] * 128
}

DICT_EMOTION_MAP = {
    "Neutral": "neutral", "Happiness": "happiness", "Surprise": "surprise", 
    "Sadness": "sadness", "Anger": "anger", "Disgust": "disgust", 
    "Fear": "fear", "Contempt": "disgust"
}

# UDPソケット設定 (送信用)
UDP_IP = "127.0.0.1"
UDP_PORT = 5005
udp_sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# 1. 顔検出モデル (YuNet)
yunet_path = 'face_detection_yunet_2023mar.onnx'
if not os.path.exists(yunet_path):
    print(f"【エラー】 {yunet_path} がありません。")
detector = cv2.FaceDetectorYN.create(yunet_path, "", (width, height), score_threshold=0.6)

# 2. 顔認証モデル (MobileFaceNet)
mobilefacenet_path = 'mobilefacenet.onnx'
try:
    auth_net = cv2.dnn.readNetFromONNX(mobilefacenet_path)
except:
    auth_net = None

# 3. 感情推論モデル (FER+)
emotion_path = 'emotion-ferplus-8.onnx'
try:
    emotion_net = cv2.dnn.readNetFromONNX(emotion_path)
except:
    emotion_net = None
emotion_labels = ["Neutral", "Happiness", "Surprise", "Sadness", "Anger", "Disgust", "Fear", "Contempt"]

def extract_features(face_img):
    if auth_net is None or face_img.size == 0:
        return []
    blob = cv2.dnn.blobFromImage(face_img, scalefactor=1.0/128.0, size=(112, 112), mean=(127.5, 127.5, 127.5), swapRB=True, crop=False)
    auth_net.setInput(blob)
    preds = auth_net.forward()
    flat_preds = preds.flatten()
    norm = np.linalg.norm(flat_preds)
    return (flat_preds / norm).tolist() if norm > 0 else []

def match_face(features):
    if not features: return "Unknown", 0.0
    best_name, max_sim, threshold = "Unknown", 0.0, 0.6
    vec = np.array(features)
    for name, reg_feat in USER_DATABASE.items():
        sim = float(np.dot(vec, np.array(reg_feat)))
        if sim > max_sim:
            max_sim = sim
            best_name = name
    if max_sim < threshold: return "Unknown", round(max_sim, 4)
    return best_name, round(max_sim, 4)

def analyze_emotion_high_sensitivity(preds):
    exp_preds = np.exp(preds - np.max(preds))
    probs = exp_preds / exp_preds.sum()
    
    neutral_idx = 0
    max_non_neutral_prob = 0.0
    best_emotion_idx = 0
    
    for i, prob in enumerate(probs):
        if i == neutral_idx: continue
        if prob > max_non_neutral_prob:
            max_non_neutral_prob = prob
            best_emotion_idx = i
            
    if max_non_neutral_prob > 0.15:
        return emotion_labels[best_emotion_idx]
    return "Neutral"

def vision_loop():
    global latest_result
    
    # TCP通信を強制してRTSPのパケットロスを最小化（タイムアウトを5秒＝5,000,000マイクロ秒に設定）
    os.environ["OPENCV_FFMPEG_CAPTURE_OPTIONS"] = "rtsp_transport;tcp|stimeout;5000000"
    cap = cv2.VideoCapture(rtsp_url, cv2.CAP_FFMPEG)
    
    frame_count = 0
    emotion_cache = {}
    auth_cache = {}
    processing_times = []

    while True:
        start_time = time.time()
        
        # 【重要】バッファに溜まった過去のフレームを強制的に捨て、最新の状況のみを捉える
        for _ in range(3):
            cap.grab()
        
        ret, frame = cap.read()
        if not ret or frame is None:
            # 映像ストリームが切断・破損した場合は再接続（フリーズ防止）
            cap.release()
            time.sleep(1.0)
            cap = cv2.VideoCapture(rtsp_url, cv2.CAP_FFMPEG)
            continue
            
        # YuNetが要求する解像度に正確にリサイズ
        frame = cv2.resize(frame, (width, height))
        frame_count += 1
        
        _, faces = detector.detect(frame)
        result_data = {"Total_Faces": 0, "Face_List": [], "Status": "Running"}
        
        if faces is not None:
            result_data["Total_Faces"] = len(faces)
            for i, face in enumerate(faces):
                x, y, w, h = map(int, face[:4])
                x, y = max(0, x), max(0, y)
                w, h = min(width - x, w), min(height - y, h)
                
                # 画面端で見切れて幅や高さが0になった場合のクラッシュ防止
                if w <= 0 or h <= 0:
                    continue
                    
                face_img = frame[y:y+h, x:x+w]
                face_id = f"face_{i}"
                center_x = x + (w / 2)
                position = "左側" if center_x < (width / 2) else "右側"
                
                if frame_count % 5 == 0:
                    features = extract_features(face_img)
                    identified_user, sim_score = match_face(features)
                    auth_cache[face_id] = {"user": identified_user, "score": sim_score}
                    
                    if emotion_net is not None and face_img.size > 0:
                        gray_face = cv2.cvtColor(face_img, cv2.COLOR_BGR2GRAY)
                        blob = cv2.dnn.blobFromImage(gray_face, scalefactor=1.0, size=(64, 64), mean=0, swapRB=False, crop=False)
                        emotion_net.setInput(blob)
                        preds = emotion_net.forward()[0]
                        
                        # 感情判定ロジックを呼び出し (変更なし)
                        emotion = analyze_emotion_high_sensitivity(preds)
                        emotion_cache[face_id] = emotion
                        
                        # UDPによる非同期データ送信 (メインプロセス連携用)
                        if i == 0:
                            mapped_emotion = DICT_EMOTION_MAP.get(emotion, "neutral")
                            
                            # バイアス補正用に各感情の確率分布(Softmax)を辞書化
                            exp_preds = np.exp(preds - np.max(preds))
                            probs = exp_preds / exp_preds.sum()
                            emotion_probs = {}
                            for idx, label in enumerate(emotion_labels):
                                mapped_key = DICT_EMOTION_MAP.get(label, "neutral")
                                # ContemptとDisgustが同じdisgustにマッピングされるため合算または最大値を保持
                                emotion_probs[mapped_key] = round(emotion_probs.get(mapped_key, 0.0) + float(probs[idx]), 4)
                            
                            # 既存のキーを維持しつつ、face_features と emotion_probs を同梱
                            payload = json.dumps({
                                "emotion": mapped_emotion,
                                "user": identified_user,
                                "face_features": [round(f, 5) for f in features], # パケットサイズ肥大化防止のため丸める
                                "emotion_probs": emotion_probs
                            }).encode('utf-8')
                            
                            try:
                                udp_sock.sendto(payload, (UDP_IP, UDP_PORT))
                            except:
                                pass
                
                current_auth = auth_cache.get(face_id, {"user": "Unknown", "score": 0.0})
                current_emotion = emotion_cache.get(face_id, "Neutral")
                result_data["Face_List"].append({
                    "Identified_User": current_auth["user"], "Similarity": current_auth["score"],
                    "Emotion": current_emotion, "Position": position,
                    "Coordinates": {"x": int(x), "y": int(y), "w": int(w), "h": int(h)}
                })
            
        elapsed_ms = (time.time() - start_time) * 1000
        processing_times.append(elapsed_ms)
        if len(processing_times) > 30: processing_times.pop(0)
        result_data["Avg_Processing_Time_ms"] = round(sum(processing_times) / len(processing_times), 2)
        latest_result = result_data

def get_latest_vision_data():
    return json.dumps(latest_result, indent=2, ensure_ascii=False)

if __name__ == "__main__":
    print("バックグラウンド処理を開始...")
    threading.Thread(target=vision_loop, daemon=True).start()
    time.sleep(3)
    while True:
        print("【自動取得】最新の解析結果:")
        print(get_latest_vision_data())
        print("-" * 30)
        time.sleep(2)
```

---

## 11. Source Code: Audio I/O Module (`ear_mouth_rev.py`)

```python
"""
=========================================================
仕様: VOSK(PyAudioリアルタイム) + Open JTalk 統合モジュール
      - 端末のPulseAudioサーバー経由でマイクストリームを直接取得(ファイルI/Oゼロ)
      - 発話中(Open JTalk再生中)はマイクを一時停止し、ハウリングを完全防止
      - MeCabによる疑問文・否定表現判定とUDP送出のアーキテクチャ
日付: 2026-11-11
変更内容（確定仕様厳守）:
      - 【FIX】MeCabの否定判定(check_has_negation)を「名詞の否定・例外」に特化。
               動詞や形容詞の否定への過剰な誤反応を排除。
      - 2026-11-11: GitHub公開に伴う自己紹介テキストの個人名マスキング
=========================================================
"""

import sys
import os
import socket
import json
import threading
import queue
import subprocess
import traceback
import time
import pyaudio
import MeCab

try:
    from vosk import Model, KaldiRecognizer
except ModuleNotFoundError:
    try:
        from vosk_termux import Model, KaldiRecognizer
    except ImportError:
        print("[CRITICAL ERROR] voskモジュールが見つかりません。")
        sys.exit(1)

# ==========================================
# 1. システム設定
# ==========================================
MY_RECV_PORT = 5002
TALK_PROCESS_PORT = 5001
TARGET_IP = "127.0.0.1"

VOSK_MODEL_PATH = "./vosk-model"
OPEN_JTALK_BIN = "open_jtalk"
DICT_PATH = "/data/data/com.termux/files/home/droid/openjtalk/open_jtalk_dic_utf_8-1.11"
VOICE_PATH = "/data/data/com.termux/files/home/droid/openjtalk/mei_normal.htsvoice"

AUDIO_RATE = 16000
CHUNK_SIZE = 4000

STATE_LISTENING = "LISTENING"
STATE_SPEAKING = "SPEAKING"

current_state = STATE_LISTENING
output_queue = queue.Queue()

# 【セキュリティ対策】開発者の個人名をマスキング
INTRO_TEXT = (
    "お世話になっています。 "
    "私は、XXXさんという、元、エンジニアに設計された、会話ドロイドです。 "
    "インターネットにはつながず、単独で動いているので、秘密は、守られますよ。うふっ。"
)

# ==========================================
# 2. サブ関数群
# ==========================================
def talk_process_receiver(sock, q):
    while True:
        try:
            data, _ = sock.recvfrom(4096)
            text = data.decode('utf-8').strip()
            if text:
                q.put(text)
        except Exception:
            pass

def check_is_question(text, tagger):
    if "?" in text or "？" in text:
        return True
    node = tagger.parseToNode(text)
    last_token = ""
    last_pos = ""
    while node:
        if node.surface:
            last_token = node.surface
            last_pos = node.feature.split(',')[0]
        node = node.next
    if last_pos == "助詞" and last_token in ["か", "の", "かな", "かしら"]:
        return True
    return False

def check_has_negation(text, tagger):
    node = tagger.parseToNode(text)
    active_noun = False
    
    while node:
        if node.surface:
            features = node.feature.split(',')
            pos = features[0]
            pos_detail = features[1] if len(features) > 1 else ""
            base_form = features[6] if len(features) > 6 else node.surface
            
            if pos == "名詞" and pos_detail != "非自立":
                active_noun = True
            elif pos in ["動詞", "形容詞"]:
                if base_form != "ある":
                    active_noun = False
            elif pos == "助詞":
                if node.surface in ["が", "を", "に", "へ", "と", "から", "より"]:
                    active_noun = False
            
            is_negation_word = False
            if node.surface in ["以外", "ダメ", "違う", "じゃなく", "ではなく"] or base_form == "違う":
                is_negation_word = True
            elif pos == "助動詞" and base_form in ["ない", "ぬ", "ず", "ん"]:
                is_negation_word = True
            elif pos == "形容詞" and base_form == "ない":
                is_negation_word = True
                
            if is_negation_word and active_noun:
                return True
                
        node = node.next
        
    return False

# ==========================================
# 3. メイン処理
# ==========================================
def main():
    global current_state
    print("[-] 最終統合マネージャー (完全I/Oゼロ・パイプストリーミング版) 初期化開始...")
   
    if not os.path.exists(VOICE_PATH) or not os.path.exists(VOSK_MODEL_PATH):
        print("[ERROR] 必須ファイルが見つかりません。パスを確認してください。")
        sys.exit(1)

    recv_sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    recv_sock.bind((TARGET_IP, MY_RECV_PORT))
    send_sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

    model = Model(VOSK_MODEL_PATH)
    recognizer = KaldiRecognizer(model, AUDIO_RATE)
    mecab_tagger = MeCab.Tagger()

    p = pyaudio.PyAudio()
    stream = p.open(format=pyaudio.paInt16, 
                    channels=1, 
                    rate=AUDIO_RATE, 
                    input=True, 
                    frames_per_buffer=CHUNK_SIZE)
    
    threading.Thread(target=talk_process_receiver, args=(recv_sock, output_queue), daemon=True).start()
    
    print("[-] 起動時自己紹介をキューに投入します...")
    output_queue.put(INTRO_TEXT)
    
    is_first_reply = True

    print("\n[+] 稼働中: リアルタイム音声対話・排他制御モード\n")

    try:
        while True:
            if not output_queue.empty():
                current_state = STATE_SPEAKING
                text_to_speak = output_queue.get()
                
                clean_text = text_to_speak.replace("……", "、").replace("…", "、").replace("...", "、")
                print(f"[SPEAKING] 「{clean_text}」")

                stream.stop_stream()

                cmd_jtalk = [OPEN_JTALK_BIN, "-x", DICT_PATH, "-m", VOICE_PATH, 
                             "-r", "0.85", "-fm", "-2.5", "-a", "0.53", "-jf", "1.5", "-ow", "/dev/stdout"]
                cmd_mpv = ["mpv", "--no-video", "--really-quiet", "-"]

                try:
                    proc_jtalk = subprocess.Popen(cmd_jtalk, stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.DEVNULL)
                    proc_mpv = subprocess.Popen(cmd_mpv, stdin=proc_jtalk.stdout, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)

                    proc_jtalk.stdin.write(clean_text.encode('utf-8'))
                    proc_jtalk.stdin.close()

                    proc_mpv.wait(timeout=25)
                except Exception as e:
                    print(f"[ERROR] 音声ストリーミング再生中にエラー: {e}")
                    try:
                        proc_jtalk.kill()
                        proc_mpv.kill()
                    except:
                        pass
                
                output_queue.task_done()
                
                recognizer.Reset()
                stream.start_stream()
                current_state = STATE_LISTENING
                print(f"[LISTENING] マイク入力待機中...")
                
                time.sleep(0.3)
                continue

            if current_state == STATE_LISTENING:
                try:
                    data = stream.read(CHUNK_SIZE, exception_on_overflow=False)
                except OSError:
                    continue

                if recognizer.AcceptWaveform(data):
                    result_json = json.loads(recognizer.Result())
                    text = result_json.get("text", "").replace(" ", "")
                   
                    if text:
                        is_question_flag = check_is_question(text, mecab_tagger)
                        has_negation_flag = check_has_negation(text, mecab_tagger)
                        
                        print(f"[RECOGNIZED]: {text} (質問: {is_question_flag}, 否定/例外: {has_negation_flag})")
                        
                        payload = {
                            "type": "speech", 
                            "status": "final", 
                            "text": text, 
                            "is_question": is_question_flag,
                            "has_negation": has_negation_flag,
                            "timestamp": time.time()
                        }
                        send_sock.sendto(json.dumps(payload, ensure_ascii=False).encode('utf-8'), (TARGET_IP, TALK_PROCESS_PORT))
                        
                        if is_first_reply:
                            output_queue.put("ありがとうございます。")
                            is_first_reply = False

    except KeyboardInterrupt:
        print("\n[-] 停止要求を受信。安全に終了します。")
    except Exception as e:
        print(f"\n[CRITICAL ERROR] ループ例外: {e}")
        traceback.print_exc()
    finally:
        try:
            stream.stop_stream()
            stream.close()
            p.terminate()
            recv_sock.close()
            send_sock.close()
        except:
            pass

if __name__ == "__main__":
    main()
```

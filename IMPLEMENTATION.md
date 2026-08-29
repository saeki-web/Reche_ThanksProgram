# WordPress への実装ガイド

## ステップ 1: ファイルをホストする

### オプション A: jsDelivr を使用（推奨）
GitHub に配置した場合、jsDelivr 経由で CDN 配信できます：

```
https://cdn.jsdelivr.net/gh/ユーザー名/リポジトリ名@main/tdc-ai-chat-widget.js
```

### オプション B: 独自サーバー
自身のサーバーにアップロード：
- Xserver などで `/public_html/widgets/` フォルダを作成
- `tdc-ai-chat-widget.js` をアップロード

## ステップ 2: WordPress に埋め込む

### 方法 1: テーマファイルエディター（簡単）
1. WordPress 管理画面 → 外観 → テーマファイルエディター
2. 右側で `footer.php` を選択
3. 末尾（`</body>` の直前）に以下を追加：

```html
<!-- たにぐちデンタルクリニック AIチャットボット -->
<script src="https://cdn.jsdelivr.net/gh/ユーザー名/リポジトリ名@main/tdc-ai-chat-widget.js" defer></script>
```

### 方法 2: プラグイン（推奨）
より安全な方法：
1. WordPress 管理画面 → プラグイン → 新規追加
2. 「Header and Footer Scripts」などのプラグインを検索してインストール
3. 設定で上記の `<script>` タグをフッターに追加

### 方法 3: ヘッダー挿入プラグイン
- 「Insert Headers and Footers」
- 「WP Code」（旧 Code Snippets）

など、任意のヘッダー・フッター挿入プラグインで対応可能。

## ステップ 3: オプション設定

### 設定項目

ファイルの先頭にある CONFIG オブジェクトを編集：

```javascript
var CONFIG = {
  // 外部予約システムのURL
  RESERVE_URL: "https://reserve.example.com",
  // 問い合わせ送信先（PHP エンドポイント）
  INQUIRY_ENDPOINT: "https://your-domain.com/api/inquiries.php"
};
```

### RESERVE_URL（予約サイトへのリンク）
チャットボットの「予約サイトへ進む」ボタンをクリックした時のリンク先：
- 外部予約システムの URL を指定
- 空のままだと機能しません

### INQUIRY_ENDPOINT（問い合わせ送信先）
問い合わせデータを POST 送信するサーバー側のエンドポイント：
- 空のままだと `localStorage` のみに保存
- PHP や Node.js で実装したエンドポイント URL を指定

## ステップ 4: サーバー側エンドポイントの実装（オプション）

### PHP の例（Xserver）

ファイル: `/public_html/api/inquiries.php`

```php
<?php
header('Content-Type: application/json');

// CORS対応（クロスオリジン許可）
header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Methods: POST, OPTIONS');
header('Access-Control-Allow-Headers: Content-Type');

if ($_SERVER['REQUEST_METHOD'] === 'OPTIONS') {
  http_response_code(200);
  exit;
}

if ($_SERVER['REQUEST_METHOD'] !== 'POST') {
  http_response_code(405);
  echo json_encode(['error' => 'Method not allowed']);
  exit;
}

$data = json_decode(file_get_contents('php://input'), true);

if (!$data) {
  http_response_code(400);
  echo json_encode(['error' => 'Invalid JSON']);
  exit;
}

// 必須フィールドをチェック
$required = ['id', 'ts', 'category', 'name', 'contact', 'summary'];
foreach ($required as $field) {
  if (empty($data[$field])) {
    http_response_code(400);
    echo json_encode(['error' => "Missing field: $field"]);
    exit;
  }
}

// ここからデータ保存処理
// 例1: ファイルに保存
$logFile = '/var/www/html/inquiries.jsonl'; // サーバーの安全な場所
$line = json_encode($data, JSON_UNESCAPED_UNICODE) . "\n";
file_put_contents($logFile, $line, FILE_APPEND);

// 例2: メール通知
$to = 'info@example.com';
$subject = '【新しい問い合わせ】' . $data['category'];
$message = sprintf(
  "日時: %s\n氏名: %s\nご連絡先: %s\n\n内容:\n%s",
  $data['ts'],
  $data['name'],
  $data['contact'],
  $data['summary']
);
mail($to, $subject, $message);

// 成功レスポンス
http_response_code(200);
echo json_encode(['success' => true, 'id' => $data['id']]);
?>
```

## ステップ 5: 動作確認

### テスト手順
1. WordPress のサイトをブラウザで開く
2. 右下にチャットボタン（ティール色）が表示されるか確認
3. チャットボタンをクリックして会話を進める
4. 左下の管理者ボタン（茶色）をクリックして管理パネルを開く
5. 問い合わせが表示されるか確認

### 管理パネルの確認
- Shift + A キーを押すと管理パネルが開く
- 問い合わせ一覧が表示される
- ステータスボタンをクリックして「未対応」「対応済み」を切り替える

## トラブルシューティング

### ウィジェットが表示されない
- ブラウザの JavaScript が有効か確認
- ブラウザの開発者ツール（F12）→ コンソールで error を確認
- URL が正しいか確認（jsDelivr の場合、リポジトリ名が合致しているか）

### CSS が競合している
- すべてのクラスに `tdcw-` プレフィックスがあるので競合しにくい
- 万が一競合しても、ウィジェットの CSS は問題解決まで待つ

### エンドポイントに送信されない
- ブラウザコンソールで「INQUIRY_ENDPOINT 未設定」というメッセージを確認
- CONFIG の INQUIRY_ENDPOINT が正しく設定されているか確認
- CORS エラーが出ていないか確認（PHP で header() で CORS ヘッダを設定）

### メール通知が来ない
- PHP の mail() が有効か確認（Xserver では通常有効）
- メールアドレスが正しいか確認
- スパムフォルダを確認

## セキュリティ上の注意

- エンドポイント URL は公開される（ブラウザのコンソールで見える）
- 本番環境では、以下のセキュリティ対策を実装してください：
  - レート制限（同じ IP から大量の POST を制限）
  - CSRF トークン検証
  - HTTPS のみ許可
  - IP ホワイトリスト（必要に応じて）

## 今後の更新

ウィジェットをアップデートする場合：
1. GitHub に新しいバージョンをアップロード
2. WordPress の script タグの URL は自動的に最新版を読み込みます
（jsDelivr の場合、キャッシュが更新されるまで数時間かかる場合があります）

ファイル URL を `@main` ではなく `@latest` に変更することで、常に最新版が読み込まれます：
```html
<script src="https://cdn.jsdelivr.net/gh/ユーザー名/リポジトリ名@latest/tdc-ai-chat-widget.js" defer></script>
```

## サポート

問題が発生した場合は、以下を確認してください：
1. ブラウザコンソール（F12 キー）のエラーメッセージ
2. ウィジェットファイルが正しく読み込まれているか（ネットワークタブで確認）
3. CONFIG が正しく設定されているか

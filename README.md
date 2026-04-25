# k-tracker 家計トラッカー

## システム構成

```
GitHub (hypnosis-create/k-tracker)
　　↓ 自動デプロイ
Vercel (k-tracker-smoky.vercel.app)
　　↓ データ読み書き
Supabase (kakeibo / hypnosis-create's Org)
```

---

## アカウント情報

| サービス | アカウント |
|---------|-----------|
| GitHub | hypnosis-create |
| Supabase | hypnosis-create's Org / kakeibo |
| Vercel | hypnosis-create's projects (Hobby) |

---

## アクセスURL

```
https://k-tracker-smoky.vercel.app/
```

---

## Supabase API情報の取得方法

### Project URL
```
1. Supabase ダッシュボードを開く
2. 左メニュー最下部「Project Settings」をクリック
3. 「General」をクリック
4. 「Project ID」を確認
5. URL → https://[Project ID].supabase.co
```

現在の Project URL:
```
https://gcplgmheiappnjxegsqj.supabase.co
```

### anon key
```
1. Supabase ダッシュボードを開く
2. 左メニュー最下部「Project Settings」をクリック
3. 「API」または「Data API」をクリック
4. 「Legacy anon, service_role API keys」タブをクリック
5. anon の行のキーをコピー
```

---

## 初回セットアップ手順

### 1. GitHubアカウント作成
```
1. github.com にアクセス
2. 新規アカウント作成
3. k-tracker リポジトリを新規作成
   - Repository name: k-tracker
   - Visibility: Public
   - Add README: On
```

### 2. Supabaseセットアップ
```
1. supabase.com にアクセス
2. 「Sign up with GitHub」で連携
3. New organization → Personal / Free
4. New project
   - Project name: kakeibo
   - Region: Asia-Pacific (Northeast Asia / Seoul)
   - Enable Data API: ON
5. SQL Editorでテーブル作成（下記SQL参照）
6. RLSを無効化（下記SQL参照）
```

### 3. Vercelセットアップ
```
1. vercel.com にアクセス
2. 「Sign up」→「Continue with GitHub」で連携
3. Plan: Hobby（無料）
4. Install GitHub App → hypnosis-create を選択
5. Import → k-tracker → Deploy
```

### 4. HTMLファイルアップロード
```
1. index.html をGitHubのk-trackerリポジトリにアップロード
   - 「Add file」→「Upload files」
   - index.html を選択
   - 「Commit changes」
2. Vercelが自動でデプロイ（約1分）
```

---

## Supabase テーブル作成SQL

```sql
-- カテゴリマスタ
create table categories (
  id serial primary key,
  name text not null,
  icon text,
  sort_order integer default 0
);

-- キーワードマスタ（店名→カテゴリ自動分類）
create table keyword_master (
  id serial primary key,
  keyword text not null,
  category_id integer references categories(id)
);

-- 取引データ（メイン）
create table transactions (
  id serial primary key,
  date date not null,
  description text,
  amount integer not null,
  category_id integer references categories(id),
  source text default 'csv',
  created_at timestamp default now()
);

-- 手入力データ（電気・水道など）
create table manual_entries (
  id serial primary key,
  year_month text not null,
  category_id integer references categories(id),
  amount integer not null,
  memo text
);

-- カテゴリ初期データ
insert into categories (name, icon, sort_order) values
  ('食費（スーパー）', '🛒', 1),
  ('食費（コンビニ）', '🏪', 2),
  ('外食', '🍜', 3),
  ('交通費', '🚗', 4),
  ('車維持費', '🚙', 5),
  ('通信費', '📱', 6),
  ('電気', '💡', 7),
  ('水道', '💧', 8),
  ('住居費', '🏠', 9),
  ('衣類', '👕', 10),
  ('医療・健康', '💊', 11),
  ('娯楽・趣味', '🎟️', 12),
  ('旅行・宿泊', '✈️', 13),
  ('税金・保険', '🏛️', 14),
  ('教育', '🎓', 15),
  ('交際費', '🎁', 16),
  ('日用品', '🛍️', 17),
  ('未分類', '❓', 18);
```

## RLS無効化SQL（初回必須）

```sql
alter table transactions disable row level security;
alter table manual_entries disable row level security;
alter table categories disable row level security;
alter table keyword_master disable row level security;
```

## キーワードマスタ 初期登録SQL

```sql
insert into keyword_master (keyword, category_id) values
  ('サンエー', 1),
  ('ファミリーマート', 2),
  ('ローソン', 2),
  ('セブンイレブン', 2),
  ('ETC', 4),
  ('CLAUDE', 12),
  ('一休', 13),
  ('赤道', 1),
  ('ツルハ', 17),
  ('ドラッグ', 17),
  ('すき家', 3),
  ('マクドナルド', 3),
  ('KDDI', 6),
  ('ソフトバンク', 6),
  ('ドコモ', 6),
  ('au', 6),
  ('Suica', 4),
  ('地方税', 14),
  ('アゴダ', 13),
  ('ホテル', 13);
```

---

## 毎月の使い方

```
1. カード会社サイトからCSVをダウンロード
   （ANA JCBカード / Shift-JIS形式）

2. アプリの「CSV取込」タブでファイルを選択

3. プレビューでカテゴリを確認・修正

4. 「すべて保存」をクリック

5. 電気・水道は「手入力」タブで入力

6. ダッシュボードで集計を確認
```

---

## HTMLファイル更新方法

```
1. 新しい index.html を用意
2. GitHubのk-trackerリポジトリを開く
3. index.html をクリック
4. 右上「...」→「Upload file」で上書き
   または「Add file」→「Upload files」
5. 「Commit changes」
6. Vercelが自動デプロイ（約1分）
```

---

## カテゴリID一覧

| ID | カテゴリ |
|----|---------|
| 1 | 🛒 食費（スーパー） |
| 2 | 🏪 食費（コンビニ） |
| 3 | 🍜 外食 |
| 4 | 🚗 交通費 |
| 5 | 🚙 車維持費 |
| 6 | 📱 通信費 |
| 7 | 💡 電気 |
| 8 | 💧 水道 |
| 9 | 🏠 住居費 |
| 10 | 👕 衣類 |
| 11 | 💊 医療・健康 |
| 12 | 🎟️ 娯楽・趣味 |
| 13 | ✈️ 旅行・宿泊 |
| 14 | 🏛️ 税金・保険 |
| 15 | 🎓 教育 |
| 16 | 🎁 交際費 |
| 17 | 🛍️ 日用品 |
| 18 | ❓ 未分類 |

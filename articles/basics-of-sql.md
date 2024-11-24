---
title: 勉強メモ：SQLの基礎
emoji: 🔎
type: tech
topics:
  - sql
  - datanalysis
  - datascience
published: false
---
## SQLとは？

SQL (Structured Query Language) はデータベースから必要な情報を抽出するのに適した言語です。

## SQLの超基本

- `SELECT` データを取得する
- `INSERT` データを追加する
- `UPDATE` データを更新する
- `DELETE` データを消去する

## サンプルデータを使って試してみよう！

データベース例： `employees`

| id  | name  | age | department |
| --- | ----- | --- | ---------- |
| 1   | Alice | 25  | HR         |
| 2   | Bob   | 30  | IT         |
| 3   | Carol | 27  | Finance    |

### テーブル内のデータ全てを取得する

```sql
SELECT * FROM employees;
```

### 特定のカラムを取得する 

```sql
SELECT name, age FROM employees
```

### 条件を満たしたものだけ取得する

```sql
SELECT * FROM employees
WHERE department = 'IT';
```


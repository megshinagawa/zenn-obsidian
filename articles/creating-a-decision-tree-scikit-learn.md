---
title: Scikit-learnで分類用の決定木を作ってみよう
emoji: 🌲
type: tech
topics:
  - datascience
  - Scikitlearn
  - Python
published: true
---
## ライブラリのインストール

```python 
import numpy as np 
import pandas as pd 
import matplotlib.pyplot as plt 
from sklearn import datasets 
from sklearn.model_selection import train_test_split 
from sklearn.tree import DecisionTreeClassifier, plot_tree 
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix
```

## データセットを読み込む

今回は `Breast cancer wisconsin (diagnostic) dataset` という Scikit-learn が提供している乳がんのデータセットを使います。（詳細は[こちらのScikit-learnのページ](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_breast_cancer.html)で確認できます。）

```python
cancer = datasets.load_breast_cancer(as_frame=True)
```

### 乳がんのデータってどんなデータ？

データがどんなものか理解しないで決定木を作っても意味がないので、まずはデータの中身を確認しましょう。

```python 
df = cancer.frame
```

![](/images/breastcancerdataset.png)

今回は、このデータの中から乳がんがある人を当てる決定木を作ります。

### 特徴量とラベルにデータを分ける

特徴量の数値からラベル（「乳がんがる」か「乳がんがない」）を選択していきます。

```python
X = cancer.data  # 特徴量
y = cancer.target  # ラベル
```

## 学習データとテストデータを分ける

決定木を作る時は、モデルの学習用データとモデルのテスト用データに受領データを分けます。

```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

:::message 
`random_state` を使うことで、同じ初期条件でモデルをトレーニングできて、結果の比較ができるようになります。
:::

## 決定木を作成

```python
clf = DecisionTreeClassifier(random_state=42)
clf.fit(X_train, y_train)
```

## モデルを使ってテストデータの結果を予測

```python
y_pred = clf.predict(X_test)
```

## モデルを評価

### 精度 (Accuracy)

正しく分類されたサンプルの割合をパーセンてーじ形式で示します。

```python
accuracy = accuracy_score(y_test, y_pred)
print(f"Accuracy: {accuracy * 100:.2f}%")
```

### 分類レポート (Classification Report)

精度、再現率、F1スコアなどが含まれているモデルの性能を詳細に評価するレポートを生成します。

```python
print("\nClassification Report:\n", classification_report(y_test, y_pred))
```

### 混同行列 (Confusion Matrix)

モデルの予測と実際のクラスとの関係を視覚的に表現し、正しく分類されたサンプルと誤分類されたサンプルの数を示します。

```python
print("Confusion Matrix:\n", confusion_matrix(y_test, y_pred))
```

## 決定木を書く

```python
plt.figure(figsize=(12, 8)) 
plot_tree(clf, filled=True, feature_names=cancer.feature_names, class_names=cancer.target_names) 
plt.title("Decision Tree Visualization") 
plt.show()
```

![](/images/breastcancerdt.png)

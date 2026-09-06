# Magic Maskで人物を切り抜く

DaVinci Resolve Studio の **Magic Mask** を使って、
動画内の人物を自動追跡し、背景から切り抜く方法をまとめます。

このチュートリアルでは、人物の指定からトラッキング、境界調整、
トラッキングが途中で崩れた場合の対処、Alpha Outputを使った背景透明化までを扱います。

---

## Overview

Magic Maskは、AIを利用して動画内の人物やオブジェクトを認識し、
フレームをまたいで自動追跡しながらマスクを生成する機能です。

人物の背景を切り抜いたり、
人物と背景を別々にカラー調整したり、
透明背景の動画素材を作成したりする場合に利用できます。

主な用途は以下です。

- 人物だけを切り抜く
- 背景を透明化する
- 人物と背景を別々にカラー調整する
- Fusionで再利用する
- Alpha Channel付き動画を作成する
- SNS・プレゼン・モーショングラフィックス素材を作成する

---

## Environment

- OS: Windows 11
- GPU: NVIDIA GPU
- Software: DaVinci Resolve Studio 20.2.1

### Features Used

- Color Page
- Magic Mask

> DaVinci Resolveのバージョンによって、
> Magic Maskの画面構成や項目名が多少異なる場合があります。

---

## Goal

今回の最終目標は、
動画内の人物だけを切り抜いて、
背景を透明として扱える状態にすることです。

作業の流れは以下です。

```text
Original Video
      ↓
Color Page
      ↓
Magic Mask
      ↓
Select Person
      ↓
Tracking
      ↓
Mask Adjustment
      ↓
Alpha Output
      ↓
Transparent Background
```

透明背景を保持した動画として書き出す方法は、
次のチュートリアルで扱います。

[02 - Alpha Export](../02-alpha-export/)

---

## Workflow

### 1. 対象クリップを選択する

まず、EditページまたはColorページで、
人物を切り抜きたい動画クリップを選択します。

長尺動画でも処理できますが、
人物の向きや画面構成が大きく変化する動画では、
後述する方法で区間を分けた方が安定する場合があります。

<!--
![Select Clip](images/01-select-clip.png)
-->

---

### 2. Colorページを開く

画面下部から **Color** ページへ移動します。

Colorページでは、主に以下の機能を使用します。

- Node
- Magic Mask
- Tracking
- Alpha Output

<!--
![Color Page](images/02-color-page.png)
-->

---

### 3. Magic Maskを開く

Colorページのツールから **Magic Mask** を開きます。

人物を切り抜く場合は、
Viewer上で対象となる人物を指定します。

DaVinci Resolveのバージョンによって、
人物やオブジェクトの選択方法、UIの配置などが異なる場合があります。

<!--
![Magic Mask Panel](images/03-magic-mask-panel.png)
-->

---

### 4. 人物を指定する

Viewer上で切り抜きたい人物を指定します。

人物の中央付近など、
対象として認識しやすい部分から指定します。

最初に人物を指定するフレームは重要です。

できるだけ以下の条件を満たすフレームを選ぶと、
トラッキングが安定しやすくなります。

- 人物全体が見えている
- 他の人物と重なっていない
- Motion Blurが少ない
- 身体の輪郭が分かりやすい
- 背景との区別がつきやすい

<!--
![Select Person](images/04-select-person.png)
-->

---

### 5. Mask Overlayで認識範囲を確認する

Magic Maskが人物をどのように認識しているか確認します。

Mask Overlayを表示すると、
マスクされている範囲を視覚的に確認できます。

特に確認したい部分は以下です。

- 髪の毛
- 指
- 腕
- 衣服の端
- 顔周辺
- 背景との境界

<!--
![Mask Overlay](images/05-mask-overlay.png)
-->

---

### 6. Trackingを実行する

人物を指定したら、
Magic Maskのトラッキングを実行します。

基準となるフレームから、
前方向または後方向へトラッキングします。

例えば動画の中央付近で人物を指定した場合は、
次のような処理になります。

```text
Beginning
    ↑
Backward Tracking
    ↑
Reference Frame
    ↓
Forward Tracking
    ↓
End
```

必ずしも動画の最初のフレームから
トラッキングを開始する必要はありません。

人物の輪郭がはっきりしていて、
Magic Maskが認識しやすいフレームから開始する方法も有効です。

<!--
![Magic Mask Tracking](images/06-tracking.png)
-->

---

## Mask Adjustment

### 7. マスクの状態を確認する

トラッキングが終了したら、
動画を再生しながらマスクの状態を確認します。

少なくとも、

```text
Start
  ↓
Middle
  ↓
End
```

のように、複数の位置で確認します。

特に以下のような場面では、
Magic Maskの認識が崩れやすくなります。

- 人物が回転する
- 手を大きく動かす
- カメラに近づく
- 他の人物や物体と重なる
- 画面外へ出る
- 再び画面内へ入る
- 照明が大きく変化する
- Motion Blurが強い

<!--
![Good Mask](images/07-good-mask.png)
-->

---

### 8. 境界を調整する

Magic Maskの調整項目を使って、
人物と背景の境界を自然にします。

DaVinci Resolveのバージョンによって
表示される設定項目は異なりますが、
主にマスクのエッジやノイズ、細かな欠けなどを確認します。

調整項目の例：

- Clean Black
- Clean White
- Blur
- Denoise
- In / Out Ratio
- エッジの滑らかさ
- 小さな穴やノイズの除去

設定値を極端に大きくすると、
髪の毛や指などの細い部分まで失われる場合があります。

実際の合成結果を確認しながら、
必要最小限の調整を行うのがポイントです。

---

### 9. 髪の毛の境界を確認する

人物切り抜きで難しい部分の1つが髪の毛です。

背景を完全に消すことだけを優先して
マスクを強く調整すると、
細い髪の毛まで消えてしまう場合があります。

以下のような順番で調整すると確認しやすくなります。

1. まず人物全体を安定して認識させる
2. 髪の毛周辺を拡大する
3. マスクの境界を少しずつ調整する
4. 実際の合成結果を確認する

<!--
![Hair Edge](images/08-hair-edge.png)
-->

---

## Troubleshooting

### Tracking途中でMagic Maskが崩れる

長尺動画では、
最初は正常でも途中から人物認識が崩れる場合があります。

例えば、

```text
0 frame
   ↓
正常
   ↓
正常
   ↓
途中のフレーム
   ↓
Tracking失敗
   ↓
以降崩れる
```

というケースです。

この場合、
1つのMagic Maskで無理に最後まで処理する必要はありません。

---

### Method 1 - クリップを分割する

Editページで、
トラッキングが安定している区間と
崩れる区間の境目でクリップを分割します。

例えば、

```text
正常な区間
     |
     | ここで分割
     |
再Trackingする区間
```

のように処理を分けます。

それぞれのクリップにMagic Maskを設定し、
必要な区間だけトラッキングします。

#### メリット

- 分かりやすい
- Tracking範囲を短くできる
- 修正箇所を見つけやすい

#### デメリット

- クリップ数が増える
- Timeline管理が複雑になる場合がある

---

### Method 2 - 複数のMagic Maskを使用する

1つのクリップ内で、
複数のMagic MaskやNodeを使い分ける方法もあります。

例えば、

```text
MagicMask 1
0 frame ～ Trackingが安定している区間

MagicMask 2
Trackingが崩れた区間 ～ End
```

のように処理を分けます。

これにより、
途中から別のMagic Maskでトラッキングをやり直すことができます。

この方法については、
別チュートリアルとして詳しく整理する予定です。

`04-multiple-magic-masks`

<!--
![Multiple Magic Masks](images/09-multiple-masks.png)
-->

---

## Alpha Output

### 10. Alpha Outputを追加する

人物のマスクが完成したら、
ColorページのNode GraphにAlpha Outputを追加します。

Node Graphの空いている場所で右クリックし、

```text
Add Alpha Output
```

を選択します。

すると、
Node Graph右側にAlpha Outputが追加されます。

<!--
![Add Alpha Output](images/10-add-alpha-output.png)
-->

---

### 11. NodeをAlpha Outputへ接続する

通常の映像出力とは別に、
マスク情報をAlpha Outputへ接続します。

Color Pageのノードでは、
ノード右側にある青い **Key Output** から
Alpha Outputへ接続します。

概念的には次のようになります。

```text
Magic Mask
     ↓
Color Node
     ├──── Image Output
     │
     └──── Key Output ──── Alpha Output
```

これによって、
Magic Maskで生成したマスクを
映像のAlpha Channelとして使用できるようになります。

<!--
![Connect Alpha Output](images/11-connect-alpha-output.png)
-->

---

## Check Transparency

### 12. 背景が透明になっているか確認する

Alpha Outputを接続したら、
人物以外の領域が正しく透明として扱われているか確認します。

特に以下の部分を再確認します。

- 人物が欠けていないか
- 背景が残っていないか
- 髪の毛の境界
- 手や指
- 動きの速い部分
- 人物が画面端に近づく部分

<!--
![Final Magic Mask](images/12-final-mask.png)
-->

---

## Final Workflow

今回の作業全体は以下の流れになります。

```text
Video
  ↓
Color Page
  ↓
Magic Mask
  ↓
Select Person
  ↓
Tracking
  ↓
Mask Adjustment
  ↓
Tracking Check
  ↓
Alpha Output
  ↓
Transparent Background
  ↓
Alpha Export
```

---

## Common Problems

### 人物以外まで選択される

人物と背景の色や明るさが近い場合、
背景の一部まで認識されることがあります。

対策として、

- 別のReference Frameを使用する
- 人物が明確に見えるフレームを選択する
- Maskを調整する
- 必要に応じて処理区間を分ける

などを試します。

---

### Tracking途中から人物を見失う

主な原因としては、

- 人物が画面外へ出る
- 他の人物や物体と重なる
- カメラが大きく動く
- Motion Blurが発生する
- 照明が急激に変化する
- 人物の姿勢が大きく変化する

などがあります。

この場合は、
トラッキング範囲を分割したり、
複数のMagic Maskを使ったりして対応します。

---

### 大きな変更を行う前にバックアップする

Magic MaskやNode構成を大きく変更する場合は、
作業状態を戻せるようにしておくと安心です。

例えば、

- Project Backup
- Timeline Backup
- Duplicate Timeline
- Version

などを利用できます。

特に長時間トラッキングした結果を扱う場合は、
大きな変更を加える前に
複製やバックアップを作成しておくと安全です。

---

## Related Tutorials

透明背景を保持した動画として書き出す方法：

[02 - Alpha Export](../02-alpha-export/)

Fusionを使ったイントロ制作：

[03 - Fusion Intro](../03-fusion-intro/)

---

## Notes

Magic Maskは非常に便利ですが、
長尺動画を常に完全自動で処理できるとは限りません。

特に、

- 人物の重なり
- カメラ移動
- 画面外への移動
- Motion Blur
- 照明変化

などがある場合は、
トラッキング結果を確認しながら
処理区間を分けることが重要です。

1つのMagic Maskで無理に最後まで処理するよりも、

```text
安定している区間ごとに処理する
```

という考え方の方が、
結果的に修正時間を短縮できる場合があります。

---

## Repository

DaVinci Resolve / Fusion / FFmpeg に関する
他の検証・チュートリアルはこちらです。

[DaVinci Resolve Lab](https://github.com/tsukasa-n/davinci-resolve-lab)

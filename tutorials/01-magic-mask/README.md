# Magic Maskで人物を切り抜く

DaVinci Resolve StudioのMagic Maskを使って、
人物を背景から切り抜く方法をまとめます。

## Environment

- Windows
- DaVinci Resolve Studio
- NVIDIA GPU

## Goal

人物だけを切り抜き、最終的に透明背景動画として
書き出せる状態にします。

## Workflow

1. Colorページを開く
2. Magic Maskを追加
3. 人物を選択
4. Trackingを実行
5. 境界を調整
6. Alpha Outputへ接続

## Problems / Notes

### Tracking途中でマスクが崩れる

長い動画ではMagic Maskを分割して処理する方法があります。

### 髪の毛の境界

Clean Black / Clean Whiteなどを調整します。

## Next

透明背景動画として書き出す方法：

→ 02-alpha-export

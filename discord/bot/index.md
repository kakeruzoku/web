# kakeru bot 
> [！] 現在更新を停止しています

## インストールする
- [サーバーインストール](https://0f.f5.si/b)
- [ユーザーインストール](https://0f.f5.si/u)
- [アフィカスして4日分の鯖代をわたす](https://bot-hosting.net/?aff=1081114793106219108) (サインアップするだけ)

## 機能
`x!` / `x@`(管理系コマンド) を先頭につけて実行
### 未定義
```
x!help / ヘルプ
x!hello / 挨拶/pingをする
x!credit / クレジット
```
### 権限管理
```
x@ahelp / 管理者向けhelp
x@adminrole [ロールID/"delete"]
x!adminrole / ロールをチェックする
```
### hookchat - グローバルチャット
```
x!hookhelp / hookchatについてのヘルプを見る
x!hooklist / hookchat参加サーバーを表示する
x!hookverify / 認証を開始する
x!hookcancel / 認証を削除する
x@hookstart / hookchatを作成する
x@hookstop / hookchatを終了する
```
### Scratch
```
x!schelp / disratchのヘルプを見る
x!scverify / scratchアカウントを連携を始める
x!sccheck / 連携を確認する
x!sclist / 連携しているscratchアカウントを表示する
x!scdel / scratchアカウントの連携を解除する
x!scgetcloud [ID] / クラウドデータを取得する
```
### kakeru point system
```
kakeru botでkakeru pointを取得できます。
チャットでも獲得できます。
x!kphelp / kakerupointのヘルプを見る
x!kppoint {ユーザーID} / 今もっているポイント、スコアを表示する
x!kptop {ページ} / ランキングが見れます。
x!kpwork / 20分ごとに働けます。レベル式でどんどんもらえる量が増えていきます。
x!kptrade [buy/sell/now] (数) / 株を売り買いします。(nowは今の状態を見る)
x!kpsend [ユーザーID] [ポイント数] / ポイントを送ります。10%の税金がかかります
=====ポイント利用先===== / x!kpuse [使い先]
bitcoin - 100p - bitcoinアドレスを生成してDMに送信します。
slot [point] - スロットをします。
count {量} - 1000p / 1回 - カウントゲームでミスした時のチケット。
short [URL] {スラッグの指定} - 250p / 短縮URLを生成します。 ドメインは0f.f5.siです。
```
### カウントゲーム
```
x!cghelp / カウントゲームのヘルプを見る
x@cgstart / カウントゲームを開始する
x@cgstop / カウントゲームを終了する
x!kpuse count {量} / ミスした時のチケットを購入します。 1回1000pです。
x!cgnow / 今のカウント状況を確認します。
x!cgdonate / チケットをサーバーに寄付します。(2ユーザーチケット→1サーバーチケット)
x!cgtop [モード] [ページ] / ランキング表示
"best" - 最高記録
"now" - 今の記録
"st" - サーバーチケット所有数
"ut" - ユーザーチケット所有数
"sc" - サーバーカウント数
"uc" - ユーザーカウント数
```
### 短縮リンク
```
短縮リンク作成サービスです。
short.io を使用
x!shhelp / このヘルプを表示
x!kpuse short [URL] {スラッグの指定} / URLを生成します。1URL 250p
x!shrinu [スラッグ] / ActiveTKの位置情報特定ツール(rinu.jp/*****形式)に対抗します
短縮リンク作成の条件
スラッグの指定の制限: 5文字以上 / A-Z a-z 0-9
スラッグ未指定の場合は4文字が出力されます。
*スラッグとは
ドメインの後に続く文字。例えば https://example.com/abc のスラッグは abc です。
```
### bump通知
```
x!bumphelp / ヘルプを表示します。
x!bumpnext / 次のbump時間を見ます。
x@bumpon [roleid] {channelid} / bump通知を送信したチャンネルでオンにします。
x@bumprole [roleid] / bumpを知らせるロールを設定します。
x@bumpch [channelID] / bump通知チャンネルを設定します。
x@bumpoff / bump通知をオフにします。
```
### スターボード
★以外でもできます。
```
x!sbhelp / starboardのヘルプを見る
x@sbon [閾値] {チャンネルID} / starboardを開始します。(同時に1つまで)
x@sboff / starboardを停止します。
```
### 翻訳サービス
```
x!gthelp / このヘルプを表示する。
x!gtdo [翻訳先] [文章(空白を使用可能)] / 翻訳します。
x@gton [翻訳先] {第2翻訳先}...(最大5) / チャンネルの全てのメッセージを翻訳します。(webhook、メッセージの削除が必要。)(1サーバー1チャンネルまで。)
x@gtoff / メッセージ翻訳をオフにします。
翻訳先は2文字の言語コードを使用してください。English - en / japanese - ja
```
### minecraft関連
```
x!mahelp / mcapiのヘルプを表示する
x!mauser [uuid/username] / ユーザー情報を取得します。
```
### Discordのデータ取得
```
x!dghelp / discogetのヘルプを表示する
x@dgon / メッセージリンク先のメッセージを取得します。
x@dgoff / メッセージリンク先のメッセージの取得をオフにします。
x!dguser [ユーザーID] / ユーザーの情報を取得します。
```
### VC作成機能
```
x!vchelp / VCのヘルプを表示
x@vcon [vcチャンネルID]/ VCチャンネル作成機能をオンにする(botにチャンネルの作成、ロールの管理権限が必要です。)
x@vcoff / VCチャンネル作成機能をオフにする
x!vcdel / 作成された自分のVCチャンネルを削除する
```
### メッセージピン留め機能
```
メッセージピン留めのヘルプ
x!pinhelp メッセージピン留めのヘルプ
x@pinon [内容] / 送信したチャンネルでピン留めを作成(または編集)する
x@pinoff / 送信したチャンネルでピン留めをオフにする
```
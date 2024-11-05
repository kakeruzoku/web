> [AD] **[ScratchAttachが使える無料Pythonサーバー](https://bot-hosting.net/?aff=1081114793106219108) - [使い方Note](https://note.com/kakeruzoku/n/n01b256a6a764)**

# Scratchattach
[Github](https://github.com/TimMcCool/scratchattach) / [公式サイト](https://scratchattach.tim1de.net/)

## Scratchattachって何?
[Timmccool](https://github.com/TimMcCool/)が作ったScratchのAPIラッパー

多機能でScratchのほぼ全ての操作ができる。

## つくったやつ
### LIVE PROJECT ID
[ここから](https://scratch.mit.edu/projects/1079411508)みれます。
![](https://uploads.scratch.mit.edu/get_image/project/1079411508_480x360.png)
Scratchの最新のプロジェクトIDが見れます。

## 自作ドキュメント(日本語版)
### 注意
不確実やバージョンアップによる変化なのがあるかもしれないため、不安な場合は常に[公式wiki](https://github.com/TimMcCool/scratchattach/wiki)を確認してください。また、この記事を使用したことによる損害等を保証しません。

### 注釈
- **limit** は取得する上限 (<400)
- **offset** は開始位置(スタート0)
- ScratchDBは**サ終**してます
- `sa.Project`を取得した時、`sa.PartialProject`がくる可能性があります。project.is_shared()を使って判定してください。
- 時間情報は **2000-01-01T00:00:00.000Z** 形式

### インストール
コマンドプロンプトで:
```
pip install -U scratchattach
```
### プロキシ
```py
from scratchattach.utils.requests import Requests
Requests.proxies = {} # dict形式でプロキシを設定
```
### サイトAPI
#### sa.BaseSiteComponent - APIベースクラス
多くのオブジェクトにはこのクラスが適用されています。
```python
BaseSiteComponent.update_function #更新するための関数(requests)
BaseSiteComponent.update_API #リクエスト先のURL
BaseSiteComponent._headers #ヘッダー情報
BaseSiteComponent._cookies #クッキー情報
BaseSiteComponent.update() #"429"|bool データを更新する True->成功 False->失敗 "429"->Too many requests
BaseSiteComponent._update_from_dict(data=データ(大体dict)) #APIから取得した生データから更新
BaseSiteComponent._assert_auth() #未ログインならエラー Unauthenticated を送出
#セッションとリンクされたオブジェクトを作成
BaseSiteComponent._make_linked_object(identificator_id=変数名, identificator=データ, Class=作成するオブジェクトクラス, NotFoundException=なかった時のエラー)
```
#### sa.session - ログイン(セッション)クラス
Scratchのログイン情報を管理します。
```py
#パスワードによるログイン
Session = sa.login("ユーザー名", "パスワード")
#セッションID[*1] によるログイン
Session = sa.login_by_id("セッションID", username="ユーザー名(None)", password="パスワード(None)")
#セッションコード(Scratchattach特有)によるログイン
Session = sa.login_by_session_string("セッションコード")

session.id #str ログインしているアカウントのセッションID
session.username #str ログインしているアカウントのユーザー名
session.xtoken #str ログインしているアカウントのXtoken (不明)
session.email #str ログインしているアカウントに登録されているメールアドレス
session.new_scratcher #bool new Scratcherか
session.mute_status #? コメントのミュート情報
session.banned #bool BANされているか
session.update() #更新
session.become_scratcher_invite() #Scratcherへの招待データ

#メッセージ
session.messages(limit=40, offset=0, date_limit=None, filter_by=None)
session.admin_messages() #Stからのメッセージ
session.clear_messages() #既読する
session.get_message_count() #メッセージカウント

#私の作品
projects = session.mystuff_projects("all", page=1, sort_by="", descending=True) #list[sa.Project]
studios = session.mystuff_studios("all", page=1, sort_by="", descending=True) #list[sa.Studio]


```
- *1 セッションIDの取得について

※ 開発者ツール(又は同等の機能)が必要です。

Chrome/Edge/Brave 等 Chromium プラウザは以下の通り。
1. Scratchでログインし、開発者ツール(F12 か fn + F12)を開きます。
1. Applicationタブ(Firefox/DuckDuckGoは "Storage"タブ) を開きます。
1. cookies→「scratchsessionsid」を探す。それがセッションID
#### sa.User - ユーザークラス
ユーザーを表します。
```py
user = session.connect_user("ユーザー名") #ユーザー名からユーザー取得
user = session.connect_user_by_id("ユーザーID") #*1 ユーザー名からユーザー取得
user = session.connect_linked_user()
user = sa.get_user("username")
find_username_from_id("ユーザーID") #str *1 ユーザーIDからユーザー名を取得

user.join_date #str Scratchに参加した日時
user.about_me #str 「私について」
user.wiwo #str 「私が取り組んでいること」
user.country #str 国
user.icon_url #str 90x90のユーザー画像データURL
user.id #int ユーザーID
user.scratchteam #bool Scratch teamか

user.update() #アップデート
user._assert_permission() #自分以外ならエラー(Unauthorized)を送出

user.message_count() #int ユーザーのメッセージ数
user.featured_data() #dict*2「注目のプロジェクト」欄
user.does_exist() #bool ユーザーが見れる(プロフィールページが消えていない)か
user.is_new_scratcher() #bool|None New scratcherか(Noneは取得エラー)

user.follower_count() #int フォロワーの数
user.following_count() #int フォロー中の数
user.project_count() #int プロジェクトの数
user.loves_count() #int 好きなプロジェクトの数
user.favorites_count() #int お気に入りのプロジェクトの数
user.studio_count() #int スタジオの数
user.studio_following_count() #int フォロー中のスタジオの数

user.follower_names(limit=40, offset=0) #list[str] フォロワーのリスト
user.following_names(limit=40, offset=0) #list[str] フォロー中のリスト
user.followers(limit=40, offset=0) #list[sa.User] フォロワーのリスト(ユーザーオブジェクト)
user.following(limit=40, offset=0) #list[sa.User] フォロー中のリスト(ユーザーオブジェクト)

user.projects(limit=40, offset=0) #list[sa.Project] 共有したプロジェクト
user.favorites(limit=40, offset=0) #list[sa.Project] お気に入りのプロジェクト
user.studios(limit=40, offset=0) #list[sa.Studio] 参加しているスタジオのリスト
user.viewed_projects(limit=24, offset=0) #list[sa.Project] *3
user.loves(limit=40, offset=0, get_full_project=「完全な」プロジェクトデータを取得するか(False)) #list[sa.Project] 好きなプロジェクトリスト(APIではない)

user.follow() #フォローする
user.unfollow() #フォロー解除する
user.is_following("ユーザー名") #bool ユーザーをフォローしているか
user.is_followed_by("ユーザー名") #bool ユーザーにフォローされているか

user.activity(limit=1000) #list[sa.Activity] アクティビティを取得
user.activity_html(limit=1000) #str アクティビティのhtml版を取得

user.comments(limit=20, page=1) #None|list[sa.Comments] コメントを取得
user.comment_by_id("コメントID") #sa.Comment コメントを「探す」

user.post_comment("内容") #Comment コメントを投稿
user.reply_comment("内容", parent_id="返信元コメント", commentee_id="メンション先ユーザーID") #Comment 返信する(post_commentと変わらない)
user.delete_comment(comment_id="comment_id") #*3 コメントを削除する
user.report_comment(comment_id="comment_id") #コメントを報告する

user.toggle_commenting() #*3 コメントのオンオフを切り替える
user.set_bio(text) #*3「私について」を編集
user.set_wiwo(text) #*3 「私が取り組んでいること」を編集
user.set_featured("project_id", label*4="") #「注目のプロジェクト」を編集
user.set_forum_signature(text) #フォーラムの署名を編集

user.ocular_status() #dict ocularのステータスを取得*5

#ScratchDBは稼働していません！
user.stats()
user.ranks() 
user.followers_over_time(segment=1, range=30)
```
- *1 - ユーザーメンションについて

ユーザーIDからの取得について自分のコメント欄にユーザーを「メンション」することで取得しています。スパムにつながるので連続実行は控えてください。(ユーザーID134755499までのユーザーリストは私にDMしてくれれば教えます)

- *2 「注目のプロジェクト」のdict形式について
```py
{
    "label":ラベル(str),
    "project":{
        "id":プロジェクトID(int),
        "author":製作者(str),
        "thumbnail_url":画像リンク(str),
        "title":タイトル(str)
    }
}
```
- *3 自分のアカウントのみ実行可能

自分ではなかったらエラー Unauthorized が送出されます。

- *4 ラベルリスト
`Featured` `ProjectFeatured` `Tutorial` `Work In Progress` `Remix This!` `My Favorite Things` `Why I Scratch`

- *5 ステータスのdict形式について
```py
{
  "_id": "ID",
  "name": "ユーザー名",
  "status": "ステータス",
  "color": "#000000",
  "meta": {
    "updated": "2000-01-01T00:00:00.000Z",
    "updatedBy": "更新した人"
  }
}
```

#### sa.Project / sa.PartialProject - プロジェクトクラス
sa.PartialProject … 「非共有」プロジェクト
```py
project = session.connect_project("project_id") #プロジェクトに接続
project = session.create_project(title="title") #プロジェクトを作成(300s/5プロジェクト の制限)
project = sa.get_project("project_id") #プロジェクトを取得

project.id  #int プロジェクトID
project.url  #str プロジェクトURL
project.title #str タイトル
project.author  #str 作者のユーザー名
project.comments_allowed  #bool コメントができるか
project.instructions #str 「使い方」欄
project.notes  #str 「メモとクレジット」欄
project.created  #str 作成日時
project.last_modified  #str 更新日時
project.share_date #str 共有日時
project.thumbnail_url #str サムネリンク
project.remix_parent #int リミックス「元(1つ前)」
project.remix_root #int リミックスの「大本」
project.loves  #int 好きな人の数
project.favorites #int お気に入りの人の数
project.remix_count  #int リミックス数
project.views  #int 閲覧数
project.project_token #str プロジェクトtoken

project.update()  #更新
project._assert_permission() #ユーザーが所有者でない場合(Unauthorized)を送出

project.author() #sa.User 作者のユーザーオブジェクト
project.create_remix() #sa.Project リミックスをする(300s/5プロジェクト) *1
project.moderation_status() #str プロジェクトステータス"safe"か"notsafe"(nfe)を返す jeffalo.netから
project.visibility() #dict 共有情報を取得?
project.is_shared() #共有されているか(再度確認) *1
project.load_description() #未共有の場合、「使い方」を取得(リミックスを使用する)*1

project.comments(limit=40, offset=0) #list[sa.Comment] コメントを取得
project.comment_by_id("comment_id") #sa.Comment IDからコメントを取得する
project.comment_replies(comment_id="comment_id", limit=40, offset=0) #list[sa.Comment] コメントの返信を取得する

project.post_comment("内容") #sa.Comment コメントする
project.reply_comment("内容", parent_id="返信元コメント", commentee_id="メンション先ユーザーID") #sa.Comment返信する
project.delete_comment(comment_id="コメントID") #コメントを消す*2
project.report_comment(comment_id="コメントID") #報告する

project.love() #好き
project.unlove() #好きを外す
project.favorite() #お気に入り
project.unfavorite() #お気に入りを外す
project.post_view() #見る(閲覧数をカウントする)

project.set_title("new title") #タイトル設定 *3
project.set_instructions("new instructions") #使い方を編集*3
project.set_notes("new notes and credits")  #メモとクレジットを編集*3
project.set_thumbnail(file="filename.png") #png画像をサムネイルとしてアップロード*3
project.share() #共有*2
project.unshare() #非共有*2
project.set_fields(fields_dict:dict , use_site_api=False) #プロジェクト情報を更新します。*3 *2

project.body() #sa.ProjectBody プロジェクトをダウンロードしてsa.ProjectBodyに変換
project.download(filename="project_name.sb3", dir="") #プロジェクトをダウンロードする
project.raw_json() #dict プロジェクトの生データをダウンロード
project.creator_agent() #str 作者のuseragentを取得

project.set_body(project_body) #sa.ProjectBodyのプロジェクトデータをアップデートする*3
project.set_json(json_data) #JSONデータからプロジェクトをアップロードする*3
project.upload_from_json(project_id) #他プロジェクトからコピーする*3

project.turn_off_commenting() #コメントオフ*2
project.turn_on_commenting() # コメントオン*2
project.toggle_commenting() # コメント切り替え*2

project.remixes(limit=None, offset=0) #list[sa.Project] リミックスされたプロジェクト 
project.studios(limit=None, offset=0) #list[sa.Studio] プロジェクトが入っているスタジオ
```
- *1 sa.PartialProjectでも実行可能

- *2 作者である必要があります。

- *3 フィールドのdictのkey一覧
```py
comments_allowed = bool #コメントができるか
shared = bool #共有するか(推測)
title = str #タイトル
instructions = str #使い方
description = str #メモとクレジット
```
#### sa.Studio - スタジオクラス
```py
studio = session.connect_studio("studio_id") #接続
studio = sa.get_studio("studio_id") #取得

studio.id #int ID
studio.title #str タイトル
studio.description #説明
studio.host_id #スタジオのオーナーのユーザーID
studio.open_to_all #bool 誰でもプロジェクトを追加できるか
studio.comments_allowed #bool コメントできるか
studio.image_url #str サムネイル画像URL
studio.created #str 作成時刻
studio.modified #str 最終更新
studio.follower_count #int フォロー中のユーザー
studio.manager_count #int マネージャー数
studio.project_count #int (<101) プロジェクト数

studio.update()  #更新

studio.follow() #フォロー
studio.unfollow() #フォローを外す

studio.comments(limit=40, offset=0)  #list[sa.Comment] コメントを取得
studio.comment_by_id(comment_id="コメントID") #sa.Comment IDからコメントを取得
studio.comment_replies(comment_id="コメントID", limit=40, offset=0) #list[sa.Comment] コメントの返信を取得

studio.post_comment("内容") #コメントを送る
studio.reply_comment("内容", parent_id="返信先親コメント", commentee_id="メンションユーザー") #返信する
studio.delete_comment(comment_id="comment_id") #コメントを削除 *2
studio.report_comment(comment_id="comment_id") #コメントを報告

studio.add_project("project_id") #プロジェクトを追加
studio.remove_project("project_id") #プロジェクトを削除 (自分の作成したプロジェクトは権限必要なし)*2

studio.set_description("new description") #説明を変更*1
studio.set_title("new title") #タイトルを変更 *1
studio.set_thumbnail(file="filename.png") #サムネイルを変更*1
studio.open_projects() #誰でも入れれるようにする*1
studio.close_projects() #キュレーター以上のみ入れれるようにする*1
studio.set_fields(fields_dict) #プロジェクト情報を更新します。*3 *2

studio.turn_off_commenting() #コメントを閉じる*2
studio.turn_on_commenting() #コメントを開く*2
studio.toggle_commenting() #コメントができるか変更する*2

studio.invite_curator("username") #キュレーターに招待*2
studio.promote_curator("username") #キュレーターを昇格*2
studio.remove_curator("username") #キュレーターを削除*2
studio.accept_invite() #招待を受け取る
studio.leave() #ぬける
#↓ {'manager': bool, 'curator': bool, 'invited': bool, 'following': bool}
studio.your_role() #dict 自分のロールを見る

studio.projects(limit=40, offset=0) #list[sa.Project] プロジェクトリスト
studio.activity(limit=24, offset=0) #list[sa.Activity] アクティビティを取得
studio.curators(limit=24, offset=0) #list[sa.User] キュレーターリスト
studio.managers(limit=24, offset=0) #list[sa.User] マネージャーリスト
studio.host() #sa.User スタジオオーナーを取得

studio.transfer_ownership("new_owner", password="password") #オーナー権限を移す *1
```
- *1 オーナーである必要があります

- *2 マネージャーである必要があります

- *3 フィールドのdictのkey一覧
```py
description = str #説明
title = str #タイトル
```
#### sa.Comment - コメントクラス
comment.update() は使用できません。
```py
#取得する
comment = project.comment_by_id("id")
comment = user.comment_by_id("id")
comment = studio.comment_by_id("id")

comment.id #int コメントID
comment.parent_id #int|None 返信元コメントID
comment.cached_parent_comment #None|sa.Comment キャッシュされた返信元コメント
comment.commentee_id #メンションされたユーザーID
comment.content #内容
comment.datetime_created #str 投稿された日時
comment.author_name #str 投稿者
comment.author_id #int|None 投稿者ID
comment.written_by_scratchteam #bool Scratch teamが投稿したか
comment.reply_count #int 返信数
comment.source #str 投稿元タイプ("studio"/"profile"/"project")
comment.source_id #int|str 投稿元の場所のID

comment.author() #sa.User 送信ユーザー
comment.place() #sa.Project|sa.User|sa.Studio|None 送信元を返す
comment.parent_comment() #None|sa.Comment 返信元のコメント ないならNone
comment.replies(use_cache=True, limit=40, offset=0) #list[sa.Comment]|None 返信を取得 キャッシュを使うか選べる

comment.reply("内容") #sa.Commentand|None 返信する
comment.delete() #コメントを削除する
comment.report() #コメントを報告する
```
#### sa.Activity - メッセージ・履歴クラス
メッセージ・ユーザーの最近行ったこと・スタジオの履歴などいろいろ混じってよくわからんことになってます。引数があったりなかったり？？？
```py
activities = session.feed(limit=20, offset=0, date_limit=None) #list[sa.Activity] 自分のアクティビティ
activites = user.activity() #list[sa.Activity] #list[sa.Activity] ユーザーのアクティビティ 
activites = studio.activity() #list[sa.Activity] #list[sa.Activity] スタジオの履歴

activity.__dict__ #データをdictに変換
activity.id #アクティビティID
activity.type #アクティビティタイプ
("loveproject", "favoriteproject", "becomecurator", "followuser", "addcomment", and many others)
activity.actor_username #アクティビティを実行したユーザー名
activity.actor_id #アクティビティを実行したユーザーID
activity.datetime_created #アクティビティができた日時

# 「コメントされた」なら:
activity.comment_fragment #内容
activity.comment_obj_title #投稿された場所の名前?
activity.comment_obj_id #コメントされた場所
activity.comment_type #0=ptoject 1=user 2=studio?
activity.comment_id #コメントID
activity.commentee_username #メンションされたユーザー
...

# 「プロジェクトを好き」または「お気に入り」したなら:
activity.project_id #プロジェクトID
activity.title #プロジェクトタイトル

#スタジオをフォローしたなら:
activity.gallery_id #スタジオID
activity.title #スタジオタイトル
...

#フォローされたなら:
activity.followed_user_id #フォローしたユーザーID
activity.followed_username #フォローされたユーザー
```
#### sa.CloudActivity - クラウド履歴クラス
update() は使用できません
```py
cloud_activity.timestamp #float いつ変更されたか
cloud_activity.username #str 誰が実行したか
cloud_activity.type #str アクティビティのタイプ("set"/"create"/"del")
cloud_activity.var #str 変数名("☁ "なしの文字列)
cloud_activity.value #float|int もし「set」なら変更した中身
cloud_activity.cloud #取得した クラウド接続 オブジェクト
cloud_activity.load_log_data() #bool 詳細なデータを読み込む 返り値は成功したかどうか(update() のような動き)
cloud_activity.actor() #sa.User 送信者のユーザーオブジェクトを作成
cloud_activity.project() #sa.Project 送信元のプロジェクトオブジェクトを作成
```

#### sa.BackpackAsset - バックパッククラス
```py
backpack = session.backpack(limit=20, offset=0) #list[sa.BackpackAsset] バックアップに入っているものを返す
session.delete_from_backpack("アセットID") #中にあるものを削除

bpasset.id #int アセットID
bpasset.type #str 種類　(custome, scriptなど)
bpasset.mime #str フォーマットタイプ
bpasset.name #str 名前
bpasset.filename #str ファイル名
bpasset.thumbnail_url #str サムネイル画像URL
bpasset.download_url #str ダウンロードURL

bpasset.download(dir="") #ダウンロードする
bpasset.delete() #バックパックから削除する
```

#### sa.ForumTopic - トピッククラス
```py
topic = session.connect_topic("topic_id") #sa.ForumTopic トピックを取得
topic = sa.get_topic("topic_id") #sa.ForumTopic トピックを取得
topic_list = session.connect_topic_list(カテゴリーID, page=0) #*1 list[sa.ForumTopic] トピックスの一覧を取得する
topic_list = sa.get_topic_list(カテゴリーID, page=0) #*1 list[sa.ForumTopic] トピックスの一覧を取得する

topic.id #int
topic.title #str
topic.category_name #str
topic.last_updated #str 最終更新(月名 日, 形式 ex) "June 4,")
# get_topic_listやconnect_topic_listでのみ参照できます。
topic.reply_count #int 投稿数
topic.view_count #int 閲覧数

topic.update() #更新する(reply_count,view_countを除く)

topic.posts(page=1) #list[sa.ForumPost] 投稿リストを返す
topic.first_post() #sa.ForumPost 最初の投稿を返す
```

- *1 カテゴリーIDリスト
```
Welcome to Scratch - Scratchへようこそ
5 - Announcements - お知らせ
6 - New Scratchers - New Scratcherたち

Making Scratch Projects - Scratchのプロジェクトを作る
7 - Help with Scripts - スクリプトのヘルプ
8 - Show and Tell - 見せて伝える
9 - Project Ideas - プロジェクトのアイディア
10 - Collaboration - コラボ
11 - Requests - (他の人に)要望
60 - Project Save & Level Codes - プロジェクトの保存・セーブコード

About Scratch - Scratchについて
4 - Questions about Scratch - 質問
1 - Suggestions - (Scratchに)提案
3 - Bugs and Glitches - バグとグリッチ
31 - Advanced Topics - 行動な内容
32 - Connecting to the Physical World - 現実との接続
48 - Developing Scratch Extensions - 拡張機能の開発
49 - Open Source Projects - オープンソースプロジェクト

Interests Beyond Scratch - オフトピック
29 - Things I'm Making and Creating - 作っているもの
30 - Things I'm Reading and Playing - 読んだり遊んだりしているもの

55 - Africa - アフリカ
36 - Bahasa Indonesia - インドネシア語
33 - Català - カタルーニャ語
13 - Deutsch - ドイツ語
26 - Ελληνικά - ギリシャ語
14 - Español - スペイン語
59 - فارسی - ペルシア語
15 - Français - フランス語
22 - עברית - ヘブライ語
23 - 한국어 - 韓国語
21 - Italiano - イタリア語
19 - Nederlands - オランダ語
18 - 日本語
24 - Norsk - ノルウェー語
17 - Polski - ポーランド語
20 - Português - ポルトガル語
27 - Pусский - ロシア語
25 - Türkçe - トルコ語
16 - 中文 - 中国語
34 - Other Languages - その他の言語
28 - Translating Scratch - 翻訳
```

#### sa.ForumPost - 投稿クラス
```py
post.id #int
post.author_name #str 投稿者
post.author_avatar_url #str 投稿者のアイコンURL
post.posted #str 投稿された日 'Nov. 1, 2010 00:00:00' 形式
post.topic_id #int トピックID
post.topic_name #str トピック名
post.topic_category #str トピックカテゴリー名
post.topic_num_pages #int 合計トピックページ数
post.deleted #bool 削除されたか(常にFalse)
post.html_content #str htmlを返す
post.content #str 投稿内容
post.post_index #int 何ページ目か
# ----- ----- #
post.update()  #更新

post.topic() #sa.ForumTopic トピックを取得
post.author() #sa.User 作成したユーザーを取得

post.edit("内容") #編集する
post.ocular_reactions() #list[dict] リアクションを見る
```

#### sa.Classrooms - クラスオブジェクト
> ~~実は俺がぷるりくおくった！！！~~
```py
#終了したクラスは取得できません。
classroom = session.connect_classroom("クラスID")
classroom = session.connect_classroom_from_token("招待ID")

classroom = sa.get_classroom("クラスID")
classroom = sa.get_classroom_from_token("招待ID")

classroom.id #int
classroom.title #str クラス名
classroom.about_class #str クラスについて
classroom.working_on #str クラスがしていること
classroom.datetime #datetime.datetime 作成された時
classroom.author #sa.User 断片的なクラス所有者のユーザークラス。classroom.author.update()で更新する

classroom.update()  #更新

classroom.student_count() #int 生徒数
classroom.student_names(page=1) #list[str] 生徒のユーザー名
classroom.class_studio_count() #int クラススタジオ数
classroom.class_studio_ids(page=1) #list[int] クラススタジオのID
```

#### その他APIたち
```py
#トップページAPI
sa.get_news(limit=10, offset=0) #list[dict]
#{'id':int, 'stamp':str 時間, 'headline':str タイトル, 'url':str リンク, 'image':str 画像リンク, 'copy':str 本文}

sa.featured_data() #↓8つのデータを一括で読み込む

sa.featured_projects() 
sa.featured_studios()
sa.top_loved()
sa.top_remixed()
sa.newest_projects()
sa.design_studio_projects()
sa.curated_projects()
#↑ぜんぶlist[dict] 

session.feed(limit=20, offset=0) #list[sa.Activity]
session.loved_by_followed_users(limit=40, offset=0)

#検索
#プロジェクト検索 list[sa.Project]
session.search_projects(query="query", mode="trending", language="en", limit=40, offset=0)
sa.search_projects(query="query", mode="trending", language="en", limit=40, offset=0)

#スタジオ検索 list[sa.Studio]
session.search_studios(query="query", mode="trending", language="en", limit=40, offset=0)
sa.search_studios(query="query", mode="trending", language="en", limit=40, offset=0) # Search studios without logging in. Methods requiring authentication won't work on the returned objects

#傾向
#プロジェクト list[sa.Project]
session.explore_projects(query="query", mode="trending", language="en", limit=40, offset=0)
sa.explore_projects(query="query", mode="trending", language="en", limit=40, offset=0) # Search projects without logging in. Methods requiring authentication won't work on the returned objects

#スタジオ list[sa.Studio]
session.explore_studios(query="query", mode="trending", language="en", limit=40, offset=0)
sa.explore_studios(query="query", mode="trending", language="en", limit=40, offset=0) # Search studios without logging in. Methods requiring authentication won't work on the returned objects

#その他API
sa.get_csrf_token() #str scratchcsrftokenを取得

sa.get_health() #scratchステータス
sa.get_total_project_count() #総プロジェクト数(timeoutします)
sa.check_username("username") #ユーザー名が使えるか
sa.check_password("password") #パスワードが使えるか

#エイプリルフール？
sa.aprilfools_get_counter()
sa.aprilfools_increment_counter()
```

### クラウド変数/イベント
turbowarpクラウドは有効な User-Agent ヘッダーを提供する必要があります。これは多くの場合に`purpose="接続する理由", contact="連絡先"`引数を使用して設定できます。
>Bots must provide a valid User-Agent header in their connection. That includes contact information (such as a Scratch profile link, email address, GitHub issue page, etc.) and the name and version of the cloud variable library being used (if applicable). Exact syntax does not matter; just needs to be human readable

>ボットは、接続時に有効な User-Agent ヘッダーを提供する必要があります。これには、連絡先情報 (Scratch プロファイル リンクに、メール アドレス、GitHub の問題ページなど) と、使用されているクラウド変数ライブラリの名前とバージョン (該当する場合) が含まれます。正確な構文は重要ではなく、人間が読める形式である必要があります。
#### sa.BaseCloud
sa.ScratchCloud(Scratchクラウド変数)/sa.TwCloud(ターボワープクラウド変数)/sa.CustomCloud(その他)に適用されてます。
```py
cloud = session.connect_scratch_cloud("プロジェクトid") #
cloud = session.connect_tw_cloud("プロジェクトid", purpose="接続する理由", contact="連絡先")
cloud = sa.get_scratch_cloud("プロジェクトid")
cloud = sa.get_tw_cloud("プロジェクトid")

cloud.set_var("変数名", "値") #☁ なしの変数名
cloud.set_vars({
    "変数名" : "値",
    "変数名" : "値",
    ...
}, intelligent_waits=bool(True)) #一度に変更する intelligent_waits=Falseでレート制限を無視する

value = cloud.get_var("変数名") #float|None 変数を取得
variables = cloud.get_all_vars() #dict 全ての変数を取得
logs = cloud.logs() #list[sa.CloudActivity] 履歴を表示

cloud.reconnect() #再接続
cloud.disconnect() #切断
```
#### 独自のクラウドクラス
[詳細はこちら](https://scratchattach.readthedocs.io/en/latest/scratchattach.html#scratchattach.cloud._base.BaseCloud)
```py
#sa.CustomCloud を使用する
cloud = sa.CustomCloud(
    project_id = "プロジェクトID", cloud_host = "wss://...", username = "ユーザー名", length_limit = 10000, allow_non_numeric = False, _session = None, header = None, cookie = None, origin = None, print_connect_messages = False
)

#独自のクラスを作成する
class MyCloud(sa.BaseCloud):

    def __init__(self, *, project_id, cloud_host, ... ): # you can add more arguments
        super().__init__()
        
        #sa.BaseCloud から継承するすべてのクラスで設定する必要がある属性:
        self.project_id = project_id
        self.cloud_host = cloud_host

        #その他いろいろ...

    def set_var(self, variable, value):

        #その他のコードが必要であれば記述

        super().set_var(variable, value)
```

#### Encoding - エンコーダー
Scratch版は[こちら](https://github.com/TimMcCool/scratchattach/raw/main/assets/Encoder.sprite3)
```py
from scratchattach import Encoding

Encoding.encode("input") #str エンコードする
Encoding.decode("encoded") #str デコードする

sa.encoder.letters #list[str] エンコーダーが使用する文字のリストを返します。このリストのインデックスを設定して文字を追加/削除できます。このリストの文字のインデックスは、Scratchスプライトのコスチュームのインデックスに対応しています。
Encoding.replace_char("元の文字", "新しい文字") #上記のリストの文字を置き換えます。Scratch スプライトのカスタムの文字も置き換えることを忘れないでください。
```
#### sa.BaseEventHandler - ベースクラウドイベント
クラウドイベントはすべてこれを継承しています。
```py
@event.event(thread=bool) #イベントを登録
events.call_event(イベント名(str),引数(list)) #イベントを呼ぶ
events.start(thread=True, ignore_exceptions=True) #開始する
events.pause() #停止する
events.resume() #再開する
events.stop() #止める
```

#### sa.CloudEvents - クラウドイベント
クラウド イベントを使用すると、クラウド イベントにリアルタイムで対応できます。Scratcher が特定のプロジェクトでクラウド変数を設定 / 作成 / 削除すると、イベントが呼び出されます。

sa.BaseCloud から継承するすべてのクラスには、クラウド イベント ハンドラーを返す .events() メソッドがあります。

このクラスは、基本クラス `sa.BaseEventHandler` から継承されています。

使用方法:
```py
import scratchattach as sa

#Scratchで
session = sa.login("username", "password") #ログインする
cloud = session.connect_scratch_cloud("project_id") #接続する
#又はturbowarpで
cloud = sa.get_tw_cloud("project_id")
#又は独自のサーバーで
cloud = sa.CustomCloud(
    project_id = "プロジェクトID", cloud_host = "wss://...", username = "ユーザー名", length_limit = 10000, allow_non_numeric = False, _session = None, header = None, cookie = None, origin = None, print_connect_messages = False
)

events = cloud.events() #クラウドイベントを作成する


@events.event
def on_set(activity): #編集された時
    print(f"Variable {activity.var} was set to the value {activity.value} at {activity.timestamp}")
    # `activity` is a sa.CloudActivity object
    # To get the user who set the variable, call activity.load_log_data() which saves the username to the activity.username attribute

@events.event
def on_del(activity): #削除された時
    print(f"{activity.user} deleted variable {activity.var}")

@events.event
def on_create(activity): #作成された時
    print(f"{activity.user} created variable {activity.var}")

@events.event #準備が完了した時
def on_ready():
   print("Event listener ready!")

events.start()
```
`activity`はすべて`sa.CloudActivity`です。

#### sa.CloudRequests - クラウドリクエスト
Scratchのプロジェクトに[このファイル](https://github.com/TimMcCool/scratchattach/raw/main/assets/CloudRequests_Template.sb3)をアップロードする

単純なプログラム
```py
import scratchattach as sa

#Scratchで
session = sa.login("username", "password") #ログインする
cloud = session.connect_scratch_cloud("project_id") #接続する
#又はturbowarpで
cloud = sa.get_tw_cloud("project_id")
#又は独自のサーバーで
cloud = sa.CustomCloud(
    project_id = "プロジェクトID", cloud_host = "wss://...", username = "ユーザー名", length_limit = 10000, allow_non_numeric = False, _session = None, header = None, cookie = None, origin = None, print_connect_messages = False
)

client = cloud.requests() #クラウドリクエストを作成する

@client.request
def ping(): #"ping"がリクエストされた時
    print("Ping request received")
    return "pong" #Scratchに"pong"が返されます。 利用可能な値は「str(intやfloatも含む)」か「list[str]」です。

@client.event
def on_ready():
    print("Request handler is running")

client.start(thread=True) #thread=True または未設定でスレッドで実行されます。
```
ファイルを実行してから、Scratchで実行:
```diff
- [Send request | request name: (ping) and wait]
```
すると、リスト response に `pong` と表示されるはずです。

- **引数**
```py
@client.request
def message_count(argument1): #メッセージカウントがリクエストされた時
    print(f"Message count requested for user {argument1}")
    user = sa.get_user(argument1) #ユーザーを取得
    return user.message_count() #メッセージ数を返す
```
```diff
- [Send request | request name: (message_count) argument1: ((ユーザー名)) and wait]
+ または
- [Send request | request name: (((ping&)と(ユーザー名))) and wait]
```
複数の引数を入力したい場合は `&` で区切って送信してください。

- **リクエストの優先順位を変更する**
```py
#クライアントを作成する時に設定する
client = cloud.requests(respond_order="receive") #デフォルト 受信した順に返す
client = cloud.requests(respond_order="finish") #応答が完了した順に返す
client = cloud.requests(respond_order="priority") #優先順位を設定

#以下のように優先順位を設定する。(数が大きいほど優先順位が高い?)
@client.request(response_priority=1)
def hoge():
    hogehoge
```

- **様々なイベント**
```py
@client.event
def on_ready():
    print("リクエスト準備が完了しました。")

@client.event
def on_request(request): #リクエストが存在する
    request.request_name :str #リクエスト名
    request.requester :str #ユーザー名 = CloudActivity.user
    request.timestamp :str #タイムスタンプ = CloudActivity.timestamp
    request.arguments :list[str] #引数リスト
    request.request_id :str #リクエストID
    request.activity :CloudActivity #リクエストのCloudActivity

@client.event
def on_unknown_request(request): #リクエストが存在しない
    pass

@client.event
def on_disabled_request(request): #リクエストが無効
    pass

@client.event
def on_error(request,e:Exception): #エラー発生
    pass
""")
```

`リクエスト`→`関数があるか`(ない:→`on_unknown_request`)

(ある:`on_request`→`関数が有効か`(無効:`on_disabled_request`)

(有効:`関数実行`→`結果`(成功:`レスポンス返す`)(失敗:`on_error`)))

- **関数の編集**

作成
```py
@client.request(thread=True,enabled=True,name="リクエスト名")
# thread...スレッドで実行するか(True)
# enabled...有効か(True)
# name...リクエスト名を設定(None 通常は関数名が適用)
def hoge():
    hogehoge

client.add_request(function, thread=False, enabled=True, name="リクエスト名")
#@client.requestとまったく同じ

client.edit_request("リクエスト名", thread=False, enabled=True, new_name="リクエスト名", new_function=new_function) #リクエスト名から指定して編集
client.remove_request("リクエスト名") #リクエストを削除
```

- **様々な機能**

`client.send("内容")` サーバー→クライアント に送信
```py
client.get_requester() #str 送信者
client.get_timestamp() #実行時間(CloudActivity.timestamp)
client.get_exact_timestamp() #実行時間を再取得(CloudActivity.timestamp)
```
```py
client = cloud.requests(
    used_cloud_vars=["1", "2", "3", "4", "5", "6", "7", "8", "9"],
    no_packet_loss=True
)
#used_cloud_vars FROM_HOST_[ここ] のリスト
#no_packet_loss 毎回返答時に再接続する
```

#### sa.CloudStorage - クラウドストレージ

普通のdbにも使えそう

[ここ](https://github.com/TimMcCool/scratchattach/raw/main/assets/CloudStorage_Template.sb3)からプロジェクトを作成

- **データベース sa.Database**

```py
db = sa.Database("名前", json_file_path="path/to/ファイル名.json", save_interval=10)
# json_file_path - 保存先(データがあれば読み込む)
# save_interval - 保存頻度

db.save_to_json() #保存
db.keys() #キーを取得
db.get(key) #データを取得
db.set(key, value) #データを編集

@db.event
def on_save():
    print("保存されました。")

@db.event
def on_set(key, value):
    print(f"キー:{key} に {value} が保存された")
```

```py
import scratchattach as sa

db = sa.Database("名前", json_file_path="path/to/ファイル名.json", save_interval=10)

#Scratchで
session = sa.login("username", "password") #ログインする
cloud = session.connect_scratch_cloud("project_id") #接続する
#又はturbowarpで
cloud = sa.get_tw_cloud("project_id")
#又は独自のサーバーで
cloud = sa.CustomCloud(
    project_id = "プロジェクトID", cloud_host = "wss://...", username = "ユーザー名", length_limit = 10000, allow_non_numeric = False, _session = None, header = None, cookie = None, origin = None, print_connect_messages = False
)
storage = cloud.storage()

storage.add_database(db)
#いくらでも追加できる
storage.start()
```
利用できる関数:
```py
storage.get(データベース名, キー) #データを取得する
storage.set(データベース名, キー, データ) #データを保存する
storage.keys(データベース名) #データベースのキーリストを取得する
storage.databases() #データベースオブジェクトのリスト
storage.database_names() #データベース名リスト
storage.add_database(sa.database) #データベースを追加
storage.get_database(データベース名) #データベースを取得
storage.save() #すべて保存
```

#### sa.MessageEvents - メッセージイベント

メッセージでイベントを起こす
```py
import scratchattach as sa

#ユーザーから
user = sa.get_user("username") # Get the user you want to observe
events = user.message_events()
#自分のユーザーで
session = sa.login("username", "password")
events = session.connect_message_events()

@events.event
def on_count_change(old_count, new_count): #メッセージ数が変わったら
    print("メッセージ数が", old_count, "から", new_count, "になりました。")

@events.event #準備が完了したら
def on_ready():
   print("準備完了")

#自分のユーザーアカウントのみ動く
@events.event
def on_message(message:sa.Activity):
    pass

events.start()
```

#### as.Filterbot - コメントフィルター

自分の**プロフィールやプロジェクト**に投稿されるメッセージを制限(削除)する
```py
import scratchattach as sa

session = sa.login("username", "password")
filterbot = session.connect_filterbot(log_deletions=True)

filterbot.add_f4f_filter() # f4fフィルターを追加
filterbot.add_ads_filter() # 宣伝フィルターを追加
filterbot.add_spam_filter() # スパムフィルターを追加

filterbot.start() #開始する
```

自分でフィルターを作る

全ての引数に反応する(AND)必要があります。

どれか(OR)で削除したい場合は複数のフィルターを作成してください。
- `HardFilter` - 1つでも引っかかると削除
- `SoftFilter` - ポイント制で1.0を超すと削除
- `SpamFilter` - 過去5分間に投稿された場合の `HardFilter`


```py
filter = HardFilter("フィルター名", equals=None, contains=None, author_name=None, project_id=None, profile=None, case_sensitive=False)
filter = SoftFilter("フィルター名", score_value, equals=None, contains=None, author_name=None, project_id=None, profile=None, case_sensitive=False)
filter = SpamFilter("フィルター名", equals=None, contains=None, author_name=None, project_id=None, profile=None, case_sensitive=False)

"""
score_value 追加したいポイント
equals 完全一致
contains 部分一致
author_name ユーザー名
project_id プロジェクトID
profile ユーザー名
case_sensitive 大文字小文字を区別するか
"""
```

#### sa.MultiEventHandler - マルチイベントハンドラー
同時に複数のプロジェクトで通信したい場合などにしようできます。
全て同じクラスが望ましい

引数は自由に設定できます。
```py
from scratchattach import MultiEventHandler

# イベントを作成

session = sa.login("username", "password")
client1 = session.connect_cloud("project_id_1").requests()
client2 = session.connect_cloud("project_id_2").requests()
client3 = session.connect_cloud("project_id_3").requests()
client4 = session.connect_cloud("project_id_4").requests()

#作成
combined = MultiEventHandler(client1, client2, client3, client4)

#全てのイベントに登録する

@combined.event
def on_ready():
    print("Cloud requests are ready")

@combined.request
def your_request():
    ...

# すべて実行する
combined.start() #開始する
combined.pause() #停止する
combined.resume() #再開する
combined.stop() #止める
```

#### 独自のクラウド変数システム

作成中

### プロジェクトの編集

作成中

### 1.0 から 2.0 への移行

コード名の変更:
```py
#旧
import scratchattach as scratch3
session = scratch3.Session("session_id")
events = scratch3.CloudEvents("project_id")
client = scratch3.CloudRequests(conn)
value = scratch3.get_var("project_id", "variable")
dicts = sa.get_project("123").comments()
a_comment = project.get_comment()

#新
import scratchattach as sa #ドキュメントではscratch3からsaとして表記
session = sa.login_by_id("session_id") #専用関数の追加
events = sa.get_cloud("project_id").events()
client = session.connect_cloud("project_id").requests() #少しわかりやすくなった
value = sa.get_cloud("project_id").get_var("project_id") #クラウドオブジェクトから取得するように。
comments = sa.get_project("123").comments() #sa.Commentが返されるように
a_comment = project.comment_by_id()
```
- アクティビティは `sa.Activity` オブジェクトを返します
- プロジェクト、スタジオ情報は「プロジェクト・スタジオオブジェクト」が100%返されます。
- 属性がユーザー名を参照する場合`.user`ではなく常に`.username`となるように。
- `CloudConnection`と`TwCloudConnection`の代わりに、クラウド変数の設定を扱うだけでなく、APIにスパムをかけることなく非常に頻繁に変数を取得するための組み込み関数を持つ`Cloud`オブジェクトと`TwCloud`オブジェクトが存在するようになりました。
- `TwCloudEvents`と`WsCloudEvents`は`CloudEvents`に統合されました。 以前は`CloudEvents`と呼ばれていたものは`CloudLogEvents`になりました。
- `TwCloudRequests`と`CloudRequests`は`CloudRequests`に統合された。
- すべてのイベントはデフォルトで常にスレッドで実行されるようになりました。 これを無効にするには、`@events.event(thread=False)`を使用します。


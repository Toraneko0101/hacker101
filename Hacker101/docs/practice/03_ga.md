# GoogleAnalyticsの導入について

## 環境

??? info

    ### 開発環境について

    ```text
    ・React 18
    ・Next.js 14 (13以上必須)
    ・GoogleAnalyticsのアカウント
    ・GoogleTagManagerのアカウント
    ```

## 導入手順

??? success
    ### Next.js v13以上を使っていると仮定する

    ```jsx
    //page routerの場合、_app.jsxに以下の様に記載
    import { GoogleTagManager } from '@next/third-parties/google'

    export default function YourApp(){
        return(
        　<YourAppContents />
          <GoogleTagManager gtmId={GTM_ID} />
        )
    }

    //app routerの場合(工事中)
    ```

    ### GAのアカウントを作る

    ```text
    ・工事中
    ```

    ### GTM(Google Tag Manager)のアカウントを作る

    ```text
    1 無料で使用するを選択

    2 アカウント名、コンテナ名、プラットフォームを選択
    　
    　アカウント名：
        分かりやすい名前を付ける
      コンテナ名：
        イベントを取得したいドメインのドメイン名(https://は除く)を記載
      プラットフォーム：
        ウェブページなら「ウェブ」
    ```

    ### 変数を作成する

    ```text
    ・GTMには「なぜか」document.titleを計測する変数がない
    
    ・しかし、幸いにもユーザ定義変数は簡単に作成できる

    ・変数
      ⇒ユーザ定義変数
      ⇒新規
      ⇒変数のタイプ
      ⇒Javascript変数
      ⇒グローバル変数名
      ⇒document.title
      ⇒保存でOK(今回は変数名をDocument Titleにした)
    
    ・別個の変数を作りたい場合も以上のようにする
    ・複雑な変数を作りたい場合は、カスタムJavascriptが役立つ
    ```

    ### タグを作成する

    ```text
    GA4の表示回数欄に閲覧数を表示させたい場合
    
    <History Changeに基づくイベント>
    
    1つ目のタグ
      1 タグの種類は、Googleアナリティクス:GA4 イベント
      2 測定IDに、GA4のG-**********(自身のID)を記載
      3 イベントパラメータにpage_view : {{Document Title}}
      4 トリガーに「履歴の変更」を選ぶ

    2つ目のタグ
      1 タグの種類はGoogleタグ
      2 タグIDはGA4のG-**********を記載
      3 設定パラメータの構成パラメータに以下を記載
        send_page_view : true
        page_title : {{Document Title}}
        page_location: {{Page Path}}
    
    1つ目のタグに戻る
      1 タグの順序付けで、○○が発効した後にタグを配信にcheck
      2 クリーンアップタグに2つ目のタグを登録
      3 1つ目のタグが、配信できなかった場合や一時停止と
        なっている場合には配信しないでくださいにcheck
    
    <Window Loadedに基づくイベント>
      ・初回読み込みの時は、History Changeが発効しないので
        別個で設定する
    
      ・2つタグを設定し、紐づけるのは同じ
      ・トリガーには、ウィンドウの読み込みを選択する
    ```

    ### トリガーをカスタマイズする

    ```text
    ・一部のページだけのイベントを取得したい場合がある

    ・その場合は、トリガーの設定で、
      トリガーの発生場所を選択する
    
    ・たとえば「ウィンドウの読み込み」トリガーで
      以下の様にすれば、/news/3のようなページで
      リロードを行った際、イベントは発火しなくなる

      PagePath 正規表現に一致しない /news/.+

    ```

    ### 注意点

    ```text
    ・Historyは、ページ遷移後に発火する。
    ・つまり、現在、/news/1ページにいるとして、
      別ページに遷移した段階で、/news/1の閲覧回数がカウント
      される。
    
    ・/news/1ページを開いた状態で、タブを閉じた場合、
      このページの閲覧回数はカウントされない
    
    ・ページを開いた瞬間にカウントさせようとすると、
      リロード時に二重取得される
    
    ・トリガーには、All Pagesというあたかもページ遷移時に
      役立ちそうなものがあるが、これはリロード時、
      ドメインへの初回訪問時にしか発火しない(Next.js)
    
    ・これはNext.jsが共通レイアウトを用い、
      ページが完全に読み込まれることを防いでいるためである
      (そのため、現状All Pagesは使い物にならなかった)
      
    ```
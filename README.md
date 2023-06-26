# プロジェクト名

## 目次

- [外部ドキュメント一覧](#system-configuration)
- [システム構成の概要](#repository)
- [使用技術](#technology-used)
- [環境構築の手順](#environment-building)
- [ディレクトリ構造](#directory-design)
- [責務早見表](#component-design)
- [Git の運用について](#git)
- [開発する上での参考記事](#kizi)


## コーディングルール・API結合

- [コーディングルール](https://github.com/mine-co-ltd/mine-web/blob/main/DEVDESC.md)を一読した上で作業を開始してください
- [API結合のやり方](https://github.com/mine-co-ltd/mine-web/blob/main/APIDOC.md)

<h2 id="system-configuration">システム構成</h2>

- https://github.com/mine-co-ltd/mine-web/issues/59 を元に構成(かわしまさんと相談済み)

![mine_apiroute 001](https://user-images.githubusercontent.com/57901015/219270741-6fb807b2-30f0-4a81-b60d-12fcc614d666.jpeg)

※ あくまで簡易図なので都度アップデート
![mineシステム構成図 001](https://user-images.githubusercontent.com/57901015/213380732-78338a8e-a32a-4a20-a725-84cc217a000c.jpeg)

<h2 id="repository">各種リポジトリのリンク</h2>

- [サーバーサイド (API)](https://github.com/mine-co-ltd/mine-app)
- [モックデータ関連](https://github.com/mine-co-ltd/mine-spec)

<h2 id="technology-used">使用技術について</h2>

### nodeバージョン

- node v19.5.0
- npm v9.3.1

### フロント

- [Next.js](https://nextjs.org/blog/next-13) 12.3.4
- [React](https://ja.reactjs.org/) 18.0.26
- [typescript](https://www.typescriptlang.org/) 4.9.4
- [Storybook](https://storybook.js.org/)
- [Recoil](https://recoiljs.org/)

### スタイル

- [styled-components](https://styled-components.com/)

### ホスティング

- [Vercel](https://vercel.com/third-scope)

### テスト関連 (必要であれば)

- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
- [Jest](https://jestjs.io/ja/)

<h2 id="environment-building">環境構築</h2>

### VsCode 拡張機能

- [ES7+ React/Redux/React-Native snippets](https://marketplace.visualstudio.com/items?itemName=rodrigovallades.es7-react-js-snippets)
- [vscode-styled-components](https://github.com/styled-components/vscode-styled-components)

### ローカル環境の立ち上げまで

```
# 環境変数ファイルの作成 (.envの中の値は管理者に確認)
$ cp .env.example .env

# パッケージのインストール
$ npm install

# ローカル環境の立ち上げ
$ npm run dev
```

下記のローカル環境にアクセスできれば OK<br>
http://localhost:3000/<br>

```
# フロント用のモックサーバーの立ち上げ(dockerを起動して)
$ npm run mockapi
```

起動して下記にアクセスできればOK
http://localhost:8080/clinic/users

### npm install 時にエラーが出る場合

- node のバージョンが異なる可能性があるので下記を参考にバージョンを変えてください。

[node.js のバージョンを変更する](https://qiita.com/k3ntar0/items/322e668468716641aa5c)

### プルリク作成時

- フォーマッターによる整形と未使用変数があるとbuild エラーが出るように設定しています

```
# フォーマッターによるコードの自動整形
$ npm run lint.fix

# eslintのエラー箇所を自動修正
$ npm run lint
```

## Build時 

```
$ npm run build

$ npm start
```

### Storybook の起動 

```
$ npm run storybook
```

```
/components/Buttonの中にindex.tsx(エントリー), Button.tsx(メインのやつ), Button.story.tsx(ストーリーブック)
```

開発環境だと以下の URL で確認できます。<br>
http://localhost:6006/
`※Vercelデプロイは未実装`

## テスト (準備中)

```
$ npm run test
```

※ 特定のファイルのみテストを行う場合

```
$ npm run test PrimaryButton.test.tsx
```

<h2 id="directory-design">ディレクトリ構造</h2>

pages配下をルーティングとして[bulletproof-react](https://github.com/alan2207/bulletproof-react)に基づいて設計されています。

```
mine-web/
    ├─.github         # プルリクテンプレートや自動デプロイの設定(基本いじらない)
    ├─public/
    |   ├─images/      # 画像を格納
    ├─src/
    |   ├─pages/        # ルーティングファイル
    |     ├─todo  
    |        ├─index.tsx  # /todoのパスで表示される画面
    |   ├─components    # ボタンやフォームなど汎用的なコンポーネントを格納する
    |     ├─page.tsx    # ルートファイル(認証チェックロジックなどを入れる)
    |   ├─const         # 都道府県などの汎用的な変数を格納
    |   ├─features/     # 機能ごとにロジックやコンポーネント型定義やstore等を管理するディレクトリ(基本ここで作業)
    |     ├─todo/       # todoという機能を追加する場合(以下todoという機能を追加する想定の参考例)
    |        ├─api/     # todo関連のAPI通信ロジックを記述(.ts)
    |        ├─components  # todoで利用するコンポーネントを格納(TodoList, TodoPage等)
    |        ├─pages   # /pagesで読み込ませるページの実態
    |        ├─const  # todoで利用する汎用的な変数(stateの固定の初期値など)
    |        ├─hooks  # todoで利用するカスタムフック(API通信ロジックやデータのstateを管理する責務を持つ)
    |        ├─stores # todoで利用するグローバルなstateを管理 (Recoil)
    |        ├─types  # todoで利用する型定義
    ├─hooks             # 汎用的なカスタムフック (トークン等の扱いなど)
    ├─lib               # ライブラリや設定済みのインスタンスをエクスポートするファイルを設置(axiosやreactQueryなど)
    ├─models/           # API通信時のデータ整合性を合わせる型定義ファイル
    ├─repository/       # API通信ロジック(/model配下の型定義を必ず参照する)
    ├─pages/
    |   ├─api/          # NextAPIルート (基本いじらない)
    | utils/            # 汎用的な関数を格納(時間の整形ロジックなどを格納)
    ├─stores            # 汎用的グローバルstateを管理 (Recoil)
    ├─.eslintrc.json    # ESlintの設定ファイル(基本いじらない)
    ├─.gitignore        # githubの差分に含まないものを格納
    ├─.prettierrc       # コード整形ツールprettierrcの設定ファイル (基本いじらない)
    ├─Dockerfile        # dockerの設定ファイル(基本いじらない)
    ├─next.config.js    # Nextアプリの設定ファイル (基本いじらない)
    ├─package.json      # yarnの設定ファイル
    ├─tsconfig.json     # TypeScript設定ファイル
```

<h2 id="component-design">コンポーネントの責務早見表</h2>

各コンポーネントに関する責務は下記の通りです。([SOLID 原則を参照](https://zenn.dev/koki_tech/articles/361bb8f2278764))

|  | API通信 | グローバルstate (Recoil) | style |
| --- | --- | --- | --- |
| /pages | ○ (基本カスタムフックで) | × | × |
| /feature/pages | △  | ○ | △ |
| /feature/components | × | ○ | ○ |
| /components | × | × | ○ |

<h2 id="work-procedure">基本的な作業手順</h2>

### (例) /todo というページを作成したい場合

1. `pages/todo/index.tsx`でルーティングファイルを作成
2. API通信がある場合は1で作成したファイル内でカスタムフックを呼び出す方針
3. `src/features/todo/components/pages/Todo.tsx`に 1 で表示するページの実態を記述 (UIの表示と状態管理を責務にもつ)
4. ボタンや入力フォーム等の汎用的なコンポーネントが必要な場合は`src/components/`配下に作成し3で読み込み (UIの表示のみ責務にもつ)
5. 必要に応じてテキストの国際化を行う

### テキストの国際化
英語、及び日本語の2言語の対応をするためサブパスでの言語の振り分けをしています。(ex. `example.com` or `example.com/en/`)
- 翻訳 : [i18next](https://github.com/i18next/next-i18next)
- 翻訳設定参考 : [Next.js で国際化(i18n)対応サイトを作る](https://goodpatch-tech.hatenablog.com/entry/2021/07/20/190715)
- ルーティング : [公式リファレンス](https://nextjs.org/docs/advanced-features/i18n-routing)

**国際化手順**
1. `public/locales/(該当の地域)`以下にjson形式で翻訳ファイルを作成。ページごとで分ける。
2. 
```
// pages/examplePage/index.tsx

import { serverSideTranslations } from 'next-i18next/serverSideTranslations';


export const getStaticProps: GetStaticProps = async ({ locale = 'ja' }) => {
  return {
    props: {
      ...(await serverSideTranslations(locale, ['common'])) // 1で作成した使いたい翻訳ファイル名を記載
    }
  };
};
```
3. 
```
// features/examplePage/pages/(任意)

import { useTranslation } from 'next-i18next';

export const examplePage: React.FC<Props> = (props) => {
  const { t } = useTranslation('common'); //翻訳に使用したいファイル名

  return (
    <div>
      <h1>{t('title')}</h1>
    </div>
  );
};
```

## アイコンフォントについて

使用可能なフォントは`public/font-icon`内に格納しています。使用方法としてはFontAwesomeと同様です。

`fontsData`内に元データとフォント一覧が見れるHTMLファイルがあるので、参考にしてください。

### アイコンの追加
https://icomoon.io/ を使用してアイコンのフォント化を行っています。

新規にアイコンの追加をしたい場合は下記URLを参考に追加してください
[使用方法](https://www.aura-office.co.jp/blog/iconfont/)

<h2 id="git">Git 運用ルール</h2>

##　z-indexについて

variable.tsによる定義
--zindex-base: 0;
--zindex-header: 999;
--zindex-modal: calc(var(--zindex-header) + 1);

### ブランチについて

基本的に devlop ブランチがテスト環境で main を本番環境にします。

| ブランチ名 | 役割                               | 派生元  | マージ先        |
| ---------- | ---------------------------------- | ------- | --------------- |
| master     | 公開するものを置くブランチ         |         |                 |
| develop    | 開発中のものを置くブランチ         | master  | master          |
| release    | 次にリリースするものを置くブランチ | develop | develop, master |
| feature-\* | 新機能開発中に使うブランチ         | develop | develop         |
| hotfix-\*  | 公開中のもののバグ修正用ブランチ   | master  | develop, master |

### コミットメッセージの記法

```
fix：バグ修正
hotfix：クリティカルなバグ修正
add：新規（ファイル）機能追加
update：機能修正（バグではない）
change：仕様変更
clean：整理（リファクタリング等）
disable：無効化（コメントアウト等）
remove：削除（ファイル）
upgrade：バージョンアップ
revert：変更取り消し
```

<h2 id="kzi">参考記事</h2>

- [クリーンなReactプロジェクトの21のベストプラクティス](https://qiita.com/baby-degu/items/ea4eede60bbe9c63a348)
- [Next.jsディレクトリ構成・設計再考（featuresが何を解決するか）](https://zenn.dev/yodaka/articles/eca2d4bf552aeb)
- [【React/Vue.js】コンポーネント設計の（個人的）ベストプラクティス](https://zenn.dev/offers/articles/20220523-component-design-best-practice)
- [リーダブルコードの要点整理と活用法をまとめた](https://qiita.com/KNR109/items/3b14e2e8f89a33c0f959)
- [フロントエンド(React)の技術質問](https://qiita.com/KNR109/items/e13a5c5b8b461e846902)
- [Webフロントエンドパフォーマンスチューニング75選](https://qiita.com/nuko-suke/items/50ba4e35289e98d95753)




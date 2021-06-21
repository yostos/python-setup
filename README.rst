読んでね
======================================================================

Python開発環境の作り方
----------------------------------------------------------------------

まずは、Docフォルダ配下にある各環境ごとのPython環境のセットアップを読んで
準備をしてください。

以下は、上記を前提にプロジェクトを作る場合の手順です。

Python関連
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

開発環境の設定は以下の通りです。

.. code-block:: sh

   $ cd hogehoge                     <- gitで取得したルートディレクトリ
   $ pipenv --python 3.9.5           <- Pythonの仮想環境を作成します
                                     <- クリーンなPython環境が作成されます。
   $ pipenv shell                    <- 仮想環境に入ります。
   (hogehoge)$  
                                     <- 必ずプロンプトで仮想環境に入っていることを
                                     <- 確認してください。
   ( テストを実施 )

   (hogehoge)$ exit                  <- 仮想環境を抜けます。
   $                                 <- 仮想環境から通常の環境に

- 前提パッケージを追加したい場合は、pipコマンドでなく、pipenvコマンドで
  パッケージの導入を行ってください。導入したパッケージ名がPipfileに記録される
  ため、別の環境ですぐに環境の再構築ができます。
 
.. code-block:: sh

   (hogehoge)$ pipenv install hogehoge-package ← パッケージが導入され
                                                  Pipfileに記録されます。
   # 別の環境に再構築する場合は以下のコマンドで必要なパッケージが一括インストール
   # できます。
   (hogehoge)$ pipenv install


上記のコマンド実施後、temp.txtの内容を確認して requirementsフォルダ配下の
本番用、開発用、共通にパッケージを仕分けて、それぞれ適切なファイルに
新規追加したものを追記してください。

文書生成関連
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

本アセットの文書は Asciidocで記述されています。Asciidocは GitHubなどでは
レンダリングされて表示可能ですが、配布のためにHTML/PDFを生成する環境の
作成方法を記述しておきます。

HTML/PDFの生成には `asciidoctor` および `asciidoctor-pdf` を使用します。

以下が、その設定を行うためのコマンドの流れです。前提として、Ruby 2.5以上の
環境が導入されていること、 Rubyのパッケージ管理ツール Gemがインストール
されていることが前提となります。

.. code-block:: sh

    gem install asciidoctor             # asciidoctorのインストール
    gem install --pre asciidoctor-pdf   # asciidoctor-pdfのインストール
    gem install pygments.rb             # コードのシンタックスハイライト用
    gem install asciidoctor-pdf-cjk     # PDF変換のレイアウト崩れ対応
    gem install asciidoctor-diagram     # PlantUMLなどの図を使用
    gem install asciidoctor-pdf-cjk-kai_gen_gothic
                                        # ascii-pdf-cjk でインストールされる
                                        # NotoSansのサブセットに日本語がないため
    $ asciidoctor-pdf-cjk-kai_gen_gothic-install
                                        # フォントのインストール

ドキュメントの生成方法は以下の通りです。

.. warning::

    PygmentsはPython製のHighligherですが、Asciidoctorから呼ぶ際は
    Python2系でなければ駄目なようです。特にPygments自体をインストール
    しなくとも、上記のpygments.rb のインストールで動作するらしいです。

    が、わたしの環境はPythonもいろいろ入れすぎて、導入したパッケージが
    定かではないのでもしかするとエラーがでるかもしれません。

.. code-block:: sh

   # PDF生成
   $ asciidoctor-pdf  input.adoc -a pdf-fontsdir=./fonts -a pdf-style=KaiGenGothicJP-theme.yml
   # HTML生成 
   $ asciidoctor -r asciidoctor-pdf-cjk input.adoc 

修正方法
----------------------------------------------------------------------

本アセットのリポジトリの修正には、GitHub Flowをカスタマイズした
フローを選択します。ルールは以下の8つです。

1. masterブランチは常に公開可能なである。
2. 開発時には developブランチを共通のブランチとして使用する。
   ただしdevelopブランチには直接コミットせず、3の作業用ブランチを使用しPull Requestにより
   マージすること。
3. 修正時には作業用ブランチを作成する。その際developブランチがある場合はdevelop
   ブランチから、developブランチがない場合はmasterブランチから作成する
4. 作業用ブランチは定期的にpushする
5. pushする場合は、flake8 [#f1]_ はかけてスタイルはきれいにしましょう。
   細かいフォーマットなどあれこれ考えたくないので自動フォーマッター Blackを
   使用してスタイルを統一してからflake8をかけてください。
6. Pull Requestを活用する。
   ある程度作業が完了したら元にブランチにPull Requestを発行する。
7. プルリクエストが承認されたら元のブランチにマージする。
8. 最終的にmasterブランチにマージが完了したら、直ちにリリースする。

.. rubric:: 脚注

.. [#f1] pycodestyle + pyflakes + mccabe(複雑度チェッカー) 的なもの。 
   pip install flake8でインストール

TODO
=======

- Windows版ガイドを修正する


<div class="back"><a href="index.html">&laquo; 索引に戻る</a></div>

#アプリケーションを作成する

Now you've been given an overview of the syntax, lets explore actually structuring and creating CoffeeScript applications. This section aims to be useful to all CoffeeScript developers, novice or advanced. Indeed, it should be relevant to pure JavaScript developers too. 
ここまでで文法の概要について学びました。ここからは実際にCoffeeScriptアプリケーションを構築しましょう。この章の狙いは全てのCoffeeScript開発者にとって使いものになることです。初心者や熟練の技術者に限らずです。本当に、純粋なJavaScriptの開発者にとっても適切となるでしょう。

For some reason, when developers are building client side JavaScript applications, tried and tested patterns and conventions often fly out the window, and the end result is a spaghetti mess of un-maintainable coupled JavaScript. I can't stress enough how important application architecture is; if you're writing any JavaScript/CoffeeScript beyond simple form validation you should implement a form of application structure, such as [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller). 
幾つかの理由により、開発者がクライアントサイドJavaScriptアプリケーションを構築する時、パターンと規約を試し、テストするもののそれらはしばしば窓から飛んでいってしまい、最終的な結果はひどいスパゲッティで、保守不可能にこんがらがったJavaScriptになります。私は常にアプリケーションアーキテクチャがどれだけ重要かを強調しています。もしあなたがJavaScript/CoffeeScriptを単純なフォームの妥当性確認を越えて用いるのであれば、[MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)のようなアプリケーション構造を実装するべきです。

The secret to building maintainable large applications is not to build large applications. In other words, build a series of modular de-coupled components. Keep application logic as generic as possible, abstracting it out as appropriate. Lastly separate out your logic into views, models and controllers (MVC). Implementing MVC is beyond the scope of this chapter, for that I recommend you check out my book on [JavaScript Web Applications](http://oreilly.com/catalog/9781449307530/) and use a framework like [Backbone](http://documentcloud.github.com/backbone/) or [Spine](https://github.com/maccman/spine). Rather than that, here we're going to cover structuring applications using CommonJS modules.
保守可能な巨大アプリケーションを実装するための秘密は巨大なアプリケーションを作らないことです。つまり言い換えれば、連続したモジュールとして分割されたコンポーネントを作るのです。アプリケーションロジックは出来る限り一般的にし、適切に抽象化します。最後にロジックを分割し、ビュー、モデル、コントローラ(MVC)に分けます。MVCの実装はこの章の範囲を越えています。MVCについては私の本、[JavaScript Web Applications](http://oreilly.com/catalog/9781449307530/)をチェックして、[Backbone](http://documentcloud.github.com/backbone/) や [Spine](https://github.com/maccman/spine)のようなフレームワークを利用して下さい。ここではそうではなくCommonJSモジュールを用いてアプリケーションの構造化を説明します。

##構造とCommonJS

So what exactly are CommonJS modules? Well, If you've used [NodeJS](http://nodejs.org/) before you've used CommonJS modules, probably without realizing it. CommonJS modules were initially developed for writing server side JavaScript libraries, in an attempt to deal with loading, namespacing and scoping issues. They were a common format that would be compatible across all JavaScript implementations. The aim was that a library written for [Rhino](http://www.mozilla.org/rhino/) would work for Node. Eventually these ideas transitioned back to browsers, and now we have great libraries like [RequireJS](http://requirejs.org) and [Yabble](https://github.com/jbrantly/yabble) to use modules client-side. 
さて、CommonJSモジュールとは一体何でしょうか？もしあなたが[NodeJS](http://nodejs.org/)を以前に使ったことがあるのなら、あなたはCommonJSを、恐らく気付かづに、使ったことがあります。CommonJSモジュールは最初にサーバサイドJavaScriptライブラリを書くために開発されました。ローディング時間や名前空間、スコープの問題に向き合うためにです。CommonJSは全てのJavaScript実装の間で互換性を保持するようするための共通フォーマットでした。その狙いは[Rhino](http://www.mozilla.org/rhino/)向けのライブラリがNodeでも動くことでした。やがてこのアイデアはブラウザに向けられました。そして今私達は[RequireJS](http://requirejs.org) や [Yabble](https://github.com/jbrantly/yabble)のような素晴しいライブラリをクライアントサイドでモジュールを用いるために持っている訳です。

Practically speaking, modules ensure that your code is run in a local namespace (code encapsulation), that you can load other modules with the `require()` function, and expose module properties via `module.exports`. Let's dive into that in a bit more depth now. 
実際に、モジュールはあなたのコードがローカルネームスペース(コードカプセル化)の中で動くことを保障します。他のモジュールを`require()`関数で読むことができます。そして`module.exports`を通してモジュールプロパティを外部に公開することができます。それではそのことについてもう少し掘り下げてみましょう。

###ファイルを要求(require)する

You can load in other modules and libraries using `require()`. Simply pass a module name and, if it's in the load path, it'll return an object representing that module. For example:
他のモジュールやライブラリ内で`require()`を用いてロードすることが可能です。単純にモジュールの名前を渡すだけです。もしロードパスの中に存在すれば、そのモジュールを表すオブジェクトを返します。次の例をご覧下さい。

    User = require("models/user")
    
Synchronous require support is a contentious issue, but has mostly been resolved with the mainstream loader libraries and latest CommonJS [proposals](http://wiki.commonjs.org/wiki/Modules/AsynchronousDefinition). It may be something you'll have to look into if you decided to take a separate route than the one I'm advocating with Stitch below. 
同期的なrequireのサポートは議論の余地のある問題です。しかし多くの場合主流のローダーライブラリと最新のCommonJSの[提案](http://wiki.commonjs.org/wiki/Modules/AsynchronousDefinition)により解決されています。もしあなたが私の支持する以下のStichとの道とは別の道を選択することを決められたのであれば、それはあなたが良く理解しなければならないものとなるでしょう。

###プロパティを外部に公開する

By default, modules don't expose any properties so their contents are completely invisible to `require()` calls. If you want a particular property to be accessible from your module, you'll need to set it on `module.exports`:
デフォルトでは、モジュールはプロパティは1つも公開しません。中身は`require()`の呼出に対し、完全に不可視です。もしあるプロパティがあなたのモジュールからアクセス可能であって欲しいと思った場合、`module.exports`を用いて設定する必要があります。

    # random_module.js
    module.exports.myFineProperty = ->
      # 何か
    
Now, whenever this module is required then `myFineProperty` will be exposed:
これでこのモジュールがrequireされる度に`myFineProperty`は公開されます。

    myFineProperty = require("random_module").myFineProperty

##Stitch: 縫い上げよう

Formatting your code as CommonJS modules is all fine and dandy, but how do you actually get this working on the client in practice? Well, my method of choice is the rather unheard of [Stitch](https://github.com/sstephenson/stitch) library. Stitch is by Sam Stephenson, the mind behind [Prototype.js](http://www.prototypejs.org) amongst other things, and solves the module problem so elegantly it makes me want to dance for joy! Rather than try and dynamically resolve dependencies, Stitch simply bundles up all your JavaScript files into one, wrapping them in some CommonJS magic. Oh, and did I mention it'll compile your CoffeeScript, JS templates, [LESS CSS](http://lesscss.org) and [Sass](http://sass-lang.com) files too!

あなたのコードをCommonJSモジュールとして形式化することは全く問題がありません。しかし実際にどうやってこれをクライアントで実行するのでしょうか？私の選択した方法はあまり聞き慣れない[Stitch](https://github.com/sstephenson/stitch)ライブラリです。StichはSam Stephensonにより開発され、多くの中でも[Prototype.js](http://www.prototypejs.org)の影響を受け、モジュールの問題をとてもエレガントに解決するので私は喜びのダンスを踊りたくなります！依存性を動的に解決するのではなく、Stichは単純にあなたのJavaScriptファイル全てを1つに結合します。それらをCommonJSの魔法で包みます。あぁ、 そういえばこれはお伝えしましたでしょうか？CoffeeScriptやJSテンプレート、[LESS CSS](http://lesscss.org) それに [Sass](http://sass-lang.com)ファイルもコンパイルします！

First things first, you'll need to install [Node.js](http://nodejs.org/) and [npm](http://npmjs.org/) if you haven't already, we'll be using those throughout this chapter.
物事には順序があります。まず[Node.js](http://nodejs.org/) と [npm](http://npmjs.org/)をまだお持ちでないのならインストールする必要があります。私達はこれからこの章を通してそれらを使用します。
    
Now let's create our application structure. If you're using [Spine](https://github.com/maccman/spine), you can automate this with [Spine.App](http://github.com/maccman/spine.app), otherwise it's something you'll need to do manually. I usually have an `app` folder for all the application specific code, and a `lib` folder for general libraries. Then anything else, including static assets, goes in the `public` directory.
それでは私達のアプリケーションの構造を作りましょう。もし[Spine](https://github.com/maccman/spine)をお使いでしたら[Spine.App](http://github.com/maccman/spine.app)を用いてこれを自動化できます。そうでなければこれは手動で行わねばなりません。私は通常、`app`フォルダを全てのアプリケーション特有のコードのために持ちます。そして`lib`フォルダを一般的なライブラリのために作ります。他の物は全て静的なリソースも含めて`public`ディレクトリに入れましょう。

    app
    app/controllers
    app/views
    app/models
    app/lib
    lib
    public
    public/index.html

Now to actually boot up the Stitch server. Let's create a file called `index.coffee` and fill it with the following script:
それではStichサーバを実際に起動します。`index.coffee`という名のファイルを作り、次のスクリプトを入力してください。

<span class="csscript"></span>

    require("coffee-script")
    stitch  = require("stitch")
    express = require("express")
    argv    = process.argv.slice(2)
    
    package = stitch.createPackage(
      # Stitchに自動的に結合して欲しいパスを指定する
      paths: [ __dirname + "/app" ]
      
      # ベースとなるライブラリを指定する
      dependencies: [
        # __dirname + '/lib/jquery.js'
      ]
    )
    app = express.createServer()
    
    app.configure ->
      app.set "views", __dirname + "/views"
      app.use app.router
      app.use express.static(__dirname + "/public")
      app.get "/application.js", package.createServer()

    port = argv[0] or process.env.PORT or 9294
    console.log "Starting server on port: #{port}"
    app.listen port
    
You can see some dependencies listed: `coffee-script`, `stitch` and `express`. We need to create a `package.json` file, listing these dependencies so npm can pick them up. Our `./package.json` file will look like this:
いくつかの依存ライブラリ、`coffee-script`, `stich`, そして`express`が並べられているのが見えますでしょうか？ 我々は`package.json`ファイルを作成し、これらの依存性を記述せねばなりません。そうすることでnpmがそれらを取り上げることができます。我々の`./package.js`ファイルは次のようになります。

    {
      "name": "app",
      "version": "0.0.1",
      "dependencies": { 
        "coffee-script": "~1.1.2",
        "stitch": "~0.3.2",
        "express": "~2.5.0",
        "eco": "1.1.0-rc-1"
      }
    }
    
And let's install those dependencies with npm:
それではnpmを用いて依存ライブラリをインストールしましょう。

    npm install .
    npm install -g coffee-script
    
Rightio, we're almost there. Now run: 
OK.もうそこまで決ました。それでは次を実行します。

    coffee index.coffee
    
You'll hopefully have a Stitch server up and running. Let's go ahead and test it out by putting an `app.coffee` script in the `app` folder. This will be the file that'll bootstrap our application.
うまくいけばStitchサーバが立ち上がるでしょう。それでは先に進み`app`フォルダの中の`app.coffee`スクリプトを与えてテストしてみましょう。このファイルは私達のアプリケーションを起動するものになります。

<span class="csscript"></span>

    module.exports = App =
      init: ->
        # アプリケーションを起動する
        
Now let's create our main page `index.html` which, if we're building a single page app, will be the only page the user actually navigates to. This is a static asset, so it's located under the `public` directory.
次にメインページとなる`index.html`を作成します。もし我々がシングルページアプリを作るのならば、ユーザがアクセスする唯一のページとなるでしょう。これは静的なリソースですから`public`の下に置きます。
  
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset=utf-8>
      <title>Application</title>
      <!-- Stitchのメインファイルを必要とする -->
      <script src="/application.js" type="text/javascript" charset="utf-8"></script>
      <script type="text/javascript" charset="utf-8">
        document.addEventListener("DOMContentLoaded", function(){
          var App = require("app");
          App.init();
        }, false);
      </script>
    </head>
    <body>
    </body>
    </html>

When the page loads, our *DOMContentLoaded* event callback is requiring the `app.coffee` script (which is automatically compiled), and invoking our `init()` function. That's all there is to it, we've got CommonJS modules up and running, as well as a HTTP server and CoffeeScript compiler. If, say, we wanted to include a module, it's just a case of calling `require()`. Let's create a new class, `User`, and reference it from `app.coffee`:
ページがロードされた時、*DOMContentLoaded*イベントコールバックは`app.coffee`スクリプトをrequireします。(それは自動的にコンパイルされます。) 次に`init()`関数を実行します。あるべきものはそれだけです。私達は既にCommonJSモジュールを起動して実行しています。HTTPサーバとCoffeeScriptコンパイラもです。例えばもしモジュールをインクルードしたい場合、`require()`を呼ぶだけです。新しいクラス、`user`を作り、`app.coffee`から参照してみましょう。

<span class="csscript"></span>

    # app/models/user.coffee
    module.exports = class User
      constructor: (@name) ->
      
    # app/app.coffee
    User = require("models/user")

##JavaScriptテンプレート

If you're moving logic to the client side, then you'll definitely need some sort of templating library. JavaScript templating is very similar to templates on the server, such as Ruby's ERB or Python's text interpolation, expect of course it runs client side. There are a whole host of templating libraries out there, so I encourage you to do some research and check them out. By default, Stitch comes with support for [Eco](https://github.com/sstephenson/eco) templates baked right in. 
ロジックをクライアントサイドに動かしたい場合、あなたは間違いなく何らかのテンプレートライブラリを必要とするでしょう。JavaScriptテンプレートはサーバのテンプレートとほぼ同じものです。例えばRubyのERBやPythonのテキスト内挿法のようなものでもちろんクライアントサイドでも動作します。世の中には数多くのテンプレートライブラリがあります。そのため私はあなたにいくらかの調査と試用を行うことをお勧めします。Stitchはデフォルトでは[Eco](https://github.com/sstephenson/eco)テンプレートを内部に持っています。

JavaScript templates are very similar to server side ones. You have template tags interoperated with HTML, and during rendering those tags get evaluated and replaced. The great thing about [Eco](https://github.com/sstephenson/eco) templates, is they're actually written in CoffeeScript. 
JavaScriptテンプレートはサーバサイドのそれと似たようなものです。HTMLの中にテンプレートタグを挿入し、描画の間にそれらのタグが評価され置換されます。[Eco](https://github.com/sstephenson/eco)テンプレートの優れている点はCoffeeScriptで記述されていることです。

Here's an example:
例を見てみましょう

    <% if @projects.length: %>
      <% for project in @projects: %>
        <a href="<%= project.url %>"><%= project.name %></a>
        <p><%= project.description %></p>
      <% end %>
    <% else: %>
      No projects
    <% end %>

As you can see, the syntax is remarkably straightforward. Just use `<%` tags for evaluating expressions, and `<%=` tags for printing them. A partial list of template tags is as follows:
ご覧のとおり、文法は際立って直接的です。ただ`<%`タグを使って式を評価し、`<%=`タグは表示のために用います。テンプレートタグの一部のリストは次のとおりです。
    
* `<% expression %>`  
  Evaluate a CoffeeScript expression without printing its return value.
  CoffeeScriptの式を評価する。その返り値はプリントしない。

* `<%= expression %>`  
  Evaluate a CoffeeScript expression, escape its return value, and print it.
  CoffeeScriptの式を評価する。その返り値をエスケープし、プリントする。

* `<%- expression %>`  
  Evaluate a CoffeeScript expression and print its return value without escaping it.
  CoffeeScriptの式を評価し、その返り値をエスケープせずにプリントする。

You can use any CoffeeScript expression inside the templating tags, but there's one thing to look out for. CoffeeScript is whitespace-sensitive, but your Eco templates aren't. Therefore, Eco template tags that begin an indented CoffeeScript block must be suffixed with a colon. To indicate the end of an indented block, use the special tag `<% end %>`. For example:
任意のCoffeeScriptの式をテンプレートタグの中で使用することが可能ですが、気をつけることが1つだけあります。CoffeeScriptは空白に敏感です。しかしEcoテンプレートはそうではありません。従ってインデントされたCoffeeScriptブロックを始めるEcoテンプレートタグはコロンを追加せねばなりません。インデントブロックの終わりを示すには特別なタグ`<% end %>`を使用します。次の例をご覧下さい。

    <% if @project.isOnHold(): %>
      On Hold
    <% end %>
    
You don't need to write the `if` and `end` tags on separate lines:
`if`と`end`のタグを行を分けて書く必要はありません。

    <% if @project.isOnHold(): %> On Hold <% end %>

And you can use the single-line postfix form of `if` as you'd expect:
ご期待通り、1行で書くための後置形式も利用可能です。

    <%= "On Hold" if @project.isOnHold() %>

Now we've got a handle on the syntax, let's define an Eco template in `views/users/show.eco`:
さて文法については取っ掛かりを得ました。`views/users/show.eco`の中にEcoテンプレートを定義しましょう。
    
    <label>Name: <%= @name %></label>
    
Stitch will automatically compile our template and include it in `application.js`. Then, in our application's controllers we can require the template, like it was a module, and execute it passing any data required. 
Stichは自動的に私達のテンプレートをコンパイルして`application.js`に含めてしまいます。そして私達のアプリケーションのコントローラではテンプレートを要求することがモジュールのように可能で、必要なデータを渡して実行することができます。
    
<span class="csscript"></span>

    require("views/users/show")(new User("Brian"))
    
Our `app.coffee` file should now look like this, rendering the template and appending it to the page when the document loads:
私達の`app.coffee`ファイルは以下のようになっているはずです。テンプレートを描画し、ドキュメントがロードされた時にページに対して追加します。

<span class="csscript"></span>

    User = require("models/user")

    App =
      init: ->
        template = require("views/users/show")
        view     = template(new User("Brian"))

        # 明かにこれはjQueryを用いて綺麗にできるでしょう
        element = document.createElement("div")
        element.innerHTML = view
        document.body.appendChild(element)
    
    module.exports = App
    
[アプリケーション](http://localhost:9294/)を開いて試してみてください。願わくばこのチュートリアルがクライアントサイドのCoffeeScriptアプリケーションをどう構造化するについて良いアイデアを与えていることを期待します。次のステップとして、私はクライアントサイドフレームワークをチェックすることをお勧めします。例えば[Backbone](http://documentcloud.github.com/backbone/) or [Spine](http://spinejs.com)などです。それらは基本的なMVC構造を与え、あなたをより面白い仕事へと解放します。
    
##Bonus - 30 second deployment with Heroku
##ボーナス - 30秒でHerokuへデプロイ

[Heroku](http://heroku.com/) is an incredibly awesome web host that manages all the servers and scaling for you, letting you get on with the exciting stuff (building awesome JavaScript applications). You'll need an account with Heroku for this tutorial to work, but the great news is that their basic plan is completely free. While traditionally a Ruby host, Heroku have recently released their Cedar stack, which includes Node support. 
[Heroku](http://heroku.com)は信じられないほど最高なWebホストで全てのサーバを管理し、スケールし、あなたにエキサイティングな物(素晴しいJavaScriptアプリケーションを構築すること)と共に乗り込むことを可能にします。このチュートリアルを行うにはHerokuのアカウントを必要とします。しかし良いニュースですがHerokuのベーシックプランは完全に無料です。元々はRubyのホストでしたが、Herokuは最近、Cedarスタックをリリースし、それにはNodeサポートを含んでいます。

Firstly we need to make a `Procfile`, which will inform Heroku about our application.
最初に`Procfile`を作ります。これはHerokuに我々のアプリケーションについて伝えます。

    echo "web: coffee index.coffee" > Procfile

Now, if you haven't already, you'll need to create a local git repository for your application. 
まだお済みでなければあなたのアプリケーションに対しローカルgitリポジトリを作成して下さい。

    git init
    git add .
    git commit -m "First commit"    
    
And now to deploy the application, we'll use the `heroku` gem (which you'll need to install if you haven't already).
それではアプリケーションをデプロイしましょう。`heroku`コマンドを使います。(まだインストールしていなければする必要があります。)

    heroku create myAppName --stack cedar
    git push heroku master
    heroku open
    
That's it! Seriously, that's all there is to it. Hosting Node applications has never been easier.
以上です！ 本当にこれだけなんです。Nodeアプリケーションをホスティングすることはこれ以上には簡単にならないでしょう。

##追加のライブラリ

[Stitch](https://github.com/sstephenson/stitch) and [Eco](https://github.com/sstephenson/eco) aren't the only libraries you can use for creating CoffeeScript & Node applications, there are a variety of alternatives.
[Stitch](https://github.com/sstephenson/stitch) と [Eco](https://github.com/sstephenson/eco)だけがCoffeeScriptとNodeのアプリケーションを作るのに利用可能なライブラリではありません。他にも各種あります。

For example, when it comes to templating, you can use [Mustache](http://mustache.github.com), [Jade](http://jade-lang.com) or write your HTML in pure CoffeeScript using [CoffeeKup](http://coffeekup.org).
例えばテンプレートに関しては[Mustache](http://mustache.github.com), [Jade](http://jade-lang.com) または純粋にCoffeeScriptを使ってHTMLを書く[CoffeeKup](http://coffeekup.org)もあります。

As for serving up application, [Hem](http://github.com/maccman/hem) is a great choice, supporting both CommonJS and NPM modules and integrating seamlessly with the CoffeeScript MVC framework [Spine](http://spinejs.com). [node-browsify](https://github.com/substack/node-browserify) is another similar project. Or if you want to go lower level with [express](http://expressjs.com/) integration, there's Trevor Burnham's [connect-assets](https://github.com/TrevorBurnham/connect-assets)
アプリケーションの提供に関しては[Hem](http://github.com/maccman/hem)は良い選択です。CommonJSとNPMモジュールの両方をサポートし、CoffeeScriptのMVCフレームワークである[Spine](http://spinejs.com)とシームレスに結合できます。[node-browsify](https://github.com/substack/node-browserify)も同様のプロジェクトです。もし[express](http://expressjs.com/)を用いて低レベルの結合をしたい場合にはTrevor Burnhamの [connect-assets](https://github.com/TrevorBurnham/connect-assets)もあります。

You can find a full list of CoffeeScript web framework plugins, on the [project's wiki](https://github.com/jashkenas/coffee-script/wiki/Web-framework-plugins).
CoffeeScriptのWebフレームワークプラグインの完全なリストは[project's wiki](https://github.com/jashkenas/coffee-script/wiki/Web-framework-plugins)にあります。

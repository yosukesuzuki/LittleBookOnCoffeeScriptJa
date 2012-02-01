#Automating CoffeeScript compilation
#自動的にCoffeeScriptをコンパイルする

An issue with CoffeeScript is that it puts another layer between you and JavaScript, and having to manually compile CoffeeScript files whenever they change quickly gets old. Fortunately CoffeeScript has some alternative forms of compilation which can make the development cycle somewhat smoother.
CoffeeScriptの問題はあなたとJavaScriptの間に他のレイヤーを増やすことです。CoffeeScriptのファイルが変更され、窟ｭな・ﾄしまう度に侍@でコンパイルをせねばなりません。幸いなことにCoffeeScriptはいくつかのコンパイル代替形態を持・ﾄおり、開発サイクルをいくらかスムーズにすらｷとが可能です。

As we covered in the first chapter, we can compile CoffeeScript files using the `coffee` executable:
最初の章で説明したとおり、CoffeeScriptは`coffee`コマンドを用いてコンパイル可能です。
    
    coffee --compile --output lib src
    
In fact in the example above, all the `.coffee` files in `src` will be compiled & their JavaScript outputted to the `lib` directory. Even calling that is a bit of a bore, so let's look into automating it.
上の例では全ての`src`ディレクトリ内の`.coffee`ファイルはコンパイルされ、`lib`ディレクトリに個々の出力が置かれます。これを呼ぶだけでもちょ・ﾆ面倒かｃﾊれません。自動化する方法を探しましょう。

##Cake

[Cake](http://jashkenas.github.com/coffee-script/#cake) is a super simple build system along the lines of [Make](http://www.gnu.org/software/make/) and [Rake](http://rake.rubyforge.org/). The library is bundled with the `coffee-script` npm package, and available via an executable called `cake`.
[Cake](http://jashkenas.github.com/coffee-script/#cake)はとてもシンプルなビルドシステムで[Make](http://www.gnu.org/software/make/) や [Rake](http://rake.rubyforge.org/)に倣・ﾄいます。このライブラリは`coffee-script`のnpmパッケージにバンドルされており`cake`という名前のコマンドで利用可能です。

You can define tasks using CoffeeScript in a file called `Cakefile`. Cake will pick these up, and can be invoked by running `cake [task] [options]` from within the directory. To print a list of all the tasks and options, just type `cake`.
`Cakefile`と呼ばれるファイルにCoffeeScriptを用いらｷとでタスクを定義できます。`cake [task] [options]`を同じディレクトリで踀sすらｷとにより、Cakeがそれらのタスクを取り純O、踀sさせらｷとが可能です。全てのタスクとオプションのリストを表示するにはただ`cake`と入力します。

Tasks are defined using the `task()` function, passing a name, optional description and callback function. For example, create a file called `Cakefile`, and two directories, `lib` and `src`. Add the following to the `Cakefile`:
タスクは`task()`関数を用いて定義します。名前と任意で詳細記述とコールバック関数を与えます。例として`Cakefile`という名のファイルを作り、`lib`と`src`という2つのディレクトリを作成してください。`Cakefile`には次の内容を追隠ｵます。

<span class="csscript"></span>

    fs = require 'fs'

    {print} = require 'sys'
    {spawn} = require 'child_process'

    build = (callback) ->
      coffee = spawn 'coffee', ['-c', '-o', 'lib', 'src']
      coffee.stderr.on 'data', (data) ->
        process.stderr.write data.toString()
      coffee.stdout.on 'data', (data) ->
        print data.toString()
      coffee.on 'exit', (code) ->
        callback?() if code is 0
    
    task 'build', 'Build lib/ from src/', ->
      build()
      
In the example above, we're defining a task called `build` that can be invoked by running: `cake build`. This runs the same command as the previous example, compiling all the CoffeeScript files in `src` to JavaScript in `lib`. You can now reference JavaScript files in the `lib` directory as per usual from your HTML:
上の例では`build`というタスクを定義しました。`cake build`で踀sできます。これは先の例と同じコマンドを踀sし`src`にあるCoffeeScriptファイルを全てJavaScriptにコンパイルし、`lib`に置きます。これでHTMLファイルから通常どおりに`lib`の中にあるJavaScriptファイルを参照できます。

<span class="csscript"></span>

    <!DOCTYPE html>
    <html>
    <head>
    <meta charset=utf-8>
    <script src="lib/app.js" type="text/javascript" charset="utf-8"></script>      
    </head>
    <body>
    </body>
    </html>

We're still having to manually run `cake build` whenever our CoffeeScript code changes, which is far from ideal. Luckily, the `coffee` command takes another option, `--watch`, which instructs it to watch a directory for changes and re-compiling as necessary. Let's define another task using that:
ここではまだ`cake build`をCoffeeScriptのコードを変更するたｄ・手で踀sせねばなりません。理想からは遠いです。幸運なことに、`coffee`コマンドは別のオプションがあります。`--watch`はコマンドにディレクトリに対して変更を見張るように指示します。そして必要な場合にはリコンパイルします。それを用いて別のタスクを定義しましょう。

<span class="csscript"></span>

     task 'watch', 'Watch src/ for changes', ->
        coffee = spawn 'coffee', ['-w', '-c', '-o', 'lib', 'src']
        coffee.stderr.on 'data', (data) ->
          process.stderr.write data.toString()
        coffee.stdout.on 'data', (data) ->
          print data.toString()

If one task relies on another, you can run other tasks using `invoke(name)`. Let's add a utility task to our `Cakefile` which is going to both open  `index.html` and start watching the source for changes.
ｃﾊあるタスクが別のタスクに依存する場合、別のタスクを`invoke(name)`を用いて踀sすらｷとが可能です。`Cakefile`にもう一つ便利なタスクを追隠ｵましょう。`index.html`を開きソースの変更の見張りを開始します。

<span class="csscript"></span>

    task 'open', 'Open index.html', ->
      # 最初に開いて、次に見張る
      spawn 'open', 'index.html'
      invoke 'watch'

You can also define options for your task using the `option()` function, which takes a short name, long name and description.
タスクには`option()`関数を用いてオプションを定義すらｷとが可能です。引数として短かい名前、長い名前、そして説明を渡せます。

<span class="csscript"></span>

    option '-o', '--output [DIR]', 'output dir'

    task 'build', 'Build lib/ from src/', ->
      # Now we have access to a `options` object
      coffee = spawn 'coffee', ['-c', '-o', options.output or 'lib', 'src']
      coffee.stderr.on 'data', (data) ->
        process.stderr.write data.toString()
      coffee.stdout.on 'data', (data) ->
        print data.toString()

As you can see, the task context now has access to an `options` object containing any data specified by the user. If we run `cake` without any other arguments, all the tasks and options will be listed.
ご覧のとおり、タスクコンテキストがユーザが指定した任意のデータを保持する`options`オブジェクトにアクセス可能となりました。

Cake's a great way of automating common tasks such as compiling CoffeeScript without going to the hassle of using bash or Makefiles. It's also worth taking a look at [Cake's source](http://jashkenas.github.com/coffee-script/documentation/docs/cake.html), a great example of CoffeeScript's expressiveness and beautifully documented alongside the code comments.
Cakeは、bashやMakeファイルを用いずにCoffeeScriptをコンパイルするような一般的なタスクを自動化する、素晴しい方法です。[Cakeのソース](http://jashkenas.github.com/coffee-script/documentation/docs/cake.html)を読むことにはとても価値があります。CoffeeScriptの表現力の素晴しい例です。コードのコメントが销sして美しくドキュメント化されています。

##サーバサイドサポート

Using Cake for CoffeeScript compilation is fine for static sites, but for dynamic sites we might as well integrate CoffeeScript compilation into the request/response cycle. Various integration solutions already exist for the popular backend languages and frameworks, such as [Rails](http://rubyonrails.org/) and [Django](https://www.djangoproject.com/). 
CakeをCoffeeScriptのコンパイルに用いるのは唇Iなサイトでは問題ありません。しかし動的なサイトではCoffeeScriptのコンパイルをリクエスト/レスポンスサイクルに統合せねばなりません。色々な統合ソリューションが人気の高いバックエンドの言語とフレームワークに対して既に存在します。例えば[Rails](http://rubyonrails.org/) や [Django](https://www.djangoproject.com/)です。

When it comes to Rails 3.1, CoffeeScript support comes via [Sprockets & the asset pipeline](https://github.com/sstephenson/sprockets). Add your CoffeeScript files under `app/assets/javascripts`, and Rails is smart enough to pre-compile them when they're requested. JavaScript & CoffeeScript files are concatenated and bundled using special comment directives, meaning you can fetch all of your application's JavaScript with one request. When it comes to production, Rails will write the compiled output to disk, ensuring it's cached and fast to serve. 
Rails3.1ではCoffeeScriptのサポートは[Sprockets & the asset pipeline](https://github.com/sstephenson/sprockets)を通して提供されます。CoffeeScriptファイルを`app/assets/javascripts`の下に追隠ｵてください。Railsは十分に賢くリクエストを受けたときに事前にコンパイルします。JavaScriptとCoffeeScriptのファイルは填ﾊなコメントの指示を用いて包まれ、連結されます。これは1つのリクエストで全てのアプリケーションのJavaScriptを誌・ｷらｷとが可能であらｷとを意味します。運用時には、Railsはコンパイル結果をディスクに記録します。キャッシュされ、高速な提供を保障します。

other ruby options include rack servers such as 37signal's [Pow](http://pow.cx/) and Joshua Peek's [Nack](http://josh.github.com/nack/), both highly recommended if your application doesn't need Rail's other features and associated overhead.
他のRubyの選択肢には[rack](http://rack.github.com/)サーバがあります。例えば37signalの[Pow](http://pow.cx/) や Joshua Peek の [Nack](http://josh.github.com/nack/)があります。両者共、ｃﾊあなたのアプリケーションがRailsの他の機能や関連するオーバーヘッドを必要としないのなら最高にお勧めです。

Django also has [support for CoffeeScript](http://pypi.python.org/pypi/django-coffeescript/) through special template tags. It works with both inline code and external files.
Djangoもまた[CoffeeScriptのサポート](http://pypi.python.org/pypi/django-coffeescript/)を填ﾊなテンプレートタグを通して行います。インラインコードでも外部ファイルでも利用可能です。

Both Ruby and Python pipe out to Node and the CoffeeScript lib behind the scenes when compiling CoffeeScript, so you'll need to have those installed during development. If you're using Node directly as a backend for your site, CoffeeScript integration is even simpler and you can use it for both the backend and frontend code. We're going to talk more about this in the next chapter, using [Stitch](https://github.com/sstephenson/stitch) to serve all our client-side CoffeeScript.
RubyとPythonは共にCoffeeScriptをコンパイルする時、NodeやCoffeeScriptのライブラリへパイプから出力します。そのためそれらを開発の間にインストールしなければなりません。ｃﾊNodeを直接、あなたのサイトのバックエンドとして使用している場合、CoffeeScriptの統合はよりシンプルで、バックエンドとフロントエンドコードの両方使用可能です。このことについては次の章でより詳しく説明します。[Stitch](https://github.com/sstephenson/stitch)を用いて全てのクライアントサイドCoffeeScriptを提供します。


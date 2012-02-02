<div class="back"><a href="index.html">&laquo; 索引に戻る</a></div>

#The Bad Parts: 悪い部分

JavaScript is a tricky beast, and knowing the parts that you should avoid is just as important as knowing about the parts you should use. As Sun Tzu says, "know your enemy", and that's exactly what we're going to do in the chapter, exploring the dark side of JavaScript and revealing all the lurking monsters ready to pounce on the unsuspecting developer. 
JavaScriptはトリッキーな猛獣です。あなた避けるべき部分を知ることは使うべき部分を知ることと同じくらい重要です。孫子は"己の敵を知れ"と言いました。それは完全に私達がこれからこの章で行おうとしていることです。JavaSriptの闇の側面を探検し、疑うことを知らない開発者を急襲するのに準備万端なモンスター達が潜んでいます。全てを陽の下に晒しましょう。

As I mentioned in the introduction, CoffeeScript's awesomeness lies not only in it's syntax, but in it's ability to fix some of JavaScript's warts. However, due to the fact that CoffeeScript statements have a direct translation into JavaScript, and don't run in a virtual machine or interpreter, the language is not a silver bullet to all of JavaScript's bugbears and there's still some issues you need to be aware about.
イントロで説明しましたとおり、CoffeeScriptの良さはその文法だけでなく、JavaScriptの欠点を直す能力にあります。しかしCoffeeScriptの文は直接JavaScriptに翻訳され、仮想機械やインタプリタ上で実行される訳ではないため、CoffeeScriptは全てのJavaScriptの恐怖に対する銀の弾丸ではなく、注意しなければならない問題がまだあります。

First, let's talk about what things the language does solve. 
最初にCoffeeScriptが何を解決するかを話しましょう。

##A JavaScript Subset
##JavaScriptの部分集合

CoffeeScript's syntax only covers a subset of JavaScript's, the famous *Good Parts*, so already there's less to fix. Let's take the `with` statement for example. This statement has for a long time been "considered harmful", and should be avoided. `with` was intended to provide a shorthand for writing recurring property lookups on objects. For example, instead of writing:
CoffeeScriptの文法はJavaScriptの部分集合をカバーするのみです。著名な*Good Parts*です。したがって既に直すものはほとんどありません。例として`with`文について考えましょう。この文は長い間"有害である(Considered harmful)"とされ、避けるべきものでした。`with`はオブジェクトに対するプロパティ操作の記述の繰返しに対する構文糖を提供することを狙っていました。次の例をご覧下さい。

    dataObj.users.alex.email = "info@eribium.org";
    
上は以下のように書けます。

    with(dataObj.users.alex) {
      email = "info@eribium.org";
    }
    
Setting aside the fact that we shouldn't have such a deep object in the first place, the syntax is quite clean. Except for one thing. It's damn confusing to the JavaScript interpreter - it doesn't know exactly what you're going to do in the `with` context, and forces the specified object to be searched first for all name lookups. 
実際に最初からそんな深いオブジェクトを持つべきでないのは置いておいて、この文法はとても簡潔です。ただし1つの例外がありました。これはとてもJavaScriptインタプリタにとって混乱の元だったのです。インタプリタはあなたが`with`の中で何をするつもりなのかわかりません。そこで全ての名前解決を最初に特定のオブジェクトに対して行うよう強制しました。

This really hurts performance and means the interpreter has to turn off all sorts of JIT optimizations. Additionally `with` statements can't be minified using tools like [uglify-js](https://github.com/mishoo/UglifyJS). They're also deprecated and removed from future JavaScript versions. All things considered, it's much better just to avoid using them, and CoffeeScript takes this a step further by eliminating them from it's syntax. In other words, using `with` in CoffeeScript will throw a syntax error. 
これは本当にパフォーマンスに悪影響を与え、インタプリタにはJIT最適化の類を全てオフにすることを強制しました。加えて`with`文は[uglify-js](https://github.com/mishoo/UglifyJS)のようなツールを用いて最小化を行なうことが不可能でした。これは将来のJavaScriptのバージョンでは非推奨となり、削除されます。考慮すべきことは、それらを使わないことを考えたほうがずっと良いことです。CoffeeScriptはさらに進めてそれらを文法から消してしまいました。つまりCoffeeScriptで`with`を使うとシンタックスエラーになります。

##大域変数

By default, your JavaScript programs run in a global scope, and by default any variables created are in that global scope. If you want to create a variable in the local scope, JavaScript requires explicitly indicating that fact using the `var` keyword. 
デフォルトではJavaScriptのプログラムはグローバルスコープで実行されます。そしてデフォルトでは全ての変数はグローバルスコープにて作成されます。もし変数をローカルスコープで作成したければJavaScriptでは`var`キーワードを用いてその意思を示さねばなりません。

    usersCount = 1;        // Global
    var groupsCount = 2;   // Global
                          
    (function(){              
      pagesCount = 3;      // Global
      var postsCount = 4;  // Local
    })()

This is a bit of an odd decision since the vast majority of the time you'll be creating local variables not global, so why not make that the default? As it stands, developers have to remember to put `var` statements before any variables they're initializing, or face weird bugs when variables accidentally conflict and overwrite each other.
これは少しおかしな決定です。なぜならほとんど多くの場合、あなたが作るのはローカル変数であり、グローバルではないからです。ですからそれをデフォルトにしてはどうでしょうか？現状では開発者は`var`文を全ての変数の前に、初期化時に置くことを覚えなければなりません。そうしなければ変数名が事故で衝突しお互いを上書きした時に、変なバグに直面することでしょう。

Luckily CoffeeScript comes to your rescue here by eliminating implicit global variable assignment entirely. In other words, the `var` keyword is reserved in CoffeeScript, and will trigger a syntax error if used. Local variables are created implicitly by default, and it's very difficult to create global variables without explicitly assigning them as properties on `window`.
幸運なことにCoffeeScriptがここではあなたの助けとなります。暗黙のグローバル変数宣言を完全に消しました。言い方を変えれば`var`キーワードはCoffeeScriptでは予約語ですが、もし使用するとシンタックスエラーとなります。ローカル変数が暗黙的にデフォルトで作成されます。明示的に`window`のプロパティとして割り当てない限り、グローバル変数を作ることはとても難しいです。

Let's have a look at an example of CoffeeScript's variable assignment:
CoffeeScriptの変数宣言の例を見てみましょう。

<span class="csscript"></span>

    outerScope = true
    do ->
      innerScope = true
      
上の例は以下のようにコンパイルされます。

    var outerScope;
    outerScope = true;
    (function() {
      var innerScope;
      return innerScope = true;
    })();
    
Notice how CoffeeScript initializes variables (using `var`) automatically in the context their first used. Whilst it's impossible to shadow outer variables, you can still refer to and access them. You need to watch out for this, be careful that you're not reusing the name of an external variable accidentally if you're writing a deeply nested function or class. For example, here we're accidentally overwriting the `package` variable in a Class function:
CoffeeScriptがどのように(`var`を用いて)変数を自動的にそれが最初に使用されたコンテキストで初期化するかご覧下さい。外部の変数を覆い隠すのは無理ですが、それらを参照し、アクセスすることは可能です。これには注意が必要です。深くネストする関数やクラスを書くばあいに、不注意で外部の変数の名前を再利用しないように気を付けてください。例えば次の例では不注意でClass関数の`package`変数を上書きしています。

<span class="csscript"></span>

    package = require('./package')
    
    class Hem
      build: ->
        # 外部変数を上書きしてる!!
        package = @hemPackage.compile()
        
      hemPackage: ->
        package.create()
        
Global variables are needed from time to time, and to create those you need to set them as properties on `window`:
グローバル変数は時には必要です。作成するには`window`のプロパティとして設定する必要があります。

<span class="csscript"></span>

      class window.Asset
        constructor: ->

By ensuring global variables are explicit, rather than implicit, CoffeeScript removes one of the major sources of bugs in JavaScript programs.
グローバル変数が暗黙的でなく明示的であることで、CoffeeScriptはJavaScriptプログラムのバグの原因の主な一つを消しています。

##セミコロン

JavaScript does not enforce the use of semicolons in source code, so it's possible to omit them. However, behind the scenes the JavaScript compiler still needs them, so the parser automatically inserts them whenever it encounters a parse error due to a missing semicolon. In other words, it'll try to evaluate a statement without semicolons and, if that fails, tries again using semicolons.
JavaScriptはセミコロンの使用をソースコード内で強制はしません。よって省略可能です。しかし、裏側ではJavaScriptコンパイラはそれを必要としています。そのためパーサはセミコロンが無いために起こるパースエラーが発生する度に、自動的にセミコロンを挿入しています。言い替えると文をセミコロン無しで評価し、もしそれが失敗したらセミコロンを用いて再評価を試します。

Unfortunately this is a tremendously bad idea, and can actually change the behavior of your code. Take the following example, seems valid JavaScript, right?
不幸なことに、これは途方もなく悪い考えでした。実際にあなたのコードの挙動を変えてしまいます。次の例を見てください。正しいJavaScriptに見えるでしょう？ でも本当に？

    function() {}
    (window.options || {}).property
    
Wrong, well at least according to the parser; it raises a syntax error. In case of a leading parenthesis, the parser will not insert a semicolon. The code gets transformed onto one line:
誤りです。少なくともパーサに従えばシンタックスエラーを出します。先の括弧に対し、パーサはセミコロンを挿入しません。コードは1行に変換されます。

    function() {}(window.options || {}).property

Now you can see the issue, and why the parser is complaining. When you're writing JavaScript, you should always include semicolons after statements. Fortunately CoffeeScript gets round all this hassle by not having semicolons in its syntax. Rather the semicolons are inserted automatically (at the right places) when the CoffeeScript is compiled down to JavaScript.
もう問題がおわかりになるでしょう。そしてなぜパーサが文句を言うのかも。JavaScriptを書くときに常に文の最後にセミコロンを置くべきです。幸いなことに、CoffeeScriptはこの問題に対しその文法にセミコロンを持たないことで対処しました。正確に言えばCoffeeScriptがJavaScriptにコンパイルされる時に、(正確な場所に)セミコロンが自動的に挿入されます。

##予約語

Certain keywords in JavaScript are reserved for future versions of JavaScript, such as `const`, `enum` and `class`. Using these as variable names in your JavaScript programs can unpredictable results; some browsers will cope with them just fine, and others will choke. CoffeeScript neatly sidesteps this issue, by detecting if you're using a reserved keyword, and escaping it if necessary.
いくつかのJavaScriptのキーワードは将来のJavaScriptのバージョンのために予約されています。例えば`const`や`enum`、`class`です。これらを変数名としてJavaScriptプログラムにて使用すると予測できない結果が起こり得ます。あるブラウザはこれに問題なく対処できますが、他のブラウザでは首を締めます。CoffeeScriptはこの問題をうまく回避します。予約語の使用を見つけると必要な場合にはエスケープします。

For example, let's say you were to use the reserved keyword `class` as a property on an object, your CoffeeScript might look like this:
例として`class`という予約語をオブジェクトのプロパティとして利用するとしましょう。CoffeeScriptは次のようになります。

<span class="csscript"></span>

    myObj = {
      delete: "I am a keyword!"
    }
    myObj.class = ->
    

The CoffeeScript parser notices you're using a reserved keyword, and quotes it for you:
CoffeeScriptのパーサはあなたが予約語を使用していることを見つけ括ってくれます。

    var myObj;
    myObj = {
      "delete": "I am a keyword!"
    };
    myObj["class"] = function() {};
    
##等価比較

The weak equality comparison in JavaScript has some confusing behavior and is often the source of confusing bugs. The example below is taken from [JavaScript Garden's equality section](http://bonsaiden.github.com/JavaScript-Garden/#types.equality) which delves into the issue in some depth. 
javascriptの弱い等価比較は混乱させる挙動を持ち、しばしばバグの温床となっています。下の例は[javascript garden's equality section](http://bonsaiden.github.com/javascript-garden/#types.equality)からの引用ですが、問題についてある深さまで掘り下げています。

<span class="csscript"></span>

    ""           ==   "0"           // false
    0            ==   ""            // true
    0            ==   "0"           // true
    false        ==   "false"       // false
    false        ==   "0"           // true
    false        ==   undefined     // false
    false        ==   null          // false
    null         ==   undefined     // true
    " \t\r\n"    ==   0             // true

the reason behind this behavior is that the weak equality coerces types automatically. i'm sure you'll agree this is all pretty ambiguous, and can lead to unexpected results and bugs. 
この挙動の理由は弱い等価比較は方を自動的に型変換を強制します。この結果はとても不明瞭で、予測できない結果とバグの原因になるという意見にあなたも同意してくれるでしょう。

The solution is to instead use the strict equality operator, which consists of three equal signs: `===`. It works exactly like the normal equality operator, but without any type coercion. It's recommended to always use the strict equality operator, and explicitly convert types if needs be.
解決方法は代わりに厳密な等価演算子を用いることです。それは3つの＝記号から成ります(`===`)。これは普通の等価演算子と全く同じように働きますが、型の強制変換を行いません。常に厳密な等価演算子を使い、必要な場合には明示的に型を変換することが推奨されています。
    
CoffeeScript solves this by simply replacing all weak comparisons with strict ones, in other words converting all `==` comparators into `===`. You can't do a a weak equality comparison in CoffeeScript, and you should explicitly convert types before comparing them if necessary.
CoffeeScriptではこれを単純に全ての弱い等価比較を厳密なものに置き換えることで解決します。つまり全ての`==`比較演算子を`===`に取り替えます。CoffeeScriptでは弱い等価比較は使えません。そしてもし必要であれば比較する前に明示的に型を変換しなければなりません。

This doesn't mean you can ignore type coercion in CoffeeScript completely though, especially when it comes to checking the 'truthfulness' of variables during flow control. Blank strings, `null`, `undefined` and the number `0` are all coerced to `false`
しかし、これはCoffeeScriptで常に型の強制変換を完璧に無視できるということではありません。特にフローコントロールにおける変数の真偽値のチェックにおいて顕著です。空文字列、`null`、`undefined`、それに数値の`0`は`false`に変換されます。

<span class="csscript"></span>

    alert("空配列")      unless [].length
    alert("空文字列")    unless ""
    alert("数値の0")     unless 0
    
If you want to explicitly check for `null` and `undefined`, then you can use CoffeeScript's existential operator:
もし明示的に`null`と`undefined`をチェックしたい場合には、CoffeeScriptの存在確認演算子を利用可能です。

<span class="csscript"></span>

    alert("これは呼ばれない") unless ""?
    
The `alert()` in the previous example won't be called, as the empty string isn't equal to `null`.
この例の`alert()`は呼ばれません。空文字列は`null`とは等しくないためです。

##関数定義

Oddly enough in JavaScript, functions can be defined after they're used. For example, the following runs absolutely fine, even though `wem` is defined after it's called:
JavaScriptでは十分に変なことに、関数が使用後に定義可能です。次の例をご覧ください。これは全く問題なく実行できます。`web`が呼ばれた後に定義されているのにです。

    wem();
    function wem() {}

The is because of function scope. Functions get hoisted before the programs execution and as such are available everywhere in the scope they were defined in, even if called before the actual definition in the source. The trouble is, hoisting behavior differs between browser; for example:
これは関数スコープのためです。関数はプログラムの実行前に引き上げられます。そのようなものとしてそれが定義されたスコープの中ではどこでも有効です。
    
    if (true) {
      function declaration() {
        return "first";
      }
    } else {
      function declaration() {
        return "second";
      }
    }
    declaration();
    
In some browsers such as Firefox, `declaration()` will return `"first"`, and in other browsers like Chrome it'll return `"second"`, even though it looks like the `else` statement is never run.
いくつかのブラウザ、例えばFirefoxでは`declaration()`は`"first"`を返します。そして他のブラウザ、例えばChromeでは例え`else`節が永久に実行されないように見えても`"second"`を返します。

If you want to know more about declarative functions, then you should read [Juriy Zaytsev's guide](http://kangax.github.com/nfe/), where he delves into the specifics. Suffice to say, they have fairly ambiguous behavior, and can lead to problems later down the road. All things considered, It's best to steer clear of them by using function expressions instead:
もし宣言的な関数についてより知りたいのであれば、[Juriy Zaytsevのガイド](http://kangax.github.com/nfe/)を読むべきです。彼は仕様について掘り下げました。それらはとても不明瞭な挙動を持ち、いつか後に問題の発端となるだろうと言うだけで十分でしょう。全てを考慮に入れて、それらの問題を解決するには代わりに関数式を用いるのが最良でしょう。

    var wem = function(){};
    wem();

CoffeeScript's approach to this is to remove declarative functions entirely, using only function expressions. 
CoffeeScriptのこの問題に対するアプローチは宣言的関数を全体的に取り除くことでした。関数式のみを用います。

##数値のプロパティを参照する

A flaw in JavaScript's parser means that the *dot notation* on numbers is interpreted as a floating point literal, rather than a property lookup. For example, the following JavaScript will cause a syntax error:
JavaScriptパーサの問題として数値に対する*ドット表記*がプロパティの参照でなく浮動小数点記述だと翻訳されることでしょう。次の例をご覧下さい。次のJavaScriptはシンタックスエラーとなります。

    5.toString();
    
JavaScript's parser is looking for another Number after the dot, and so raises an `Unexpected token` error when it encounters `toString()`. The solution to this is to either use parenthesis, or add an additional dot.
JavaScriptのパーサはドットの後ろに別の数値を探します。そして`toString()`に出くわした時に`Unexpected token`エラーを起こします。これに対する解決法は括弧を用いるか、追加のドットを足すことです。
    
    (5).toString();
    5..toString();
    
Fortunately CoffeeScript's parsers is clever enough to deal with this issue by using double dot notations automatically (as in the example above) whenever you access properties on Numbers.
幸い、CoffeeScriptのパーサはこの問題に対処するに十分に賢く、数値のプロパティにアクセスする場合全てにおいて、上の例に対しても自動的に、2つのドット表記を用います。

#直されていない部分

Whilst CoffeeScript goes some length to solving some of JavaScript's design flaws, it can only go so far. As I mentioned previously, CoffeeScript's strictly limited to static analysis by design, and doesn't do any runtime checking for performance reasons. CoffeeScript uses a straight source-to-source compiler, the idea being that every CoffeeScript statement results in a equivalent JavaScript statement. CoffeeScript doesn't provide an abstraction over any of JavaScript's keywords, such as `typeof`, and as such some design flaws in JavaScript's design also apply to CoffeeScript.
CoffeeScriptがJavaScriptの設計上の問題に対し、いくらかの解決法を提供しているにせよ、ここまでの程度になります。先にお伝えしたとおり、CoffeeScriptは設計上、静的解析に厳密に制限されています。実行時チェックはパフォーマンスを理由に全く行っていません。CoffeeScriptはソース・トゥ・ソースなコンパイラであり、そのアイデアは全てのCoffeeScriptの文は等価なJavaScriptの文に置き換わるです。CoffeeScriptは例えば`typeof`のようなJavaScriptのキーワードのどれ1つにも抽象化を与えず、そのようなものとして、JavaScriptの設計上の問題のいくつかはCoffeeScriptにもそのまま当て嵌ります。

In the previous sections we covered some design flaws in JavaScript that CoffeeScript fixes. Now let's talk about some of JavaScript's flaws that CoffeeScript can't fix.
先のセクションで、CoffeeScriptが直したJavaScriptの設計上の問題について説明しました。ここからはCoffeeScriptが直していないJavaScriptの問題について話しましょう。

##evalの使用

Whilst CoffeeScript removes some of JavaScript's foibles, other features are a necessary evil, you just need to be aware of their shortcomings. A case in point, is the `eval()` function. Whilst undoubtedly it has its uses, you should know about its drawbacks, and avoid it if possible. The `eval()` function will execute a string of JavaScript code in the local scope, and functions like `setTimeout()` and `setInterval()` can also both take a string as their first argument to be evaluated. 
CoffeeScriptはJavaScriptのいくつかの欠点を無くしましたが、他の機能は必要悪です。あなたはそれらの欠点について注意しなければなりません。該当する例は`eval()`関数です。疑いなくそれにはその用途があります。しかしあなたはその欠点も知る必要があります。そして可能なら防がなければなりません。`eval()`関数はJavaScriptコードの文字列をローカルスコープにて実行します。また`setTimeout()`や`setInterval()`といった関数もまた最初の引数として文字列を取り、評価します。

However, like `with`, `eval()` throws the compiler off track, and is a major performance hog. As the compiler has no idea what's inside until runtime, it can't perform any optimizations like inlining. Another concern is with security. If you give it dirty input, `eval` can easily open up your code for injection attacks. 99% of the time when you're using `eval`, there are better & safer alternatives (such as square brackets).
しかし`with`のように、`eval()`はコンパイラから手掛かりを失わせます。パフォーマンスを悪くする主な原因です。コンパイラには実行時に中身に何が入っているかは検討もつきません。インライン展開のような最適化を実行することも不可能です。もう1つの憂慮点はセキュリティです。もし精査していない入力を与えれば`eval`は簡単にあなたのコードをインジェクション攻撃に対し無力にします。あなたが`eval`を使用する場合の99%にはより良く、より安全な、(角括弧のような)代替法があります。

<span class="csscript"></span>

    # やってはいけません
    model = eval(modelName)
    
    # 代わりに角括弧を使いましょう
    model = window[modelName]
    
##typeofを使う

The `typeof` operator is probably the biggest design flaw of JavaScript, simply because it's basically completely broken. In fact, it really has only one use, checking to see if a value is `undefined`.
`typeof`演算子は恐らく最も大きなJavaScriptの設計上の問題でしょう。なぜならば基本的に完全に壊れているからです。実際に、それの用途は本当に1つしかありません。値が`undefined`であるかチェックすることです。

<span class="csscript"></span>

    typeof undefinedVar is "undefined"

For all other types of type checking, `typeof` fails rather miserably, returning inconsistent results depending on the browser and how instances were instantiated. This isn't something that CoffeeScript can help you either, since the language uses static analysis and has no runtime type checking. You're on your own here.
他のタイプの型チェックに関しては`typeof`はとても惨めに失敗します。ブラウザの種類とインスタンスがどのようにインスタンス化されたかにより異なった結果を返します。これはCoffeeScriptでもあなたを助けることができません。なぜならCoffeeScriptは静的解析のみを用いており、実行時の型チェックを持たないからです。ここで頼れるのはあなただけです。

To illustrate the problem, here's a table taken from [JavaScript Garden](http://bonsaiden.github.com/JavaScript-Garden/) which shows some of the major inconstancies in the keyword's type checking. 
問題をはっきりさせるために、[JavaScript Garden](http://bonsaiden.github.com/JavaScript-Garden/)から引用した表を用意しました。これはキーワードの型チェックにおける主な不安定さを表示します。
  
    値                  クラス     型
    -------------------------------------
    "foo"               String     string
    new String("foo")   String     object
    1.2                 Number     number
    new Number(1.2)     Number     object
    true                Boolean    boolean
    new Boolean(true)   Boolean    object
    new Date()          Date       object
    new Error()         Error      object
    [1,2,3]             Array      object
    new Array(1, 2, 3)  Array      object
    new Function("")    Function   function
    /abc/g              RegExp     object
    new RegExp("meow")  RegExp     object
    {}                  Object     object
    new Object()        Object     object
    
As you can see, depending on if you define a string with quotes or with the `String` class affects the result of `typeof`. Logically `typeof` should return `"string"` for both checks, but for the latter it returns `"object"`. Unfortunately the inconstancies only get worse from there. 
ご覧のとおり、文字列をクォートで括るか、`String`クラスで定義するかが`typeof`の結果に影響します。論理的には`typeof`は`"string"`を両者のチェックに対し返すべきです。しかし後者には`"object"`を返します。不幸なことに不安定さはここからさに悪くなります。

So what can we use for type checking in JavaScript? Well, luckily `Object.prototype.toString()` comes to the rescue here. If we invoke that function in the context of a particular object, it'll return the correct type. All we need to do is massage the string it returns, so we end up with the sort of string `typeof` should be returning. Here's an example implementation ported from jQuery's `$.type`:
それではJavaScriptにおける型チェックには何を使えるのでしょうか？幸運なことに`Object.prototype.toString()`がここでは救いとなります。この関数を適切なオブジェクトのコンテキストで実行した場合、正しい型を返します。やらねばならぬ事は返り値をマッサージして、`typeof`が本来返すべきだった値のような文字列にすることです。次にコードはjQueryの`$.type`から移植した実装例です。

<span class="csscript"></span>

    type = do ->
      classToType = {}
      for name in "Boolean Number String Function Array Date RegExp Undefined Null".split(" ")
        classToType["[object " + name + "]"] = name.toLowerCase()
      
      (obj) ->
        strType = Object::toString.call(obj)
        classToType[strType] or "object"
    
    # 期待する型を返します
    type("")         # "string"
    type(new String) # "string"
    type([])         # "array"
    type(/\d/)       # "regexp"
    type(new Date)   # "date"
    type(true)       # "boolean"
    type(null)       # "null"
    type({})         # "object"
    
If you're checking to see if an variable has been defined, you'll still need to use `typeof` otherwise you'll get a `ReferenceError`.
もし変数が既に定義済みであるかを調べたいのならば、今でも`typeof`を使用する必要があります。そうしなければ`ReferenceError`を受け取ることになるでしょう。

<span class="csscript"></span>

    if typeof aVar isnt "undefined"
      objectType = type(aVar)
      
Or more succinctly with the existential operator:
またはより簡潔に存在確認演算子を用います。

    objectType = type(aVar?)
    
As an alternative to type checking, you can often use duck typing and the CoffeeScript existential operator together to eliminating the need to resolve an object's type. For example, let's say we're pushing a value onto an array. We could say that, as long as the 'array like' object implements `push()`, we should treat it like an array:
型チェックの代わりに、しばしばダックタイピングとCoffeeScriptの存在確認演算子を一緒に用いることでオブジェクトの型を解く必要を無くすことが可能です。例として、配列に値をプッシュするとしましょう。こう言うことができます。もし`array`であるかのようなオブジェクトが`push()`を実装しているのであれば、それを配列として扱うべきだろう。

<span class="csscript"></span>

    anArray?.push? aValue
    
If `anArray` is an object other than an array than the existential operator will ensure that `push()` is never called.
もし`anArray`が配列ではないオブジェクトだとしても、存在確認演算子が`push()`が呼ばれないことを保障します。
    
##instanceofの使用

JavaScript's `instanceof` keyword is nearly as broken as `typeof`. Ideally `instanceof` would compare the constructor of two object, returning a boolean if one was an instance of the other. However, in reality `instanceof` only works when comparing custom made objects. When it comes to comparing built-in types, it's as useless as `typeof`. 
JavaScriptの`instanceof`キーワードは`typeof`のようにほとんど壊れています。理想的には`instanceof`は2つのオブジェクトのコンストラクタを比較し、一方が他方のインスタンスであるかの真偽値を返すはずです。しかし実際には`instanceof`はカスタムメイドのオブジェクトを比較する場合のみ、うまく動作します。ビルトインタイプを比較する場合には`typeof`と同じように使いものになりません。

<span class="csscript"></span>

    new String("foo") instanceof String # true
    "foo" instanceof String             # false
    
Additionally, `instanceof` also doesn't work when comparing object from different frames in the browser. In fact, `instanceof` only returns a correct result for custom made objects, such as CoffeeScript classes.
さらに`instanceof`はまたブラウザの異なるフレームからのオブジェクトを比較する場合に動作しません。実際に`instanceof`はカスタムメイドのオブジェクトに対してのみ正しい結果を返します。例えばCoffeeScriptのクラスです。

<span class="csscript"></span>

    class Parent
    class Child extends Parent
    
    child = new Child
    child instanceof Child  # true
    child instanceof Parent # true
    
Make sure you only use it for your own objects or, even better, stick clear of it.
あなた自身が作成したオブジェクトに用いるか、より良くは、無かったことにしましょう。

##deleteの使用

The `delete` keyword can only safely be used for removing properties inside objects. 
`delete`キーワードはオブジェクトの中のプロパティを消去する場合にだけ安全に使用可能です。

<span class="csscript"></span>

    anObject = {one: 1, two: 2}
    delete anObject.one
    anObject.hasOwnProperty("one") # false

Any other use, such as deleting variables or function's won't work.
他のどんな使用も、例えば変数や関数を消す場合はうまくいきません。

<span class="csscript"></span>

    aVar = 1
    delete aVar
    typeof Var # "integer"

It's rather peculiar behavior, but there you have it. If you want to remove a reference to a variable, just assign it to `null` instead.
とても奇妙な挙動ですが、とにかく動きました。もし変数への参照を消したいのであれば、ただ代わりに`null`を代入しましょう。

<span class="csscript"></span>

    aVar = 1
    aVar = null

##parseIntの使用

JavaScript's `parseInt()` function can return unexpected results if you pass a string to it without informing it of the proper base. For example:
JavaScriptの`parseInt()`関数は適切な基数を与えずに文字列を与えると思いもよらない結果を返します。

    # 8を返します。10でなく!
    parseInt('010') is 8
    
Always pass a base to the function to make it work correctly:
常に基数を関数に渡して正しく動作するようにしましょう。

    # 10を基数として用いることで正しい結果に
    parseInt('010', 10) is 10

This isn't something CoffeeScript can do for you; you'll just have to remember to always specify a base when using `parseInt()`.
これはCoffeeScriptがどうにかしてあげられることではありません。`parseInt()`を用いる場合には必ず基数を指示することを覚えなければなりません。
    
##ストリクトモード

Strict mode is a new feature of ECMAScript 5 that allows you to run a JavaScript program or function in a *strict* context. This strict context throws more exceptions and warnings than the normal context, giving developers some indication when they're straying from best practices, writing un-optimizable code or making common mistakes. In other words, strict mode reduces bugs, increases security, improves performance and eliminates some difficult to use language features. What's not to like?
ストリクトモードはECMAScript 5の新しい機能で、JavaScriptプログラムや関数を*strict*なコンテキストで実行することを許可します。このストリクトなコンテキストではより多くの例外や警告を通常のコンテキストに比べて投げるようになります。開発者に対しベストプラクティスから離れた場合や、最適化不能なコードや一般的な間違いを犯した場合に目安のようなものを与えます。つまりストリクトモードはバグを減らし、セキュリティを向上し、パフォーマンスも向上し、言語機能を用いるにおいての難しさを解消します。誰か嫌いますか？

Strict mode is currently supported in the following browsers:
ストリクトモードは執筆時現在では次のブラウザでサポートされています。

* Chrome >= 13.0
* Safari >= 5.0
* Opera >= 12.0
* Firefox >= 4.0
* IE >= 10.0

Having said that, strict mode is completely backwards compatible with older browsers. Programs using it should run fine in either a strict or normal context. 
そうはいってもストリクトモードは完全に後方互換性が古いブラウザに対してあります。それを用いたプログラムはストリクトとノーマルの両方のコンテキストで問題なく動作するはずです。

###Strict mode changes
###ストリクトモードでの変化

Most of the changes strict mode introduces pertain to JavaScript's syntax:
ストリクトモードの変更のほとんどはJavaScriptの文法に関連するものです。

* Errors on duplicate property and function argument names
* プロパティと関数引数名の複製のエラー
* Errors on incorrect use of the `delete` operator
* 不正な`delete`演算子の使用上のエラー
* Access to `arguments.caller` & `arguments.callee` throws an error (for performance reasons)
* `arguments.caller`と`arguments.callee`へのアクセスはエラーを投げる。(パフォーマンス上の理由で)
* Using the `with` operator will raise a syntax error
* `with`演算子を使用するとシンタックスエラーを上げる
* Certain variables such as `undefined` are no longer writeable 
* `undefined`のような変数には最早書込不可である
* Introduces additional reserved keywords, such as `implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static`, and `yield`
* 追加の予約語が増える。例えば`implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static`, `yield`

However, strict mode also changes some runtime behavior:
しかし、ストリクトモードはまた実行時の挙動にも変更があります。

* Global variables are explicit (`var` always required). The global value of `this` is `undefined`.
* グローバル変数が明示的になり、`var`が常に要求される。`this`のグローバルでの値は`undefined`
* `eval` can't introduce new variables into the local context
* `eval`はローカルコンテキストに新しい変数を追加できない
* Function statements have to be defined before they're used (previously functions could be [defined anywhere](http://whereswalden.com/2011/01/24/new-es5-strict-mode-requirement-function-statements-not-at-top-level-of-a-program-or-function-are-prohibited/)).
* 関数は使用される前に定義されねばならない。(以前は[関数はどこで定義しても良かった](http://whereswalden.com/2011/01/24/new-es5-strict-mode-requirement-function-statements-not-at-top-level-of-a-program-or-function-are-prohibited/))。
* `arguments` is immutable
* `arguments`は変更不可(インミュータブル)

CoffeeScript already abides by a lot of strict mode's requirements, such as always using `var` when defining variables, but it's still very useful to enable strict mode in your CoffeeScript programs. Indeed, CoffeeScript is taking this a step further and in [future versions](https://github.com/jashkenas/coffee-script/issues/1547) will check a program's compliance to strict mode at compile time.
CoffeeScriptは既にストリクトモードの要求の多くを遵守している。例えば変数定義では常に`var`を用いる。しかしそれでもあなたのCoffeeScriptプログラムにおいてストリクトモードを許可するのはとても有益である。実際にCoffeeScriptはこれをより一歩進めて、[将来のバージョン](https://github.com/jashkenas/coffee-script/issues/1547)ではコンパイル時にストリクトモードに対する整合性をチェックするようになる。

###ストリクトモードの使用

All you need to do to enable strict checking is start your script or function with the following string:
ストリクトモードを使用するのに必要なことはスクリプトや関数を次の文字列で開始するだけである。

<span class="csscript"></span>
    
    ->
      "use strict"
    
      # ... あなたのコード ...
      
That's it, just the `'use strict'` string. Couldn't be simpler and it's completely backwards compatible. Let's take a look at strict mode in action. The following function will raise a syntax error in strict mode, but run fine in the usual mode:
これで全て。`'use strict'`で始めるだけだ。これ以上簡単にならないくらいであり、後方互換性も完璧にある。実際にストリクトモードを見てみよう。次の関数はシンタックスエラーをストリクトモードでは上げる。しかし通常のモードでは問題なく動作する。

<span class="csscript"></span>

    do ->
      "use strict"
      console.log(arguments.callee)
      
Strict mode has removed access to `arguments.caller` & `arguments.callee` as they're major performance hogs, and is now throwing syntax errors whenever they're used.
ストリクトモードでは`arguments.caller`と`arguments.callee`に対するアクセスを削除しました。それらはパフォーマンス悪化の主な原因であるためです。そしてそれらが使われる場合全てにおいてシンタックスエラーを上げます。

There's a particular gotcha you should look out for when using strict mode, namely creating global variables with `this`. The following example will throw a `TypeError` in strict mode, but run fine in a normal context, creating a global variable:
ストリクトモードを用いる場合に注意すべき心得があります。すなわちグローバル変数を作るときに`this`を用いる場合です。次の例は`TypeError`をストリクトモードでは投げます。しかし通常のコンテキストでは問題なく動作し、グローバル変数を作ります。

<span class="csscript"></span>

    do ->
      "use strict"
      class @Spine
      
The reason behind this disparity is that in strict mode `this` is `undefined`, whereas normally it refers to the `window` object. The solution to this is to explicitly set global variables on the `window` object.
この違いの裏にある理由はストリクトモードでは`this`が`undefined`えあるためです。通常では`window`オブジェクトを参照します。この問題の解決法は明示的に外部変数を`window`オブジェクトに対し設定することです。

<span class="csscript"></span>

    do ->
      "use strict"
      class window.Spine
      
Whilst I recommend enabling strict mode, but it's worth noting that strict mode doesn't enable any new features that aren't ready possible in JavaScript, and will actually slow down your code a bit by having the VM do more checks at runtime. You may want to develop with strict mode, and deploy to production without it.
ストリクトモードを許可することをお勧めしましたが、ストリクトモードは何も新しい機能をJavaScriptで使用可能にする訳ではありません。それに実際にはコードのパフォーマンスを少しだけ遅くします。VMにより多くのチェックを実行時にさせるためです。ストリクトモードで開発を行い、運用ではそれを外すのも良いでしょう。

##JavaScript Lint

[JavaScript Lint](http://www.javascriptlint.com/) is a JavaScript code quality tool, and running your programs through it is a great way of improving code quality and best practices. The project was based on a similar tool called [JSLint](http://www.jslint.com). Check out JSLint's site for a [great list](http://www.jslint.com/lint.html) of issues that it checks for, including global variables, missing semicolons and weak equality comparisons.
[JavaScript Lint](http://www.javascriptlint.com/)はJavaScriptコードの品質チェックツールです。あなたのプログラムをLintに通してみることはコード品質を向上させるために最良の方法であり、ベストプラクティスでもあります。このプロジェクトは似たようなツールである[JSLint](http://www.jslint.com)を基にしています。JSLintのサイトにあるチェック対象の問題の[リスト](http://www.jslint.com/lint.html)を見てみてください。グローバル変数やセミコロンの存在や弱い等価比較などが含まれます。

The good news is that CoffeeScript already 'lints' all of its output, so CoffeeScript generated JavaScript is already JavaScript Lint compatible. In fact, the `coffee` tool has support for a `--lint` option:
良いニュースとして、CoffeeScriptは既に全ての出力に対して`lints`をかけています。従ってCoffeeScriptで生成されたJavaScriptは既にJavaScript Lint互換です。実際に`coffee`ツールは`--lint`オプションをサポートしています。

    coffee --lint index.coffee
      index.coffee:	0 error(s), 0 warning(s)

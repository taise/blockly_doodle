
![Coding for Carrots](https://lh3.googleusercontent.com/1x7kj4C6ScjFbJg9PCpe9U_cXCYeVYlAo97Y-pbdUtiWqjrb1UgobmxvTVPdMmg_pZ1k4nfc5O-kC1U-BxmwyxJjHBQVjAwhy1BiGfE=s0)

https://www.google.com/doodles/celebrating-50-years-of-kids-coding

- Blocklyを使っていた
- 50周年記念なのでGoogleが気合い入れて作っているに違いない

> We programmed a little green turtle to move around and draw lines on a black screen. That programming language was called Logo.

[Logo (programming_language)](https://en.wikipedia.org/wiki/Logo_(programming_language))

![Turtle graphics](https://upload.wikimedia.org/wikipedia/commons/3/3d/Turtle-animation.gif)

> With today’s Doodle -- the first coding Doodle ever -- we celebrate fifty years of coding languages for kids by “Coding for Carrots.” In the interactive Doodle, you program and help a furry friend across 6 levels in a quest to gather its favorite food by snapping together coding blocks based on the Scratch programming language for kids.

コードを見てみる
`logo17.2.js` にBlocklyが入っていそう
=> あたりまえだけど、難読化されている

```js
    /*

     Visual Blocks Editor

     Copyright 2012 Google Inc.
     https://developers.google.com/blockly/
```

`logo17.2.js` にBlocklyが入っていそう
=> あたりまえだけど、難読化されている


### 難読化コードの解読

難読化されたコードのインデントをきれいにしてくれるツール
https://beautifier.io/


js難読化コードを解読するコツ

以下のようなものは難読化されない
- リテラルな文字列 (エラーメッセージなど)
- Objectのフィールド(ex: `this.bubble_`)

難読化前のライブラリを探したり、意味を推測したりする。


実際にやっていたこと。

```js
        Zh = function(a, b) {
            if (!eb(a.topBlocks_, b)) throw "Block not present in workspace's list of top-most blocks.";
        },
```

```js
Blockly.Workspace.prototype.removeTopBlock = function(block) {
  if (!Blockly.utils.arrayRemove(this.topBlocks_, block)) {
    throw Error('Block not present in workspace\'s list of top-most blocks.');
  }
};
```
https://github.com/google/blockly/blob/d471f5104539843cb2912c2749600c8a80f0cdbe/core/workspace.js

リテラルな文字列は難読化されても変換されないはず。
Blocklyに一致するものがあった。どうやらworkspace.jsらしい。

`Blockly.Workspace` == Wh

同様に追っていくと

`Blockly.WorkspaceSvg` == V
`goog.inherits` = w

`goog.array` とか使われていて、ClojureScriptの片鱗が見える。
`package.json` のdevDependenciesにも`google-closure-library`があったりする。`Promise`とか`Map`とかを使えるように入れてるっぽい。


とはいえ、一向にこんなのが並んでいる状況である...

```js
        Ca = function(a) {
            var b = typeof a;
            return "object" == b && null != a || "function" == b
        },
        Fa = function(a) {
            return a[Da] || (a[Da] = ++Ea)
        },
        Da = "closure_uid_" + (1E9 * Math.random() >>> 0)
        Ea = 0
```

すごくプリミティブな関数が並んでいる感じっぽい。
よくよく見てみると `"closure_uid_"` と書いてある。

( ﾟдﾟ)ﾊｯ!

`google-closure-library`

```js
/**
 * Name for unique ID property. Initialized in a way to help avoid collisions
 * with other closure JavaScript on the same page.
 * @type {string}
 * @private
 */
goog.UID_PROPERTY_ = 'closure_uid_' + ((Math.random() * 1e9) >>> 0);
```
https://github.com/google/closure-library/blob/261446f590b49aead02b750a4a7117a987751af4/closure/goog/base.js#L1598

なるほど、最初の部分はほとんど `closure/goog/base.js` の定義が並んでいたのか。
どうやら、ほとんどimportされた依存コードっぽい。

minimizeの仕組みを考えてみると、
依存元の定義が先に来て依存後のコードがあとに来るはず。
=> ずべてではないにせよ、logo17.2の実際のコードは後半の方にくるはず。

coreライブラリを眺めると `goog.require("hoge")`とか使ってimportしてる。
`google-closure-library` => `Blockly` => logo17.2 と推定できる。


7000行くらいsvgのアニメーションの定義が書かれている。
BIRD_LOOPSとかBLOCK06_FLOWERとか、そんなのが数百個くらい。

`#hpofcanvas` に描画している
![img](/images/hpofcanvas.png)

```js
$s = Ss(function() {
    return document.getElementById("hpofcanvas")
}),
```

この`$s`は、一見 `jQuery` に関係しそうだと思うけど、ただの変数で全く関係ない。
minimizeするときに、`Ys`, `Zs`, `$s`, `at`, `bt`... となっているだけの様子。



resources

`L1.json`

Lessonの設計が書かれている様子。
具体的には、SVGパーツの初期位置の定義。

- 23, 24, 25
  - 足元のBlock
- 10, ...
  - うさぎ (BUNNY_EAST_0)
  - `BUNNEY_*`
- 67
  - ニンジン

この67のニンジンが取得されたかどうかを判定しているコードが有るはず。

```js
if (
    lo && oo(lo) && jj(lo) && (
        67 == a.keyCode
            ? (Di(), pq(lo))
            : 88 == a.keyCode && (
               pq(lo), Di(), lo.dispose(2 != Yn, !0), ej && (C(Ui.highlightedPath_), delete Ui.highlightedPath_, ej = null)
            )
    ),
    86 == a.keyCode
)
nq && (ci(!0), qq(oq), ci(!1));
```



```js
N049_L3_BLOCK01_001: [5, 20, 20, 88, 46],
```

```js
N078_L3_LEAVES_001: [5, 20, 30250, 86, 78]
```

```js
var G = {
    BLOCK01_DECOR: [11, 20, 37050, 68.2, 34.9],
    BLOCK02_06_DECOR: [11, 20, 37105, 54.8, 27.8],
    //...
}
// ...
Ts = function(a) {
    var b = [],
        c;
    for (c in G) {
        var e = G[c];
        e[0] == a && b.push(e)
    }
    return b
},
// ...
Us = function(a) {
    var b = yh.getInstance();
    a = Ts(a);
    a = l(a);
    for (var c = a.next(); !c.done; c = a.next())
        if (!b.canvases_.has(c.value + ",1")) return !1;
    return !0
},
// ...
    var gh = function(a, b, c) {
            this.x = a;
            this.y = b;
            this.z = c
        };
    gh.prototype.scale = function(a) {
        this.x *= a;
        this.y *= a;
        this.z *= a;
        return this
    };
    gh.prototype.add = function(a, b, c) {
        this.x += a;
        this.y += b;
        this.z += c;
        return this
    };
    gh.prototype.set = function(a, b, c) {
        this.x = a;
        this.y = b;
        this.z = c;
        return this
    };
    gh.prototype.release = function() {
        0 > mh.indexOf(this) && mh.push(this)
    };
    gh.prototype.equals = function(a) {
        return this == a || !!a && this.x == a.x && this.y == a.y && this.z == a.z
    };


// ...
var D = function(a, b, c) {
        var e = mh.shift();
        return e ? e.set(a, b, c) : new gh(a, b, c)
    },
// ...
var E = function(a) {
    a = void 0 === a ? D(0, 0, 0) : a;
    Ed.call(this);
    this.worldPosition = D(0, 0, 0);
    this.position = a;
    this.initialPosition_ = hh(this.position);
    this.objectAnchor_ = null;
    this.opacity = this.scale = 1;
    this.renderTransform = new rh;
    this.renderOpacity = this.opacity;
    this.forceIsometric = !1;
    this.children = [];
    this.childrenChanged_ = !1;
    this.descendants_ = [];
    this.actionManager = new eh
};

// ...
var gt = yh.getInstance(),
ht = function(a) {
    E.call(this);
    this.spriteSheets_ = [];
    this.spritesToLoad_ = new Map;
    a = l(a);
    for (var b = a.next(); !b.done; b = a.next()) b = b.value, Us(b) || (this.spriteSheets_.push(b), this.spritesToLoad_.set(b, Ts(b)));
    this.spriteIndex_ = this.spriteSheetIndex_ = 0
};
```

`spriteSheets_`にSVGを描画しているっぽい。
`gh(a,b,c)` や `D(a, b, c)` は player(Rabbit) のactionをコントロールしているっぽい。

このままここを深堀りしても、時間がひたすら掛かりそうなのでBlocklyの部分にフォーカスしてみる。

### Blockly

HTML
![BlocklyWorkspace](/images/BlocklyWorkspace.png)

SVG描画
![BlocklyWorkspace HTML](/images/BlocklyWorkspaceHTML.png)

```js
H.logo17_move_forward = {
    json: {
        id: "logo17_move_forward",
        message0: "%1 %2",
        args0: [{
            type: "field_image",
            src: "/logos/2017/logo17/move_forward.svg",
            width: 40,
            height: 40
        }, {
            type: "input_value",
            name: "DURATION",
            check: "Number"
        }],
        inputsInline: !0,
        previousStatement: null,
        nextStatement: null,
        category: null,
        colour: I.motion.primary,
        colourSecondary: I.motion.secondary,
        colourTertiary: I.motion.tertiary,
        enableContextMenu: !1,
        tooltip: y("Forward Block Hover")
    }
};
```

```js
Ms = function(a, b, c) {
    for (; b;) {
        var e = null;
        switch (b.type) {
            case "logo17_run_code":
                e = new ds(b, c, b.type);
                break;
            case "logo17_for_loop":
                e = new hs(b, c);
                var f = to(b, "SUBSTACK");
                f && (f = f.connection && S(f.connection)) && Ms(a, f, e);
                break;
            case "logo17_move_forward":
                e =
                    new Is(b, c, a.module$exports$logo17$AbstractSyntaxTree$player_, b.type, function() {
                        var b = a.module$exports$logo17$AbstractSyntaxTree$player_,
                            c = hh(Hs.get(b.module$exports$logo17$Player$orientation_)).scale(1);
                        a: {
                            var e = b.module$exports$logo17$Player$puzzle_;
                            var f = b.position;
                            var p = D(f.x + c.x, f.y + c.y, f.z + c.z),
                                r = e.tileGrid_.get(Ns(f.x, f.y - 1, f.z)),
                                t = e.tileGrid_.get(Ns(p.x, p.y, p.z)),
                                x = e.tileGrid_.get(Ns(p.x, p.y + 1, p.z));e = e.tileGrid_.get(Ns(p.x, p.y + 2, p.z));
                            if (t && "carrot" != t.type) {
                                if ("passable" == t.type && !r) {
                                    --p.y;
                                    f = p;
                                    break a
                                }
                            } else if (x && "carrot" != x.type) {
                                if ("passable" == x.type) {
                                    f = p;
                                    break a
                                }
                            } else if (e && "carrot" != x.type && "passable" == e.type) {
                                p.y += 1;
                                f = p;
                                break a
                            }
                            f = hh(f)
                        }
                        c.release();
                        fh(b.actionManager, Es(b, f))
                    });
                break;
            case "logo17_turn_left":
                e = new Is(b, c, a.module$exports$logo17$AbstractSyntaxTree$player_, b.type, function() {
                    return Fs(a.module$exports$logo17$AbstractSyntaxTree$player_)
                });
                break;
            case "logo17_turn_right":
                e = new Is(b, c, a.module$exports$logo17$AbstractSyntaxTree$player_, b.type, function() {
                    return Gs(a.module$exports$logo17$AbstractSyntaxTree$player_)
                });
                break;
            default:
                throw Error("S`" + b.type);
        }
        c.children.push(e);
        b = Xi(b)
    }
};
```

block.typeを見てswitch caseしている。

"logo17_move_forward"では、"passable" (通行可能かどうか) や "carrot" (にんじん) が取れたかどうかを見ている。Blockの描画部分だけを使っていて、どうやら実行部分は独自で実装している。


```js
var ds = function(a, b, c) {
    this.id = a ? a.id : Th();
    this.children = [];
    this.block = a;
    this.blockType = c;
    this.parentNode_ = b;
    this.nextNodeGenerator_ = this.getNextNodeGenerator();
    this.isStarted_ = !1;
    this.minExecutionTimeMs = this.module$exports$logo17$AstNode$timeElapsedMs_ = 0
};

var Js = function(a) {
        a.rootNode_ = new ds(null, null, "logo17_root_node");
        var b = $h(a.workspace_, !1);
        b = l(b);
        for (var c = b.next(); !c.done; c = b.next())
            if (c = c.value, "start_block_id" == c.id || "tutorial_start_block_id" == c.id) try {
                Ms(a, c, a.rootNode_);
                break
            } catch (e) {
                break
            }
    },
```

```js
this.workspace_ = bs;

var bs = document.getElementById("hpofblockly") ? Wr(document.getElementById("hpofblockly"), { /* ... */ }) : null,
```


```js
//...
l = function(a) {
    oa();
    var b = a[Symbol.iterator];
    return b ? b.call(a) : na(a)
}

// ...
$h = function(a, b) {
    var c = [].concat(a.topBlocks_);
    if (b && 1 < c.length) {
        var e = Math.sin(3 * Math.PI / 180);
        a.RTL && (e *= -1);
        c.sort(function(a, b) {
            var c = a.getRelativeToSurfaceXY(),
                f = b.getRelativeToSurfaceXY();
            return c.y + e * c.x - (f.y + e * f.x)
        })
    }
    return c
},
```

`Blockly.Workspace.topBlocks_` を順に読み込んで、アクションが実行されている。

RTL = Right-To-Left
アラビア語などの左から右に読む言語の対応


```js
var hs = function(a, b) {
    ds.call(this, a, b, "logo17_for_loop");
    this.minExecutionTimeMs = 333;
    this.iterations_ = 0;
    var c = cs(a);
    c && (c = parseInt(ki(c, "NUM"), 10), isNaN(c) || (this.iterations_ = c))
}
```

"control_repeat" blockの

### 全体の設計

canvasにSVGでアニメーション描画をしている。
描画はx,y,zで表現される3D空間になっている。
- tileLayer(足元のブロック)とobjectLayer(ニンジンや木など)がそれぞれある
レッスンごとにjsonファイルで初期状態を定義して読み込んでいる。

player(Rabbit)
- 曲がるか進むか
- ニンジンをすべて集められたらレッスン成功
- アクション判定ロジック
    - 進めるかどうか
    - ニンジンを取得できたかどうか

SVGアニメーション
- すべてのobjectやtileにアニメーション用のsvgが書かれている
    - [shared-sprite.svg](https://www.google.com/logos/2017/logo17/shared-sprite.svg)
    - [one-sprite.svg](https://www.google.com/logos/2017/logo17/one-sprite.svg)
- どの順番でspriteを描画するかが定義されている

Blockly

- Blockly.Workspace

`Blockly.Workspace` をベースとして、blockの操作ができるようにしている。
blockの定義などは基本的にそのまま利用している。

blockの実行部分は、完全に独自実装をしている。
`Blockly.Workspace.topBlocks_` を順に読み込んで、アクションが実行されている。
=> evalに頼らない実装方法


### 試しに作ってみた

Yet another Blockly interpreter (maybe forever)

```js
Array.prototype.clone = function() {
  return this.slice(0);
};

const workspace = Blockly.inject(
  'blocklyDiv',
  {
    toolbox: document.getElementById('toolbox'),
    trashcan: true,
  },
);

function repeatNumber(block) {
  if (block.type !== "controls_repeat") {
    throw "This block is NOT controls_repeat.";
  }

  var n = block.inputList[0].fieldRow[0].text_;
  var i = parseInt(n);
  if (isNaN(i)) {
    return 0;
  }
  return i;
};

function parseBlockTree(blocks, execList) {
  for (;;) {
    let block = blocks.pop();
    if(block === void 0) {
      return execList;
    }
    execList.push(block.type)

    switch (block.type) {
    case "controls_repeat":
      const n = repeatNumber(block);
      for (let i = 1; i <= n; i++) {
        execList = parseBlockTree(block.childBlocks_.clone(), execList);
      }
      break;
    default:
      if(block.childBlocks_.length > 0) {
        execList = parseBlockTree(block.childBlocks_.clone(), execList);
      }
      break;
    }
  }

  return execList;
}

function runCode() {
  let execList = [];
  const blocks = workspace.topBlocks_.clone();

  execList = parseBlockTree(blocks, execList);
  console.log("# Execution List")
  execList.forEach( (exec) => console.log(exec) );
}

document.getElementById('runCode').addEventListener('click', runCode, false);
```
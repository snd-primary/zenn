# プログレスサークルを実装しながらCSS@property × WAAの便利さを知る

## 前提

## 作ったもの

## やっていること

* `conic-gradient()`を使って、円錐形のグラデーションを作成
* グラデーションの
* `@property`ルールで、CSS変数`--angle`を定義
	* これにより、グラデーション`deg`の値そのものをアニメさせることができるようになる
* WAAでアニメ実装
	* これにより、アニメの再生時間や再生・停止などのイベントを柔軟に制御できるようになる

## @propertyルール

```css
/* 今回の場合は<angle>型のほか、<parcentage>型でも同様の挙動が実現できる */
@property --angle {
	syntax: "<angle>"; /* CSS変数--angleのデータ型 */
	inherits: false; /* 親からの値を継承するか: boolean */
	initial-value: 360deg; /* CSS変数--angleの初期値 */
}

.progressCircle {
	width: 300px;
	height: 300px;
	border-radius: 50%;
	background-image: conic-gradient(
		lch(86.29% 94.85 119.35) 0deg, /* 0deg の地点は黄緑*/
		#9127e7 var(--angle), /* --angleは、360deg -> 0degまでリニアに遷移する */
		#000000 var(--angle) /* --angleは、360deg -> 0degまでリニアに遷移する */
	);
}
```

**説明**

- `1`で作成した`.progressCircle`要素に対して、複数色を持つ円錐形のグラデーションカラーを適用しています。

- `bckground-image`に`conic-gradient`というCSS関数をセットすることで、簡単に円錐形のグラデーションを生成することできます。

- 今は割合が動的に変化するような円グラフを作りたいので、紫と黒の角度(deg)にはCSS変数`var(--angle)`を設定しています。（この`--angle`を0~360degの間でアニメさせる）


## 3. `@property`ルールを用いて、CSS変数`--angle`を定義する

以下の通り、CSS変数`--angle`を@propertyルールで定義します。

```css

```

**説明**

@propertyは、CSS変数（カスタムプロパティ）の型や初期値を設定するためのルールです。比較的新しい仕様ですが、2025年4月現在はたいていのモダンブラウザに対応しているようです。

@propertyルールは、定義したCSS変数の値を直接アニメーションさせることができるという特徴があります。これはなかなか革新的な機能で、従来のCSSでは不可能でした。

今回のケースでも、`--angle`を、`0deg~360deg`の間で増減させるために@propertyルールを使用しています。

ちなみに`CSS.registerProperty()`メソッドを用いれば、JavaScript側で@propertyの登録を行うこともできます。（グローバルスコープである点は変わりませんが・・）

@propertyで定義したCSS変数は、グローバルスコープとなります。

![alt text](image.png)


### 4.WebAnimationAPI(WAA)でアニメを実装

Reactはいったん置いておいて、WebAnimationAPI(以下WAA)の説明を。

WAAはCSSアニメーションをJSから柔軟に操作するためのAPIです。

以下のように記述します。

```tsx
// Web Animation API を使用してアニメーションを作成
const animation = circle.animate(
	// キーフレーム: CSSカスタムプロパティ '--angle' を 360deg から 0deg に変化させる
	[{ "--angle": "360deg" }, { "--angle": "0deg" }],

	// オプション: 実行時間、イージング関数、アニメーション終了後の状態
	{
		duration: duration,
		easing: "linear",
		fill: "forwards",
	}
);
```

**説明**

`Element.animate(keyframes, options)`とすることで、そのHTML要素にCSSアニメーションを適用させることができます。

`animate()`の引数に注目してください。

- keyframes -> CSSで記述する`@keyframes`そのものです。ちょっと書き方が変わりますが、やっていることは全く同じです。
- option -> animationプロパティをオブジェクト形式で指定します。これもCSSで指定するプロパティと同様ですね。

**WAAの何が良いのか**




### 5.Reactコンポーネントとして完成させる


### 備考

**境界線がギザギザする?**

黒と紫の境界は1degの範囲で線形補完されるため、境界線がぼやけます。
これにより、

**@propertyのパフォーマンス**

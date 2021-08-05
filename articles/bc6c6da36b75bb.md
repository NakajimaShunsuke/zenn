---
title: "【Vuejs】画像をモノトーンに設定できるコンポーネントを作成"
emoji: "🌊"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["javascript","TailWindCSS","HTML","CSS","Vuejs"]
published: true
---

# 執筆に至った経緯
https://zenn.dev/grimm_marchen/articles/7ea42fe913c9068ec1c6
上記プロジェクトをモノトーンな雰囲気にしていきたいと考えているので、まずは画像をCSSでモノトーンにするコンポーネントを作成します。

# コンポーネントの仕様
- 画像をモノトーンにするCSS(色付きと切り替える)
- 画像サイズはワイド100%を設定
- imgタグをブロックレベル要素として扱う
- alt属性に初期テキストを設定

主にこの３つの要件を満たしたコンポーネントを作成します。
画像のサイズは基本ワイド100%に設定し、呼び出し元でサイズを可変させる仕様にします。

CSSは勿論TailWindCSSを使います

https://tailwindcomponents.com/cheatsheet/

# コンポーネントを作成
`src/components`に`ImageBox.vue`というファイルを作成します。

```vue:ImageBox.vue
<template>
	<img />
</template>

<script setup="props">

</script>

```

## imgタグをブロックレベル要素に設定とワイド設定

```diff vue:ImageBox.vue

<template>
+  <img class="block w-full" />
</template>

<script setup="props">

</script>

```

インラインレベルからブロックレベルに変更する理由は文章の一部として扱う事がほとんどない為です。
ワイドは呼び出し先で設定する設計にするため、画像自体は100%にしておきます

## クラスバインディング
filterとgrayscaleのスタイルが記述されたclassの有無をクラスバインディングを使って設定する。

```diff vue:ImageBox.vue

<template>
+	<img
+		class="block"
+		:class="{ filter: isFilter, grayscale: isGrayscale }"
+	/>
</template>

<script setup="props">

</script>

```

# 値の受け渡しでfilterとgrayscaleの有無を受け取る

```diff vue:ImageBox.vue

<template>
+	<img
+		class="block"
+		:class="{ filter: isFilter, grayscale: isGrayscale }"
+	/>
</template>

<script setup="props">
+  import { defineProps } from "vue";
  defineProps({
+   isFilter: { type: Boolean, default: true },
+   isGrayscale: { type: Boolean, default: true },
  });
</script>

```

`filter`と`grayscale`の有無を`isFilter`と`isGrayscale`という`Boolean`型の変数を通して呼び出し元から画像をモノトーンにするかを決定する。
デフォルトで`true`を設定

# 画像ファイルパスを受け取る

```diff vue:ImageBox.vue

<template>
	<img
	  class="block"
		:class="{ filter: isFilter, grayscale: isGrayscale }"
+   :src="imgItem"
	/>
</template>

<script setup="props">
  import { defineProps } from "vue";
  defineProps({
    isFilter: { type: Boolean, default: true },
    isGrayscale: { type: Boolean, default: true },
    imgItem: { type: String, default: "" },
+    altText: { type: String, default: "WEB-Image" },
  });
</script>

```

## alt属性の設定
同じS`tring`型でalt属性も呼び出し元から受け取れるようにする
:::message
alt属性は設定し忘れる事もあるので初期値を設定しておける事がポイントです
:::

```diff vue:ImageBox.vue

<template>
	<img
	  class="block"
	  :class="{ filter: isFilter, grayscale: isGrayscale }"
    :src="imgItem"
+		:alt="altText"
	/>
</template>

<script setup="props">
  import { defineProps } from "vue";
  defineProps({
    isFilter: { type: Boolean, default: true },
    isGrayscale: { type: Boolean, default: true },
    imgItem: { type: String, default: "" },
+   altText: { type: String, default: "WEB-Image" },
  });
</script>

```

##　実行確認
呼び出し元に下記記述を追加します
```vue
<template>
	<article
		class="
			max-w-7xl
			w-full
			bg-gray-200
			sm:bg-red-200
			md:bg-yellow-200
			lg:bg-green-200
			xl:bg-blue-200
		"
	>
		<p>モノトーン画像</p>
		<ImageBox imgItem="/src/assets/images/main-img.jpg" />
		<p>モノトーン設置OFF</p>
		<ImageBox imgItem="/src/assets/images/main-img.jpg" :isFilter="false" :isGrayscale="false" />
	</article>
</template>

<script setup>
import ImageBox from "../components/ImageBox.vue";

// This starter template is using Vue 3 experimental <script setup> SFCs
// Check out https://github.com/vuejs/rfcs/blob/master/active-rfcs/0040-script-setup.md
</script>

```
![](https://storage.googleapis.com/zenn-user-upload/2bd23216669487e81fd8e457.png)

# まとめ
コンポーネントを作成する記事書くのが想像以上に難しいと感じました。
こういうサイトを作りたいみたいな大枠の記事を書いてからのほうがよかったような気がします。
もう少し綺麗にまとめる方法がみつかりましたら修正いたします。
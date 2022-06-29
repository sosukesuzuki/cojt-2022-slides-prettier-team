---
highlighter: shiki
lineNumbers: false
---

# オープンソースのコードフォーマッター Prettier 2.7 の開発・リリース

2022 年度 COJT ソフトウェアコース 成果発表会

---

# メンバー

<div class="flex gap-8">

<div class="flex">
  <img class="w-30 h-30 mr-5" src="https://avatars.githubusercontent.com/sosukesuzuki">
  <div>
    <h2>鈴木 颯介</h2>
    <p>
      <a href="https://github.com/sosukesuzuki">GitHub: @sosukesuzuki</a>
    </p>
    <p>情報科学類</p>
  </div>
</div>

<div class="flex">
  <img class="w-30 h-30 mr-5" src="https://avatars.githubusercontent.com/HosokawaR">
  <div>
    <h2>細川 隆之介</h2>
    <p>
      <a href="https://github.com/HosokawaR">GitHub: @HosokawaR</a>
    </p>
    <p>情報科学類</p>
  </div>
</div>

</div>

---
layout: center
---

# オープンソースのコードフォーマッター<br> Prettier 2.7 の開発・リリース

---

# Prettier

- 多言語対応のコードフォーマッター
- OSS として https://github.com/prettier/prettier で開発されている
- JavaScript で記述されており、Node.js や Web ブラウザ上で実行できる
- 2020年の StateOfJS (全世界のJavaScriptデベロッパーへのアンケート) では60%の開発者が日常的に使用していると回答した
- npm での週間ダウンロード数は 23,548,067
- もともとは元 Mozilla の James Long 氏が開発したもので、その後 Meta(当時はFacebook) の Vjuex 氏が業務として開発に取り組み、Meta 文化圏を中心に使われるようになっていった

---

# Prettier

- 世界中の企業や個人によって使われている実用的なツールだが、OSS なので学生でも GitHub アカウントがあれば開発に参加できる
- COJT ではこの Prettier のバージョン 2.7 に必要な機能を実装し、いくつかのバグを修正し、実際にリリースを行った

---

# Prettier

整っていないコードを入力するといい感じにフォーマットして出力してくれる

<div class="flex gap-10">

<div>

**Input:**

```js
function foo      () {
  console.log
   ( "Hello"  )}
```

</div>

<div>

**Output:**

```js
function foo() {
  console.log("Hello");
}
```

</div>

</div>

---

# Prettier 2.7

- 我々が開発の対象としたバージョン
- 主な機能として、キャッシュ、TypeScript 4.7 対応が含まれている
- いくつかのバグ修正も含まれている

---

# Prettier 2.7

## 我々がやったこと

大体全部

---

# リリースブログの執筆

https://prettier.io/blog/2022/06/14/2.7.0.html

<img class="border border-solid border-gray-500" src="/assets/images/prettier-blog-screenshot.png">

---

# `--cache` CLI オプション

```bash
prettier src --write --cache
```

有効にすると、直前のフォーマットから変更のないファイルはフォーマットされなくなる。
CLI での実行の速度を大幅に改善する機能。

---
layout: center
---


なぜこんな機能を？

---

<img class="border border-solid border-gray-500" src="/assets/images/rome-blog.png">

https://www.publickey1.jp/blog/22/javascripttypescriptrome_formatterrustprettier10.html

---
layout: center
---

Twitter とかを眺めるとみんな盛り上がっている

---
layout: center
---

おれ「みんな速いフォーマッター好きなんだ...」

---

# `--cache` CLI オプション

## なぜキャッシュなのか

Prettier の実行速度を改善する方法をいくつか検討した

- 効率的なデータ構造・アルゴリズムを使う
- マルチスレッド
- キャッシュ

---

# `--cache` CLI オプション

## 1. 効率的なデータ構造・アルゴリズム

不採用

- Invest: 高い, Return: 低い

<img class="border border-solid border-gray-500" src="/assets/images/optimization01.png">

---

# `--cache` CLI オプション

## パフォーマンス改善案2. マルチスレッド 

不採用

- Invest: 高い, Return: (多分)高い

<img class="border border-solid border-gray-500" src="/assets/images/optimization02.png">

---

# `--cache` CLI オプション

## パフォーマンス改善案3. キャッシュ 

採用

- Invest: 低い, Return: 高い

<img class="border border-solid border-gray-500" src="/assets/images/optimization03.png">

---

# `--cache` CLI オプション

実装

- https://github.com/prettier/prettier/pull/12800
- ESLint の `--cache` オプションを参考にした
  - https://eslint.org/docs/latest/user-guide/command-line-interface#caching
- `file-entry-cache` というライブラリを使ってファイルが変更されたかどうかを判断し分岐させるだけ
- キャッシュファイルは `./node_modules/.cache/prettier/.prettier-cache` に保存される
  - これは nyc や ava のキャッシュファイルを保存する場所と同じ

---

# `--cache` CLI オプション

キャッシュキーは以下

- Node.js のバージョン
- Prettier のバージョン
- Prettier のオプション
- (`--cache-strategy` が `content` の場合) ファイルの内容
- (`--cache-strategy` が `metadata` の場合) タイムスタンプ等のファイルのメタデータ

---

# `--cache-strategy` オプション

- `content` or `metadata`
- キャッシュキーとして、ファイルの内容を使うか、メタデータを使うか選べる
- デフォルトでは `content`
- `content` は CI で使えるが遅い、`metadata` は CI で使えないが速い
- 用途に合わせてお好みでどうぞ

---

# Prettier 2.7

## TypeScript 4.7 対応

- 2022年5月24日 Microsoft から TypeScript の新しいバージョン 4.7 がリリースされた
  - https://devblogs.microsoft.com/typescript/announcing-typescript-4-7/
- TypeScript 4.7 には新しい構文が３つ実装されている
  - Instantiation expression
  - `extends` constraints on `infer`
  - Optional variance
- Prettier はこれらの構文を適切にフォーマットできるように対応する必要がある

---

# Prettier 2.7

## TypeScript 4.7 対応

- まず、Prettier 本体ではなく Prettier が依存している構文解析器(パーサー)を新しい構文に対応させる必要がある
- Prettier は TypeScript のパーサーを２種類持っており、オプションで切り替えることができる
  - `--parser=babel-ts` (Babel (https://github.com/babel/babel) のパーサー)
  - `--parser=typescript` (typescript-eslint (https://github.com/typescript-eslint/typescript-eslint) のパーサー)

---

# Prettier 2.7

## TypeScript 4.7 対応

**Babel:**

- `extends` constraints on `infer` 構文の実装
  - https://github.com/babel/babel/pull/14476

こういうやつ

```ts
type X3<T> = T extends [infer U extends number] ? MustBeNumber<U> : never;
```

---

# Prettier 2.7

## TypeScript 4.7 対応

**typescript-eslint:**

バグ報告

- 三項演算子のパーサーのバグ
  - https://github.com/microsoft/TypeScript/issues/48733
- `api-extractor` というツールが TypeScript 4.7 上で動作しない
  - https://github.com/microsoft/rushstack/issues/3349

---

# Prettier 2.7

## TypeScript 4.7 対応

**typescript-eslint:**

新しい構文の対応に必要なこと全部

- https://github.com/typescript-eslint/typescript-eslint/pull/4938
- https://github.com/typescript-eslint/typescript-eslint/pull/4830
- https://github.com/typescript-eslint/typescript-eslint/pull/4831

---

# Prettier 2.7

## TypeScript 4.7 対応

**Prettier:**

3つの構文 × 2つのパーサー全部対応した

- https://github.com/prettier/prettier/pull/12896
- https://github.com/prettier/prettier/pull/12897
- https://github.com/prettier/prettier/pull/12898
- https://github.com/prettier/prettier/pull/12900
- https://github.com/prettier/prettier/pull/12921
- https://github.com/prettier/prettier/pull/12924
- https://github.com/prettier/prettier/pull/12959

---

# Prettier 2.7

## Test Callee の特殊なフォーマットを Playwright に対応させた

https://github.com/prettier/prettier/pull/12779

<div class="flex gap-10">

<div>

**Test Callee として認識される**と強調した部分が自然な英文に見えて嬉しい。

```js {1-2}
describe("I am very very very very very very hungry", () => {
  test("there is a big big big big big big bread", () => {
    // ...
  });
});
```

</div>

<div>

**Test Callee として認識されない**と行の長さが長い場合、折り返される。（`--print-width` を 40 に設定）

```js {1-2|3-4}
_describe(
  "I am very very very very very very hungry",
  () => {
    _test(
      "there is a big big big big big big big bread",
      () => {
        // ...
      }
    );
  }
);
```

</div>

</div>

これに Playwright の `test.describe.parallel.only` なども対応するようにした

---

# Prettier 2.7

## `as` が `:` と出力されてしまうバグを修正

https://github.com/prettier/prettier/pull/12706

[Babel Parser のバグ](https://github.com/babel/babel/issues/14498)により

<div class="flex gap-10">

<div>

入力

```ts
[x as any] = x;
```

</div>

<div>

修正前のフォーマット

```ts
[x: any] = x;
```

</div>

<div>

修正後のフォーマット

```ts
[x as any] = x;
```

</div>

</div>


※現在は Babel の該当バグが修正されたので、この PR も Revert された。

---

# Prettier 2.7

## Export のコメントのフォーマットに一貫性がない挙動を修正 

https://github.com/prettier/prettier/pull/12860

<div class="flex gap-10">

<div>

入力

```ts
export {
  foo,
  
  bar as  // comment
		 baz,
}

import {
  foo,
  
  bar as  // comment
		 baz,
} from 'foo'
```

</div>

<div>

修正前のフォーマット

```ts
export {
  foo,
  bar as baz, // comment
};

import {
  foo,
  // comment
  bar as baz,
} from "foo";
```

</div>

<div>

修正後のフォーマット

```ts
export {
  foo,
  // comment
  bar as baz,
};

import {
  foo,
  // comment
  bar as baz,
} from "foo";
```

</div>

--- 

# Prettier 2.7

## Enum の Computed Properties が消えるバグを修正 

https://github.com/prettier/prettier/pull/12930

<div class="flex gap-10">

<div>

入力

```ts
enum A {
  [i++],
}
```

</div>

<div>

修正前のフォーマット

```ts
enum A {
  i++,
}
```

</div>

<div>

修正後のフォーマット

```ts
enum A {
  [i++],
}
```

</div>

</div>

---
# try also 'default' to start simple
theme: apple-basic
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

# Prettier 2.7

リリースブログの執筆

---

# Prettier 2.7

## `--cache` CLI オプション

https://github.com/prettier/prettier/pull/12800

- Rome Formatter とか dprint を見るとみんな速いフォーマッターに興味あるっぽい
- 需要があるなら速くしたほうがいいけど、Prettier の実行速度を腕力で速くするのはきつい(JSだしパーサーがそもそも遅いし)
- なので飛び道具的に速度を上げるためのキャッシュ
- 一回フォーマットしてから変更がないファイルはスキップする
- あと GW 暇だった

---

# Prettier 2.7

## `--cache` CLI オプション

- ESLint の `--cache` オプションを参考にした
- `file-entry-cache` というライブラリを使ってタイムスタンプ等のファイルのメタデータに基づいてキャッシュ情報を取得して、`./node_modules/.cache/prettier/.prettier-cache` というファイルにしまっておく
- 次のフォーマットでは、キャッシュを参照し、スキップできそうならスキップする
- キャッシュを確認してスキップできるかを判断する処理は 0ms ~ 1ms くらいで完了するので全体をフォーマットするときはだいぶ速くなりそう(普通にファイルをフォーマットすると、言語やファイルサイズにもよるけど n * 100ms とかの単位で実行時間がかかる)

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

## バグ修正

<!-- by hosokawa -->

- https://github.com/prettier/prettier/pull/12706
- https://github.com/prettier/prettier/pull/12779
- https://github.com/prettier/prettier/pull/12860
- https://github.com/prettier/prettier/pull/12930

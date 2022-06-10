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

- https://github.com/prettier/prettier/pull/12800

---

# Prettier 2.7

## TypeScript 4.7 対応

**Babel:**

- https://github.com/babel/babel/pull/14476

**typescript-eslint:**

- https://github.com/typescript-eslint/typescript-eslint/pull/4938
- https://github.com/typescript-eslint/typescript-eslint/pull/4830
- https://github.com/typescript-eslint/typescript-eslint/pull/4831

---

# Prettier 2.7

## TypeScript 4.7 対応

**Prettier:**

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

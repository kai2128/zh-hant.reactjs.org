---
id: lists-and-keys
title: 列表與 Key
permalink: docs/lists-and-keys.html
prev: conditional-rendering.html
next: forms.html
---

> 嘗試新的 React 文件。
>
> 這些新的文件頁面教導 modern React 並包括即時範例：
>
> - [Rendering Lists](https://beta.reactjs.org/learn/rendering-lists)
>
> 新的文件將會很快取代目前的文件，它將會被歸檔。[提供回饋。](https://github.com/reactjs/reactjs.org/issues/3308)


首先，讓我們複習一下在 JavaScript 中如何改變列表。

在以下的程式碼中，我們使用 [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) function 來接收 `numbers` array，並將其中的每個值乘以兩倍。我們將 `map()` 回傳的新 array 設定為變數 `doubled` 的值並印出：

```javascript{2}
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

這段程式碼在 console 中印出 `[2, 4, 6, 8, 10]`。

在 React 中，將 array 轉變成 [element](/docs/rendering-elements.html) 列表幾乎是一樣的方式。

### Render 多個 Component {#rendering-multiple-components}

你可以建立一系列的 element 並用大括號 `{}` [將它們包含在 JSX 裡面](/docs/introducing-jsx.html#embedding-expressions-in-jsx)。

下面，我們會用 JavaScript 的 [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) function 迭代 `numbers` array，我們每次都會回傳一個 `<li>` element。最後，我們會把結果產生的 element array 設定為 `listItems`：

```javascript{2-4}
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
```

接著，我們會把整個 `listItems` array 包含在一個 `<ul>` element 內：

```javascript{2}
<ul>{listItems}</ul>
```

[**在 CodePen 上試試看吧！**](https://codepen.io/gaearon/pen/GjPyQr?editors=0011)

這段程式碼展示了一個數字列表，從 1 到 5。

### 基本列表 Component {#basic-list-component}

一般來說，你會在一個 [component](/docs/components-and-props.html) 內 render 列表。

我們可以將上面的範例改寫為一個接收 `numbers` array 並輸出一個沒有排序的 element 列表的 component。

```javascript{3-5,7,13}
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<NumberList numbers={numbers} />);
```

當你執行這段程式碼時，你會收到一個關於你應該提供 key 給每一個列表項目的警告。「key」是當你在建立一個 element 列表時必須使用的特殊的 string attribute。在下一個段落中，我們將會討論其重要性。

讓我們為 `numbers.map()` 列表中的每個項目分配一個 `key`，並修正遺漏 key 的問題。

```javascript{4}
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}
```

[**在 CodePen 上試試看吧！**](https://codepen.io/gaearon/pen/jrXYRR?editors=0011)

## Key {#keys}

Key 幫助 React 分辨哪些項目被改變、增加或刪除。在 array 裡面的每個 element 都應該要有一個 key，如此才能給予每個 element 一個固定的身份：

```js{3}
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```

選擇 key 最佳的方法是在列表中使用唯一識別字串來區別 sibling 項目。通常，你會使用資料的 ID 作為 key：

```js{2}
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```

當你 render 的項目沒有固定的 ID 且你也沒有更好的辦法時，你可以使用項目的索引做為 key：

```js{2,3}
const todoItems = todos.map((todo, index) =>
  // 請在項目沒有固定的 ID 時才這樣做
  <li key={index}>
    {todo.text}
  </li>
);
```

我們並不建議你使用索引作為 key，尤其如果項目的順序會改變的話。這會對效能產生不好的影響，也可能會讓 component state 產生問題。請參考 Robin Pokorny 這篇[深入剖析使用索引作為 key 的負面效應](https://robinpokorny.com/blog/index-as-a-key-is-an-anti-pattern/)ㄧ文。如果你選擇不明確分配 key 到列表項目時，React 預設將會使用索引作為 key。

如果你想了解更多，請參考[深度剖析 key 的必要性](/docs/reconciliation.html#recursing-on-children)一文。

### 用 Key 抽離 Component {#extracting-components-with-keys}

Key 只有在周遭有 array 的情境中才有意義。

例如，如果你要[抽離](/docs/components-and-props.html#extracting-components)一個 `ListItem` component 的話，你應該把 key 放在 array 裡的 `<ListItem />` element 上，而不是把它放在 `ListItem` 裡面的 `<li>` element 上。

**範例：Key 的錯誤使用方式**

```javascript{4,5,14,15}
function ListItem(props) {
  const value = props.value;
  return (
    // 錯！你不需要在這裡指出 key：
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 錯！你應該要在這裡指出 key：
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

**範例：Key 的正確使用方式**

```javascript{2,3,9,10}
function ListItem(props) {
  // 正確！你不需要在這裡指出 key：
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 正確！Key 應該在 array 內被指定。
    <ListItem key={number.toString()} value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

[**在 CodePen 上試試看吧！**](https://codepen.io/gaearon/pen/ZXeOGM?editors=0010)

一個好的經驗法則是，在 `map()` 呼叫中的每個 element 都會需要 key。

### Key 必須在 Sibling 中是唯一的 {#keys-must-only-be-unique-among-siblings}

在 array 中使用的 key 應該要是唯一的值。然而，它們不必在全域中唯一。當我們產生兩個不同的 array 時，我們仍然可以使用相同的 key：

```js{2,5,11,12,19,21}
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Blog posts={posts} />);
```

[**在 CodePen 上試試看吧！**](https://codepen.io/gaearon/pen/NRZYGN?editors=0010)

Key 的功能是提示 React，但它們不會被傳遞到你的 component。如果你在 component 中需要同樣的值，你可以直接把這個值用一個不同的名稱作為 prop 傳下去：

```js{3,4}
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```

在上面的例子中，`Post` component 可以讀取 `props.id`，但不能讀取 `props.key`。

### 在 JSX 中嵌入 map() {#embedding-map-in-jsx}

在上面的例子中，我們宣告了另一個 `listItems` 變數並把它包含在 JSX 中：

```js{3-6}
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

JSX 讓你在大括號中[嵌入任何表達式](/docs/introducing-jsx.html#embedding-expressions-in-jsx)，所以我們能夠 inline `map()` 的結果：

```js{5-8}
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```

[**在 CodePen 上試試看吧！**](https://codepen.io/gaearon/pen/BLvYrB?editors=0010)

有時候這會產生更乾淨的程式碼，但這種風格也可能被濫用。就像 JavaScript 一樣，是否要將變數抽取出來以增加可讀性完全是看你的決定。請記得，如果 `map()` 的程式碼層級變得過度巢狀，也許就是使用[抽離 component](/docs/components-and-props.html#extracting-components)的時候了。

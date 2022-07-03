---
title: Web3系列教程之入门篇---1：了解React (Next.js)
description: null
author: 李留白
weight: 0
date: 2022-07-03T09:23:00.012Z
lastmod: 2022-07-03T13:53:59.972Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

> 传送门: [WEB3系列教程之新手篇](https://hicoldcat.com/posts/web3/freshman-track-sumary/)

在新手课程中，我们建立了一个非常简单的dApp，使用HTML、CSS和一些JavaScript。然而，在现实世界中，这些类型的"vanilla"网站实现已经是过去式了。

今天，我们使用web框架来简化web开发过程。但它更简单吗？取决于你从谁的角度看问题。如果你刚刚开始，以前从未做过这件事，那么你可能需要一段时间才能理解所有必要的概念。但是，从长远来看，你会感谢自己，并为自己花时间学习而感到高兴。

现在最大和最常用的网络框架是：

- [React](https://reactjs.org/)
- [Angular](https://angular.io/)
- [Vue](https://vuejs.org/)

虽然它们各有优缺点，但到目前为止，React已经在Web开发领域掀起了风暴。在Web3领域也是如此，React是构建dApps最常用的网络框架。在整个新手课程中，以及所有以后的课程中，我们将使用大量的React，所以可以把这个级别看作是React的速成课程，它将教会你足够的东西来开始。这并不是要取代在专注于Web2教学的平台上学习React，而是作为一个指南，让你了解有多少东西是必须开始学习的，这样你就不会陷入教程的地狱。😅

> 实际上，我们将使用Next.js--它是React本身的一个扩展--但后面会有更多的内容。让我们先学习一些React。

## 什么是React?

React是一个网络框架，它使得构建和响应你的网络应用的 "视图 "变得容易。视图 "是在屏幕上显示的内容，它如何变化，如何更新，等等。React本质上只是给你一个模板语言，你可以创建返回HTML的Javascript函数。

正常的Javascript函数会返回Javascript相关的东西--字符串、数字、布尔值、对象等等。React基本上结合了Javascript和HTML，产生了一种他们称之为JSX的语言。在JSX中，类似Javascript的函数返回HTML，而不是常规的Javascript事物。基本上就是这样了。

返回HTML的Javascript函数的组合被称为组件。组件是用JSX编写的。虽然一开始看起来很笨拙，但一旦你习惯了，它们实际上是很容易操作的。

下面是一个简单组件的例子。

```jsx
function ShoppingList() {
    return (
      <div className="shopping-list">
        <h1>Shopping List</h1>
        <ul>
          <li>Apples</li>
          <li>Bananas</li>
          <li>Grapes</li>
        </ul>
      </div>
    );
}
```

[运行代码](https://codesandbox.io/s/icy-water-xrm2ch?file=/src/App.js)

很好，但这与HTML没有什么区别。但是，如果你想根据一个数组来呈现一个项目的列表呢？

```jsx
function ShoppingList() {
    const items = ["Apples", "Bananas", "Grapes"]
    
    return (
      <div className="shopping-list">
        <h1>Shopping List</h1>
        <ul>
            {items.map((item, index) => <li key={index}>{item}</li>)}
        </ul>
      </div>
    );
}
```

[运行代码](https://codesandbox.io/s/confident-tesla-5l3k1t?file=/src/App.js)

哇，看看这个! 我们刚刚在HTML中使用了Javascript。在JSX中，你可以通过用大括号`{`和`}`包裹JS代码在HTML中编写Javascript。实际上，如果你多想一下，你就会明白发生了什么。`.map()`是一个Javascript函数，它在一个数组上循环，并为每个项目返回一些东西。在这种情况下，它在 `items `数组上循环，并返回一个 `li `元素，其中有 Javascript 变量 `item` 的值，也就是 HTML。明白了吗？🤔

在我们的组件中，我们基本上嵌入了另一个组件。map函数是一个返回HTML的JS函数。它是一个组件。尽管它没有被明确定义为顶级函数，它仍然是一个组件。

将组件嵌入到其他组件中是 React 的强大功能。这称为组合。我们将多个返回 HTML 的 Javascript 函数组合在一起，并从中构建一个组合的 HTML 文档，该文档将显示在 Web 应用程序上。

## 组件之间的数据传递

如果组件只是静态的，它们就不是很有用。当然，遍历数组和其他东西都很好，但当今大多数 Web 应用程序都不是静态文档。今天的大多数 Web 应用程序都会从​​某种服务器、数据库或区块链中动态获取数据。这意味着经常需要相同的组件来显示不同的数据。

拥有组件的主要用例是能够编写可重用的代码，并且可以在其中包含不同的信息，而无需再次重写整个代码。

让我们看一个例子。这两个代码哪个更易读？

```jsx
<div class="cards">
    <div class="card">
      <img src="img_avatar.png" alt="Avatar">
      <div class="container">
        <h4><b>Alice</b></h4>
        <p>Frontend Developer</p>
      </div>
    </div> 

    <div class="card">
      <img src="img_avatar.png" alt="Avatar">
      <div class="container">
        <h4><b>Bob</b></h4>
        <p>Backend Developer</p>
      </div>
    </div> 

    <div class="card">
      <img src="img_avatar.png" alt="Avatar">
      <div class="container">
        <h4><b>Charlie</b></h4>
        <p>Full Stack Developer</p>
      </div>
    </div>
</div>
```

```jsx
function Cards() {
    return (
        <div className="cards">
            <!--Data is passed to children through HTML attributes-->
            <Card name="Alice" job="Frontend Developer" />
            <Card name="Bob" job="Backend Developer" />
            <Card name="Charlie" job="Full Stack Developer" />
        </div>
    )
}

// Card receives an object as an argument
// We can destructure the object to get specific variables
// from inside the object - name and job
function Card({name, job}) {
    return (
        <div className="card">
            <img src="img_avatar.png" />
            <div className="container">
                <h4><b>{name}</b></h4>
                <p>{job}</p>
            </div>
        </div>
    )
}
```

[运行代码](https://codesandbox.io/s/zealous-carson-44om2s?file=/src/App.js)

纯 HTML 示例重复使用相同的代码 3 次，尽管真正改变的只是人名和他们的职位。

在 JSX 中，我们可以将每个组件抽象`Card`为一个组件，该组件从其父组件（在本例中为 ）获取某些数据`Cards`。父组件通过类似 HTML 的属性`name="Alice"`（然后，`Card`组件可以根据从父级接收到的内容返回带有可变数据的 HTML。

这段代码更容易重用和扩展。想要稍微改变所有卡片的外观吗？只需修改一个组件！并非所有复制粘贴的 HTML。

## 交互式组件

好的，所以我们现在可以在组件之间传递数据。这一切都很好，但我们还没有添加交互性。诸如在单击按钮或在输入框中键入文本时能够运行一些代码等。

值得庆幸的是，在 Javascript 中，函数可以在其中包含函数。例如，

```jsx
function someFunc() {
    
    function otherFunc() {
        console.log("Hello!")
    }
    
    otherFunc();
}

someFunc(); // will print "Hello!"

otherFunc(); // will throw an error! undefined here
```

`otherFunc`只能在`someFunc`自身内部使用。这是常规 Javascript 中很少使用的功能，但在使用 React 时使用非常频繁。让我们通过一个例子来看看为什么。

```jsx
function Button() {
    
    function handleClick() {
        console.log("Hello")
    }
    
    return (
        <button 
            className="button" 
            onClick={handleClick}>
            Click Me!
        </button>
    )
    
}
```

[运行代码](https://codesandbox.io/s/quizzical-sammet-59fi5i?file=/src/App.js)

我们有一个名为`Button`. 在这个函数中，我们有另一个函数叫做`handleClick`。在 HTML`<button>`标记中，我们指定`onClick={handleClick}`当单击按钮时`handleClick`调用该函数。此功能仅在`Button`组件内部可用。单击 Web 应用程序上的按钮将`Hello`在浏览器控制台中打印。这就是我们使用 React 构建交互式网站的方式！

这个例子仍然相当简单，因为`handleClick`没有参数。如果我们想在用户输入输入框时在控制台中打印文本怎么办？我们如何将文本传递给函数？

就是这样。

```jsx
function PrintTypedText() {
    
    function handleOnChange(text) {
        console.log(text);
    }
    
    return (
        <input 
            type="text" 
            onChange={(e) => handleOnChange(e.target.value)}
        />
    )
}
```

[运行代码](https://codesandbox.io/s/mutable-feather-el4sbe?file=/src/App.js)

HTML`input`元素提供了一个方便的事件侦听器 -`onChange`每次输入框中的文本发生更改（键入新字符、删除字符等）时都会触发该事件侦听器。

但是，除了触发一个函数之外，它还传递了更改的 HTML 元素（在`e`此处引用）。然后我们可以获取 HTML 元素`e`并使用提取文本`e.target.value`并将其作为参数传递给该参数，该参数`handleOnChange`会将文本记录到浏览器控制台。

不同的 HTML 元素有不同的事件处理程序 -这是两个示例`onChange`，`onClick`但还有更多！[您可以在此处](https://www.w3schools.com/jsref/dom_obj_event.asp)找到所有 HTML 事件的列表。

通过将 HTML 事件与函数处理程序相结合，我们可以做各种很酷的事情！从服务器加载数据、向服务器发送数据、更新我们的视图等。

## React Hooks - useState 和 useEffect

好的，我们已经讨论了组合、数据传递和交互性。但是，我们的应用程序仍然很愚蠢。交互性将允许您在单击按钮等时运行一些代码，但是如果您想更新一些变量怎么办？

不幸的是，以下不起作用

```jsx
function DoesNotWork() {
    let myNumber = 0;
    
    function increment() {
        myNumber++;
    }
    
    return (
        <div>
            <p>{myNumber}</p>
            <button onClick={increment}>Increment!</button>
        </div>
    )
}
```

[运行代码](https://codesandbox.io/s/romantic-euclid-44hh8x)

无论点击多少次`Increment`，屏幕上显示的数字都会卡在`0`。这是因为当您像`myNumber`从 React 组件中更新常规变量时，即使值已更新，React 实际上也不会重新渲染 Web 应用程序的视图。它不会自动更新页面的 HTML 视图。

React Hooks 是“挂钩”到 React 组件的不同部分的函数，允许您执行诸如在变量值更改时更新视图，或在每次加载页面或更改变量时自动运行一些 JS 代码等操作，以及许多更酷的东西！我们将主要关注 95% 的时间使用的三个 React 钩子 - `useState`、`useEffect`和`useRef`。

## useState

很多时候，您希望 HTML 视图根据某些变量的值变化进行更新。我们可以使用该`useState`钩子来维护一个变量，该变量会在每次更改其值时自动重新渲染屏幕上显示的 HTML。这是一个例子：

```jsx
function ThisWorks() {
    // myNumber is the variable itself
    // setMyNumber is a function that lets us update the value
    // useState(0) initializes the React Hook 
    // with the starting value of 0
    const [myNumber, setMyNumber] = useState(0);
    
    function increment() {
        // Sets the new value to the old value + 1
        setMyNumber(myNumber + 1);
    }
    
    return (
        <div>
            <p>{myNumber}</p>
            <button onClick={increment}>Increment!</button>
        </div>
    )
}
```

[运行代码](https://codesandbox.io/s/intelligent-hoover-31bwmg)

如果您尝试运行上述代码，您将看到 Web 应用程序的视图自动更新以反映变量的新值。

`useState`在 React 中使用创建的变量称为状态变量。状态变量可以更新并自动更新应用程序的视图。这是另一个在输入框中使用状态变量的示例。

```jsx
function StateWithInput() {
    // myName is the variable
    // setMyName is the updater function
    // Create a state variable with initial value
    // being an empty string ""
    const [myName, setMyName] = useState("");
    
    function handleOnChange(text) {
        setMyName(text);
    }
    
    return (
        <div>
            <input type="text" onChange={(e) => handleOnChange(e.target.value)} />
            <p>Hello, {myName}!</p>
        </div>
    )
}
```

[运行代码](https://codesandbox.io/s/young-microservice-c1e5be?file=/src/App.js)

我们看到 HTML 上显示的文本随着输入框内容的变化而变化。伟大的！

关于 useState，我想说的最后一件事是，您还可以将它用于字符串和数字等基本类型。您还可以使用它们来存储数组和对象。但是，这里有一个警告。让我们看一个例子：

```jsx
function StateArrayDoesNotWork() {
  const [fruits, setFruits] = useState([]);
  const [currentFruit, setCurrentFruit] = useState("");

  function updateCurrentFruit(text) {
    setCurrentFruit(text);
  }

  function addFruitToArray() {
    fruits.push(currentFruit);
  }

  return (
    <div>
      <input type="text" onChange={(e) => updateCurrentFruit(e.target.value)} />
      <button onClick={addFruitToArray}>Add Fruit</button>

      <ul>
        {fruits.map((fruit, index) => (
          <li key={index}>{fruit}</li>
        ))}
      </ul>
    </div>
  );
}
```

[运行代码](https://codesandbox.io/s/kind-jasper-vbs0f9?file=/src/App.js)

如果您尝试运行它，您将看到屏幕上没有显示任何水果。另请注意，我们没有在`setFruits`任何地方使用该函数，而只是尝试`.push`使用`fruits`数组。

当我们尝试直接更新数组时，React 不会注册状态更改，这也被认为是无效的状态更新，可能导致应用程序出现意外行为。我们知道我们需要以某种方式使用`setFruits`，但是如何使用呢？答案是我们实际上需要创建水果数组的副本，将水果添加到其中，并将状态变量完全设置为新数组。下面的例子：

```jsx
function StateArray() {
  const [fruits, setFruits] = useState([]);
  const [currentFruit, setCurrentFruit] = useState("");

  function updateCurrentFruit(text) {
    setCurrentFruit(text);
  }

  function addFruitToArray() {
      // The spread operator `...fruits` adds all elements
      // from the `fruits` array to the `newFruits` array
      // and then we add the `currentFruit` to the array as well
      const newFruits = [...fruits, currentFruit]
      setFruits(newFruits);
  }

  return (
    <div>
      <input type="text" onChange={(e) => updateCurrentFruit(e.target.value)} />
      <button onClick={addFruitToArray}>Add Fruit</button>

      <ul>
        {fruits.map((fruit, index) => (
          <li key={index}>{fruit}</li>
        ))}
      </ul>
    </div>
  );
}
```

[运行效果](https://codesandbox.io/s/suspicious-monad-gpes3r?file=/src/App.js)

如果您尝试运行上述代码，您将看到它按预期工作。每次按下按钮时，输入框中的当前文本都会添加到数组中，这会导致 HTML 上显示的水果列表更新。您可以继续添加任意数量的水果！

> 对象也是如此。如果您的状态变量包含一个对象，您需要先创建该对象的副本，更新一个值，然后将状态变量完全设置为新对象。

## useEffect

所以我们现在可以管理状态，太好了！状态变化也会影响我们渲染的 HTML，也很棒！

但是，通常需要在第一次加载页面时自动运行一些代码——可能是从服务器或区块链获取数据——并且还需要在某个状态变量发生变化时自动运行一些代码。

这些类型的功能称为副作用。React 为我们提供了`useEffect`允许我们编写这些效果的钩子。`useEffect`接受两个参数 - 一个函数和一个依赖数组。函数是运行效果时运行的代码，依赖数组指定何时触发副作用。

考虑一个示例，当网站首次加载时，它想从服务器加载一些数据。这样做时，它希望向用户显示一个加载屏幕，然后在加载数据后，移除加载屏幕并显示实际内容。我们如何做到这一点？

```jsx

function LoadDataFromServer() {
  // Create a state variable to hold the data returned from the server
  const [data, setData] = useState("");
  // Create a state variable to maintain loading state
  const [loading, setLoading] = useState(false);

  async function loadData() {
    // Set `loading` to `true` until API call returns a response
    setLoading(true);

    // Imaginary function that performs an API call to load
    // data from a server
    const data = await apiCall();
    setData(data);

    // We have the data, set `loading` to `false`
    setLoading(false);
  }

  // loadData is the function that is run
  // An empty dependency array means this code is run
  // once when the page loads
  useEffect(() => {
    loadData();
  }, []);

  // Display `"Loading..."` while `loading` is `true`,
  // otherwise display `data`
  return <div>{loading ? "Loading..." : data}</div>;
}
```

[运行代码](https://codesandbox.io/s/practical-cache-ib3c0f?file=/src/App.js:186-756)

如果您从链接运行上述代码，您将看到它`Loading...`在屏幕上显示 5 秒钟，然后显示`ABCDEF`. 这是因为`apiCall`是一个等待 5 秒然后返回字符串的函数`ABCDEF`。

首次加载页面时的`useEffect`调用`loadData`（由于依赖项数组为空）和状态变量使 HTML 呈现适当的内容。

这对于在页面第一次加载时运行代码很有用，但是每次状态变量的值发生变化时运行一些代码呢？例如，当您在 Facebook 上搜索某人的姓名时，Facebook 如何在您每次添加/删除角色时获取并显示推荐？

你也可以`useEffect`通过在依赖数组中提供状态变量来做到这一点。每次该变量的值发生变化时，都会运行效果。

```jsx
function DependentEffect() {
  const names = ["Alice", "Bob", "Charlie", "David", "Emily"];

  const [recommendations, setRecommendations] = useState([]);
  const [searchText, setSearchText] = useState("");

  useEffect(() => {
    // If user is not searching for anything, don't show any recomendations
    if (searchText.length === 0) {
      setRecommendations([]);
    } 
    // Else, find recommendations
    else if (searchText.length > 0) {
      const newRecs = names.filter((name) =>
        name.toLowerCase().includes(searchText.toLowerCase())
      );
      setRecommendations(newRecs);
    }
  }, [searchText]);

  return (
    <div>
      <input type="text" onChange={(e) => setSearchText(e.target.value)} />
      <h2>Recommendations:</h2>
      <ul>
        {recommendations.map((rec, index) => (
          <li key={index}>{rec}</li>
        ))}
      </ul>
    </div>
  );
}
```

[运行代码](https://codesandbox.io/s/cold-darkness-eub0eh?file=/src/App.js)

如果您运行上述代码并尝试输入一些字母，您将看到推荐列表会随着您在搜索框中添加/删除新字符而自动更新。这是因为当你更新输入框时，`searchText`是通过`onChange`处理程序更新的，它触发了`useEffect`，它更新了`recommendations`列表，它更新了 HTML 视图。

您也可以类似地创建依赖于多个状态变量的副作用，而不仅仅是一个。如果任何因变量发生变化，就会产生副作用。您只需将更多状态变量添加到依赖项数组即可。

```jsx
useEffect(() => {
    // Some code
}, [stateVar1, stateVar2, stateVar3, andSoOn])
```


## useRef

`useRef`是另一个比较常用的 React 钩子。它与表面上非常相似`useState`，但有一些实际上非常重要的细微差别使得这个 React Hook 学习起来很重要。

`useRef`变量创建如下：

```jsx

function Component() {
    const myValue = useRef();
    
    function updateMyValue(newValue) {
        myValue.current = newValue;
    }
    
    function printMyValue() {
        console.log(myValue.current);
    }
}
```

### 1 - 没有重新渲染
与类似`useState`，`useRef`钩子还允许我们将变量存储在可以随时间更新的组件中。但是，与状态变量不同，更新 ref 变量的值不会导致 HTML 视图重新呈现。

因此，如果您有一个`useRef`变量并且您在 HTML 视图中显示它的值，则更新该变量不会更新 HTML 视图。

```jsx

function CounterWithRef() {
    const myNumber = useRef();
    
    function increment() {
        if (myNumber.current !== undefined) {
            myNumber.current += 1;
        } else {
            myNumber.current = 1;
        }
        console.log(myNumber.current);
    }
    
    return (
        <div>
            <p>{myNumber}</p>
            <button onClick={increment}>Increment!</button>
        </div>
    )
}
```

[运行代码](https://codesandbox.io/s/small-paper-9ecbfp?file=/src/App.js)

如果您运行上面的代码，您会注意到每次单击按钮时，值都会递增并打印在浏览器控制台中，但 HTML 视图实际上并没有更新。事实上，HTML 视图不显示任何内容，因为 is 的初始值，`myNumber.current`并且`undefined`由于 HTML 没有更新，因此`undefined`即使值实际上正在更新，它仍然保持与 HTML 相关的内容。

### 2 - 同步更新

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703170339.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703170410.png)

我们之前没有提到`useState`的是，当我们使用`setXYZ`函数更新状态变量时，它实际上并没有立即更新。

在 React 中为状态变量设置新值是异步发生的，这意味着如果您在将状态变量设置为新值后立即尝试使用它的值，您可能实际上看不到新值被反映，因为它是异步发生的。

我们再看一下使用时的Counter例子`useState`。

```jsx

function AsyncStateVariables() {
  const [number, setNumber] = useState(0);

  function increment() {
    setNumber(number + 1);
    console.log(number);
  }

  return (
    <div>
      <p>{number}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

[运行代码](https://codesandbox.io/s/reverent-lichterman-k4n448?file=/src/App.js)

当您运行它时，请注意视图上发生了什么以及控制台中发生了什么。当您第一次单击按钮时，状态变量应该更新为`1`- 这就是视图上发生的情况，网页显示`1`. 但是，如果您查看浏览器控制台，`0`则会打印该值而不是`1`. 当您继续单击按钮时，此模式将继续。

这是因为`setNumber`调用是异步运行的，当我们到达该`console.log(number)`行时，该值尚未更新，因此它会打印`number`. 当它确实被更新时，HTML 被重新渲染以显示新值。

`useRef`另一方面，允许同步更新。当您使用它更新引用变量的值时，`myVar.current = newValue`它会立即更新，并且没有延迟。这有时会派上用场。

### 3 - 引用 DOM 元素

让我们做的另一件很酷的事情`useRef`是它允许我们直接引用 DOM 元素。这是`useState`.

例如，您可以`input`使用直接引用元素`useRef`

```jsx

function InputFocus() {
  const inputRef = useRef();

  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return <input ref={inputRef} type="text" />;
}
```

[运行代码](https://codesandbox.io/s/input-focus-zntci?file=/src/App.js)

当您运行上面的示例时，您会注意到页面加载后，该`input`元素已经处于焦点位置，即您可以开始输入而无需先单击它。这是因为我们持有一个对`input`元素的引用，并且`useEffect`由于有一个空的依赖数组，它在页面加载时运行，它专注于`input`元素。


## React文件结构

太好了，如果你刚刚开始使用 React，我们已经介绍了你应该知道的主要概念。但到目前为止，我们只处理了孤立的组件示例。一个实际的 React 项目是什么样子的？

React 应用程序通常使用`create-react-app`（CRA）之类的工具创建。CRA 是一个命令行工具，可帮助您设置新的 React 项目并安装所有必需的依赖项，而无需手动创建所有样板。

当您使用 CRA 时，您最终会得到如下所示的文件结构。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703170819.png)

该`package.json`文件应该是可识别的。CRA 通过 Node.js 环境工作，并且package.json是创建所有依赖项和项目元数据的地方 - 与任何其他 Node.js 项目一样。

该`src/`文件夹包含组件和 CSS 样式，基本上是任何特定于 React 的代码。这里的主要组件是`App.js`，它是您第一次设置 React 应用程序时创建的自动生成的组件。`index.js`是 React 应用程序的主要入口点，但通常你不需要太多（或根本不需要）修改它。它只包含一些样板 React 代码，这些代码获取您的组件并将其转换为可以在浏览器中运行的实际 HTML 和 JS。

然后该`public/`文件夹默认只包含一个文件 - `index.html`. 你通常不会自己碰这个。这是一个超级简单的准系统 HTML 文件。当一个 React 应用程序运行时，React 会在后台执行一些魔法，它会获取所有组件和 Javascript 代码，将其转换为可以在浏览器中运行的实际 HTML 和 JS，并`index.html`用所有这些替换其中的内容。然后，更新`index.html`的就是向用户显示的内容。

如果您想将图像、字体、音乐等添加到您的网站，它们也会进入该`public/`文件夹。该`public/`文件夹基本上包含您希望在您的网站上直接访问的所有内容。

例如，如果您在文件夹中添加了一个名为的图像`avatar.png`，`public/`那么您可以在 React 组件中显示该图像，如下所示：

`<img src="/avatar.png" />`

虽然这可能看起来很奇怪，因为您的组件位于`src/`文件夹中而不是`public/`文件夹中 - 它工作的原因是因为图像与位于同一文件夹中`index.html`- 并且`index.html`是您的 React 代码实际结束的地方。因此，当使用 的相对路径引用图像时`/avatar.png`，它知道它`avatar.png`必须在`public`文件夹内。

## 后端部分

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703171108.png)

到目前为止，我们一直在讨论 React，以及它的所有前端功能。但是后端呢？

React 不是后端框架，因此如果您想创建自己的 API 后端，则必须使用 Node.js 和 Express 之类的东西建立一个单独的项目。然而这很麻烦，就好像后端和前端是同一个项目一样，你可能有很多代码可以在两者之间重用和共享。此外，维护两个项目总是比只维护一个项目更难。

输入，Next.js

接下来是 React 的元框架。这是什么意思？嗯，React 本身就是一个构建 Web 应用程序的框架。接下来是一个 React 框架，它还引入了一些 React 没有的附加功能。

如果你了解 React，那么 Next 90% 是完全一样的东西，你可以很快开始使用它，但是我想谈谈 Next 带来的这些额外功能。

首先，正如标题和介绍所暗示的那样，Next 允许您在单个项目中编写前端和后端代码。您使用 React 构建前端，并使用与使用 Express 类似的语法编写后端 API 端点 - 但都在同一个项目中。

其次，Next 使创建多页 Web 应用程序变得非常容易。React 最初旨在帮助创建单页应用程序（SPA），组件非常适合！但是，如果您的网站有多个页面怎么办？例如`https://learnweb3.io/`和`https://learnweb3.io/about`等等`https://learnweb3.io/tracks`。

为此，`React Router`引入了诸如此类的库，这使之成为可能，但也有点麻烦。“下一步”通过允许基于文件名的自动页面路由大大简化了这一点。

最后，Next 还具有服务器端渲染 (SSR) 和静态站点生成 (SSG)。这些不是我们将在我们的曲目中使用的功能，所以我不会在这里花太多时间，但如果您想了解更多关于它们的信息，请随时阅读推荐阅读。

## Next中的路由

在讨论创建后端服务器之前，我们将讨论路由，因为这将帮助您了解它是如何工作的。

类似于`create-react-app`，Next 有一个名为的工具`create-next-app`，可以自动帮助您轻松设置新的 Next.js 项目。

当您创建一个新的 Next.js 项目时，您最终会得到一个如下所示的文件结构：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703171455.png)

这是很多文件！但别担心，其中很多与我们已经讨论过的 React 类似。

该`public/`文件夹的工作方式完全相同，但不包含`index.html`文件。但是，如果您想添加图像、图标、字体、音乐等，您可以将它们全部放在`public/`文件夹中。

该`styles/`文件夹是一个很好的补充，为您的所有 CSS 文件提供了一个专用位置。

这`pages/`就是伟大的。`_app.js`是一个自动生成的文件，通常您不会自己接触它，并设置了一些样板代码，允许 Next 呈现正确的组件。

`pages/index.js`是您网站的主页。基本上，文件`pages`夹中的每个文件都是您网站的路线。遵循 Javascript/HTML 样式的命名约定，这意味着`index`文件是“主”文件。因此，`pages/index.js`当您第一次打开您的网站时将加载的视图。

如果您在`pages`文件夹下添加更多文件，例如一个名为`about.js`- 的文件，它将在`YOUR_DOMAIN/about`（有趣的事实：LearnWeb3 的网站是使用 Next 创建的，这正是我们`https://learnweb3.io/about`页面的工作方式）。

这很棒，因为您不必处理诸如 React Router 之类的事情，并且构建多页网站就像在`pages/`文件夹下创建一个新文件一样简单，Next 会自动为您生成基于文件名的路由。

您还可以通过在文件夹下创建子文件夹来进行多级路由pages/。例如，类似`pages/tracks/freshman.js`的东西会有 route `YOUR_DOMAIN/tracks/freshman`。

## 在 Next 中编写 API

然而，下面有一个特殊的文件夹，`pages/`它也是自动生成的。`pages/api`文件夹。与呈现 HTML 视图的常规页面不同，`pages/api`文件夹下的任何内容都充当 API 端点。

让我们看一下自动生成的`pages/api/hello.js`文件：

```jsx

export default function handler(req, res) {
  res.status(200).json({ name: 'John Doe' })
}
```

这是一个非常类似于 Express 的功能。如果你要去`YOUR_DOMAIN/api/hello`- 而不是呈现 HTML 视图，这将返回一个 JSON 对象`{name: 'John Doe'}`- 这是一个超级简单的 API 端点。

与常规的 HTML 视图类似，您可以`pages/api`通过创建新文件来创建 API 端点，并且端点路由基于文件名。

## 结论
我希望这篇文章对您有所帮助，并且可以作为速成课程。我故意在这里更多地关注 React 而不是 Next，因为习惯前端部分对我们来说将比后端部分更相关。此外，后端代码基本上是常规的 Javascript，而前端是 JSX，我想让你更熟悉它。


## 读物/视频
- [30 分钟学会 React](https://www.youtube.com/watch?v=hQAHSlTtcmY)
- [Next.js in 100 Seconds // 加上完整的教程](https://www.youtube.com/watch?v=Sklc_fQBmcs)
- [Scrimba 的完整 React 课程](https://scrimba.com/learn/learnreact)
- [Next.js 速成班](https://www.youtube.com/watch?v=mTz0GXj8NN0)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
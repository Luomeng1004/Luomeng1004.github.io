---
title: 深入浅出React和Redux——React组件的数据
date: 2022-05-25 14:25:16
tags: React
categories: 前端/移动
---



> “差劲的程序员操心代码，优秀的程序员操心数据结构和它们之间的关系。”
>
> —Linus Torvalds，Linux创始人



毫无疑问，如何组织数据是程序的最重要问题。

React组件的数据分为两种，prop和state，无论prop或者state的改变，都可能引发组件的重新渲染，那么，设计一个组件的时候，什么时候选择用prop什么时候选择用state呢？其实原则很简单，prop是组件的对外接口，state是组件的内部状态，对外用prop，内部用state。



<!--more-->



### React的prop

在React中，prop（property的简写）是从外部传递给组件的数据，一个React组件通过定义自己能够接受的prop就定义了自己的对外公共接口。

每个React组件都是独立存在的模块，组件之外的一切都是外部世界，外部世界就是通过prop来和组件对话的。



#### 给prop赋值

```html
<SampleButton
  id="sample" borderWidth={2} onClick={onButtonClick}
  style={{color: "red"}}
/>
```

React组件的prop所能支持的类型则丰富得多，除了字符串，可以是任何一种JavaScript语言支持的数据类型。



#### 读取prop值

```javascript
class Counter extends Component {
  constructor(props) {
    super(props);

    this.onClickIncrementButton = this.onClickIncrementButton.bind(this);
    this.onClickDecrementButton = this.onClickDecrementButton.bind(this);

    this.state = {
      count: props.initValue || 0
    }
  }
```

如果一个组件需要定义自己的构造函数，一定要记得在构造函数的第一行通过super调用父类也就是React.Component的构造函数。如果在构造函数中没有调用super（props），那么组件实例被构造之后，类实例的所有成员函数就无法通过this.props访问到父组件传递过来的props值。很明显，给this.props赋值是React.Component构造函数的工作之一。



#### propTypes检查

既然prop是组件的对外接口，那么就应该有某种方式让组件声明自己的接口规范。简单说，一个组件应该可以规范以下这些方面：

- 这个组件支持哪些prop；

- 每个prop应该是什么样的格式。

React通过propTypes来支持这些功能。

在ES6方法定义的组件类中，可以通过增加类的propTypes属性来定义prop规格，这不只是声明，而且是一种限制，在运行时和静态代码检查时，都可以根据propTypes判断外部世界是否正确地使用了组件的属性。

比如，对于Counter组件的propTypes定义代码如下：

```javascript
Counter.propTypes = {
  caption: PropTypes.string.isRequired,
  initValue: PropTypes.number
};
```

其中要求caption必须是string类型，initValue必须是number类型。可以看到，两者除了类型不同之外，还有一个区别：caption带上了isRequried，这表示使用Counter组件必须要指定caption；而initValue因为没有isRequired，则表示如果没有也没关系。

很明显，有了propTypes的检查，可以很容易发现对prop的不正确使用方法，可尽早发现代码中的错误。

从上面可以看得出来propTypes检查可以防止不正确的prop使用方法，那么如果组件根本就没有定义propTypes会怎么样呢？

可以尝试在src/Counter.js文件中删除掉那一段给Counter.propTypes赋值的语句，在浏览器Console里可以看到红色警告不再出现。可见，没有propTypes定义，组件依然能够正常工作，而且，即使在上面propTypes检查出错的情况下，组件依旧能工作。也就是说propTypes检查只是一个辅助开发的功能，并不会改变组件的行为。

propTypes虽然能够在开发阶段发现代码中的问题，但是放在产品环境中就不大合适了。

首先，定义类的propTypes属性，无疑是要占用一些代码空间，而且propTypes检查也是要消耗CPU计算资源的。其次，在产品环境下做propTypes检查没有什么帮助，毕竟，propTypes产生的这些错误信息只有开发者才能看得懂，放在产品环境下，在最终用户的浏览器Console中输出这些错误信息没什么意义。

所以，最好的方式是，开发者在代码中定义propTypes，在开发过程中避免犯错，但是在发布产品代码时，用一种自动的方式将propTypes去掉，这样最终部署到产品环境的代码就会更优。现有的babel-react-optimize就具有这个功能，可以通过npm安装，但是应该确保只在发布产品代码时使用它。



### React的state

驱动组件渲染过程的除了prop，还有state，state代表组件的内部状态。由于React组件不能修改传入的prop，所以需要记录自身数据变化，就要使用state。

#### 初始化state

```javascript
constructor(props) {
  ...
  this.state = {
    count: props.initValue || 0
  }
}
```

组件的state必须是一个JavaScript对象，不能是string或者number这样的简单数据类型，即使我们需要存储的只是一个数字类型的数据，也只能把它存作state某个字段对应的值。

#### 读取和更新state

```javascript
onClickIncrementButton() {
  this.setState({count: this.state.count + 1});
}
```

在代码中，通过this.state可以读取到组件的当前state。值得注意的是，我们改变组件state必须要使用this.setState函数，而不能直接去修改this.state



### prop和state的对比

总结一下prop和state的区别：

- prop用于定义外部接口，state用于记录内部状态；

- prop的赋值在外部世界使用组件时，state的赋值在组件内部；

- 组件不应该改变prop的值，而state存在的目的就是让组件来改变的。

组件的state，就相当于组件的记忆，其存在意义就是被修改，每一次通过this.setState函数修改state就改变了组件的状态，然后通过渲染过程把这种变化体现出来。

但是，组件是绝不应该去修改传入的props值的，我们设想一下，假如父组件包含多个子组件，然后把一个JavaScript对象作为props值传给这几个子组件，而某个子组件居然改变了这个对象的内部值，那么，接下来其他子组件读取这个对象会得到什么值呢？当时读取了修改过的值，但是其他子组件是每次渲染都读取这个props的值呢？还是只读一次以后就用那个最初值呢？一切皆有可能，完全不可预料。也就是说，一个子组件去修改props中的值，可能让程序陷入一团混乱之中，这就完全违背了React设计的初衷。

还记得第1章中我们看到的那个公式吗？

UI＝render（data）

React组件扮演的是render函数的角色，应该是一个没有副作用的纯函数。修改props的值，是一个副作用，组件应该避免。

严格来说，React并没有办法阻止你去修改传入的props对象。所以，每个开发者就把这当做一个规矩，在编码中一定不要踩这儿红线，不然最后可能遇到不可预料的bug。

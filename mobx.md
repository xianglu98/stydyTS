## mobx学习
# mobx学习笔记
[TOC]
版本：5+
测试：https://jsfiddle.net/mweststrate/wv3yopo0/
React 和 MobX 是一对强力组合。React 通过提供机制把应用状态转换为可渲染组件树并对其进行渲染。而MobX提供机制来存储和更新应用状态供 React 使用。

# 入门
```
// 实际使用中，使用import引入
// import { observable, computed, action } from 'mobx'
// import { observer } from 'mobx-react'
// import React, {Component} from 'react';
// import ReactDOM from 'react-dom';

const {observable, computed, action} = mobx;
const {observer} = mobxReact;
const {Component} = React;

class Todo {
    id = Math.random();
    @observable title;
    @observable finished = false;
    @action setFinished() {
				this.finished = !this.finished
		}
    constructor(title) {
        this.title = title;
    }
}

class TodoList {
    @observable todos = [];
    @action addTodo(todo) {
    		this.todos.push(todo)
    };
    @computed get unfinishedTodoCount() {
        return this.todos.filter(todo => !todo.finished).length;
    }
}

@observer
class TodoListView extends Component {

		state = {
			value:'add a todo'
		}
    
		handleAdd(){
    	if(this.state.value){
      	this.props.todoList.addTodo(new Todo(this.state.value))
      	this.setState({
      			value:''
      	})
      }
    }
  
    render() {
        return <div>
            <input 
              value={this.state.value} 
              onChange={(e) => this.setState({value:e.target.value})}
            />
            <button onClick={() => this.handleAdd() }>add</button>
            <ul>
                {this.props.todoList.todos.map(todo => 
                    <TodoView todo={todo} key={todo.id} />
                )}
            </ul>
            Tasks left: {this.props.todoList.unfinishedTodoCount}
           <mobxDevtools.default />
        </div>
    }
}

const TodoView = observer(({todo}) => 
    <li>
        <input
            type="checkbox"
            checked={todo.finished}
            onClick={() => todo.setFinished()}
        />{todo.title}
    </li>
);

const store = new TodoList();
[new Todo("Get Coffee"),new Todo("Write simpler code")].forEach(todo => {
	store.addTodo(todo)
})

ReactDOM.render(<TodoListView todoList={store} />, document.getElementById('mount'));
```

# observable
由mobx引入，用来定义可观察状态的变量；数据结构可以是js基本数据类型、数组、对象（普通对象和类实例）、映射等。
# observable(value)
```
这是observable的第一种用法。
// observable监听对象

// 1.引用数据类型的监听
const person = observable({
    firstName: "Clive Staples",
    lastName: "Lewis"
});
// 严格模式下，会报错；严格模式下修改state必须使用action
// person.firstName = "C.S.";
const editPerson = action(() => person.firstName = "C.S.")
editPerson()

const list = observable([1, 2, 4]);
// list[2] = 3;
const editList = action(() => list[2] = 3)
editList()

const map = observable.map({ key: "value"});
// map.set("key", "new value");
const editMap = action(() => map.set("key", "new value"))
editMap()

// 2.基本数据类型的监听
const temperature = observable.box(20);
// temperature.set(25);
const editTemperature = action(() => temperature.set(25))
editTemperature()
```

@observable classProperty = value
这是observable的第二种用法，用于class中修饰起属性。
```
import {observable, action} from 'mobx';
class Store {
  @observable count = 0;
  @action add = () => {
    this.count++;
  }
}

const store = new Store();
store.add();
```

action
基本用法
- action(fn)
- action(name, fn)
- @action classMethod() {}
- @action(name) classMethod () {}
- @action boundClassMethod = (args) => { body }
- @action(name) boundClassMethod = (args) => { body }
- @action.bound classMethod() {}
参数name表示对action的描述，可以不传；@action.bound用来绑定this，相当于bind，不可以用于箭头函数。当在react中以解构的形式从store中取值，store中的action必须使用bound来绑定this或者action直接使用箭头函数。
```
// action用法：普通对象
const person = observable({
    firstName: "Clive Staples",
    lastName: "Lewis"
});
// 严格模式下，会报错；严格模式下修改state必须使用action
// person.firstName = "C.S.";
const editPerson = action(() => person.firstName = "C.S.")
editPerson()

const editPerson  = action(function(){
  person.firstName = "C.S."
})
editPerson()

// @action用法：类方法
class Person{
  @observable firstName = "Clive Staples"
  lastName = "Lewis"
  
  // 使用1:不绑定this
  // @action editPerson(){
  //   this.firstName = "C.S."
  // }
  
  // 使用2:绑定this
  @action.bound editPerson(){
    this.firstName = "C.S."
  }
}
const person = new Person()
person.editPerson()

// 使用方法2
class Person{
  @observable firstName;
  lastName = "Lewis";
  // 调用constructor对属性进行初始化时，不需要使用action
  constructor(firstName){
    this.firstName = firstName
  }
  @action editPerson(firstName){
    this.firstName = firstName
  }
}
const person = new Person("Clive Staples")
person.editPerson("C.S.")
```

处理异步action
action只会对当前运行的函数起作用，而不会对函数中所调用的函数做出反应。因此当action中出现setTimeout、promise 的 then 或 async 语句，并且在回调函数中某些状态改变了；那么action是无法反应的。
解决问题的常见办法是：1.action包裹回调函数；2.runInaction包裹状态修改的部分；3.flows配和生成器。以下使用官方demo：
```
mobx.configure({ enforceActions: true }) // 不允许在动作之外进行状态修改

class Store {
    @observable githubProjects = []
    @observable state = "pending" // "pending" / "done" / "error"

    @action
    fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        fetchGithubProjectsSomehow().then(
            projects => {
                const filteredProjects = somePreprocessing(projects)
                this.githubProjects = filteredProjects
                this.state = "done"
            },
            error => {
                this.state = "error"
            }
        )
    }
}
```
# 方法1:
```
mobx.configure({ enforceActions: true })

class Store {
    @observable githubProjects = []
    @observable state = "pending" // "pending" / "done" / "error"

    @action
    fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        fetchGithubProjectsSomehow().then(
            // 内联创建的动作
            action("fetchSuccess", projects => {
                const filteredProjects = somePreprocessing(projects)
                this.githubProjects = filteredProjects
                this.state = "done"
            }),
            // 内联创建的动作
            action("fetchError", error => {
                this.state = "error"
            })
        )
    }
}
```

# 方法2:
```
mobx.configure({ enforceActions: true })

class Store {
    @observable githubProjects = []
    @observable state = "pending" // "pending" / "done" / "error"

    @action
    async fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        try {
            const projects = await fetchGithubProjectsSomehow()
            const filteredProjects = somePreprocessing(projects)
            // await 之后，再次修改状态需要动作:
            runInAction(() => {
                this.state = "done"
                this.githubProjects = filteredProjects
            })
        } catch (error) {
            runInAction(() => {
                this.state = "error"
            })
        }
    }
}
```

# 方法3:
```
mobx.configure({ enforceActions: true })

class Store {
    @observable githubProjects = []
    @observable state = "pending"

    fetchProjects = flow(function * () { // <- 注意*号，这是生成器函数！
        this.githubProjects = []
        this.state = "pending"
        try {
            const projects = yield fetchGithubProjectsSomehow() // 用 yield 代替 await
            const filteredProjects = somePreprocessing(projects)
            // 异步代码块会被自动包装成动作并修改状态
            this.state = "done"
            this.githubProjects = filteredProjects
        } catch (error) {
            this.state = "error"
        }
    })
}
```

# computed
```
// 使用方法1
import {observable, computed} from "mobx";

class Person {
    @observable firstName = "Clive Staples";
    @observable lastName = "Lewis";

    @computed get fullName() {
        return this.firstName + " " + this.lastName;
    }
}

// 使用方法2:observable.object 和 extendObservable 都会自动将 getter 属性推导成计算属性
const person = observable.object({
    firstName: "Clive Staples",
    lastName: "Lewis",
    get fullName() {
        return this.firstName + " " + this.lastName;
    }
})
```

# autorun
autorun在初始化时，自动执行一次；当函数内观察的数据变化时，再次执行。
```
import { observable, autorun } from 'mobx';

const count = observable(0);

autorun(() => {
  console.log(count.get());
});
count.set(10)
// 0
// 10
```

mobx-react
单组件使用store
使用@observer装饰的react组件将转换成一个监听者，当observable修饰的数据变化，react组件就会重新渲染。
```
import React from 'react';
import { observable, useStrict, action } from 'mobx';
import { observer } from 'mobx-react';
useStrict(true);

class Store {
  @observable num = 0;
  @action addNum = () => {
    this.num++;
  };
}

const store = new Store();

@observer
export default class App extends React.Component {

  render() {
    return (
      <div>
        <p>{store.num}</p>
        <button onClick={store.addNum}>+1</button>
      </div>
    )
  }
}
```

子组件store通信
多组件通信：操作同一个store对象。
```
class Store {
  @observable num1 = 0;
  @observable num2 = 100;

  @action addNum1 = () => {
    this.num1 ++;
  };
  @action addNum2 = () => {
    this.num2 ++;
  };
  @computed get total() {
    return this.num1 + this.num2;
  }
}

// 定义一个store对象，供多个组件使用
const store = new Store();

const AllNum = observer((props) => <div>num1 + num2 = {props.store.total}</div>);

const Main = observer((props) => (
  <div>
    <p>num1 = {props.store.num1}</p>
    <p>num2 = {props.store.num2}</p>
    <div>
      <button onClick={props.store.addNum1}>num1 + 1</button>
      <button onClick={props.store.addNum2}>num2 + 1</button>
    </div>
  </div>
));

@observer
export default class App extends React.Component {

  render() {
    return (
      <div>
        <Main store={store} />
        <AllNum store={store} />
      </div>
    );
  }
}
```


根组件store注入
Provider以组件的形式存在，用来包裹最外层组件节点，并且传入 store（通过）context 传递给后代组件。
@inject为了使被装饰的组件以 props 的形式获取到 Provider 传递过来的数据。
```
import TodoListView from './TodoListView';
import store from './store';
import {Provider} from 'mobx-react';

export default class App extends Component {
  // 将 store 传给 Provider
  render() {
    return (
      <Provider todolist={store}>
        <TodoListView />
      </Provider>
    );
  }
};
```

```
import {observable, computed, action} from 'mobx';

class Todo {
    // 定义一个 TODO 项目的类，id 是随机数，没有使用@observable 装饰可以理解为是只读的
    id = Math.random();
    // todo 的 title 及完成状态 finished 是可被观察及更新的，同时 finished 的初始状态为 false
    @observable title;
    @observable finished = false;
  	@action setFinished(){
      this.finished = !this.finished
    }
    // 构造函数接收title
    constructor(title) {
        this.title = title;
    }
}
// TODO List 类
class TodoList {
    // 可被观察的待办项 todos
    @observable todos = [];
    // 计算属性，重可观察属性 todos 衍生出来，返回没有完成的待办项的个数
    @computed get unfinishedTodoCount() {
        return this.todos.filter(todo => !todo.finished).length;
    }
    // 动作用来更新 todos 属性的值，添加待办项
    @action
    addTodo = title => {
        if (!title) return;
        this.todos.push(new Todo(title));
    }
}

// 我们创建TodoList 对象，在手动的添加两个待办项，此处的 store 对象可以理解为是一个单例，在将其引用暴露出去
const store = new TodoList();
store.todos.addTodo('Get Coffee');
store.todos[0].setFinished();

export default store;
```



```
// 方法1:class组件的注入
import {observer, inject} from 'mobx-react';
const TodoView = ({todo}) => (
    <li>
        <input
            type="checkbox"
            checked={todo.finished}
            onChange={() => {todo.setFinished()}}
        />
        {todo.title}
    </li>
)

@inject('todolist') // 或者 @inject(({todolist,otherstore}) => ({todolist, otherstore}))
@observer
export default class TodoListView extends Component {
    state = {
        title: ''
    }
    changeTitle = e => {
        let title = e.target.value;
        this.setState({title});
    }
    // 调用 store 中的 addTodoaction 更新 store 里面的数据
    submit = () => {
        this.props.todolist.addTodo(this.state.title);
    }
    render() {
        return (
            <div>
                <input type="text" value={this.state.title} onChange={this.changeTitle} />
                <button onClick={this.submit}>submit</button>
                <ul>
                    {this.props.todolist.todos.map(todo => (
                        <TodoView todo={todo} key={todo.id} />
                    ))}
                </ul>
                Tasks left: {this.props.todolist.unfinishedTodoCount}
            </div>
        );
    }
}
```

```
// 方法2:function组件的注入
import {observer, inject} from 'mobx-react';
import React, { useState } from 'react';
import { TodoList } from './store';
const TodoView = ({todo}) => (
    <li>
        <input
            type="checkbox"
            checked={todo.finished}
            onChange={() => {todo.setFinished()}}
        />
        {todo.title}
    </li>
)

const TodoListView:React.FC<{ todolist: TodoList }> = ({todolist}) => {
  	const [title,setTitle]  = useState('')
    changeTitle = e => {
      	setTitle(e.target.value)
    }
    // 调用 store 中的 addTodoaction 更新 store 里面的数据
    submit = () => {
        todolist.addTodo(title);
    }
    render() {
        return (
            <div>
                <input type="text" value={title} onChange={changeTitle} />
                <button onClick={submit}>submit</button>
                <ul>
                    {todolist.todos.map(todo => (
                        <TodoView todo={todo} key={todo.id} />
                    ))}
                </ul>
                Tasks left: {todolist.unfinishedTodoCount}
            </div>
        );
    }
}

// <Provider todolist={store}>:在这里注入
export default inject('todolist')(observer(TodoListView));
```


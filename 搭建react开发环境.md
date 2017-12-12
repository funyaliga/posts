```
yarn webpack webpack-dev-server

yarn add css-loader style-loader image-loader

yarn add react react-dom babel-preset-react babel-preset-es2015 babel-loader babel-core babel-plugin-transform-runtime babel-preset-stage-0

```

```
vi webpack.config.js
```



webpack.js:

```javascript
var webpack = require("webpack")
var path = require("path")
module.exports = { 
    entry: "./src/entry.js", // 项目打包入口文件
    output: {
        path: __dirname,
        filename: "./build/bundle.js" // 项目打包出口文件
    },
    module: {
        loaders: [
            {
                test: /\.js[x]?$/,
                exclude: /node_modules/,
                loader: 'babel-loader',
            },
            {
                test: /\.css$/,
                loader: 'style-loader!css-loader'
            },
            {
                test: /\.(png|jpg)$/,
                loader: 'url-loader?limit=8192'
            }
        ]
    }
};

```



package.json:

```json
"scripts": {
	"test": "echo \"Error: no test specified\" && exit 1",
	"start": "webpack-dev-server --devtool eval --progress --colors --hot",
	"build": "webpack"
},
```



### 安装antd ###

```
yarn add antd babel-plugin-import
```

.babelrc：

```json
{
    "presets": ["es2015", "stage-0", "react"],
    "plugins": [
        "transform-runtime",
        ["import", {
            "libraryName": "antd",
            "style": "css"
        }]
    ]
}
```



### 支持 async/await

```
npm install --save babel-polyfill
```

```javascript
// app.js
import 'babel-polyfill'
```





### 运行 ###

— src
​      — entry.js
— .babelrc
— index.html
— package.json
— webpack.config.js



entry.js: 

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import { Button } from 'antd'

class App extends React.Component {
    render() {
        return (
            <div>
                <Button>按钮</Button>
            </div>
        );
    }
}

ReactDOM.render(
    <App />,
    document.getElementById('app')
)
```



index.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title></title>
</head>
<body>
    <div id="app"></div>
    <script src="build/bundle.js"></script>
</body>
</html>
```



### react-router-v4 ###

```
yarn add react-router react-router-dom react-router-config
```



package.json:

```javascript
"start": "webpack-dev-server --devtool eval --progress --colors --hot --history-api-fallback", // 加上--history-api-fallback
```



exact 完全匹配

```react
<Route path="/" exact component={HomePage} />
```



Switch 唯一匹配

```react
<Switch>
  <Route path="/" exact component={HomePage} />
  <Route path="/users/add" component={UsersAddPage} />
  <Route path="/users" exact component={UsersPage} />
</Switch>
```



### 支持Decorators（修饰器）

```
yarn add babel-plugin-transform-decorators-legacy
```

.babelrc:

```
{
    "plugins": [
        "transform-decorators-legacy",
    ]
}
```





### redux

```
yarn add redux react-redux
```



小例子：

```react
// index.js
import React from 'react'
import AddTodo from './AddTodo'
import VisibleTodoList from './VisibleTodoList'
import Footer from './Footer'

const App = () => (
    <div>
        <AddTodo />
        <VisibleTodoList />
        <Footer />
    </div>
)

export default App
```

```react
// AddTodo.js
import React, { PropTypes, Component } from 'react'
import { Link } from 'react-router-dom'
import { connect } from 'react-redux'
import { addTodo } from '../../actions'

@connect()
class Index extends Component {
    constructor(props) {
        super(props)
    }

    input = null

    render() {
        return (
            <div>
                <form onSubmit={e => {
                    e.preventDefault()
                    if (!this.input.value.trim()) {
                        return
                    }
                    this.props.dispatch(addTodo(this.input.value))
                    this.input.value = ''
                }}>
                    <input ref={node => { this.input = node }} />
                    <button type="submit">Add Todo</button>
                </form>
            </div>
        )
    }
}

export default Index
```

```react
// VisibleTodoList.js
import React, { Component } from 'react'
import { connect } from 'react-redux'
import { toggleTodo } from '../../actions'

const getVisibleTodos = (todos, filter) => {
    switch (filter) {
        case 'SHOW_ALL':
            return todos
        case 'SHOW_COMPLETED':
            return todos.filter(t => t.completed)
        case 'SHOW_ACTIVE':
            return todos.filter(t => !t.completed)
        default:
            throw new Error('Unknown filter: ' + filter)
    }
}
@connect(
    ({todos, visibilityFilter}) => ({
        todos: getVisibleTodos(todos, visibilityFilter)
    }),
)
class Index extends Component {
    constructor(props) {
        super(props)
    }
    render() {
        return (
            <ul>
                {this.props.todos.map(todo =>
                    <li
                        key={todo.id}
                        onClick={() => this.props.dispatch(toggleTodo(todo.id))}
                        style={{
                            textDecoration: todo.completed ? 'line-through' : 'none'
                        }}
                    >
                        {todo.text}
                    </li>
                )}
            </ul>
        )
    }
}

export default Index
```

```react
// Footer.js
import React, { Component } from 'react'
import FilterLink from './FilterLink'

class Index extends Component {
    render() {
        return (
            <p>
                Show:
                {
                    [
                        <FilterLink key="SHOW_ALL" filter="SHOW_ALL">All</FilterLink>,
                        <FilterLink key="SHOW_ACTIVE" filter="SHOW_ACTIVE">Active</FilterLink>,
                        <FilterLink key="SHOW_COMPLETED" filter="SHOW_COMPLETED">Completed</FilterLink>,
                    ].reduce((prev, curr) => [prev, ', ', curr])}
                }
            </p>
        )
    }
}

export default Index
```

```react
// actions.js
let nextTodoId = 0
export const addTodo = (text) => ({
    type: 'ADD_TODO',
    id: nextTodoId++,
    text
})

export const setVisibilityFilter = (filter) => ({
    type: 'SET_VISIBILITY_FILTER',
    filter
})

export const toggleTodo = (id) => ({
    type: 'TOGGLE_TODO',
    id
})
```

```react
// reducers.js
const visibilityFilter = (state = 'SHOW_ALL', action) => {
    switch (action.type) {
        case 'SET_VISIBILITY_FILTER':
            return action.filter
        default:
            return state
    }
}

const todos = (state = [], action) => {
    switch (action.type) {
        case 'ADD_TODO':
            return [
                ...state,
                {
                    id: action.id,
                    text: action.text,
                    completed: false
                }
            ]
        case 'TOGGLE_TODO':
            return state.map(todo =>
                (todo.id === action.id)
                    ? { ...todo, completed: !todo.completed }
                    : todo
            )
        default:
            return state
    }
}


import { combineReducers } from 'redux'
const todoApp = combineReducers({
    todos,
    visibilityFilter
})

export default todoApp
```

```react
// app.js

import React from 'react'
import ReactDOM from 'react-dom'
import reducer from './reducers'
import { createStore } from 'redux'
const store = createStore(reducer)

ReactDOM.render(
    <Provider store={store}>
        <BrowserRouter>
            <App />
        </BrowserRouter>
    </Provider>
    ,
    document.getElementById('app')
)
```



#### Store

- `getState() `  // 返回应用当前的 state 树
- `dispatch(action) ` // 分发 action
- `subscribe(listener)` // 添加一个变化监听器
- `replaceReducer(nextReducer) `  // 替换 store 当前用来计算 state 的 reducer。



redux流程： 

上：原始
下：使用中间件middleware

![avatar](https://raw.githubusercontent.com/funyaliga/posts/master/redux.png)



中间件流程： 

```js
const test1 = store => next => action => {
	console.log('before test1')
	next(action)
    console.log('next test1')
}
const test2 = store => next => action => {
	console.log('before test2')
	next(action)
    console.log('next test2')
}

createStore(
  reducer,
  preloadedState,
  applyMiddleware(
    test1,
    test2,
  )
)

// before test1
// before test2
// next test2
// next test1
```



![avatar](https://raw.githubusercontent.com/funyaliga/posts/master/redux-middleware.png)


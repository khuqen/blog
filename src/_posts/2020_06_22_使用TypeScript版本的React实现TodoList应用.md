---
category: Web
tags:
  - React
  - TypeScript
date: 2020-06-22
title: 使用TypeScript版本的React实现TodoList应用
vssue-title: 使用TypeScript版本的React实现TodoList应用
---

使用TS+React实现一个简单的TodoList应用，目的是熟悉和入门TypeScript和React

<!-- more -->

# 使用TypeScript版本的React实现TodoList应用



## 新建项目

1. 确保你安装了较新版本的[Node.js](https://nodejs.org/en/)

2. 创建一个新的项目

   ```shell
   npx create-react-app todo-react-ts --template typescript
   ```

3. 删除多余的文件

   ```shell
   cd todo-react-ts
   rm src/*
   ```

4. 新建下列文件

   ```shell
   touch src/index.tsx src/App.tsx
   ```

   并分别填入下面的内容：

   ```tsx
   // index.tsx
   import React from 'react';
   import ReactDOM from 'react-dom';
   import App from './App';
   ReactDOM.render(
     <App />,
     document.getElementById('root')
   );
   
   // App.tsx
   import React from 'react';
   const App: React.FC = () => {
       return <div>Hello World!</div>
   };
   export default App;
   ```

5. 执行`npm start`，在浏览器中如下图所示则表示环境准备成功。

   ![÷](https://cdn.jsdelivr.net/gh/khuqen/blog@master/src/assets/image-20200425141447034.png)

## 组件组成

![image-20200425180951425](https://cdn.jsdelivr.net/gh/khuqen/blog@master/src/assets/image-20200425180951425.png)

应用组件结构如下:

```
App
--TodoList
---- TodoListItem
--AddTodoForm
```



## 实现TodoListItem组件

1. 在`src`下新建一个`TodoListItem.tsx`文件，一个`types.ts`文件，一个`TodoListItem.css`文件

   ```shell
   touch src/TodoListItem.tsx
   touch src/types.ts
   touch	src/TodoListItem.css
   ```

2. 写入下列的内容

   ```tsx
   // TodoListItem.tsx
   
   import React from 'react';
   import { Todo } from './types'
   import './App.css';
   
   interface TodoListItemProps {
       todo: Todo
   }
   
   export const TodoListItem: React.FC<TodoListItemProps> = ({ todo }) => {
       return (
           <li>
               <label className={todo.complete ? "complete" : undefined}>
                   <input type="checkbox" checked={todo.complete} />
                   {todo.text}
               </label>
           </li>
       );
   }
   
   
   // types.ts
   
   export type Todo = {
       text: string;
       complete: boolean;
   }
   
   // TodoListItem.css
   .complete {
       text-decoration: line-through;
   }
   ```

   刚刚我们定义一个函数组件`TodoListItem`,  从`interface	`可以看出，它接受一个`Todo`类型的属性，`Todo`有两个字段，分别是`text`用于展示文本内容，`complete`用来表示是否完成，它控制`label`样式和`checkbox`的状态。

3. 在`App.tsx`中引用我们定义的`TodoListItem`组件

   ```tsx
   import React from 'react';
   import { TodoListItem } from './TodoListItem';
   import { Todo } from './types'
   
   const todos: Todo[] = [
       {text: "Javascript", complete: true},
       {text: "Typescript", complete: false}
   ]; 
   
   const App: React.FC = () => {
       return (
           <div>
               <TodoListItem  todo = {todos[0]}/>
               <TodoListItem  todo = {todos[1]}/>
           </div>
       );
   };
   
   export default App;
   ```

4. 最终效果如下：

   ![image-20200425161101084](https://cdn.jsdelivr.net/gh/khuqen/blog@master/src/assets/image-20200425161101084.png)

## 实现TodoList组件

1. 新建`TodoList.tsx`

   ```shell
   touch src/TodoList.tsx
   ```

2. 在`TodoList.tsx`写入下列内容

   ```tsx
   import React from 'react';
   import { TodoListItem } from './TodoListItem';
   import { Todo, ToggleTodo } from './types'
   
   interface TodoListProps {
       todos: Todo[],
       toggleTodo: ToggleTodo // type ToggleTodo = (selectedTodo: Todo) => void
   };
   
   export const TodoList: React.FC<TodoListProps> = ({ todos, toggleTodo }) => {
       return (
           <ul>
               {todos.map(todo => {
                   return <TodoListItem key={todo.text} todo={todo} toggleTodo={toggleTodo} />
               })}
           </ul>
       );
   };
   
   ```

   `todos`和`toggleTodo`是由父组件传递的属性，其中`todos`是代办数组，而`toggleTodo`则是改变`todo`状态的函数，由父组件定义

3. 将`App.tsx`修改为下列内容

   ```tsx
   import React, { useState } from 'react';
   import { Todo, ToggleTodo } from './types'
   import { TodoList } from './TodoList';
   
   const initialTodos: Todo[] = [
       {text: "Javascript", complete: true},
       {text: "Typescript", complete: false}
   ]; 
   
   const App: React.FC = () => {
       const [todos, setTodos] = useState(initialTodos);
       const toggleTodo: ToggleTodo = selectedTodo => {
           const newTodos = todos.map(todo => {
               if (todo === selectedTodo) { // 被选择的todo更新其状态
                   return {
                       ...todo,
                       complete: !todo.complete
                   };
               }
               return todo; // 其他todo直接返回
           });
           setTodos(newTodos); // 将todos更新为newTodos
       };
       return (
           <div>
               <TodoList todos={todos} toggleTodo={toggleTodo} />
           </div>
       );
   };
   
   export default App;
   ```

   1. 我们使用`useState`来为函数组件`App`引入一个状态`todos` ，该状态用来保存所有的`todo`，然后赋予其一个初始值`initialTodos`
   2. 定义了一个`todo`状态改变函数`toggleTodo`，入参是被选择的`todo`，处理逻辑是将该`todo`状态改变，即反转其`complete`属性，其余保持不变，并使用`setTodos`更新`todos`
   3. 引用我们刚刚定义的`TodoList`组件，并传递`todos`和`toggleTodo`属性

4. 最终效果如下

   ![](https://cdn.jsdelivr.net/gh/khuqen/blog@master/src/assets/TodoList-demo.gif)



## 实现AddTodoForm组件

1. 新建`AddTodoForm.tsx`文件

   ```shell
   touch src/AddTodoForm.tsx
   ```

2. 写入下列内容

   ```tsx
   import React, { useState, ChangeEvent, FormEvent } from 'react';
   import { AddTodo } from './types';
   
   interface AddTodoFormProps {
       addTodo: AddTodo  // type AddToto = (newTodo: string) => void
   };
   
   export const AddTodoForm: React.FC<AddTodoFormProps> = ({ addTodo }) => {
       const [newTodo, setNewTodo] = useState('');
       const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
           setNewTodo(e.target.value);
       };
       const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
           e.preventDefault();
           addTodo(newTodo);
           setNewTodo('');
       };
       return (
           <form onSubmit={handleSubmit}>
               <input type="text" value={newTodo} onChange={handleChange} />
               <button type="submit">Add Todo</button>
           </form>
       );
   };
   
   ```

   1. 使用`useState`为函数组件引入一个状态`newTodo`，用来保存当前用户输入的新`todo`
   2. 将`<input/>`的`value`赋为`newTodo`, 并监听`change`事件，每次改变时调用`handleChange`将`newTodo`置为新输入值，实现双向绑定
   3. 当点击按钮，即`submit`时，首先使用` e.preventDefault()`阻止事件的默认行为，然后调用父组件传过来的`addTodo`函数（这是因为`todos`状态都在父组件中，所以修改、增加都必须使用父组件传过来的函数），将当前`newTodo`加入到所有`todo`中，最后调用`setNewTodo`将`newTodo`置空

3. 修改`App.tsx`为下列内容

   ```tsx
   import React, { useState } from 'react';
   import { Todo, ToggleTodo, AddTodo } from './types'
   import { TodoList } from './TodoList';
   import { AddTodoForm } from './AddTodoForm';
   
   const initialTodos: Todo[] = [
       {text: "Javascript", complete: true},
       {text: "Typescript", complete: false}
   ]; 
   
   const App: React.FC = () => {
       const [todos, setTodos] = useState(initialTodos);
       const toggleTodo: ToggleTodo = selectedTodo => {
           const newTodos = todos.map(todo => {
               if (todo === selectedTodo) {
                   return {
                       ...todo,
                       complete: !todo.complete
                   };
               }
               return todo;
           });
           setTodos(newTodos);
       };
       const addTodo: AddTodo = newTodo => {
           if (newTodo.trim() === '')  // 空值，则跳过
               return;
           setTodos([	// 更新todos
               ...todos,
               {
                   text: newTodo,
                   complete: false
               }
           ]);
       };
       return (
           <div>
               <TodoList todos={todos} toggleTodo={toggleTodo} />
               <AddTodoForm addTodo={addTodo} />
           </div>
       );
   };
   
   export default App;
   ```

4. 最终效果如下

   ![addtodoform-demo](https://cdn.jsdelivr.net/gh/khuqen/blog@master/src/assets/addtodoform-demo.gif)
---
title:  react 插件
date: 2022-08-05 11:19:28
tags: react
categories: react
---



### react.PureComponent 与 React.Component 区别

> 唯一的区别在于 Rect.Component 没有实现 shouldComponentUpdate() ***一个不太常用的生命周期方法\***, 而 React.PureComponent 中以浅层对比 prop 和 state 的方式来实现了该函数。





> **注意**
>
> React.PureComponent 中的 **shouldComponentUpdate()** 仅作**对象的浅层比较**。如果对象中包含复杂的数据结构，则可能无法检查到深层的差别，产生错误的对比结果。所以只在props 和 state 比较简单时使用这个，或者在深层数据结构发生变化时调用**forceUpdate()**来确保组件被正确地更新。也可以使用**immutable** 对象加速嵌套数据的比较。
>
> 此外，React中 的 **shouldComponentUpdate()** 将跳过所有子组件树的 prop 更新。因此，确保所有子组件也是“纯的”组件。

<!--more-->

### useMemo与useCallback

> 在class 组件中，内部有 state 状态管理。
>
> 1. 调用setState，就会触发组件的重新渲染，无论前后的state是否不同
> 2. 父组件更新，子组件也会自动的更新
>
> 基于上面的两点，我们通常的解决方案是：使用immutable进行比较，在不相等的时候调用[setState](https://so.csdn.net/so/search?q=setState&spm=1001.2101.3001.7020)；在shouldComponentUpdate中判断前后的props和state，如果没有变化，则返回false来阻止更新

在 function 组件中,没有 shouldComponentUpdate 钩子函数，useEffect 不区分mount和update两个状态

因此useMemo 和useCallback就是解决性能问题的杀手锏。

```react
import React from 'react';
 
 
export default function WithoutMemo() {
    const [count, setCount] = useState(1);
    const [val, setValue] = useState('');
 
    function expensive() {
        console.log('compute');
        let sum = 0;
        for (let i = 0; i < count * 100; i++) {
            sum += i;
        }
        return sum;
    }
 
    return <div>
        <h4>{count}-{val}-{expensive()}</h4>
        <div>
            <button onClick={() => setCount(count + 1)}>+c1</button>
            <input value={val} onChange={event => setValue(event.target.value)}/>
        </div>
    </div>;
}
```

上面例子中，expensive 方法在任何状态更新时都会执行一次，它应该是只依赖 count 状态。此处可使用useMemo 优化，useMemo 返回缓存的值

```react
import React from 'react';
 
 
export default function WithMemo() {
    const [count, setCount] = useState(1);
    const [val, setValue] = useState('');
 
    const expensive = useMemo(() => {
        console.log('compute');
        let sum = 0;
        for (let i = 0; i < count * 100; i++) {
            sum += i;
        }
        return sum;
    }, [count]);
 
    return <div>
        <h4>{count}-{val}-{expensive}</h4>
        <div>
            <button onClick={() => setCount(count + 1)}>+c1</button>
            <input value={val} onChange={event => setValue(event.target.value)}/>
        </div>
    </div>;
}

```

useCallback 它与 useMemo 相似，返回 缓存的函数. 

当 count 更新时，返回新的函数，使用 Set 来判断返回函数是否更新。每次修改count，set.size就会+1

```react
import React, { useState, useCallback } from 'react';
 
const set = new Set();
 
export default function Callback() {
    const [count, setCount] = useState(1);
    const [val, setVal] = useState('');
 
    const callback = useCallback(() => {
        console.log(count);
    }, [count]);
    set.add(callback);
 
 
    return <div>
        <h4>{count}</h4>
        <h4>{set.size}</h4>
        <div>
            <button onClick={() => setCount(count + 1)}>+</button>
            <input value={val} onChange={event => setVal(event.target.value)}/>
        </div>
    </div>;
}
```

使用场景，父子组件传值。父组件更新子组件也会更新。但大多数是没有必要的，通过 useCallback 可以优化。

```react
import React, { useState, useCallback, useEffect } from 'react';
// 父组件
function Parent() {
    const [count, setCount] = useState(1);
    const [val, setVal] = useState('');
 
    const callback = useCallback(() => {
        return count;
    }, [count]);
    return <div>
        <h4>{count}</h4>
        <Child callback={callback}/>
        <div>
            <button onClick={() => setCount(count + 1)}>+</button>
            <input value={val} onChange={event => setVal(event.target.value)}/>
        </div>
    </div>;
}
// 子组件
function Child({ callback }) {
    const [count, setCount] = useState(() => callback());
    useEffect(() => {
        setCount(callback());
    }, [callback]);
    return <div>
        {count}
    </div>
}
```

所有依赖本地状态或props来创建函数，需要使用到缓存函数的地方，都是useCallback的应用场景。



### 组件条件渲染优化

从 Solid.js 框架获得灵感，可以创建一个 `Show` 组件来优化条件渲染的写法：

```react
// Show 组件类型定义
interface ShowProps<T> {
  when: T | undefined | null | false;
  fallback?: React.ReactNode;
  children: React.ReactNode | ((item: T) => React.ReactNode);
}
```

**实现方案**

创建一个简单但强大的 `Show` 组件：

```react
function Show({ when, fallback = null, children }) {
  return when ? children : fallback;
}
```

使用示例

**1. 基础条件渲染**

```react
// ❌ 旧写法
{isLoading && <Spinner />}

// ✅ 新写法
<Show when={isLoading}>
  <Spinner />
</Show>
```

**2. 条件分支渲染**

```react
// ❌ 旧写法
{isAdmin 
  ? <AdminPanel /> 
  : <UserPanel />
}

// ✅ 新写法
<Show 
  when={isAdmin}
  fallback={<UserPanel />}
>
  <AdminPanel />
</Show>
```

**3. 复杂条件渲染**

```react
// ❌ 旧写法
{isCommentsEnabled && (
  <>
    <CommentsHeader />
    {comments.map(comment => (
      <CommentItem key={comment.id} {...comment} />
    ))}
    {isLoggedIn && <CommentForm />}
  </>
)}

// ✅ 新写法
<Show when={isCommentsEnabled}>
  <CommentsHeader />
  {comments.map(comment => (
    <CommentItem key={comment.id} {...comment} />
  ))}
  <Show when={isLoggedIn}>
    <CommentForm />
  </Show>
</Show>
```

**4. 带数据处理的条件渲染**

```react
// ❌ 旧写法
{user && (
  <div>
    Welcome, {user.name}!
    {user.isAdmin && <AdminBadge />}
  </div>
)}

// ✅ 新写法
<Show when={user}>
  {(userData) => (
    <div>
      Welcome, {userData.name}!
      <Show when={userData.isAdmin}>
        <AdminBadge />
      </Show>
    </div>
  )}
</Show>
```

**扩展功能**

还可以添加一些实用的功能：

```react
// 支持异步数据
const AsyncShow = ({ when, fallback, children }) => {
  const [isLoading, setIsLoading] = useState(true);
  const [data, setData] = useState(null);

  useEffect(() => {
    Promise.resolve(when).then(result => {
      setData(result);
      setIsLoading(false);
    });
  }, [when]);

  if (isLoading) return fallback;
  return data ? children : null;
};

// 使用示例
<AsyncShow 
  when={fetchUserData()} 
  fallback={<Loading />}
>
  {user => <UserProfile data={user} />}
</AsyncShow>
```

这个简单的组件不仅能让代码更加清晰易读，还能提升开发效率。有时候最简单的改进反而能带来最大的收益！🎉
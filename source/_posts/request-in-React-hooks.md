---
title: request in React hooks
date: 2019-12-24 11:00:09
tags:
- React
- React Hooks
---
只需要把 React 升级到 16.8+ 就可以在项目中使用到最新的 Hooks 功能。并且推荐在 eslint 中增加 eslint-plugin-react-hooks 插件协助检查 Hooks 的使用情况，但也不必完全参考 eslint-plugin-react-hooks 的提示对 deps 管理。  
对于常用的 API ，通过查看官方文档基本都能掌握使用方法和使用场景。    

这里主要想讨论下请求的相关处理。  

通常对于项目中的异步请求获取数据的逻辑处理，会有一个公共的方法用来做统一的错误处理和参数格式化，以便于在组件中使用起来更方便。在 Hooks 项目中，同样也可以使用这样的逻辑，类似于：

```js
function List() {

    const [data, setData] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);

    const fetchList = useCallBack(() => {
        setLoading(true);
        fetchList.then(resp => {
            setData(resp);
            setLoading(false);
        }).catch(e => {
            setError(e);
            setLoading(false);
        });
    }, []);

    useEffect(() => {
        fetchList();
    }, [fetchList]);

    const handleClickRefresh = useCallBack(() => {
        fetchList();
    }, [fetchList]);

    return (
        <button onClick={handleClickRefresh}>refresh</button>
        <ul>
            {
                data.map(d => (
                    <li key={d.id}>{d.name}</li>
                ));
            }
        </ul>
    );
}
```
这应该是最简单的实践方式，但是既然用了 hooks，对于 loading，error, response 的处理可以用一个自定义 hooks 封装起来。
```js
function useDataList(filter) {
    const [data, setData] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    const doFetchList = useCallBack(params => {
        setLoading(true);
        fetch(params).then(resp => {
            setData(resp);
            setLoading(false);
        }).catch(e => {
            setError(e);
            setLoading(false);
        });
    }, []);
    return {data, error, loading, doFetchList};
}

function List() {

    const {data, error, loading, doFetchList} = useDataList();

    useEffect(() => {
        doFetchList();
    }, [doFetchList]);

    const handleClickRefresh = useCallBack(() => {
        doFetchList();
    }, [doFetchList]);

    return (
        <button onClick={handleClickRefresh}>refresh</button>
        <ul>
            {
                data.map(d => (
                    <li key={d.id}>{d.name}</li>
                ));
            }
        </ul>
    );
}

```
其实此处的 `useEffect` 和 `handleClickRefresh` 中的 deps 是可以去掉 `doFetchList` 的, `doFetchList`并不会有变化。    

再进一步，可以把对 list 的增加删除等相关操作都可以写在 useDataList 里面；对于请求操作中 loading error response 的相关代码可以提取一个 useRequest Hook，处理一些 response 的通用逻辑。

```js
function useDataList(filter) {
    const [data, setData] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);

    const {
        response,
        requestLoading,
        requestError,
        doFetch: doFetchList
    } = useRequest({
        url: '/api/fetch/list'
    });

    useEffect(() => {
        setData(response);
        setLoading(requestLoading);
        setError(requestError);
    }, [response, requestLoading, requestError]);

    // 其它如删除、新增操作同理

    return {data, error, loading, doFetchList};
}
```

看了[这篇](https://www.zhihu.com/question/357020049/answer/909484669)回答后，发现还可以进行更深层次的封装。    
    
使用 hooks 之后感受比较深的就是状态的管理复用会方便很多，可能在原来对一个后台管理系统中展示用的 table 进行封装的时候，需要考虑各个ui的组件怎么封装已经 table 用到的数据之前的通信共享，现在有了hooks，可以将 ui 和状态管理更好地解耦，并且状态管理的 hooks 也能更方便地复用。
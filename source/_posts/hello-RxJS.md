---
title: hello RxJS
date: 2019-06-18 19:38:11
tags:
- React
- Redux
- RxJS
---
一直都听说 RxJS 用起来很方便，很好用，但看了几次文档，都是看了一点就看不进去了，整体认识也很模糊。直到今天又看了下 redux-observable 的例子，才有了初步的认识。  
整体上，感觉是和 redux-saga 有点像的，在 redux-saga 中是对特定的 action 进行监测，然后近似同步地处理 effects ；在 redux-observable 中，则是使用 RxJS 把 actions 当作一个流，通过 RxJS 的多个 api 进行操作，达到想要的目的，封装好的方法会更方便，但需要去熟悉。  
只能说是有了很初步的认识，还是应该结合实践才能有更具体的认识。

---
title: option 'sourceType' in babel
date: 2019-06-21 23:02:14
tags:
---
最近用 Typescript 写了一个 ui 组件，参考 antd 的打包方式，也便于按需引用，所以是每个组件分别转译一下生成 es5 的代码。但没想到的是，在实际使用中，引用部分组件的时候，会一直报 `exports is not defined`的错误。按照以往的经验来看，应该是 webpack 在处理代码的时候，模块解析部分有问题，但当时看 ui 组件的产出文件不仔细，一直都觉得没有问题，搜了半天相关问题，最后都指向说是源文件的模块使用可能有问题。仔细看了下，报错的 ui 组件产出文件中，有`babel-transform-runtime`增加的 helper 代码，但是使用的是`import`引入的，而 typescript 编译的产出是按照的`commonJS`规范，所以才会导致在使用时，一直报错。  

解决办法，在对 ui 组件编译时，将 babel 的`sourceType`参数设置为`unambiguous`，babel 会根据文件来决定是否按照 ESM 语法来处理文件。  
参数的[官方文档](https://babeljs.io/docs/en/options#sourcetype)如下：

>sourceType  
Type: "script" | "module" | "unambiguous"
Default: "module" 
> - "script" - Parse the file using the ECMAScript Script grammar. No import/export statements allowed, and files are not in strict mode.
> - "module" - Parse the file using the ECMAScript Module grammar. Files are automatically strict, and import/export statements are allowed.
> - "unambiguous" - Consider the file a "module" if import/export statements are present, or else consider it a "script".
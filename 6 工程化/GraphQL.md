# GraphQL

Facebook 创建的 API 查询语言。

## 使用 GraphQL.js

```bash
yarn add graphql

# 或
npm install --save graphql
```

GraphQL.js 提供两个能力：定义类型模式，针对该类型模式提供查询。

首先定义符合代码库 GraphQL 类型模式。

```javascript
import {
  graphql,
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLString
} from 'graphql';

let schema = new GraphQLSchema({
  query: new GraphQLObjectType({
    name: 'RootQueryType',
    fields: {
      hello: {
        type: GraphQLString,
        resolve() {
          return 'world';
        }
      }
    }
  })
})
```

上面定义了一个类型和一个返回固定值的域的简单模式。`resolve` 可以返回一个值，一个 promise，或者 promise 数组。

针对上面模式的查询：

```javascript
let query = '{ hello }'

graphql(schema, query).then(result => {
  // Prints
  // {
  //   data: { hello: "world"}
  // }
  console.log(result);
})
```

它返回定义的域。在执行查询之前，`graphql` 函数首先会检查查询的语法和语义是有效的，否则会报错。

```javascript
var query = '{ boyhowdy }';
 
graphql(schema, query).then(result => {
  // Prints
  // {
  //   errors: [
  //     { message: 'Cannot query field boyhowdy on RootQueryType',
  //       locations: [ { line: 1, column: 3 } ] }
  //   ]
  // }
  console.log(result);
});
```

## 在浏览器使用

GraphQL.js 是通用库，可以在 Node 服务器或浏览器使用。

---

参考：

[npm](https://www.npmjs.com/package/graphql)
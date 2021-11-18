# [create custom javascript syntax with babel](https://lihautan.com/creating-custom-javascript-syntax-with-babel/)

## parse

```typescript
// filename: packages/babel-parser/src/tokenizer/index.js
// 存放需要比对的关键字或符号

// filename: packages/babel-parser/src/tokenizer/index.js
// 增加匹配的switch case处理

// filename: packages/babel-parser/src/parser/statement.js
// 把node.curry = atat
```



## Transformer



```typescript
// filename: babel-plugin-transformation-curry-function.js
import customParser from './custom-parser';

export default function ourBabelPlugin() {
  return {
    parserOverride(code, opts) {
      return customParser.parse(code, opts);
    },
  };
}
```

Babel-plugin-transformation-curry-function

```typescript
// filename: babel-plugin-transformation-curry-function.js
export default function ourBabelPlugin() {
  return {
    // ...
    visitor: {
      FunctionDeclaration(path) {
        if (path.get('curry').node) {
          // const foo = curry(function () { ... });
          path.node.curry = false;
          path.replaceWith(
            t.variableDeclaration('const', [
              t.variableDeclarator(
                t.identifier(path.get('id.name').node),
                t.callExpression(t.identifier('currying'), [
                  t.toExpression(path.node),
                ])
              ),
            ])
          );
        }
      },
    },
  };
}
```



## @babel/generator

```typescript
// 在 parsing + transformation之后
// 还要用@babel/generator从transformed AST里生成代码
// 所以要在generator里注入currying的函数处理
function currying(fn) {
  const numParamsRequired = fn.length;
  function curryFactory(params) {
    return function (...args) {
      const newParams = params.concat(args);
      if (newParams.length >= numParamsRequired) {
        return fn(...newParams);
      }
      return curryFactory(newParams);
    }
  }
  return curryFactory([]);
}
```

实际转化

```typescript
// from
function @@ foo(a, b, c) {
  return a + b + c;
}

// to
const foo = currying(function foo(a, b, c) {
  return a + b + c;
})
```



## 提供currying function的方式

a. 全局定义



b. 使用@babel/helpers

```typescript
helpers.currying = helper("7.6.0")`
  export default function currying(fn) {
    const numParamsRequired = fn.length;
    function curryFactory(params) {
      return function (...args) {
        const newParams = params.concat(args);
        if (newParams.length >= numParamsRequired) {
          return fn(...newParams);
        }
        return curryFactory(newParams);
      }
    }
    return curryFactory([]);
  }
`;
```


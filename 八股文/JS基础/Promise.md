## Promise 



```typescript
/**
 * @description 手写Promise
 */

Promise.resolve(1).then();
type Func = (resolve: (val: any) => void, reject: (val: any) => void) => void;
type ResolveFunc = (val: any) => void;
type RejectFunc = (val: any) => void;
enum STATE {
  PENDING = "pending",
  FULFILLED = "fulfilled",
  REJECTED = "rejected",
}
class PromiseJ {
  state = STATE.PENDING;
  resolveRes: undefined;
  rejectRes: undefined;
  resolveList: ResolveFunc[] = [];
  rejectList: RejectFunc[] = [];
  constructor(func: Func) {
    this.state = STATE.PENDING;
    func(this.resolve.bind(this), this.reject.bind(this));
  }

  then(resolveFunc: ResolveFunc, rejectFunc?: RejectFunc): PromiseJ {
    if (resolveFunc && this.state === STATE.FULFILLED) {
      resolveFunc(this.resolveRes);
      this.resolveRes = undefined;
      return new PromiseJ((resolve) => {
        resolve(undefined);
      });
    }
    if (this.state == STATE.REJECTED && rejectFunc) {
      rejectFunc(this.rejectRes);
      this.rejectRes = undefined;
    }
  }
  catch(func: RejectFunc): void {
    if (this.rejectRes !== undefined && func) {
      func(this.rejectRes);
      this.rejectRes = undefined;
    }
  }
  finally(func: () => void): void {
    if (func) func();
  }

  resolve = (val: any): void => {
    this.state = STATE.FULFILLED;
    this.resolveRes = val;
  };
  reject(val: any): void {
    this.state = STATE.REJECTED;
    this.rejectRes = val;
  }
  static resolve(val: any): PromiseJ {
    console.log(val, "1111");
    if (val instanceof PromiseJ) return val;
    return new PromiseJ((resolve) => {
      resolve(val);
    });
  }
  static all(iterator: any[]): PromiseJ {
    const arrFunc = Array.from(iterator);
    const res: any[] = [];
    let count = 0;
    return new PromiseJ((resolve, reject) => {
      for (let i = 0; i < arrFunc.length; i++) {
        PromiseJ.resolve(arrFunc[i])
          .then((d) => {
            // console.log(i, d);
            res[i] = d;
            // console.log(res);
            if (++count === arrFunc.length) {
              console.log("触发");
              resolve(res);
            }
          })
          .catch((error) => {
            reject(error);
          });
      }
    });
  }
  static allSettled(arrFunc: any[]): PromiseJ {
    const res: any[] = [];
    let count = 0;
    return new PromiseJ((resolve, reject) => {
      for (let i = 0; i < arrFunc.length; i++) {
        Promise.resolve(arrFunc[i])
          .then((d) => {
            console.log(i, d, count);
            res[i] = d;
            // console.log(res);
            if (++count === arrFunc.length) {
              console.log(1111);
              resolve(res);
            }
          })
          .catch((error) => {
            res[i] = error;
            console.log(i, error, count);
            if (++count === arrFunc.length) {
              console.log(2222);
              resolve(res);
            }
          });
      }
    });
  }
  static race(arrFunc: []): PromiseJ {
    return new PromiseJ((resolve, reject) => {
      for (let i = 0; i < arrFunc.length; i++) {
        PromiseJ.resolve(arrFunc[i])
          .then((d) => {
            resolve(d);
          })
          .catch((error) => {
            reject(error);
          });
      }
    });
  }
}

// const a = new Promise((resolve, reject) => {
//   console.log("1");
//   // resolve(null);
//   reject("b");
// })
//   .then(
//     (d) => {
//       console.log(d);
//       console.log(2);
//     }
//     // (d) => {
//     //   console.log(d);
//     //   console.log(3);
//     // }
//   )
//   .then(
//     (d) => {
//       console.log(d);
//       console.log(2.5);
//     }
//     // () => {
//     //   console.log(3.5);
//     // }
//   )
//   .catch((d) => {
//     console.log(d);
//     console.log(4);
//   })
//   .finally(() => {
//     console.log(5);
//   });

// const b = new PromiseJ((resolve) => {
//   console.log("b1");
//   resolve("b1v");
// })
//   .then((d) => {
//     console.log(d);
//     console.log("b2");
//   })
//   .then((d) => {
//     console.log(d);
//     console.log("b3");
//   })
//   .finally(() => {
//     console.log("b-end");
//   });

PromiseJ.all([
  PromiseJ.resolve(3),
  new PromiseJ((resolve, reject) => {
    setTimeout(resolve, 100, "foo");
  }),
  42,
]).then((d) => {
  console.log(d);
});

// Promise.resolve(
//   new Promise(function (resolve, reject) {
//     setTimeout(resolve, 100, "foo");
//   })
// ).then((d) => console.log(d));
// PromiseJ.resolve(3).then((d) => console.log(d));
export { PromiseJ };

```


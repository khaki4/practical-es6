## Callback
```javascript
var request = require('request');
var url1='http://httpbin.org/', url2=url1, url3=url1, url4=url1;

function foo(finalCallback) {
  request.get(url1, function(err1, res1) {
    if (err1) { return finalCallback(err1); }
    request.post(url2, function(err2, res2) {
      if (err2) { return finalCallback(err2); }
      request.put(url3, function(err3, res3) {
        if (err3) { return finalCallback(err3); }
        request.del(url4, function(err4, res4) {
          // let's stop here
          if (err4) { return finalCallback(err4); }
          finalCallback(null, "whew all done");
        })
      })
    })
  })
}

foo(function(err, message) {
  if (err) {
    return console.log("error!", err);
  }
  console.log("success!", message);
});
```
## Promises
```javascript
var Promise = require('bluebird');
var request = Promise.promisifyAll(require('request'));
var url1='http://httpbin.org/', url2=url1, url3=url1, url4=url1;

function foo() {
  return request.getAsync(url1)
  .then(function(res1) {
    return request.postAsync(url2);
  }).then(function(res2) {
    return request.putAsync(url3);
  }).then(function(res3) {
     return request.delAsync(url4);
  }).then(function(res4) {
     return "whew all done";
  });
}

foo().then(function(message) {
  console.log("success!", message);
}).catch(function(err) {
  console.log("error!", err);
});
```

callback과 promise 차이점

callback: 주도권이 콜백을 호출하는 쪽에 있다
e.g) setTimeout(callback, 1000)

promise: 작업시작 ------ 작업완료 ------ 대기 ------ 내가 resolve 싶을때 실행 후 resolve

callback:

1. 콜백은 작업이 완료되자마자 호출되는 경우밖에 없다
2. 프라미스는 객체값으로 보관할 수 있으므로
3. 내가 원할때 then을 호출할 수 있고 그전까지 대기시킬 수 있다.
4. 만약 작업완료전에 then을 호출하면 콜백과 같은 의미가 되고
5. 작업완료이후에 호출한다면 작업완료측의 사정이 아니라 콜백을 사용하는 측의 입장에 맞춰서 타이밍을 정할 재량권을 갖게 된다

프라미스란 비동기 상태를 값으로 환원한 것
값이기 때문에 chaining 도 가능하다.
callback hell을 벗어나기 위한 design이 아니다.

## es7 next async/await
```javascript
var Promise = require('bluebird');
var request = Promise.promisifyAll(require('request'));
var url1='http://httpbin.org/', url2=url1, url3=url1, url4=url1;

async function foo() {
  var res1 = await request.getAsync(url1);
  var res2 = await request.getAsync(url2);
  var res3 = await request.getAsync(url3);
  var res4 = await request.getAsync(url4);
  return "whew all done";
}

// use that function somewhere
foo().then(function(message) {
  console.log("success!", message);
}).catch(function(err) {
  console.log("error!", err);
});
```
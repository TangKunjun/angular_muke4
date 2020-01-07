# RXJS

**rxjs的冷**

像点播 所有观察者都是从头开始观察

```const const$ = Rx.Observable.interval(1000);
   const sub1 = const$.subscribe(v => console.log("第一个观察者:" + v));
   const sub2 = setTimeout(() => {
   const$.subscribe(v => console.log("第二个观察者:" + v));
    }, 2000)
```
以上代码输出结果为：第一个观察者从零开始，第二个观察者2秒钟后也从零开始



**rxjs的热**

像直播 所有观察者都是一样的

```
    const const$ = Rx.Observable.interval(1000).share();
    const sub1 = const$.subscribe(v => console.log("第一个观察者:" + v));
    const sub2 = setTimeout(() => {
        const$.subscribe(v => console.log("第二个观察者:" + v));
    }, 2000)
```
以上代码输出结果为：第一个观察者从零开始，第二个观察者2秒钟后输出和第一个观察者一样的结果



# subject

subject即使观察者也是订阅者

 ```
     const conunt$ = Rx.Observable.interval(1000).take(5);
 
     const observe1 = {
         next: (val) => console.log("observe1:" + val),
         error: (err) => console.log("observe1Error:" + err),
         complete: () => console.log("observe1完成")
     }
 
     const observe2 = {
         next: (val) => console.log("observe2:" + val),
         error: (err) => console.log("observe2Error:" + err),
         complete: () => console.log("observe2完成")
     }
 
     conunt$.subscribe(observe1);
     setTimeout(() => {
          conunt$.subscribe(observe2)
     },2000)
 ```

以上代码采用subject的方式为

```
     const conunt$ = Rx.Observable.interval(1000).take(5);
     const observe1 = {
         next: (val) => console.log("observe1:" + val),
         error: (err) => console.log("observe1Error:" + err),
         complete: () => console.log("observe1完成")
     }
 
     const observe2 = {
         next: (val) => console.log("observe2:" + val),
         error: (err) => console.log("observe2Error:" + err),
         complete: () => console.log("observe2完成")
     }
     
     const subject = new Rx.Subject();
     subject.subscribe(observe1);
     subject.next("我在observe1流里面");
     
     subject.subscribe(observe2);
     subject.next("我在observe1和observe2流里面");
     
     conunt$.subscribe(subject);
```


**ReplaySubject**

重播指定的流
```

    const subject = new Rx.ReplaySubject(2);

    subject.subscribe(observe1);
    subject.next(11);
    subject.next(12);
    subject.next(15);       // ReplaySubject表示重播两个流，原本observe2是没有这三个新增的流 已经过去了 现在observe2会有12和15这两个流 因为还原2个流

    subject.subscribe(observe2);

    conunt$.subscribe(subject);
```

**BehaviorSubject**
  
  记住最新的值

```
   const subject = new Rx.BehaviorSubject('new');
   
   subject.subscribe(observe1);
   subject.next(11);
   subject.next(12);
   subject.next(15);       // BehaviorSubject只记住最新的值，即便已经发射完毕了 它仍然能记住最后的的最新值

   subject.subscribe(observe2);

   conunt$.subscribe(subject); 
```
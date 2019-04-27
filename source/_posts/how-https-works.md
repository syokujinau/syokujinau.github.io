---
title: 草履蟲也能懂的HTTPS
date: 2019-03-02 21:14:47
tags: 
- HTTPS
- Web
thumbnail: https://i.imgur.com/1SpDhK2.jpg
---

> 參考自[HTTPS explained with carrier pigeons](https://medium.freecodecamp.org/https-explained-with-carrier-pigeons-7029d2193351)

試想一個簡單的情境，Alice跟Bob想互相用飛鴿傳書寄信，但是送信過程可能被Mallory攔截下來偷看內容...
* 方法一，使用[凱薩密碼](https://zh.wikipedia.org/wiki/%E5%87%B1%E6%92%92%E5%AF%86%E7%A2%BC)(Caesar cipher，推移字母替代原文，因為知道加密方法就能解密，所以屬於對稱式加密)，這時Alice跟Bob想先傳送這個密碼，但是Mallory會攔截，然後就能解碼Alice跟Bob的信件內容，除非Alice跟Bob先私下見面制定密碼。
* 方法二，當Bob想寄信給Alice，Bob先傳一隻不帶訊息的鴿子，Alice在讓鴿子帶上沒上鎖的盒子，自己留著盒子的鑰匙，然後傳給Bob，Bob收到沒上鎖的盒子後，放入信件並鎖上，傳回給Alice，這時候如果鴿子被攔截，Mallory也無法打開，因為只有Alice有這個盒子的鑰匙，這就是所謂的公鑰與私鑰，雖然叫做公鑰，但是作用是**盒子**，打開盒子的叫做私鑰。

Q: 當Bob想傳訊息給Alice，如何確定這個盒子來自Alice?
A: Alice在盒子上簽名。
Q: 這時候問題又來了，Bob怎麼認得這個簽名是Alice的，而不是Mallory偽造的?
A: 這時候交給公正的第三方Ted來負責簽名。

Ted很有名，所以大家都認得他的簽名，而且Ted知道這個盒子是Alice請他簽名的，所以Mallory不能攔截盒子然後再請Ted簽名。

*Ted就是所謂的Certification Authorities。*

雖然Alice與Bob有了安全的方法寄信，這時候他們發現每次都要經過這麼麻煩的程序，所以他們決定在寄信前用盒子傳凱薩密碼(方法二)，這樣他們就不用私下見面制定密碼了，然後之後傳訊息都透過凱薩密碼(方法一)加解密即可。
這就是HTTPS運作的概念，只是跑這些流程的速度比鴿子快多了而已。
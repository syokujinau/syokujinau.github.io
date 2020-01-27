---
title: Matlab 常用作圖函數
date: 2019-07-19 11:44:07
tags:
- Matlab
categories:
- Miscellaneous
thumbnail: https://i.imgur.com/ZuCwVX4.png
---


## Symbolic function

`ezplot`可以直接輸入字串

```m
fx = '10*sin(x)';
h = ezplot(fx);
```

![](https://i.imgur.com/mpZ9KDr.png)


與`ezplot`不同，`fplot`的第一個參數是匿名函式，第二個參數可指定x軸範圍

<!-- more -->

```m
fy = @(y) 10*sin(y) + tan(y/2)/10;
h = fplot(fy, [-15 5]);
```

![](https://i.imgur.com/VyVudmW.png)



## Implicit function



```m
fts = @(r,t,s) 2.^(t.*s) + r.*s - t.*s.^(1/3);
h = fimplicit3(fts);
rotate3d on, axis image
set(h, 'linestyle', 'none')
```

![](https://i.imgur.com/ZuCwVX4.png)

## Polar function


```m
cardioid = @(t) 2*(1-cos(t));
h = ezpolar(cardioid);
set(h, 'linew', 2, 'color', 'r')
```

![](https://i.imgur.com/Qhe8B5V.png)

## Function limits

使用`limit`求$\lim_{x\to 5}  \frac{x^2 - 25}{x^2+x-30}$，並作圖。

```m 
syms x
fx = (x^2 - 25) / (x^2+x-30);
fplot(fx, [-10 10], 'linew', 2)

a = 5;
lim5 = limit(fx, x, a);

hold on
plot([1 1]*a, get(gca, 'ylim'), 'r--')
plot(get(gca, 'xlim'), [1 1]*lim5, 'r--')
title('$$f(x) = \frac{x^2 - 25}{x^2+x-30}$$','interpreter','latex')
```

![](https://i.imgur.com/jITma8a.png)

計算$x\to -6$的左極限與右極限

```m
limit(fx, x, -6, 'left')
limit(fx, x, -6, 'right')
```

## Function Derivatives

$$f(x) = \frac{e^{sin(x)^x}}{3^x cos(x)}$$

// TBD
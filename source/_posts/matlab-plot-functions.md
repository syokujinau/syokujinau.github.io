---
title: Matlab常用函數 - 作圖與微分積分求解
date: 2020-01-09 11:44:07
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
```

用`syms`宣告**symbolic variables**，可視為一個placeholder，這樣就不用宣告特定x的vector，Matlab會幫我們解析表達式。

若宣告為vector形式，`x = [-10:.01:10]`稱為**numeric variables**

```m
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

1. 求$f(x) = sin(x)$導數並作圖
2. 回傳$f(x)$與$\frac{df(x)}{dx}$在$x=\pi / 3$的**symbolic expression**

```m 
syms x % symbolic variables
f = sin(x);
df = diff(f);

subplot(211)
fplot(f)
hold on
fplot(df)

legend(['f(x) = ' char(f)], ['df = ' char(df)])
```

![](https://i.imgur.com/einqTrv.png)


`subs`: Symbolic substitution

```m
a = pi / 3;
subs(f, a)   % 3^(1/2)/2
subs(df, a)  % 1/2
```

一樣的函數，在numeric運算，注意會有取樣間隔與導數的vector長度的問題

```m 
Ts = .001;
t = -5:Ts:5;
y = sin(t);
dy = diff(y) * 1/Ts; % 這裡是discrete derivatives，所以輸出的vector長度會比原本少1
subplot(212)
plot(t, y)
hold on
plot(t(1:end-1), dy)  % t少取一個才會跟dy長度一樣
legend({'y(t)', 'dy'})
```


---


接下來

1. `fplot`畫出以下函數與其導函數的圖形
2. `pretty`在console印出數學式
3. 縱軸範圍-300到300

$$f(x) = \frac{e^{sin(x)^x}}{3^x cos(x)}$$

```m
syms x
f = exp(sin(x))^x/(3^x * cos(x));
df = diff(f);

pretty(f)
```

![](https://i.imgur.com/JExYWaZ.png)

可用來對照有沒有打錯函數

```m
pretty(df)
```

![](https://i.imgur.com/5EtGIsw.png)
嗯...雖然還是醜醜的，但是相對好閱讀

```m
figure(8), clf
fplot(f, 'linew', 2)
hold on
fplot(df, 'linew', 2)
legend({'f(x)', 'df'})
set(gca, 'ylim', [-300 300])
```

![](https://i.imgur.com/eN6TExS.png)


## Function integration

求$f(x) = x^4$ 不定積分並作圖


```m
syms x
f = x^4;
intf = int(f);

figure(9), clf, hold on
fplot(f, [-2 2])
fplot(intf, [-2 2])
legend(['f(x) = ' char(f)], ['\int f dx = ' char(intf) '+C'])
```

![](https://i.imgur.com/21O7qUM.png)

1. 分別對x與y做$f(x,y) = x^2 + y^3$分部積分
2. 用`subs`代入$(x, y) = (4, 3)$求值

```m 
syms x y
fxy = x^2 + y^3;

int(fxy, x) % x^3/3 + x*y^3

int(fxy, y) % x^2*y + y^4/4

subs(int(fxy, x), [x y], [4 3]) % 388/3
```


---

對以下函數不定積分

$$f(x)=\frac{sec(x) log(tan(x)sin(x) + sin(x)) }{e^x}$$

```m
syms x 
f = sec(x) * log(tan(x)*sin(x) + sin(x)) / exp(x);

figure(10), clf, hold on
fplot(f, [-20 10], 'linew', 2)
```

若直接用`int`求

```m 
intf = int(f);
```

將會回傳int((exp(-x)*log(sin(x) + sin(x)*tan(x)))/cos(x),x)，代表這個函數沒有一個symbolic的解

較好的方式是用numerical方法去近似，也就是代入一個vector的每個值到匿名函數去求解

1. `integral` - Numerically evaluate integral
2. 匿名函數要用element-wise運算
3. 積分後`real`取實部，用`plot`畫出

```m 
syms x 
f = sec(x) * log(tan(x)*sin(x) + sin(x)) / exp(x);

figure(10), clf, hold on
fplot(f, [-20 10], 'linew', 2)


intf = int(f);

% calculate numerically

intvec = linspace(-20, 10, 100);
intf2 = zeros(size(intvec)); % initialize

funcf = @(x) sec(x) .* log(tan(x).*sin(x) + sin(x)) ./ exp(x);

for i = 1:length(intvec)
    intf2(i) = integral(funcf, intvec(1), intvec(i));
    %   Q = INTEGRAL(FUN,A,B) approximates the integral of function FUN from A
    %   to B using global adaptive quadrature and default error tolerances.

end

plot(intvec, real(intf2), 'linew', 2)
legend(['f(x)'], ['\int f dx'])
```

![](https://i.imgur.com/bC6x2u0.png)


## Solving Differential Equations

常見電路分析，電容與電感在時域是微分項，也會有初始電壓等情況(initial condition)，透過matlab可以驗證電路行為。

1. 求解以下微分方程式
    * General solution
    * Particular solution $y(0) = \{2,1, 0.75,0.5 \}$
2. 使用`syms`, `diff`, `dsolve`, `meshgrid`, `ezplot`, `quiver`



$$\frac{dy}{dt} = e^{-t} - 2y$$


```m 
syms y(t)

eq = diff(y) == exp(-t) - 2*y;
```

`dsolve`基本用法

```m
dsolve(eq) % exp(-t) + C1*exp(-2*t)

dsolve(eq, y(0) == 2) % exp(-t) + exp(-2*t)
```

畫出背景的gradient vector
* `meshgrid`: $2 \leq x \leq 3, -1 \leq y \leq 2$分別為30點與32點的網格
* `quiver`: 參數為x、y座標、正規化向量、箭頭大小

```m
% slope curves
[t1, y1] = meshgrid(linspace(-2, 3, 30), linspace(-1, 2, 32));
f = exp(-t1) - 2*y1;  
L = sqrt(1^2 + f.^2); 

% plot
figure(11), clf, hold on
h = quiver(t1, y1, 1./L, f./L, .5); 
```

h是個placeholder，用來設置樣式，例如將箭頭顏色設成灰色

```m
set(h,'color', [.8 .8 .8])
```
![](https://i.imgur.com/yK1Ih10.png)



用`ezplot`畫出不同initial value的解的曲線

```m
% initial values
initvals = [2 1 .75 .5];

% draw solution curves for the initial values
h1 = zeros(size(initvals));
for i = 1:length(initvals)
    h1(i) = ezplot(dsolve(eq, y(0) == initvals(i)), [min(t1(:)) max(t1(:))]);
end

% set axis limit
axis([min(t1(:)) max(t1(:)) min(y1(:)) max(y1(:))])
```

![](https://i.imgur.com/auNfnUK.png)


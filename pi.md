# SymPy だけでは解けない積分

2019 の日付のある誰かのツイートで「Wi-fi パスワードを数式にするのが流行っている」とかあって，そこで示されているのが

$$
\int_{-2}^2 \left(x^3\cos\frac{x}{2}\right)\sqrt{4-x^2}\ \ dx
$$

SymPy でやれば簡単じゃないかと思って，やってみるとおやまあという結果だったので記事にしてみる。


```julia
using SymPy
@syms x
expression = (x^3 * cos(x/2) + 1/2) * sqrt(4-x^2)
expression |> println
```

    sqrt(4 - x^2)*(x^3*cos(x/2) + 0.5)


なにはともあれ，やってみる。


```julia
a = integrate(expression, (x, -2, 2))
```




$\begin{equation*}1.0 \int_{-2}^{2} 1.0 x^{3} \sqrt{4 - x^{2}} \cos{\left(\frac{x}{2} \right)}\, dx + 1.0 \int\limits_{-2}^{2} 0.5 \sqrt{4 - x^{2}}\, dx\end{equation*}$




随分時間がかかって，出てきた答えが以下のようなもの。2 つの定積分の和という答えを出してもらっても困る。ちなみに，Integral という関数はない。integrate だ。


```julia
a |> println
```

    1.0*Integral(1.0*x^3*sqrt(4 - x^2)*cos(x/2), (x, -2, 2)) + 1.0*Integral(0.5*sqrt(4 - x^2), (x, -2, 2))


前半部分をもう一度やってみる。


```julia
b = integrate(x^3*sqrt(4 - x^2)*cos(x/2), (x, -2, 2))
```




$\begin{equation*}\int\limits_{-2}^{2} x^{3} \sqrt{- \left(x - 2\right) \left(x + 2\right)} \cos{\left(\frac{x}{2} \right)}\, dx\end{equation*}$





```julia
b |> println
```

    Integral(x^3*sqrt(-(x - 2)*(x + 2))*cos(x/2), (x, -2, 2))


なんの進展もない結果が帰ってくる。後で対処する。

後半部分をやってみる。


```julia
c = integrate(0.5*sqrt(4 - x^2), (x, -2, 2))
```




$\begin{equation*}1.0 \pi\end{equation*}$




こちらはすぐに，（1.0* というのは気に入らないが）きれいな答えが得られた。π だ。


```julia
c |> println
```

    1.0*pi


さて，b の integrate(x^3 * sqrt(-(x - 2) * (x + 2)) * cos(x/2), (x, -2, 2)) だが。


```julia
b0 = integrate(x^3 * sqrt(-(x - 2) * (x + 2)) * cos(x/2), (x, 0, 2))
```




$\begin{equation*}\int\limits_{0}^{2} x^{3} \sqrt{2 - x} \sqrt{x + 2} \cos{\left(\frac{x}{2} \right)}\, dx\end{equation*}$




結局何も進展しない。


```julia
b0 |> println
```

    Integral(x^3*sqrt(2 - x)*sqrt(x + 2)*cos(x/2), (x, 0, 2))


ここで，式をちゃんと見ると，う?奇関数（懐かしい用語）。


```julia
expression2 = x^3 * sqrt(4 - x^2) * cos(x/2)
```




$\begin{equation*}x^{3} \sqrt{4 - x^{2}} \cos{\left(\frac{x}{2} \right)}\end{equation*}$





```julia
expression2 |> println
```

    x^3*sqrt(4 - x^2)*cos(x/2)



```julia
expression2(x => -x)
```




$\begin{equation*}- x^{3} \sqrt{4 - x^{2}} \cos{\left(\frac{x}{2} \right)}\end{equation*}$





```julia
expression2(x => -x) |> println
```

    -x^3*sqrt(4 - x^2)*cos(x/2)


奇関数でした。なので，a の第 1 項は 0 なので，a の答えは a の第 2 項の π ということになる。

やれやれ。

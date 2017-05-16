Pythonで最小二乗法を使って指定の点を通るベジェ曲線を近似する
きっかけは、自分が過去に書いたプログラムを整理していると、ベジェ曲線のカーブフィッティング([曲線あてはめ](https://ja.wikipedia.org/wiki/%E6%9B%B2%E7%B7%9A%E3%81%82%E3%81%A6%E3%81%AF%E3%82%81))をするJavaScriptが出てきて、懐かしいなーと思って読んでみたのですが数式が難しすぎて全く理解できませんでした。

何でこんな謎の数式で近似できるねん、昔の俺すげーな。

どうやら最小二乗法を使って近似していたようです、復習がてらPythonで説明したいと思います。
参考書をひっぱりだして勉強しなおしました、ちなみに参考文献は金谷健一先生の[「これなら分かる最適化数学―基礎原理から計算手法まで」](https://www.amazon.co.jp/%E3%81%93%E3%82%8C%E3%81%AA%E3%82%89%E5%88%86%E3%81%8B%E3%82%8B%E5%BF%9C%E7%94%A8%E6%95%B0%E5%AD%A6%E6%95%99%E5%AE%A4%E2%80%95%E6%9C%80%E5%B0%8F%E4%BA%8C%E4%B9%97%E6%B3%95%E3%81%8B%E3%82%89%E3%82%A6%E3%82%A7%E3%83%BC%E3%83%96%E3%83%AC%E3%83%83%E3%83%88%E3%81%BE%E3%81%A7-%E9%87%91%E8%B0%B7-%E5%81%A5%E4%B8%80/dp/4320017382)です。非常に解りやすく解説されている本でオススメです。

# 最小二乗法とは何なのか？
[最小二乗法](https://ja.wikipedia.org/wiki/%E6%9C%80%E5%B0%8F%E4%BA%8C%E4%B9%97%E6%B3%95)とは、複雑なデータや関数を近似する代表的な手法で、データ解析における最も重要な基礎となります。
適用範囲は非常に広く、微分可能であればどんな関数も近似が可能という実用性と美しさを兼ね備えた手法です。

例えば、ここに20個の点があったとします。

![ダウンロード (1).png](https://qiita-image-store.s3.amazonaws.com/0/140075/02e3d6be-a2b9-a3d1-2e4b-47cec11ce322.png)
 
20個の点に対して最も近い場所を通る直線を置いてみましょう。

![ダウンロード.png](https://qiita-image-store.s3.amazonaws.com/0/140075/c2bba2a4-20a0-2d2b-cae6-74a2e5fcc288.png)

直線の関数$f(x) = ax + b$について、すべての点に最も近い場所を通るパラメータ$a$,$b$を求めます。
この$a$,$b$を求める代表的な方法が**最小二乗法**なのです。

指定の場所とプロットした場所の**誤差を二乗した総和が最小になる**ように近似するので、最小二乗法と呼びます。

# 直線の近似

まずは、一番簡単な直線の近似をやってみましょう、以下の4点を近似する直線を引いてみます。

```python
points = [(107, 101), (449, 617), (816, 876), (1105,1153)]
```

![ダウンロード (2).png](https://qiita-image-store.s3.amazonaws.com/0/140075/9d65c5a2-ea7c-61e0-9928-5ec9eaedcb01.png)


直線の関数は$f(x) = ax + b$です、$a$が傾きで$b$が切片ですね。
関数$f(x)$に対して、指定の点の誤差の二乗が最小になるパラメータ$a$と$b$を求めます。

指定の場所$y$とすると、誤差の二乗を求める関数$j(x, y)$は以下のようになります。

```math
j(x, y) = (y - (ax + b))^2
```

N個の点に対して$j$の総和が最小になればよいので、最小二乗法の式は以下のようになります。

```math
J = \frac {1}{2}\sum_{k=1}^{n} j(x_k, y_k)
```


## 誤差関数j(x, y)の偏導関数を求める

いきなり難解ワードの**偏導関数**が出てきましたが、何も心配ありません。
計算方法さえ判れば小学生でも計算できます。

2つ以上の変数がある関数をある1つの変数について微分することを**偏微分**と言います。
そして、偏微分によって求められた関数を**偏導関数**と言います。

### <i class="fa fa-info-circle" aria-hidden="true"></i> 微分を忘れた人用の説明
微分は、$(X^n)' = nX^{n-1}$で、指数をnを係数にして指数をn-1するやつです。
例：
  $(x^2)' = 2x$
  $(2x^2)' = 4x$
  $(4x^3)' = 12x^2$

微分はグラフの接線の傾きとなります、グラフの瞬間の速度を求めると言い換える事もできます。
微分が0の点は、凸のグラフの場合は変曲点となります。

## Sympyを使って偏微分を求める
微分が解れば偏微分も別に目新しい事もないのですが、難しい事は解らなくても平気です。
我々にはSympy(とJupyter)があるのです！

Sympyを知らない人は[Sympy+Jupyterで最強の電卓環境を作る](http://qiita.com/pashango2/items/500d23c8f43784b54315)をどうぞ。

さっそくJupyterを起動しましょう。


```python
from sympy import *
init_session()
```

これでSympyとJupyterのセットアップは完了です。
誤差を求める関数$j$を定義します。

```python
a, b = symbols("a b")

j = (y - (a * x + b)) ** 2
```

`sympy.diff`関数で偏微分を求める事ができます、まずは$a$について偏微分します。
$a$についての偏導関数は$\frac{\partial j}{\partial a}$、$b$については$\frac{\partial j}{\partial b}$と書きます。

```python
j_a = diff(j, a)
j_b = diff(j, b)
```
```math
\frac{\partial j}{\partial a}
=
- 2 x \left(- a x - b + y\right) \\

\frac{\partial j}{\partial b}
=
2 a x + 2 b - 2 y
```

直線関数の偏導関数が求めることができました、`Sympy`があれば楽勝ですね。

最小二乗法は誤差の総和の1/2なので計算してみましょう。
式への値の代入は`sympy.subs`関数で行います。

```python
sum_a = sum([j_a.subs([(x, _x), (y, _y)]) for _x, _y in points]) / 2.
sum_b = sum([j_b.subs([(x, _x), (y, _y)]) for _x, _y in points]) / 2.
```
```math
\frac {1}{2}\sum_{k=1}^{n} \frac{\partial j}{\partial a}
=
2099931.0 a + 2477.0 b - 2276721.0 \\
\frac {1}{2}\sum_{k=1}^{n} \frac{\partial j}{\partial b}
=
2477.0 a + 4 b - 2747.0
```

各総和の1/2の式が出ましたね、あとはこの2つの式の連立方程式を解くだけです。

```math
\begin{bmatrix}
2099931.0 & 2477.0 \\
2477.0 & 4 
\end{bmatrix}

\begin{bmatrix}
a \\
b
\end{bmatrix}
=
\begin{bmatrix}
2276721.0 \\
2747.0
\end{bmatrix}
```

```python
solve([sum_a, sum_b], [a, b])
# {a:1.01694642025091,b:57.0059292596265} 
```

`a=1.01694642025091`、`b=57.0059292596265`が答えです。直線の方程式$y = ax + b$に代入すれば良いのです。
さっそくプロットしてみましょう。

![ダウンロード (4).png](https://qiita-image-store.s3.amazonaws.com/0/140075/15b52052-7442-1aa4-2491-0208c146eace.png)

直線のため全ての点を通過できていませんが、それに近しい場所を通っています。

# ベジェ曲線の近似

先程は単純な直線を近似しましたが、3次曲線のベジェ曲線も全く同じ手順で近似できます。

ベジェ曲線は、$p_1$は始点、$p_4$は終点、$p_2$と$p_3$がそれぞれコントロールポイントとなります。

![bitmap.png](https://qiita-image-store.s3.amazonaws.com/0/140075/02faee43-f04d-08c7-20bb-049cbf49d2b7.png)


3次ベジェ曲線の式は以下のとおりです。

```math
bz = p_{1} \left(- t + 1\right)^{3} + p_{2} t \left(- 3 t + 3\right) \left(- t + 1\right) + p_{3} t^{2} \left(- 3 t + 3\right) + p_{4} t^{3}
```
tの範囲は0 ≦ t ≦ 1です。


ベジェ曲線の式を$bz$とし、近似したい点を$p_x$とすると、誤差を求める関数$j$は以下のようになります。

```math
j = (p_x - bz)^{2}
```

最小二乗法の定義は以下のとおりでしたね。

```math
J = \frac {1}{2}\sum_{k=1}^{n} j(x_k, y_k)
```

求めたいパラメータは$p_2$と$p_3$ですので、それぞれのパラメータで偏微分します。
最小二乗法の定義の1/2がありますので、偏導関数に1/2をあらかじめ掛けてしまいましょう。

さっそく`Sympy`で書いてみましょう。

```python
p0, p1, p2, p3, p4, px, t = symbols("p0 p1 p2 p3 p4 px t")

bz = (1-t)**3*p1 + 3*(1-t)**2*t*p2 + 3*(1-t)*t**2*p3 + t**3*p4
j = (px - bz) ** 2

jp2 = simplify(diff(j, p2) / 2)
jp3 = simplify(diff(j, p3) / 2)
```

$p_2$と$p_3$の偏導関数の1/2は、それぞれ以下のようになります。

```math
\frac{1}{2} \frac{\partial j}{\partial p_2}
=
3 t \left(t - 1\right)^{2} \left(- p_{1} \left(t - 1\right)^{3} + 3 p_{2} t \left(t - 1\right)^{2} - 3 p_{3} t^{2} \left(t - 1\right) + p_{4} t^{3} - px\right)
\\
\frac{1}{2} \frac{\partial j}{\partial p_3}
=
3 t^{2} \left(t - 1\right) \left(p_{1} \left(t - 1\right)^{3} - 3 p_{2} t \left(t - 1\right)^{2} + 3 p_{3} t^{2} \left(t - 1\right) - p_{4} t^{3} + px\right)
```

通過させたい点$p_x$と、その時に$t$のペア値を定義します、例として以下の4点があるとします。

```python
points = [(0, 0), (0.2, 65), (0.7, 45), (1.0, 100)]
```

![ダウンロード (5).png](https://qiita-image-store.s3.amazonaws.com/0/140075/ce6c5050-47e9-02e7-581a-df1b84581b17.png)

始点と終点が$p_1$と$p_4$になりますので、この2つは既知の値となります。
求めたい$p_2$と$p_3$について、偏導関数の総和を連列方程式で解きます。

```python
const = ((p1, points[0][1]), (p4, points[-1][1]))
tp2 = sum([jp2.subs(const + ((t, x[0]), (px, x[1]))) for x in points[1:-1]])
tp3 = sum([jp3.subs(const + ((t, x[0]), (px, x[1]))) for x in points[1:-1]])

solve([tp2, tp3], [p2, p3])
# {p2:180.456349206349,p3:−53.0753968253968} 
```
答えは`p2=180.456349206349,p3=−53.0753968253968`です。

![ダウンロード (6).png](https://qiita-image-store.s3.amazonaws.com/0/140075/4622d55c-6cdd-c37b-7b96-03174e8ba0ab.png)

ちゃんと近似できていますね！

色々なパラメータを見てみましょう。

```python
points = [(0, 0), (0.3, 50), (0.7, 80), (1.0, 100)]
```
![ダウンロード (7).png](https://qiita-image-store.s3.amazonaws.com/0/140075/8c79c155-3112-6f3c-374a-def73508d9ab.png)

```python
points = [(0, 0), (0.2, 34), (0.4, 44), (0.6, 46), (0.8, 60), (1.0, 100)]
```
![ダウンロード (10).png](https://qiita-image-store.s3.amazonaws.com/0/140075/75106f3c-6a57-a461-761a-c70195063757.png)


あまり極端な値でない限り、ちゃんと通過できるようです。
もちろん通過できない値もありますが、なんとなくそれっぽく線を引いてくれます。

## いろいろな曲線を生成してみる

最後にJupyter上でベジェ曲線を最小二乗法で近似して、プロットするセルを記載します。

```python
%matplotlib inline
import matplotlib.pyplot as plt

p0, p1, p2, p3, p4, px, t = symbols("p0 p1 p2 p3 p4 px t")

bz = (1-t)**3*p1 + 3*(1-t)**2*t*p2 + 3*(1-t)*t**2*p3 + t**3*p4
j = (px - bz) ** 2
jp2 = simplify(diff(j, p2) / 2)
jp3 = simplify(diff(j, p3) / 2)
    
def plot(points):
    const = ((p1, points[0][1]), (p4, points[-1][1]))
    tp2 = sum([jp2.subs(const + ((t, x[0]), (px, x[1]))) for x in points[1:-1]])
    tp3 = sum([jp3.subs(const + ((t, x[0]), (px, x[1]))) for x in points[1:-1]])

    vec = solve([tp2, tp3], [p2, p3])

    const = {p1: points[0][1], p2: vec[p2], p3: vec[p3], p4: points[-1][1]}
    bz2 = bz.subs(const)
    x = np.arange(0, 1+0.01, 0.01)
    y = [
        bz2.subs(t, _t)
        for _t in x
    ]
    plt.plot(x, y)
    plt.scatter([p[0] for p in points], [p[1] for p in points])
```

`plot`関数に通過して欲しい点のリストを入力すると、最小二乗法で頑張ってその点を通ってくれます。
使い方は以下のように呼びます。

```python
points = [(0, 0), (0.1, -40), (0.5, -50), (0.8, 30), (1.0, 100)]
plot(points)
```
![ダウンロード.png](https://qiita-image-store.s3.amazonaws.com/0/140075/ffd07906-0fce-f650-e03c-b3e550f84156.png)


# Sympyを使わないで解く

C/C++などSympyがない環境での実装を書きます。

```math
\frac{1}{2} \frac{\partial j}{\partial p_2}
=
3 t \left(t - 1\right)^{2} \left(- p_{1} \left(t - 1\right)^{3} + 3 p_{2} t \left(t - 1\right)^{2} - 3 p_{3} t^{2} \left(t - 1\right) + p_{4} t^{3} - px\right)
\\
\frac{1}{2} \frac{\partial j}{\partial p_3}
=
3 t^{2} \left(t - 1\right) \left(p_{1} \left(t - 1\right)^{3} - 3 p_{2} t \left(t - 1\right)^{2} + 3 p_{3} t^{2} \left(t - 1\right) - p_{4} t^{3} + px\right)
```
とはいえ、使用するのは上の算出済みの2式だけで、不明な点$p_2$と$p_3$を1.0と置き、各項ごとに計算しているだけです。できるだけC/C++っぽく書いてみます。

```python
def lsm(points):
    ''' 最小二乗法の解を行列で返す '''
    p1, p4 = points[0][1], points[-1][1]

    a = b = c = d = e = f = 0.
    for _t, _px in points[1:-1]:
        _t_1_d = (_t - 1.) ** 2
        _t_1 = (_t - 1.)
        
        _p1 = p1 * _t_1 
        _p2 = 3 * _t * _t_1_d
        _p3 = 3 * _t ** 2 * _t_1
        _p4 = p4 * _t ** 3
        
        _j_p2 = 3 * _t * _t_1_d
        _j_p3 = 3 * _t ** 2 * _t_1

        a += _p2 * _j_p2
        b += -_p3 * _j_p2
        c += -_p2 * _j_p3
        d += _p3 * _j_p3
        e += -((-_p1 + _p4 - _px) * _j_p2)
        f += -((_p1 - _p4 + _px) * _j_p3)

    return [
        [a, b],
        [c, d]
    ], [e, f]


def inv(mat, vec):
    ''' 逆行列で連立方程式を解く '''
    dm = mat[0][0] * mat[1][1] - mat[1][0] * mat[0][1]
    assert (dm != 0.0)
    
    a = mat[1][1] / dm
    b = -mat[0][1] / dm
    c = -mat[1][0] / dm
    d = mat[0][0] / dm
    
    return (vec[0] * a + vec[1] * b, 
            vec[0] * c + vec[1] * d)

points = [(0, 0), (0.2, 65), (0.7, 45), (1.0, 100)]
inv(*lsm(points))
```

# あとがき

最小二乗法は、微分可能な関数であれば近似可能という非常に強力な手法です。

ベジェ曲線の近似はゲームプログラムに応用すると楽しそうですね。
偏導関数を事前に`Sympy`で計算しておけば、C/C++などの他言語でも連立方程式を解くアルゴリズムを実装するだけで計算できます。
（連立方程式は「ガウスジョルダンの掃き出し法」など、沢山のアルゴリズムがあります）

やはり`Sympy`は非常に強力なツールですね、変数が多い数式も簡単に最適化/計算してくれます。
計算が苦手なので非常に重宝しています。
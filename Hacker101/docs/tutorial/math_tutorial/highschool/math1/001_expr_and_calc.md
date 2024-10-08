# 旧：クイズ(1:式と計算)

## Q1 多項式と単項式について知っていますか?

??? success
    ### ここで学ぶこと

    ```text
    ・複雑に見える式を、整理して簡潔にする
    ・この節の公式は、主に式を簡潔にするために存在する
    ```

    ### 言葉から、記号へ(コラム：一説であることに留意)

    ```text
    =
      ・2本の平行線が等しく感じられることから ->  等しい
    
    x,y,zが未知数の代表
      ・デカルトによるもの
      ・既知の定数をa,b,cとアルファベットの初めの方にし
      ・未知数をx,y,zと後の方で表現することで分かりやすく分割
    
    +, -
      ・船乗りが水量を管理する際、
        水面に当たる箇所に、-を書き、
        水が下がるたびに、-を書き直し、
        水を足した場合は、-と区別するために縦棒を加え
        +と記したことから
    
    ×, ・, *
      ・数学者の提唱や、書籍から
      ・プログラマにとっては、*に親和性があるかもしれない
    
    ÷, /
      ・起源とは関係ないが、
        上下の２つの・が分母と分子の数値、
        横線が分数の横線を表していると考えられている
      ・ちなみに、/の方が起源が古い
      ・÷を除算記号として使うべきではないと
      　国際規格は定めている（いずれ消えるのでは？）

    ∩, ∪
      ・∪はunionの頭文字から来ており、∩はその対比

    [参考]
      数学記号の由来について[https://www.nli-research.co.jp/report/detail/id=62419?site=nli]
    ```

    ### 単項式

    ```text
    2, x, 3a², (-5)x²yのように、
    数や文字および、それらを掛け合わせてできる式
    ```

    ### 次数

    ```text
    ・掛け合わされた文字の個数を、その単項式の次数という
    ・数だけの単項式の次数は0である
    ・数0の次数については定義しない
    ```

    ### 係数

    ```text
    単項式において、数の部分
    ```

    ### 多項式

    ```text
    ・x²+3xy+(-2y²)のように幾つかの単項式の和として
    　表される式

    ・この時、各単項式を、その多項式の項という

    ・単項式は、項が1つの多項式とも言え、
      多項式は、整式といわれることもある
    ```

    ### 問題1

    ```text
    1 
      Q. 4a⁵の次数
      A. 5
    
    12
      Q. -2xyの次数
      A. 2
    
    3
      Q. -x³y²zの次数
      A. 6
    ```

    ### 特定の文字に着目する

    ```text
    2種類以上の文字を含む単項式の場合、
    特定の文字に着目して係数や次数を考えることがある

    その場合、他の文字は数と同様になる
    ```

    ### 問題2

    ```text
    1
      Q -5ax³y²において、[x]に着目した時の係数と次数
      A 係数: -5ay²　次数3

      Q [y]に着目した場合
      A 係数: -5ax³ 次数2

      Q [a]に着目した場合
      A 係数: -5x³y² 次数 1
    
    2 
      Q 2abxy³において、[x,y]に着目
      A 係数: 2ab 次数4
    ```

## Q2 同類項について知っていますか?

??? success
    ### 同類項

    ```text
    5x²+4x-2x²-3における、5x²と-2x²のように、
    多項式の項の中で、文字の部分が同じである項を指す

    多項式は、同類項を1つにまとめて整理可能
    3と-2のように、文字がない場合も整理可能
    ```

    ### 多項式の次数

    ```text
    ・同類項をまとめて整理したうえで、
      最も次数の高い項の次数を指す
    
    ・次数がnであるとき、その多項式をn次式という

    （繰り返すが、単項式も、項が1つの多項式なので同様）
    ```

    ### 問題3

    ```text
    同類項をまとめて、多項式の次数も言え

    1
      Q 4x²-2x-5-3x²+8x-3
      A x²+6x-8
        2次式

    2 
      Q 3a²-ab+6b²-5a²+9ab-4b²
      A -2a²+8ab+2b²
        2次式

    3
      Q -2x⁴+x³-8x²+7x-1+2x⁴-3x³+x+5
      A -2x³-8x²+8x+4
        3次式
    ```

    ### 多項式(=整式)でないもの

    ```text
    ・1/xは、数、文字、およびそれらの積として表される式に
    　当てはまらないため、多項式ではない

    ・多項式/多項式で表現できるなら有理式
    　または、分数式と言ったりする。
　　
    ・同様の理由で、√xなども、多項式ではない

    ・項が無限個あるようなものも多項式とは言わない
      (-> 冪級数といわれる)
    ```

    ### 問題(深める)

    ```text
    Q 多項式でないものを選べ
      [1] 2x+1,  [2] 1/x,  [3] x²/2 - 3x, [4] x²

    A 整式の定義を見る限り、2,3は有理式であり、多項式ではない
      よって、2,3
    ```

    ### 数0の次数を定義しない理由

    ```text
    ・多項式の次数は、最も次数の高い項の次数を言う
    ・これを念頭に置いたうえで、以下を考える

    ・xについて2次式の多項式を文字であらわすと、
      ax²+bx+c (a≠0)となる。

    ・つまり、xについて0次式を考えた場合、
      c (c≠0)

    ・ここで、一般にm次式とn次式の積は(m+n)次式になる
    ・たとえば、xについて考えるとき

      2x²(2次式) * x(1次式) = 3x³(3次式)
      x²(2次式) * 6(0次式) = 6x²(2次式)

      しかし、0の場合
      x²(2次式) * 0 = 0となる。
      よって、数0を0次式と仮定すると、
      一般にm次式とn次式の積は(m+n)次式になるという関係は
      成り立たなくなり、不都合が生じる

      よって、数0の次数は考えないとすることで、
      明快さを保っている
    ```

## Q3 定数項について知っていますか?

??? success
    ### 定数項

    ```text
    ・多項式において、着目した文字を含まない項

    ・たとえばax²+bx+cの時、xについて見ると定数項はc
    ```

    ### 問題4

    ```text
    次の多項式を見て、次数と定数項を述べよ

    [1] ax²+x -3
      A. xに着目した場合、2次式で、定数項は-3
      A. aに着目した場合、1次式で、定数項はx-3
    
    [2] x²-(a+b)x+ab
      A. xに着目した場合2次式で、定数項はab
    
    [3] 5x²+2x²y-y²+1
      A. xに着目した場合2次式で、定数項は-y²+1
      A. yに着目した場合2次式で、定数項は5x²+1
      A. xとyに着目した場合3次式で、定数項は1
    ```

## Q4 降べきの順と昇べきの順について知っていますか?

??? success
    ### 降べきの順

    ```text
    ・多項式を、ある文字に着目して、
      次数が高いものから順に並べること
    ```

    ### 昇べきの順

    ```text
    ・多項式を、ある文字に着目して、
      次数が低いものから順に並べること
    ```

    ### 問題5

    ```text
    xについて降べきの順に整理せよ
    
    [1] 2x²-1+5x+x⁴-3x³
    A. x⁴-3x³+2x²+5x-1

    [2] 2x²+xy+3y²-7x-2y+5
    A. 2x²+ (y-7)x + 3y²- 2y +5
    ```

## Q5 多項式の加法と減法および乗法のやり方について知っていますか?

??? success
    ### 交換律/結合律/分配法則

    ```text
    多項式の加法/減法/乗法の場合、数の場合と同様、
    以下の法則が成り立つ

    交換法則(交換律)
      ・与えられた演算の二つの引数を
      　互いに入れ替えても結果が変わらないこと
      ・多項式の加算/減算/乗算は、交換律を満たす

      ・A + B = B + A
      ・AB = BA
    
    結合法則(結合律)
      ・どの順番で二項演算を行っても、結果は変わらない
      ・(A+B)+C = A + (B+C) = A + B + C
      ・(A*B)*C = A * (B * C) = ABC
    
    分配法則
      ・集合Sに対して、積×と和+が定義されているとき、
        以下が任意の元a,b,cについて成り立てば、
        この積は和に対して分配法則を満たすという
      ・式の展開を行う際に用いると、
        多項式の積を1つの多項式で表現可能になる
      ・A*(B+C) = AB + AC
      ・(A+B)*C = AC + BC
    ```

    ### 問題6(多項式の加減)

    ```text
    多項式A,Bについて、A+BとA-Bを計算せよ

    [1]  A=4x³-3x²-5x+2 , B = -3x³+x²-2x-7
      A. A+B = (4x³-3x²-5x+2) +  (-3x³+x²-2x-7)
             = x³-2x²-7x-5
        
         A-B = (4x³-3x²-5x+2) -  (-3x³+x²-2x-7)
             = (4x³-3x²-5x+2) + (3x³-x²+2x+7)
             = 7x³-4x²-3x+9
    
    [2] A = 2x³-1-x²,  B = -x²+2x-4x³+1
      A. A+B = (2x³-x²-1) + (-4x³-x²+2x+1)
             = -2x³-2x²+2x
         
         A-B = (2x³-x²-1) - (-4x³-x²+2x+1)
             = (2x³-x²-1) + (4x³+x²-2x-1)
             = 6x³ -2x - 2

    [3] A = 5x²+2xy-y² , B = -3x²+4xy-2y²
    　A. A+B =  5x²+2xy-y² + (-3x²+4xy-2y²)
             = 2x²+6xy-3y²

         A-B =  5x²+2xy-y² - (-3x²+4xy-2y²)
             =  5x²+2xy-y² + (3x²-4xy+2y²)
             = 8x²-2xy+y²
    ```
    
    ### 問題7

    ```text
    A = 2x²+3xy-y², B = -3x²-xy+2y², C = -x²+xy+3y²
    2(A-B)-(4A+B-C)を計算せよ

    2(A-B)-(4A+B-C)
    =2A-2B-4A-B+C
    = -2A-3B+C
    = -2(2x²+3xy-y²) -3(-3x²-xy+2y²) + (-x²+xy+3y²)
    = (-4x²-6xy+2y²) + (9x²+3xy-6y²) +  (-x²+xy+3y²)
    = 4x² -2xy - y²
    ```

    ### 多項式の乗法

    ```text
    ・文字aをいくつか掛け合わせたものを、aの累乗という
    ・aをn個かけた累乗を、aのn乗といい、a^nと表記する

    ・a^nにおける、nを、a^nの指数という
    ```

    $$
      \begin{align}
        & a^1 = a \\
        & a^2 = a \times a \\
        & a^n = \underbrace{a \times a \times \dots \times a}_{n個}
      \end{align}
    $$

## Q6 指数法則について知っていますか?

??? success
    ### 指数法則

    ```text
    ★m,nは正の整数とする
    ```

    $$
      \begin{align}
      & 1. \quad a^{m}a^{n} = a^{m+n} \\
      & 2. \quad (a^m)^n = a^{mn} \\
      & 3. \quad (ab)^n = a^{n}b^{n} \\
      \end{align}
    $$

    ### 例示

    ```text
    a²a³ = (aa)×(aaa) = aaaaa = a^{2+3} = a⁵
    (a²)³ = (aa)³ = (aa)*(aa)*(aa) = a^{2×3} = a⁶
    (ab)³ = ab * ab * ab = aaa*bbb = a³b³
    ```

    ### 問題8

    ```text
    [1] 2x⁴ * 7x²
    = 14x⁶

    [2] 4a⁵bc² * (-3a⁴b³c²)
    = -12a⁹b⁴c⁴

    [3] (-2x²y)³ * (3xy³)²
      = -8x⁶y³ * 9x²y⁶
      = -72x⁸y⁹
    ```

    ### 展開

    ```text
    ・多項式の積を、分配法則を用いて、1つの多項式の形に
      すること。
    ```

    ### 問題9

    ```text
    式を展開せよ
    [1] 
      3x²(3x²-5x+2)
      = 3x²*3x² + 3x²(-5x) + 3x²(2)
      = 9x⁴ - 15x³ + 6x²

    [2]
      (x²-2xy-3y²)(-xy²)
      = x²(-xy²) -2xy(-xy²) -3y²(-xy²)
      = -x³y² + 2x²y³ + 3xy⁴
    
    [3]
      (x³+3x²-4)(x-2)
      = x(x³+3x²-4) -2(x³+3x²-4)
      = x⁴+3x³-4x -2x³-6x²+8
      = x⁴ + x³ -6x² -4x + 8

    [4]
      (x³-3+4x²)(2+x²)
      = x²(x³+4x²-3) + 2(x³+4x²-3)
      = x⁵ + 4x⁴ - 3x² + 2x³ + 8x² -6
      = x⁵ + 4x⁴ + 2x³ + 5x² - 6
    
    [5]
      (x+y)(x²-xy+2y²)
      = x(x²-xy+2y²) + y(x²-xy+2y²)
      = x³ - x²y + 2xy² + x²y -xy² + 2y³
      = x³ + xy² + 2y³
    
    [6]
      (2x-3y+1)(x+y-2)
      = x(2x-3y+1) + y(2x-3y+1) -2(2x-3y+1)
      = (2x² - 3xy + x) + (2xy -3y² + y) + (-4x+6y-2)
      = 2x² -xy -3x -3y² + 7y -2
      = 2x² + (-y-3)x -3y² + 7y - 2
    ```

## Q7 展開の公式について知っていますか?

??? success
    ### 展開の公式

    ```text
    ・分配法則を行えば、同じ結果が得られる
    ・式展開時に、計算の速度上昇目的で利用
    ```

    $$
      \begin{align}
        & (a+b)^2 = a^2 + 2ab + b^2 \\
        & (a-b)^2 = a^2 -2ab + b^2 \\
        & (a+b)(a-b) = a^2 -b^2 \\
        & (x+a)(x+b) = x^2 + (a+b)x + ab \\
      \end{align}
    $$

    ### 問題10

    ```text
    ・展開せよ

    [1] (2x+3)²  : 2x = a, 3 = bとみなして、(a+b)²
    = 4x² + 12x + 9

    [2] (2x-5y)²  : 2x = a, -5x = bとみなして、(a+b)²
    = 4x² - 20xy + 25x²

    [3] (4x+3)(4x-3) : 4x=a, 3 = bとみなして、(a+b)(a-b)
    = 16x² - 9

    [4] (x+2)(x+8) : (x+a)(x+b)
    = x² + 10x + 16

    [5] (x-4)(x-3): (x+a)(x+b)
    = x² -7x + 12

    [6] (x-3y)(x+5y): (x+a)(x+b)
    = x² + 2xy - 15y²
    ```

    ### 展開の公式(続き)

    $$
      (ax+b)(cx+d) = acx² + (ad+bc)x + bd
    $$

    ```text
    展開して確かめればいい

    (ax+b)(cx+d)
    = ax(cx+d) + b(cx+d)
    = acx² + (ad + bc)x + bd
    ```

    ### 問題11

    ```text
    [1]
      (2x+3)(6x+5) : (ax+b)(cx+d)= acx² + (ab+bc)x + bd
    = 12x² + 28x + 15

    [2] (5x+2)(3x-8)
    = 15x² -34x - 16

    [3] (2x-y)(x+3y)
    = 2x² + 5xy - 3y²

    [4] (3x-a)(4x-5a)
    = 12x² -19ax + 5a²

    ```

    ### 展開を工夫する

    ```text
    (a+b+c)² = (a+b+c)(a+b+c)
    = a(a+b+c)+ b(a+b+c)+ c(a+b+c)
    = a² + ab + ca + ab + b² + bc + ca + bc + c²
    = a² + b² + c² + 2(ab + bc + ca)
    ```

    ### 深める

    ```text
    (a+b+c+d)(x+y+z)(p+q)を展開すると、幾つの項ができるか?

    全ての文字が異なるとき、展開しても同類項はできない。
    よって、多項式の項数×多項式の項数になる

    項数は
    4 * 3 * 2 = 24
    ```

    ### 複数の文字を1つの文字で置く

    ```text
    (a+b+c)² 
    a+b = Aとおくと、

    (A+c)²
    = A² + 2Ac + c²
    = (a+b)² + 2(a+b)c + c²
    = a² + 2ab + b² + 2ac + 2ab + c²
    = a² + b² + c² + 2(ab + bc + ca)
    ```

    ### 問題12

    ```text
    展開せよ
    [1] (a+b-c)²
    a - cをAとおく
    (A + b)²
    = A² + 2Ab + b²
    = (a-c)² + 2(a-c)b + b²
    = a² - 2ac + c² + 2ab - 2bc + b²
    = a² + b² + c² + 2(ab - bc - ca)

    [2] (x-2y+z)²
    x-2yをAとおくと

    = A² + 2Az + z²
    = (x-2y)² + 2(x-2y)z + z²
    = x² - 4xy + 4y² + 2xz -4yz + z²
    ```

    ### 共通に現れる式を1つにまとめる方法

    ```text
    指数法則を思い出す

    a²b² = (ab)²
    ```

    ### 問題13

    ```text
    [1]
     (a+b)²(a-b)²
    = {(a+b)(a-b)}²
    = (a²-b²)²
    = a⁴ - 2a²b² + b⁴

    [2]
      (x²+1)(x+1)(x-1)
    = (x²+1)(x²-1)   : (a+b)(a-b)が使える
    = x⁴ - 1

    [3]
      (x²+2x+3)(x²-2x+3)

      x² + 3を共通部分Aとする

      (A+2x)(A-2x)
    = A² -4x²
    = (x²+3)² - 4x²
    = x⁴ + 6x² + 9 - 4x²
    = x⁴ + 2x² + 9

    [4]
      (x-y+z)(x+y-z)
    = {x - (y-z)}{x + (y-z)}

    y -z を共通部分Aとする

    (x-A)(x+A)
    = x² - A²
    = x² - (y-z)²
    = x² - (y² -2yz + z²)
    = x² - y² -z² + 2yz
    ```

## Q8 因数分解について知っていますか?

??? success
    ### 因数分解

    ```text
    ・1つの多項式を、1次以上の多項式の積の形に表す事
      = 元の式を因数分解する
    
    ・この時、積を作っている各式(整式)を、元の式の因数という

    ・因数分解するときは、共通因数をくくりだし
      因数分解の公式を利用することが基本
      式の特徴に着目した工夫を加えることもできる
    ```

    ### 問題14

    ```text
    分配法則からできる
    AB + AC = A(B+C)

    以下の式を因数分解せよ

    [1] 2x²y-6xy² + 10xyz
    = 2xy(x - 3y + 5z)

    [2]
      4xy²z - x²yz² + 2xyz
    = xyz(4y - xz + 2)

    [3]
      a(x-y) - bx + by
      = a(x-y) -b(x-y)
      = (a-b)(x-y)

    [4]
      y(5x-3) + 2(3-5x)
    = y(5x-3) -2(5x-3)
    = (y-2)(5x-3)
    ```

    ### 因数分解の公式(展開の公式を逆に見たもの)

    $$
      \begin{align}
        & a^2 + 2ab + b^2 = (a+b)^2 \\
        & a^2 - 2ab + b^2 = (a-b)^2 \\
        & a^2 - b^2 = (a+b)(a-b) \\
        & x^2 + (a+b)x + ab = (x+a)(x+b) \\
      \end{align}
    $$

    ### 問題15

    ```text
    次の式を因数分解せよ

    [1] x²-8x+16
    = (x-4)²

    [2] 4x² + 28xy + 49y²
    = (2x-7y)²

    [3] 9a² -48ab + 64b²
    = (3a - 8b)²

    [4]
      16x² - 25y²
    = (4x+5y)(4x-5y)

    [5]
      x² + 6x + 8
    = (x+4)(x+2)

    [6]
      x² -5xy + 6y²
    =(x-3y)(x-2y)

    [7]
      x² + xy -12y²
    =(x+4y)(x-3y)

    [8]
      x² - 2ax -15a²
    = (x-5a)(x+3a)
    ```

    ### 因数分解の公式(続き)

    ```text
    acx² + (ad+bc)x + bd = (ax+b)(cx+d)
    ```

    ### たすき掛けについて

    ```text
    acx² + (ab+bc)x + bd = (ax+b)(cx+d)において

    ac = 3, ad + bc = 7, bd = 2となる
    abcdを見つけたいと考える。

    a = 1, c = 3とすると、bd = 2となるのは以下のケース

    {b,d} = {1,2}, {2,1}, {-1,-2}, {-2,-1}
    この中で、ab + bc = 7を満たすのは、b=2, d=1
    つまり、
    3x² + 7x + 2 = (x+2)(3x+1)となるが、
    以上のような計算は、たすき掛けで行うと楽である
    ```

    $$
      \begin{array}{lllll}
      a && b & \rightarrow & bc \\
      & \times &&& \\
      c && d & \rightarrow & ad \\ \hline
      ac && bd && ad + bc
      \end{array}
    $$


    $$
      \begin{array}{lllll}
      1 && 2 & \rightarrow & 6 \\
       & \times &&& \\
      3 && 1 & \rightarrow & 1 \\ \hline
      3 && 2 && 7
      \end{array}
    $$

    ### 問題16

    ```text
    因数分解せよ。適宜、たすき掛けを用いること

    [1]
      2x² + 3x + 1
      ac = 2, bd = 1, ad + bc =  3とすると、
    = (2x+1)(x+1)

    [2]
      4x² - 15x + 9
    = (4x-3)(x-3)

    [3]
      6x² -5x -6
      ac = 6, bd = -6 ad + bc = -5とすると
    = (3x +2)(2x - 3)

    [4]
      3x²-2xy-y²
    = (3x+y)(x-y)

    [5]
      3a²-14ab+8b²
    = (3a -2b)(a -4b)

    [6]
      4x² + 7ax - 2a²
    = (4x - a)(x +2a)
    ```

    ### 式の特徴に着目して変形する

    ```text

    与えられた式を変形して、平方の差 X² - Y²の形にすることで
    (X+Y)(X-Y)に因数分解できる

    x² -y²-2y -1
    = x² - (y² + 2y + 1)
    = x² - (y+1)²
    = {x + (y+1)}{x - (y+1)}
    = (x+y+1)(x-y-1)
    ```

    ### 問題17

    ```text
    [1]
      x²-y² +6y-9
    = x² - (y² -6y + 9)
    = x² - (y-3)²
    = {x + (y-3)}{x - (y-3)}
    = (x+y-3)(x-y+3)

    [2]
      x²-4x + 4 -9y²
    = (x-2)² - 9y²
    = {(x-2) + 3y}{(x-2) - 3y}
    = (x+3y-2)(x-3y-2)
    ```

    ### 問題18

    ```text
    [1]
      x⁴ - 5x² + 4
      x²= Aと置くと

      A² - 5A² + 4
    = (A-4)(A-1)
    = (x²-4)(x²-1)
    = (x+2)(x-2)(x+1)(x-1)

    [2]
      x⁴-81
    = (x²+9)(x²-9)
    = (x²+9)(x-3)(x+3)

    [3]
      (x²+3x)² - 2(x²+3x)-8
    x² + 3x = Aと置くと

    A² - 2A - 8
    = (A-4)(A+2)
    = (x²+3x-4)(x²+3x-2)
    = (x+4)(x-1)(x²+3x-2)
    ```

## Q9 複数種類の文字を含む多項式を、因数分解する方法を知っていますか?

??? success
    ### 次数の低い方の文字について、降べきの順に整理

    ```text
    x³ + x²y - x² - y

    = y(x²-1) + x²(x-1)
    = y(x+1)(x-1) + x²(x-1)
    = (x-1){(x+1)y + x²}
    = (x-1)(x² + xy + y)
    ```

    ### 問題19

    ```text
    [1] zについて降べきの順に整理
      x²-yz+zx-y²
    = x(x-y) + (x-y)(x+y)
    = (x-y)(2x + y)

    [2]
      9b - 9 -3ab + a²
      = b(9-3a) + (a+3)(a-3)
      = -3b(a-3) + (a+3)(a-3)
      = (a-3)(a -3b + 3)
    
    [3]
      2x² + 6xy + x - 3y - 1
    = 3y(2x - 1) + 2x² + x - 1
    = 3y(2x - 1) + (2x - 1)(x + 1)
    = (2x-1)(x + 3y + 1)
    ```

    ### 問題20

    ```text
    1番次数が小さい項が2つあるなら、やりやすい方で
    
    [1] たすき掛けをいかせる
      x² + 3xy + 2y² + 2x + 5y - 3
    = x² + (3y + 2)x + 2y² + 5y - 3
    = x² + (3y + 2)x + (2y-1)(y+3)
    (2y-1) + (y+3) = 3y + 2なので
    a,c = 1, b = 2y-1, d = y+3
    = (x + 2y -1)(x + y + 3)

    [2]
      3x² - xy - 2y² + 6x -y + 3
    = 3x² + (6-y)x - 2y² - y + 3
    = 3x² + (6-y)x + (-2y-3)(y-1)

    acx² + (ad + bc)x + bd =(ax+b)(cx+d)の式に当てはめる
    たすき掛けを試みる
    
    a = 3, c = 1, b = 2y+3, d = -y+1

    3  2y+3   --> 2y+3
    1  -y+1   --> -3y+3

    よって、(3x + 2y + 3)(x -y + 1)
    ```

    ### 問題21

    ```text
    因数分解せよ
    aについて降べきの順に整理しただけ

    ab(a-b) + bc(b-c) + ca(c-a)
    = a²b -ab² + b²c - bc² + c²a - ca²
    = a²(b-c) + a(c²-b²) + bc(b-c)
    = a²(b-c) - a(c+b)(b-c) + bc(b-c)
    = (b-c){a² -a(b+c) + bc}
    = (b-c)(a-b)(a-c)
    = -(a-b)(b-c)(c-a)
    ```

    ### 深める

    ```text
    下記の式をyについて降べきの順に整理して解け

    2x² + 7xy + 6y² + 5x + 7y - 3
    = 6y² + (7x + 7)y + 2x² + 5x -3
    = 6y² + (7x + 7)y + (2x - 1)(x + 3)

    3  2x-1 --> 4x-2
    2  x+3  --> 3x + 9

    = (3y + 2x -1)(2y + x + 3)

    ```

## Q10 3次式の展開と因数分解について知っていますか?

??? success
    ### (a+b)³ と (a-b)³

    ```text
    ・分配法則をいちいち疲れると脳がつかれるので、
      公式が存在する

    (a+b)³ = (a+b)²(a+b)
    = (a² + 2ab + b²)(a+b)
    = a(a² + 2ab + b²) + b(a² + 2ab + b²)
    = a³ + 2a²b + ab² + a²b + 2ab² + b³
    = a³ + 3a²b + 3ab² + b³  ...(1)

    (a-b)³ (1)を使って展開する
    = (a + (-b))³

    = a³ + 3a²(-b) + 3a(-b)² + (-b)³
    = a³ -3a²b + 3ab² -b³
    ``` 

    $$
      \begin{align}
        & (a+b)^3 = a^3 + 3a^2b + 3ab^2 + b^3 \\
        & (a-b)^3 = a^3 - 3a^2b + 3ab^2 - b^3 \\
      \end{align}
    $$

    ### 問題1

    ```text
    展開せよ

    [1] 
      (x+1)³
      = x³ + 3x² + 3x + 1
    
    [2]
      (x-2)³
      = x³ - 6x² + 12x - 8
    
    [3]
      (3a+b)³
      = 27a³ + 3*(3a)²b + 3(3a)b² + b³
      = 27a³ + 27a²b + 9ab² + b³
    
    [4]
      (2x-3y)³
      = (2x)³ -3(2x)²(3y) + 3(2x)(3y)² + -(3y)³
      = 8x³ - 36x²y + 54xy² - 27y³ 

    ```

    ### 3次式の展開公式 & 3次の因数分解の公式
    $$
      \begin{align}
        & (a+b)(a^2-ab+b^2) = a^3 + b^3 \\
        & (a-b)(a^2+ab+b^2) = a^3 - b^3 \\
      \end{align}
    $$

    ### 問題2

    ```text
    ・上記が成り立つことを、左辺を展開して確かめよ

    (a+b)(a² - ab + b²)
    = a²(a+b) -ab(a+b) + b²(a+b)
    = a³ + a²b - a²b -ab² + ab² + b³
    = a³ + b³

    (a-b)(a² + ab + b²)
    a(a² + ab + b²) -b(a² + ab + b²)
    = a³ -b³
    ```

    ### 問題3

    ```text
    ・次の式を展開せよ

    [1]
      (x+2)(x²-2x+4)
    = x³ + 8

    [2]
      (x-3)(x²+3x+9)
    = x³ - 27

    [3]
      (3x+y)(9x²-3xy+y²)
      = 27x³ + y³
    
    [4]
      (2a-3b)(4a²+6ab+9b²)
      = 8a³ - 27b²
    ```

    ### 問題4

    ```text
    因数分解せよ

    [1]
      x³+27
    = (x+3)(x²-3x+9)

    [2]
      a³ - 64
      = (a-4)(a²+4a+16)
    
    [3]
      8x³ - 125y³
      = (2x-5y)(4x²+10xy+25y²)
    ```

## Q11 節末問題

??? success
    ### 復習のため後程実施(~5/16まで)

## Q12 有理数について知っていますか?

??? success

    ### 有理数(Q)について

    ```text
    ・自然数1,2,3 ....に、0, -1,-2,...を合わせて整数という
    ・分母分子ともに整数で、分母が0でない値を有理数という

    ・mを整数とすると、m/1と表現できるため、整数は有理数

    有限小数
      ・小数第何位かで終わる小数
      例） 9/80 = 0.1125
    
    無限小数
      ・小数点以下の数字が無限に続く小数
      ・循環しない場合は、分数で表現できないので無理数
    
    循環小数
      ・幾つかの数字の配列が繰り返されるもの
    ```

    $$
      \begin{align}
      & 循環小数の表記 \\
      & 0.666\dots = 0.\dot{6} \\
      & -0.13636\dots = 0.1\dot{3}\dot{6} \\
      \\
      & 厳密な有理数の定義 \\
      & \mathbb{Q} = \{q | m \in \mathbb{Z}, n \in \mathbb{Z}, n \neq 0, q = m / n \} \\
      \end{align}
    $$

    ### 問題22

    ```text
    ・分数を少数で表せ。循環小数についても上記の様に表せ

    [1]
      1/8 = 0.125

    [2]
      8 /9 = 0.8...
    
    [3]
      7 / 33 = 0.2121...
    
    [4]
      55 / 54 = 1.0185185...
    ```

    $$
      \begin{align}
        & \frac{8}{9} = 0.\dot{8} \\
        & \frac{7}{33} = 0.\dot{2}\dot{1} \\
        & \frac{55}{54} = 1.0\dot{1}8\dot{5} \\
      \end{align}
    $$

    ### 循環小数の表現方法

    ```text
    有理数の定義を思い出す
      ・整数、有限小数、循環小数は有理数
        (=分母分子が整数な、分数の形で表現できるため)
    ```

    ```text
    m,nが正の整数であるとき、
    m/nが、整数、有限小数、循環小数のいずれかであることを
    数の割り算の仕組みから確かめる

    mをnで割ると、各段階の割り算の余りは以下の様になる
    0, ..., n-1

    余りが0になった時点で、計算が終わり
    分数は整数または有限小数と表現できる

    余りに0が出てこない時、余りは1からn-1までの
    (n-1)個の整数のいずれかであるため
    n回目までには、それまでに出てきた余りと
    同じ余りが登場する。

    よって、それ以降は、その間の割り算の繰り返しとなる
    ⇒よって、循環小数で表現できる

    循環小数を分数の形に戻す場合、
    差を考えることで実現可能
    ```

    ### 問題23

    ```text
    次の循環小数を分数で表現せよ
    ```

    $$
      \begin{align}
        & [1] \quad 0.\dot{1} \\
        & [2] \quad  0.\dot{1}\dot{2} \\
        & [3] \quad  0.\dot{6}4\dot{8} \\
        & [4] \quad  6.\dot{2}\dot{7} \\
      \end{align}
    $$

    ```text
    [1]
      10x = 1.111
        x = 0.111
       9x = 1
        x = 1/9

    [2]
      100x = 12.12
         x =  0.12
        99x = 12
          x = 4/33
    
    [3]
      1000x = 648.648
          x =   0.648
      999x = 648
      111x = 72
       37x = 24
         x = 24/37
    
    [4]
      100x = 627.2727
         x =   6.2727
      99x = 627
      33x = 209
      3x = 19
       x = 19/3
    ```

## Q13 実数について知っていますか?

??? success
    ### 実数

    ```text
    ・有理数または無理数で表される数

    ・循環しない無限小数が無理数に当たる

    実数(R)
    ├──有理数(Q)
    |   ├──整数(Z)
    |   |   ├──自然数(N)
    |   |   ├──0
    |   |   └──負の整数
    |   ├──有限小数
    |   └──循環小数
    └──無理数
    ```

    ### 集合上の二項演算と四則演算の範囲

    ```text
    2つの数からそれらの和、差、積、商を得る計算を
    特に、四則演算という
    ※ただし、0除算は考えないものとする

    [1] Nは正の整数とする
      + : N×N --> N
      × : N×N --> N

      つまり、自然数は、足し算、掛け算について閉じている
      一方、引き算、割り算については閉じていない(閉性なし)

      ※このあたりの厳密な理解は、
        公理系を学ぶ際にやることにする
      
      ※後々分かった場合は、単元ごとに書き足すことにする

    [2] Zは整数とする
      + : Z×Z --> Z
      - : Z×Z --> Z
      × : Z×Z --> Z

      つまり、整数は、和、減、乗算について閉じている
      除算については閉じていない

    [3] Qは有理数とする
      Qは、和、減、乗、除算について閉じている
      以下で、除算について閉じていることを証明する
    ```

    $$
      \begin{align}
        & 任意の有理数a,b \in \mathbb{Q} (b \neq 0)に対して、 \\
        & a/bが有理数であることを示したい。\\
        & ここで、有理数a,bは定義より、以下の様に表せる \\
        & a = \frac{m}{n}, \quad b = \frac{p}{q} \\
        & なお、有理数の定義より、m,n,p,q \in \mathbb{Z} \\
        & n \neq 0 , q \neq 0 \\
        & また、b \neq 0なので、p \neq 0になる \\
        & \frac{a}{b} = \frac{m \times q}{n \times p} \\
        & ここでp,n \neq 0であり、整数は乗算について閉じているため、\\
        & \frac{m \times q}{n \times p}は、分母、分子ともに整数であり、 \\
        & 分母が0ではない分数となる。これは有理数の定義に一致する \\
        & よって、\frac{a}{b}は有理数であり、\\
        & 有理数の除算が閉じていることが示された \\
      \end{align}
    $$

    ```text
    [4] Rは実数とする
      Rは、和、差、乗、除算について閉じている
    ```

    ### 問題24

    ```text
    ・それぞれの数の範囲上で、四則計算を行う時、
      演算が閉じているか否かを表にせよ
    ```

    | 数の範囲 | 加法 | 減法 | 乗法 | 除法 |
    | -------- | ---- | ---- | ---- | ---- |
    | 自然数   | 〇   | 〇   | 〇   | ✖    |
    | 整数     | 〇   | 〇   | 〇   | ✖    |
    | 有理数   | 〇   | 〇   | 〇   | 〇   |
    | 実数     | 〇   | 〇   | 〇   | 〇   |

## Q25　数直線と座標について知っていますか?

??? success
    ### 数直線について

    ```text
    ・直線上に基準となる点Oを取り、
      単位の長さと、正の向きを定める
    
    ・仮に、正の向きを右にすると、
      直線上の点Pに対して、次のように実数を対応可能
    
    ・PがOの右側にあり、OPの長さがaの時、正の実数a
    ・PがOの左側にあり、OPの長さがaの時、負の実数-a

    直線上の各点に、1つの実数を対応させるとき、
    この直線を数直線という。Oをその原点という
    ```

    ### 座標について

    ```text
    ・数直線上で、点Pに実数aが対応しているとき、
      aを点Pの座標という

      座標
        ⇒点の位置を指定するために与えられる数の組
          あるいはその各数。
        ⇒二次元の時は座標平面というが、
          座標直線と言ったりしないのだろうか?
    
    ・座標がaである点Pを、P(a)で表す
      (ここら辺は定義か)

    ・直線上の各点に、1つの実数を対応させたので、
      どんな実数aに対しても、aを座標に持つ数直線上の点は
      ただ1つ集まる。
    
    ・つまり、原点と、単位の長さと、正の向きを定め
      各点に実数を1つずつ対応させたとき、
      実数はすべて直線上の点で表現可能   
    ```

    ### 小数部分と整数部分

    ```text
    ・正の向きを右向きとした場合
      実数の大小関係は、左 < 右となる。
    
    ・実数aについて、aを超えない最大の整数n(a >= n)を
      aの整数部分という
    
    ・a-nをaの小数部分という

    ・実数aの整数部分がnであるとき、
      n <= a < n + 1が成り立つ。
    ```

    ### 問題25

    ```text
    [1]
      2.5の整数部分と小数部分

      整数部分 2
      小数部分 2.5 - 2 = 0.5
    
    [2]
      6.5454...の整数部分と小数部分
      整数部分6
      小数部分 6.5454... - 6 = 0.5454...
    
    [3]
      √2の整数部分と小数部分
      整数部分 1
      小数部分 √2 - 1
    
    [4]
      πの整数部分と小数部分
      整数部分 3
      小数部分 π - 3  : 実数 - 整数部分
    ```

## Q26 絶対値の性質について知っていますか?

??? success

    ### 絶対値について

    ```text
    数直線上で、原点O(0)と、点P(a) : 点Pに実数を割り当てた物
    の間の距離を、実数aの絶対値といい、記号|a|で表す
    ```

    ### もう少し詳しく(絶対値の定義)

    $$
      \begin{align}
        & 実数全体の集合\mathbb{R}に属する任意の元xは、
        x \in \mathbb{R}と表現できる。 \\
        & この時、(\mathbb{R}, +)は群であり、
        xの逆元は、-xとなる。 \\
        & ここで、x,-xを要素とする\mathbb{R}の非空な部分集合について考える。 \\
        & つまり、\{x, -x \} \subset \mathbb{R} \\
        & この時、集合\{x, -x \}の最大値を、xの絶対値と呼び、以下の様に表す \\
        & |x| = max\{x, -x \} \\
      \end{align}
    $$

    ### 絶対値の性質の確認

    ```text
    ・上記の定義が、非負性を意味していることを
      以下で述べる
    ```

    $$
      \begin{align}
        & 任意の元x \in \mathbb{R}について考える \\
        & 場合1: x >= 0 \\
        & この時、 -x <= 0となるため(必要十分) \\
        & 大小関係<=の推移律より -x <= x \\
        & よって、|x| = max\{x, -x \} = x \\
        \\
        & 場合2: x < 0 \\
        & この時、-x > 0となるため\\
        & 狭義大小関係 <の推移律より　x < -x \\
        & よって、|x| = max\{x, -x \} = -x \\
        & 従って、実数x \in \mathbb{R}の絶対値は、\\
        & 次のように定義される \\
        & |x| = 
        \begin{cases}
          & x &&(if\quad x >= 0) \\
          & -x &&(if\quad x < 0) \\
        \end{cases} \\
        & よって、絶対値は常に0以上の値を持つ \\
        & |x| >= 0 \quad \forall{x} \in \mathbb{R}
      \end{align}
    $$  

    ### 絶対値の性質まとめ

    ```text
    1 |a| >= 0
    2 a >= 0の時、|a| = a,
    3 a < 0の時、|a| = -a
    ```

    ### 補足(必要十分, 推移律, 狭義大小関係)

    ### 補足(公理, 定義, 定理の違い)

    ```text
    ・先に公理について理解してから、
    　ここに戻ってくることにする
    ```
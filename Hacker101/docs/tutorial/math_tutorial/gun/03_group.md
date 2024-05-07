# クイズ(3:群)

## Q29 席替えと写像の関係性について知っていますか?

??? success
    ### 席替えについて考える

    ```text
    1,2,3という座席番号がついた3つの座席があるとする
    席替えを行い、以下のような席になったとする
    座席1 --> 座席2
    座席2 --> 座席1
    座席3 --> 座席3
    これ(席替えという写像)を以下の様に表記する

    ※具体例を出すのは、現実世界の対象を数学的に考察できる
      ようにするためらしい
    ```

    $$
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 1 & 3 
      \end{pmatrix}
    $$

    ```text
    もう1つ例を出す

    座席1 --> 座席2
    座席2 --> 座席3
    座席3 --> 座席1
    ```

    $$
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 3 & 1 
      \end{pmatrix}
    $$

    ### 席替えと写像

    ```text
    ・座席番号全体の集合をX = {1,2,3}とすると、
      席替えは、集合Xから集合Xへの全単射

    ・定義域の元に終域の元が1つずつ対応しているし、
    　ダブることも、もれもないからね
    ```

    $$
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 1 & 3 \\ 
      \end{pmatrix} = f
      \\
      \begin{align}
        & f: X \rightarrow X \\
        & f: 1 \mapsto 2 \\
        & f: 2 \mapsto 1 \\
        & f: 3 \mapsto 3
      \end{align}
    $$

    ### 全単射である意味

    ```text
    全射
      ・席替えを行った後、空いている座席が存在しない
    
    単射
      ・同じ席に座る人間は存在しない
    
    恒等写像
      ・席が変わらない
    
    fの逆写像
      ・席替えfが行われる前に戻す
    ```

    ### 例

    $$
    \begin{align}
    & a = \begin{pmatrix}
      1 & 2 & 3 \\
      2 & 1 & 3 \\ 
    \end{pmatrix} の逆写像は、
    a^{-1} = \begin{pmatrix}
      2 & 1 & 3 \\
      1 & 2 & 3 \\ 
    \end{pmatrix} であり、 \\
    \\
    & 1行目と2行目を逆にしたものに相当する \\
    \end{align}
    $$

    ```text
    ・ここで、写像と逆写像は相当関係にある
    
    ・なぜなら、定義域X = {1,2,3}の任意の元nに対して、
      a(n) = a^-1(n)だからである

      a(1) = 2 = a^{-1}(1)
      a(2) = 1 = a^{-1}(2)
      a(3) = 3 = a^{-1}(3)

      ⇒ところでそもそも定義域と終域が異なると、
      　二つの写像の対応関係が直接比較できない

      つまり、以下の様に表現できる
    ```

    $$
      \begin{pmatrix}
      1 & 2 & 3 \\
      2 & 1 & 3 \\ 
      \end{pmatrix}
      = 
      \begin{pmatrix}
      2 & 1 & 3 \\
      1 & 2 & 3 \\ 
      \end{pmatrix}
      = 
      \begin{pmatrix}
      1 & 2 & 3 \\
      2 & 1 & 3 \\ 
      \end{pmatrix} ^{-1}
    $$

    $$
      \begin{align}
        a = a^{-1}
      \end{align}
    $$

    ### 例2

    ```text
    ・以下の場合、写像とその逆写像は相当関係にない
    ```

    $$
     r = 
     \begin{pmatrix}
      1 & 2 & 3 \\
      2 & 3 & 1
     \end{pmatrix}
    $$

    ### ここで、席替えという写像全体の集合について考える

    ```text
    ・席替えにより、座席番号全体の集合Xの元をXへ対応させる
      ことは紛れもない写像
    
    ・この対応関係（写像）を集合の要素として以下の様に表す
    ```

    $$
      \begin{align} 
      席替え全体の集合S_3 =
      \left\{
       \begin{pmatrix}
        1 & 2 & 3 \\
        1 & 2 & 3 
      \end{pmatrix} ,
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 1 & 3 
      \end{pmatrix} ,
      \begin{pmatrix}
        1 & 2 & 3 \\
        1 & 3 & 2 
      \end{pmatrix} , \\
       \begin{pmatrix}
        1 & 2 & 3 \\
        3 & 2 & 1 
      \end{pmatrix} ,
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 3 & 1 
      \end{pmatrix} ,
      \begin{pmatrix}
        1 & 2 & 3 \\
        3 & 1 & 2 
      \end{pmatrix}
      \right\}
      \end{align}
    $$

    $$
      \begin{align}
        & 便宜上、以下の様に名前を付ける \\
        & e = 
        \begin{pmatrix}
        1 & 2 & 3 \\
        1 & 2 & 3 
        \end{pmatrix} 全員が同じ座席のまま動かない \\
        & a = 
        \begin{pmatrix}
          1 & 2 & 3 \\
          2 & 1 & 3 
        \end{pmatrix} 座席1,2の2人が座席交換 \\
        & b = 
        \begin{pmatrix}
          1 & 2 & 3 \\
          1 & 3 & 2 
        \end{pmatrix} 座席2,3の2人が座席交換 \\
        & c = 
        \begin{pmatrix}
          1 & 2 & 3 \\
          3 & 2 & 1 
        \end{pmatrix} 座席1,3の2人が座席交換 \\
        & r = 
        \begin{pmatrix}
          1 & 2 & 3 \\
          2 & 3 & 1 
        \end{pmatrix} 全員がぐるっと座席交換 \\
        & s = 
        \begin{pmatrix}
          1 & 2 & 3 \\
          3 & 1 & 2 
        \end{pmatrix} rの逆回り \\
      \end{align}
    $$

    ### 合成写像と席替え

    ```text
    ・2回の席替えを続けて行うと、結果、1回の席替えになる
    ・これは2個の写像を合成した合成写像を求めていることと同義

    例)
      席替えa の次に 席替えrを行う
      これは、r 〇 a　に相当し、以下の様に表現できる
    ```

    $$
      r \circ a = 
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 3 & 1
      \end{pmatrix} \circ
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 1 & 3
      \end{pmatrix}
    $$

    ```text
    ・合成写像が何に相当するか考える
    ```

    $$
      \stackrel{a}{\longmapsto}
    $$




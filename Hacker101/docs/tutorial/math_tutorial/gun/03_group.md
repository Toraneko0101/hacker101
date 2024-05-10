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
    $$

    $$
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

    ※矢印の上に、写像が載っている下の表記は
      写像による、左の像が右であることを示している
    ```


    $$
      \begin{align}
        & 1 \stackrel{a}{\longmapsto} 2 \stackrel{r}{\longmapsto} 3 \\
        & 2 \stackrel{a}{\longmapsto} 1 \stackrel{r}{\longmapsto} 2 \\
        & 3 \stackrel{a}{\longmapsto} 3 \stackrel{r}{\longmapsto} 1 \\
        \\
        & つまり\\
        & 1 \stackrel{r \circ a}{\longmapsto} 3 \\
        & 2 \stackrel{r \circ a}{\longmapsto} 2 \\
        & 3 \stackrel{r \circ a}{\longmapsto} 1 \\
        \\
        & r \circ a = 
        \begin{pmatrix}
          1 & 2 & 3 \\
          3 & 2 & 1 \\
        \end{pmatrix} = c
      \end{align}
    $$

    ### ${r \circ a \neq a \circ r ?}$

    ```text
    ・実際に確かめて考える
    ```

    $$
    \begin{align}
      & a \circ r = 
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 1 & 3
      \end{pmatrix} 
      \circ
      \begin{pmatrix}
        1 & 2 & 3 \\
        2 & 3 & 1
      \end{pmatrix}
      = 
      \begin{pmatrix}
        1 & 2 & 3 \\
        1 & 3 & 2
      \end{pmatrix}  
      = b \\
      \\
      & よって、 r \circ a \neq a \circ r
    \end{align}
    $$

    ### 二項演算の演算表について考える

    ```text
    ・席替え全体の集合S_3には6個の元がある。
    　そのため、二回の席替えを続けて行うと、
      合成写像の種類は36個になる

      これを、席替えの演算とみなし、
      定義域をS_3×S_3(席替えの組み合わせの直積)
      値域をS_3(結果)とすると、
      席替えの演算は、直積S_3×S_3からS_3への写像なので
      S_3上の二項演算ということができる
    ```

    $$
     \begin{align}
      \circ : S_3 \times S_3 \rightarrow S_3
     \end{align}
    $$

    ```text
    ・二項演算を表で表すと以下の様になる
    ```

    | 〇  | e   | a   | b   | c   | r   | s   |
    | --- | --- | --- | --- | --- | --- | --- |
    | e   | e   | a   | b   | c   | r   | s   |
    | a   | a   | e   | r   | s   | b   | c   |
    | b   | b   | s   | e   | r   | c   | a   |
    | c   | c   | r   | s   | e   | a   | b   |
    | r   | r   | c   | a   | b   | s   | e   |
    | s   | s   | b   | c   | a   | e   | r   |


    ### まとめ

    ```text
    ・二つの席替えから、1回の席替えを作成
    ・誰も動かない席替え
    ・逆の席替え

    以上の3点について述べてきた。
    これらを抽象化することで、群が定義される
    ```

## Q30 群の定義について知っていますか?


# Javaチュートリアル

## 目的

```text
・Spring Frameworkで適当なアプリが作れるくらいになりたい
  というのが表の目標

・Javaの基本的な記法から初めて、2カ月くらいで達成したい

・ところで、現状、Javaの印象はよくない

・Write once, Write once, run anywhere.の代わりに
  冗長的で、不要な記述が多いという印象

・型推論があることは知っているが、
  個人開発以外で使っていいのかどうかについても知らない

・この印象を覆すのが裏の目標
```

## 参考書籍

```text
・プロになるJava(Java17であることに注意)
・改訂新版-Spring-Framework超入門-
  やさしくわかるWebアプリ開発

※Java18以上の機能については、気が向いたら補完する
  string templates(JDK22の奴)については使うかも
```

## 環境

```text
$ java --version
openjdk 22.0.1 2024-04-16
OpenJDK Runtime Environment (build 22.0.1+8-16)
OpenJDK 64-Bit Server VM (build 22.0.1+8-16, mixed mode, sharing)
```

## 補足(String template)

```text
C:\>jshell --enable-preview
|  JShellへようこそ -- バージョン22.0.1
|  概要については、次を入力してください: /help intro

jshell> STR."Today is \{new Date()} now"
$1 ==> "Today is Thu May 23 22:48:46 JST 2024 now"

jshell> /exit
```

# useSyncExternalStore

### 概要
- 外部ストアをサブスクライブできるようにするReact Hook
- Reactを通さずにブラウザ側で起こった変化を管理したい場合に用いる

### 例
- ブラウザは`navigator.onLine`というプロパティを用いてネットワーク接続の状態を公開しており、これはReactの管理外で変更される可能性がある。そのため`useSyncExternalStore`を使用して読み取る

### 注意点
- React18以上でないと使えない


```js
import {useSyncExternalStore} from "react";

function useOnlineStatus(){
    //subscribe: ストアが変更されるたびに呼び出されるコールバック
    //getSnapshot: 現在のストアの値を返す
    //getSnapshot関数は、ブラウザAPIから現在の値を読み取る
    //subscribe関数は、コールバック引数に対応するイベントを登録し、戻り値として登録を解除する関数を返す
    return useSyncExternalStore(subscribe, getSnapshot);
}

function subscribe(callback){
    //navigator.onLineの値をcallbackで使用している
    //接続状態が変わったときに発火
    window.addEventListener("online", callback);
    window.addEventListener("offline", callback);

    return () =>{
        window.removeEventListener("online", callback);
        window.removeEventListener("offline", callback);
    }
}

function getSnapshot(){
    return navigator.onLine;
}

export default function App(){
    const isOnline = useOnlineStatus();
    return <h1>{isOnline ? 'Online' : 'Disconnected'}</h1>
}
```

## 補足(callbackについて)
- callback関数は、引数として他の関数に渡され、外側の関数の中で呼び出され、何らかのアクションを行う関数を指す

```js
function greeting(name){
    alert(`Hello, ${name}`);
}

function main(callback){
    const name = prompt("Please enter your name.");
    callback(name); //greetingの引数としてmainを呼ぶ
}

main(greeting);
```
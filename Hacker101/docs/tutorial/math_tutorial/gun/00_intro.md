# Kindleを買ったものの末路

## 学習方法

```text
・画面を二分割して、Kindle Cloud Readerを左側、
  VSCodeを右側において、メモを取っている

・Kindle Cloud Readerは「何故か」拡大縮小機能がついていない
　ため、以下の様にして対策した

・Kindle for PCも試したが、画質が何か合わなかった


[参考]
  Kindle Cloud Reader 2x Zoom Extension(studio77mm)
```

## 拡張機能のコード

```text
・上がmanifest.jsonで、
  下がcontent.js

・二つをディレクトリの中に入れたら、
  デベロッパーモードをONにして、
  パッケージ化されていない拡張機能を読み込む感じで
  (Keyの割り当ては好きなようにどうぞ)
```

```json
{
  "name": "I hate Kindle",
  "description": "Kindleの拡大縮小機能",
  "version": "0.1",
  "manifest_version": 3,
  "content_scripts": [
    {
      "matches": [
        "https://read.amazon.co.jp/?asin=*"
      ],
      "js": [
        "content.js"
      ]
    }
  ]
}
```

```js
"use strict";

// 定数とグローバル変数
const ZOOM_RATIO = 1.5;
let isZoomed = false;
const SCROLL_STEP = 50; // スクロールのステップ量

const iframeSelectors = [
  "iframe#column_0_frame_0",
  "iframe#column_0_frame_1",
  "iframe#column_0_frame_2",
  "iframe#column_0_frame_3",
  "iframe#column_0_frame_4",
  "iframe#column_0_frame_5"
];

// イベントリスナーの設定
//window.addEventListener('dblclick', handleDoubleClick);
window.addEventListener('wheel', handleWheel);
window.addEventListener('resize', handleResize);
window.addEventListener('keydown', handleKeyDown);

// ダブルクリックイベントハンドラー
// function handleDoubleClick() {
//   const visibleFrameIndex = getVisibleFrameIndex();
//   if (visibleFrameIndex !== -1) {
//     const iframe = document.querySelector(iframeSelectors[visibleFrameIndex]);
//     const doc = iframe.contentDocument;
//     zoomContent(doc, iframe, ZOOM_RATIO);
//   }
// }

// ホイールイベントハンドラー
function handleWheel(event) {
  if (isZoomed) {
    const visibleFrameIndex = getVisibleFrameIndex();
    if (visibleFrameIndex !== -1) {
      adjustScroll(visibleFrameIndex, event.deltaY, 0);
    }
  }
}

// リサイズイベントハンドラー
// function handleResize() {
//   location.reload();
// }

// リサイズイベントハンドラー
function handleResize() {
  // location.reload(); // 不要なリロードを避ける
  const visibleFrameIndex = getVisibleFrameIndex();
  if (visibleFrameIndex !== -1) {
    resetScrollPosition(visibleFrameIndex);
  }
}

// キーダウンイベントハンドラー
function handleKeyDown(event) {
  const visibleFrameIndex = getVisibleFrameIndex();
  if (visibleFrameIndex === -1) return;
  switch (event.key) {
    case 'p':
      const iframe = document.querySelector(iframeSelectors[visibleFrameIndex]);
      const doc = iframe.contentDocument;
      zoomContent(doc, iframe, 1 / ZOOM_RATIO);
      break;
    case 'o':
      const iframe2 = document.querySelector(iframeSelectors[visibleFrameIndex]);
      const doc2 = iframe2.contentDocument;
      zoomContent(doc2, iframe2, ZOOM_RATIO);
      break;
    case 'h':
      adjustScroll(visibleFrameIndex, 0, -SCROLL_STEP);
      break;
    case 'l':
      adjustScroll(visibleFrameIndex, 0, SCROLL_STEP);
      break;
    case 'j':
      adjustScroll(visibleFrameIndex, SCROLL_STEP, 0);
      break;
    case 'k':
      adjustScroll(visibleFrameIndex, -SCROLL_STEP, 0);
      break;
  }
}

// 現在表示中のiframeのインデックスを取得
function getVisibleFrameIndex() {
  try {
    for (let i = 0; i < iframeSelectors.length; i++) {
      const iframe = document.querySelector(iframeSelectors[i]);
      if (iframe && iframe.style.visibility === "visible") {
        return i;
      }
    }
  } catch (error) {
    console.error("Visibility check error:", error);
  }
  return -1;
}

// コンテンツのズーム
function zoomContent(doc, iframe, ratio) {
  const imgs = doc.querySelectorAll("img.amzn-sliced-inimg-imgslice");
  if (imgs.length === 0) {
    console.error("No images found for zooming.");
    return;
  }

  imgs.forEach(img => {
    img.style.height = `${parseFloat(img.style.height) * ratio}px`;
    img.style.width = `${parseFloat(img.style.width) * ratio}px`;
  });

  const containerDiv = doc.querySelector("div.amzn-sliced-inimg-div");
  if (containerDiv) {
    containerDiv.style.height = `${parseFloat(containerDiv.style.height) * ratio}px`;
    containerDiv.style.width = `${parseFloat(containerDiv.style.width) * ratio}px`;
  }

  iframe.style.height = `${parseFloat(iframe.style.height) * ratio}px`;
  iframe.style.width = `${parseFloat(iframe.style.width) * ratio}px`;
  iframe.style.left = "0px";
  iframe.style.top = "0px";

  const rendererDiv = document.querySelector("div#kr-renderer");
  if (rendererDiv) {
    rendererDiv.style.left = "0px";
    rendererDiv.querySelectorAll("div").forEach(div => {
      div.style.height = `${parseFloat(div.style.height) * ratio}px`;
      div.style.width = `${parseFloat(div.style.width) * ratio}px`;
    });
  }

  isZoomed = ratio !== 1;
  setupMutationObservers();
}

// スクロール位置の調整
function adjustScroll(visibleFrameIndex, deltaY, deltaX) {
  try {
    const iframe = document.querySelector(iframeSelectors[visibleFrameIndex]);
    const imgs = iframe.contentDocument.querySelectorAll("img.amzn-sliced-inimg-imgslice");

    if (imgs.length === 0) {
      console.error("No images found for scrolling.");
      return;
    }

    let newTop = parseFloat(imgs[0].style.top) - deltaY;
    let newLeft = parseFloat(imgs[0].style.left) - deltaX;
    const maxScrollV = parseFloat(imgs[0].style.height) * imgs.length - window.innerHeight;
    const maxScrollH = parseFloat(imgs[0].style.width) - window.innerWidth;

    if (newTop > 0) {
      newTop = 0;
    } else if (newTop < -maxScrollV) {
      newTop = -maxScrollV;
    }

    if (newLeft > 0) {
      newLeft = 0;
    } else if (newLeft < -maxScrollH) {
      newLeft = -maxScrollH;
    }

    imgs.forEach(img => {
      img.style.top = `${newTop}px`;
      img.style.left = `${newLeft}px`;
    });
  } catch (error) {
    console.error("Scroll adjustment error:", error);
  }
}

// スクロール位置のリセット
function resetScrollPosition(visibleFrameIndex) {
  try {
    const iframe = document.querySelector(iframeSelectors[visibleFrameIndex]);
    const imgs = iframe.contentDocument.querySelectorAll("img.amzn-sliced-inimg-imgslice");

    if (imgs.length === 0) {
      console.error("No images found for resetting scroll position.");
      return;
    }

    imgs.forEach(img => {
      img.style.top = "0px";
      img.style.left = "0px";
    });
  } catch (error) {
    console.error("Reset scroll position error:", error);
  }
}

// MutationObserverの設定
function setupMutationObservers() {
  const config = { attributes: true, attributeFilter: ['style'] };

  iframeSelectors.forEach(selector => {
    const iframe = document.querySelector(selector);
    if (iframe) {
      new MutationObserver(() => {
        iframe.style.left = "0px";
      }).observe(iframe, config);
    }
  });
}

```

## ここだけの話

```text
・拡張機能を書いてみたはいいけれど、
　手動でPDFに変換した方がずっとまし
  でもそれも面倒だし、画質もよくないし......

・ああ、固定レイアウトが憎い！
　でも横に並べて読みたい！ ......ってことで、
　結局見開き用のブックスタンドと書籍版を買うのでした
```
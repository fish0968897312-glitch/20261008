---
title: 選擇題測驗卷網站講義（學生版）.md

---

---
title: 選擇題測驗卷網站講義（學生版）

---

---
title: 選擇題測驗卷網站講義（學生版）
tags: [114程式設計與實習_上學期]

---

# 選擇題測驗卷網站講義（學生版）

學號：＿＿＿＿＿＿＿＿　　姓名：＿＿＿＿＿＿＿＿

> **填寫方式**
> 1. 每個學習都要放：**執行截圖**、**三次問 AI 的提示詞**、**最後採用的程式碼**。
> 2. 問 AI 的提示詞請**逐字貼上**自己實際輸入的內容（不要寫摘要），第一次、第二次、第三次依序記錄。
> 3. 程式碼貼在「點開貼上」的收合區塊裡，貼上**你最後真正採用、而且能執行**的版本。

---

## 學習1：產生一個選擇題測驗卷網站

https://cfchen58.synology.me/115/week4/stage1/

**這個階段的目標：** 用 p5.js 做出一個一次顯示一題、四個選項、答完會顯示對錯與總分的測驗網站（題目先寫在程式裡）。
**這個階段會修改的檔案：** index.html、sketch.js

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）


![螢幕擷取畫面 2026-10-08 141141]
![415730125](https://hackmd.io/_uploads/S1eQC34izx.gif)

### 第一次問 AI
使用p5.js撰寫一個選擇題網頁測驗系統，我已經產生一個p5.js專案，請把程式碼寫到sketch.js檔案內，每條指令都須加上中文註解，測驗系統題目設定為五題，測驗題目的內容為程式設計p5.js簡易指令練習測驗，系統採用全螢幕畫布，使用者答錯時，系統會在正確答案選項上，加上559cad背景顏色，該選項藥商下跳動，答錯的選項採用fe5a4b背景顏色，選項左右移動。選擇題選項共有四個選項，當五題結束後，需要顯示答對的題數，每次顯示一個題目，需要有下一題的按鈕 

-
```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容
// 測驗題目資料庫（共 5 題 p5.js 基礎指令測驗）
let questions = [
  {
    question: "1. 在 p5.js 中，設定畫布大小為 800x600 的指令是什麼？",
    options: ["createCanvas(800, 600)", "setupCanvas(800, 600)", "size(800, 600)", "windowSize(800, 600)"],
    answer: 0
  },
  {
    question: "2. 想要在畫布上繪製一個圓形，應該使用哪一個指令？",
    options: ["rect()", "line()", "circle()", "triangle()"],
    answer: 2
  },
  {
    question: "3. 若要設定圖形的填滿顏色為紅色，正確的指令是？",
    options: ["stroke(255, 0, 0)", "fill(255, 0, 0)", "color(255, 0, 0)", "background(255, 0, 0)"],
    answer: 1
  },
  {
    question: "4. p5.js 中會以每秒約 60 次頻率重複執行的主要函數是？",
    options: ["setup()", "start()", "loop()", "draw()"],
    answer: 3
  },
  {
    question: "5. 代表當前滑鼠 X 軸座標位置的內建變數是？",
    options: ["mouseX", "cursorX", "pointX", "posX"],
    answer: 0
  }
];

let currentQuestion = 0;       // 目前進行到的題目編號（從 0 開始）
let score = 0;                 // 答對題數統計
let selectedOption = -1;       // 使用者點選的選項索引（-1 表示尚未選擇）
let isAnswered = false;        // 目前題目是否已經回答過
let animTime = 0;              // 用於驅動動態效果的時間計數器

// 按鈕與選項版面配置變數
let optionButtons = [];        // 儲存四個選項的按鈕範圍區域
let nextBtnBounds = {};        // 儲存下一題按鈕的範圍區域

function setup() {
  // 建立全螢幕畫布
  createCanvas(windowWidth, windowHeight);
  // 設定文字對齊方式為居中
  textAlign(CENTER, CENTER);
}

function draw() {
  // 每一影格更新時間計數器（用於跳動與左右震動動畫）
  animTime += 0.1;

  // 設定背景顏色為深灰藍色
  background(30, 41, 59);

  // 判斷測驗是否已經結束
  if (currentQuestion < questions.length) {
    // 繪製目前的測驗題目與選項
    drawQuizScreen();
  } else {
    // 繪製最終結算成績畫面
    drawResultScreen();
  }
}

// 繪製測驗畫面
function drawQuizScreen() {
  let q = questions[currentQuestion];

  // 繪製頂部進度條背景與文字
  fill(255);
  textSize(20);
  text(`第 ${currentQuestion + 1} 題 / 共 ${questions.length} 題`, width / 2, 50);

  // 繪製題目內文
  textSize(26);
  fill(241, 245, 249);
  text(q.question, width / 2, 130);

  // 設定選項尺寸與位置參數
  let optWidth = min(width * 0.8, 600);   // 選項外框寬度
  let optHeight = 60;                     // 選項外框高度
  let startY = 200;                       // 第一個選項的起始 Y 座標
  let spacing = 80;                       // 選項之間的間距

  optionButtons = []; // 清空前一次計算的按鈕點擊區域

  // 迴圈繪製四個選擇題選項
  for (let i = 0; i < q.options.length; i++) {
    let x = width / 2;
    let y = startY + i * spacing;

    // 預設樣式：未點選時為深藍灰色背景
    let bgColor = color(51, 65, 85);
    let textColor = color(255);
    let offsetX = 0; // X 軸偏移量（左右震動）
    let offsetY = 0; // Y 軸偏移量（上下跳動）

    // 當使用者已作答時的樣式與動畫邏輯
    if (isAnswered) {
      if (i === q.answer) {
        // 【正確答案選項】：採用 #559cad 背景顏色
        bgColor = color("#559cad");
        // 如果使用者答錯，正確答案會產生上下跳動效果
        if (selectedOption !== q.answer) {
          offsetY = sin(animTime * 3) * 12; // 使用正弦波產生上下躍動
        }
      } else if (i === selectedOption && selectedOption !== q.answer) {
        // 【使用者答錯的選項】：採用 #fe5a4b 背景顏色
        bgColor = color("#fe5a4b");
        // 答錯選項產生左右劇烈震動效果
        offsetX = sin(animTime * 10) * 12; // 使用高頻正弦波產生左右搖晃
      }
    }

    // 儲存選項點擊偵測區域（包含偏移量計算）
    optionButtons.push({
      x: x - optWidth / 2,
      y: y - optHeight / 2,
      w: optWidth,
      h: optHeight
    });

    // 推入圖層樣式狀態
    push();
    translate(x + offsetX, y + offsetY);

    // 繪製選項圓角矩形背景
    fill(bgColor);
    stroke(100, 116, 139);
    strokeWeight(2);
    rectMode(CENTER);
    rect(0, 0, optWidth, optHeight, 12);

    // 繪製選項文字
    noStroke();
    fill(textColor);
    textSize(20);
    text(q.options[i], 0, 0);

    // 還原圖層樣式狀態
    pop();
  }

  // 若使用者已點選答案，顯示「下一題」按鈕
  if (isAnswered) {
    drawNextButton(startY + 4 * spacing + 20);
  }
}

// 繪製「下一題」按鈕
function drawNextButton(btnY) {
  let btnW = 180; // 按鈕寬度
  let btnH = 50;  // 按鈕高度
  let btnX = width / 2;

  // 紀錄下一題按鈕的點擊偵測區域
  nextBtnBounds = {
    x: btnX - btnW / 2,
    y: btnY - btnH / 2,
    w: btnW,
    h: btnH
  };

  push();
  translate(btnX, btnY);

  // 按鈕背景顏色（綠色）
  fill(34, 197, 94);
  noStroke();
  rectMode(CENTER);
  rect(0, 0, btnW, btnH, 25);

  // 按鈕文字內容
  fill(255);
  textSize(22);
  let btnLabel = (currentQuestion === questions.length - 1) ? "查看結果" : "下一題";
  text(btnLabel, 0, 0);

  pop();
}

// 繪製測驗結果結算畫面
function drawResultScreen() {
  fill(255);
  textSize(36);
  text("測驗結束！", width / 2, height / 2 - 80);

  // 顯示得分與答對題數
  textSize(28);
  fill(148, 163, 184);
  text(`您的總得分為：${score * 20} 分`, width / 2, height / 2 - 10);

  textSize(24);
  fill(52, 211, 153);
  text(`答對題數：${score} / ${questions.length} 題`, width / 2, height / 2 + 50);

  // 繪製重新開始測驗按鈕
  let btnW = 180;
  let btnH = 50;
  let btnX = width / 2;
  let btnY = height / 2 + 140;

  nextBtnBounds = {
    x: btnX - btnW / 2,
    y: btnY - btnH / 2,
    w: btnW,
    h: btnH
  };

  push();
  translate(btnX, btnY);
  fill(59, 130, 246); // 藍色背景
  rectMode(CENTER);
  rect(0, 0, btnW, btnH, 25);

  fill(255);
  textSize(20);
  text("再試一次", 0, 0);
  pop();
}

// 監聽滑鼠點擊事件
function mousePressed() {
  // 當測驗還在進行中時
  if (currentQuestion < questions.length) {
    // 情況一：尚未作答，檢測是否點擊了四個選項之一
    if (!isAnswered) {
      for (let i = 0; i < optionButtons.length; i++) {
        let btn = optionButtons[i];
        // 判斷滑鼠座標是否在選項邊界內
        if (mouseX >= btn.x && mouseX <= btn.x + btn.w &&
            mouseY >= btn.y && mouseY <= btn.y + btn.h) {
          
          selectedOption = i;  // 記錄選取的選項
          isAnswered = true;   // 標記為已作答

          // 判斷是否答對並累加分數
          if (selectedOption === questions[currentQuestion].answer) {
            score++;
          }
          break;
        }
      }
    } 
    // 情況二：已經作答，檢測是否點擊了「下一題」按鈕
    else {
      if (mouseX >= nextBtnBounds.x && mouseX <= nextBtnBounds.x + nextBtnBounds.w &&
          mouseY >= nextBtnBounds.y && mouseY <= nextBtnBounds.y + nextBtnBounds.h) {
        
        currentQuestion++;     // 進入下一題
        isAnswered = false;    // 重置作答狀態
        selectedOption = -1;   // 重置選擇選項
      }
    }
  } 
  // 當測驗結束，點擊「再試一次」按鈕重置測驗
  else {
    if (mouseX >= nextBtnBounds.x && mouseX <= nextBtnBounds.x + nextBtnBounds.w &&
        mouseY >= nextBtnBounds.y && mouseY <= nextBtnBounds.y + nextBtnBounds.h) {
      
      currentQuestion = 0;
      score = 0;
      isAnswered = false;
      selectedOption = -1;
    }
  }
}

// 當使用者調整視窗大小時，自動重繪畫布大小以維持全螢幕
function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}
:::info
:::spoiler 點開貼上學習1的程式碼
```javascript=
//學習1程式碼所在

```
:::


---

## 學習2：網頁設定為響應式網頁

https://cfchen58.synology.me/115/week4/stage2/

**這個階段的目標：** 讓網站在電腦、平板、手機（直向與橫向）都能正常顯示，視窗大小改變時版面自動調整。
**這個階段會修改的檔案：** index.html、sketch.js

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）


![{CC80814A-77F6-41DE-A3ED-F01DA5A0D923}](https://hackmd.io/_uploads/B1Nly6Nozx.png)

### 第一次問 AI
讓網站在電腦、平板、手機（直向與橫向）都能正常顯示，視窗大小改變時版面自動調整。題目也不可以切到
```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容
// 測驗題目資料庫（共 5 題 p5.js 基礎指令測驗）
let questions = [
  {
    question: "1. 在 p5.js 中，設定畫布大小為 800x600 的指令是什麼？",
    options: ["createCanvas(800, 600)", "setupCanvas(800, 600)", "size(800, 600)", "windowSize(800, 600)"],
    answer: 0
  },
  {
    question: "2. 想要在畫布上繪製一個圓形，應該使用哪一個指令？",
    options: ["rect()", "line()", "circle()", "triangle()"],
    answer: 2
  },
  {
    question: "3. 若要設定圖形的填滿顏色為紅色，正確的指令是？",
    options: ["stroke(255, 0, 0)", "fill(255, 0, 0)", "color(255, 0, 0)", "background(255, 0, 0)"],
    answer: 1
  },
  {
    question: "4. p5.js 中會以每秒約 60 次頻率重複執行的主要函數是？",
    options: ["setup()", "start()", "loop()", "draw()"],
    answer: 3
  },
  {
    question: "5. 代表當前滑鼠 X 軸座標位置的內建變數是？",
    options: ["mouseX", "cursorX", "pointX", "posX"],
    answer: 0
  }
];

let currentQuestion = 0;       // 目前進行到的題目編號（從 0 開始）
let score = 0;                 // 答對題數統計
let selectedOption = -1;       // 使用者點選的選項索引（-1 表示尚未選擇）
let isAnswered = false;        // 目前題目是否已經回答過
let animTime = 0;              // 用於驅動動態效果的時間計數器

// 按鈕與選項版面配置變數
let optionButtons = [];        // 儲存四個選項的按鈕範圍區域
let nextBtnBounds = {};        // 儲存下一題按鈕的範圍區域

function setup() {
  // 建立全螢幕畫布
  createCanvas(windowWidth, windowHeight);
  // 設定文字對齊方式為水平與垂直皆居中
  textAlign(CENTER, CENTER);
}

function draw() {
  // 每一影格更新時間計數器（用於跳動與左右震動動畫）
  animTime += 0.1;

  // 設定背景顏色為深灰藍色
  background(30, 41, 59);

  // 判斷測驗是否已經結束
  if (currentQuestion < questions.length) {
    // 繪製目前的測驗題目與選項（回應式置中版）
    drawQuizScreen();
  } else {
    // 繪製最終結算成績畫面（回應式置中版）
    drawResultScreen();
  }
}

// 繪製測驗畫面（支援手機直/橫向、平板與電腦全自動適應）
function drawQuizScreen() {
  let q = questions[currentQuestion];

  // 計算整個測驗區塊的中心基準點
  let centerX = width / 2;
  let centerY = height / 2;

  // 動態計算字型大小（兼顧高解析度電腦與小螢幕手機）
  let titleSize = constrain(min(width * 0.025, height * 0.03), 14, 18);
  let questionSize = constrain(min(width * 0.038, height * 0.04), 16, 24);
  let optionTextSize = constrain(min(width * 0.032, height * 0.032), 13, 18);

  // 動態計算元件寬度與高度
  let optWidth = min(width * 0.88, 560);
  let optHeight = constrain(height * 0.07, 38, 56); // 隨視窗高度彈性調整選項高度
  let spacing = optHeight + constrain(height * 0.018, 8, 16); // 彈性調整選項間距

  // 題目換行寬度限制（防止題目文字溢出畫布）
  let maxQuestionWidth = optWidth;

  // 計算頂部進度條與題目高度 offset，確保全畫面完美垂直居中
  let headerOffset = constrain(height * 0.1, 40, 70);
  let optionsStartY = centerY - (spacing * 1.5) + 10;

  // 1. 繪製頂部進度條文字
  fill(148, 163, 184);
  textSize(titleSize);
  text(`第 ${currentQuestion + 1} 題 / 共 ${questions.length} 題`, centerX, optionsStartY - headerOffset - 35);

  // 2. 繪製題目內文（使用 auto wrap 自動換行，絕不超出視窗）
  rectMode(CENTER);
  textSize(questionSize);
  fill(241, 245, 249);
  text(q.question, centerX, optionsStartY - headerOffset, maxQuestionWidth, 80);

  optionButtons = []; // 清空前一次計算的按鈕點擊區域

  // 3. 迴圈繪製四個選擇題選項
  for (let i = 0; i < q.options.length; i++) {
    let x = centerX;
    let y = optionsStartY + i * spacing;

    // 預設樣式：未點選時為深藍灰色背景
    let bgColor = color(51, 65, 85);
    let textColor = color(255);
    let offsetX = 0; // X 軸偏移量（左右震動）
    let offsetY = 0; // Y 軸偏移量（上下跳動）

    // 當使用者已作答時的樣式與動畫邏輯
    if (isAnswered) {
      if (i === q.answer) {
        // 【正確答案選項】：採用 #559cad 背景顏色
        bgColor = color("#559cad");
        // 如果使用者答錯，正確答案會產生上下跳動效果
        if (selectedOption !== q.answer) {
          offsetY = sin(animTime * 3) * 8; // 使用正弦波產生上下躍動
        }
      } else if (i === selectedOption && selectedOption !== q.answer) {
        // 【使用者答錯的選項】：採用 #fe5a4b 背景顏色
        bgColor = color("#fe5a4b");
        // 答錯選項產生左右劇烈震動效果
        offsetX = sin(animTime * 10) * 8; // 使用高頻正弦波產生左右搖晃
      }
    }

    // 儲存選項點擊偵測區域（包含動態偏移量計算）
    optionButtons.push({
      x: x - optWidth / 2,
      y: y - optHeight / 2,
      w: optWidth,
      h: optHeight
    });

    // 推入圖層樣式狀態
    push();
    translate(x + offsetX, y + offsetY);

    // 繪製選項圓角矩形背景
    fill(bgColor);
    stroke(100, 116, 139);
    strokeWeight(1.5);
    rectMode(CENTER);
    rect(0, 0, optWidth, optHeight, 10);

    // 繪製選項文字（支援超過寬度自動縮放限制）
    noStroke();
    fill(textColor);
    textSize(optionTextSize);
    text(q.options[i], 0, 0, optWidth - 20, optHeight);

    // 還原圖層樣式狀態
    pop();
  }

  // 4. 若使用者已點選答案，顯示「下一題」按鈕
  if (isAnswered) {
    let nextBtnY = optionsStartY + 3.5 * spacing + constrain(height * 0.05, 30, 50);
    drawNextButton(centerX, nextBtnY);
  }
}

// 繪製「下一題」按鈕（回應式大小與位置）
function drawNextButton(btnX, btnY) {
  let btnW = constrain(min(width * 0.4, 180), 120, 200); // 按鈕動態寬度
  let btnH = constrain(height * 0.06, 36, 48);          // 按鈕動態高度

  // 紀錄下一題按鈕的點擊偵測區域
  nextBtnBounds = {
    x: btnX - btnW / 2,
    y: btnY - btnH / 2,
    w: btnW,
    h: btnH
  };

  push();
  translate(btnX, btnY);

  // 按鈕背景顏色（綠色）
  fill(34, 197, 94);
  noStroke();
  rectMode(CENTER);
  rect(0, 0, btnW, btnH, 25);

  // 按鈕文字內容
  fill(255);
  textSize(constrain(btnH * 0.45, 14, 20));
  let btnLabel = (currentQuestion === questions.length - 1) ? "查看結果" : "下一題";
  text(btnLabel, 0, 0);

  pop();
}

// 繪製測驗結果結算畫面（全動態回應式）
function drawResultScreen() {
  let centerX = width / 2;
  let centerY = height / 2;

  let titleSize = constrain(min(width * 0.07, height * 0.06), 22, 36);
  let scoreSize = constrain(min(width * 0.05, height * 0.045), 18, 26);
  let subTextSize = constrain(min(width * 0.04, height * 0.038), 16, 22);

  fill(255);
  textSize(titleSize);
  text("測驗結束！", centerX, centerY - constrain(height * 0.12, 50, 90));

  // 顯示得分與答對題數
  textSize(scoreSize);
  fill(148, 163, 184);
  text(`您的總得分為：${score * 20} 分`, centerX, centerY - constrain(height * 0.03, 10, 25));

  textSize(subTextSize);
  fill(52, 211, 153);
  text(`答對題數：${score} / ${questions.length} 題`, centerX, centerY + constrain(height * 0.05, 20, 40));

  // 繪製重新開始測驗按鈕（居中）
  let btnW = constrain(min(width * 0.4, 180), 120, 200);
  let btnH = constrain(height * 0.06, 38, 48);
  let btnX = centerX;
  let btnY = centerY + constrain(height * 0.15, 70, 110);

  nextBtnBounds = {
    x: btnX - btnW / 2,
    y: btnY - btnH / 2,
    w: btnW,
    h: btnH
  };

  push();
  translate(btnX, btnY);
  fill(59, 130, 246); // 藍色背景
  noStroke();
  rectMode(CENTER);
  rect(0, 0, btnW, btnH, 25);

  fill(255);
  textSize(constrain(btnH * 0.45, 14, 20));
  text("再試一次", 0, 0);
  pop();
}

// 監聽滑鼠點擊與觸控點擊事件
function mousePressed() {
  handleInteraction();
}

// 處理選擇與下一題按鈕互動邏輯
function handleInteraction() {
  if (currentQuestion < questions.length) {
    // 情況一：尚未作答，檢測是否點擊了四個選項之一
    if (!isAnswered) {
      for (let i = 0; i < optionButtons.length; i++) {
        let btn = optionButtons[i];
        if (mouseX >= btn.x && mouseX <= btn.x + btn.w &&
            mouseY >= btn.y && mouseY <= btn.y + btn.h) {
          
          selectedOption = i;  // 記錄選取的選項
          isAnswered = true;   // 標記為已作答

          if (selectedOption === questions[currentQuestion].answer) {
            score++;
          }
          break;
        }
      }
    } 
    // 情況二：已經作答，檢測是否點擊了「下一題」按鈕
    else {
      if (mouseX >= nextBtnBounds.x && mouseX <= nextBtnBounds.x + nextBtnBounds.w &&
          mouseY >= nextBtnBounds.y && mouseY <= nextBtnBounds.y + nextBtnBounds.h) {
        
        currentQuestion++;     // 進入下一題
        isAnswered = false;    // 重置作答狀態
        selectedOption = -1;   // 重置選擇選項
      }
    }
  } 
  // 當測驗結束，點擊「再試一次」按鈕重置測驗
  else {
    if (mouseX >= nextBtnBounds.x && mouseX <= nextBtnBounds.x + nextBtnBounds.w &&
        mouseY >= nextBtnBounds.y && mouseY <= nextBtnBounds.y + nextBtnBounds.h) {
      
      currentQuestion = 0;
      score = 0;
      isAnswered = false;
      selectedOption = -1;
    }
  }
}

// 當使用者縮放或調整視窗大小時，自動重繪畫布並重新計算回應式元件位置
function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}
:::info
:::spoiler 點開貼上學習2的程式碼
```javascript=
//學習2程式碼所在

```
:::


---

## 學習3：設定嵌入 Google 字型，網頁文字採用這些字型

https://cfchen58.synology.me/115/week4/stage3/

**這個階段的目標：** 從 Google Fonts 嵌入繁體中文字型，並讓畫布上的題目與選項文字使用這些字型。
**這個階段會修改的檔案：** index.html、sketch.js

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）

![學習3截圖](請貼上截圖)

### 第一次問 AI

```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容

:::info
:::spoiler 點開貼上學習3的程式碼
```javascript=
//學習3程式碼所在

```
:::


---

## 學習4：設定題庫並抽題顯示題目網頁（CSV 檔案）

https://cfchen58.synology.me/115/week4/stage4/

**這個階段的目標：** 把題目移到 questions.csv，網站讀取題庫後每次隨機抽出 5 題。
**這個階段會修改的檔案：** index.html、sketch.js、questions.csv

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）

![學習4截圖](請貼上截圖)

### 第一次問 AI

```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容

:::info
:::spoiler 點開貼上學習4的程式碼
```javascript=
//學習4程式碼所在

```
:::


---

## 學習5：利用 Google Sheets 當題庫

https://cfchen58.synology.me/115/week4/stage5/

**這個階段的目標：** 把題庫放在 Google 試算表，網站直接讀取，老師改試算表，網站題目就跟著更新。
**這個階段會修改的檔案：** index.html、sketch.js（questions.csv 當備用題庫）

### 執行截圖

（把截圖拖曳到這裡，或貼上圖片連結）

![學習5截圖](請貼上截圖)

### 第一次問 AI

```tex!
（逐字貼上你第一次問 AI 的提示詞）
```

### 第二次問 AI

```tex!
（逐字貼上你第二次問 AI 的提示詞）
```

### 第三次問 AI

```tex!
（逐字貼上你第三次問 AI 的提示詞）
```

### 程式碼內容

:::info
:::spoiler 點開貼上學習5的程式碼
```javascript=
//學習5程式碼所在

```
:::


---

## 我的心得

這五個學習中，哪一個最困難？你是怎麼解決的？（請寫出實際發生的事）

＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿＿

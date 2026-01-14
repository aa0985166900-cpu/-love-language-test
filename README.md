<!doctype html>
<html lang="zh-Hant">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Love Language Test｜愛的語言測驗</title>
<style>
body{
  margin:0;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI","Noto Sans TC",sans-serif;
  background:#0b1220;color:#eaf1ff; line-height: 1.5;
}
.wrap{max-width:600px;margin:auto;padding:20px}
.card{background:#0f1b33;border:1px solid #20365f;border-radius:14px;padding:20px;margin-bottom:16px}
h1,h2,h3{margin:8px 0; color: #6ea8ff;}
p{color:#b6c7e6;line-height:1.6}

/* 題目樣式 */
.q-item { margin-bottom: 25px; border-bottom: 1px solid #1c3470; padding-bottom: 15px; }
.q-title { font-weight: bold; margin-bottom: 10px; display: block; }
.opt-label { 
    display: block; background: #14264d; padding: 12px; margin: 8px 0; 
    border-radius: 8px; cursor: pointer; transition: 0.3s;
}
.opt-label:hover { background: #1c3470; }
.opt-label input { margin-right: 10px; }

button.main-btn {
  width: 100%; padding: 15px; border-radius: 10px; border: none;
  background: #3b82f6; color: #fff; font-size: 18px; font-weight: bold; cursor: pointer;
}
button.main-btn:hover { background: #2563eb; }

.small{font-size:13px;color:#9bb0d1}
.resultBox{background: #162545; padding: 15px; border-radius: 8px; border-left:4px solid #6ea8ff; margin:12px 0}
.lang{float:right}
.lang button { background: transparent; color: #6ea8ff; border: 1px solid #6ea8ff; margin-left: 5px; cursor: pointer; border-radius: 4px;}
</style>
</head>

<body>
<div class="wrap">

<div class="card">
  <div class="lang">
    <button onclick="setLang('zh')">中文</button>
    <button onclick="setLang('en')">EN</button>
  </div>
  <h1 id="title">愛的語言測驗</h1>
  <p id="desc">這是一個幫助你了解自己在親密關係中如何感受與表達愛的心理測驗。</p>
</div>

<div class="card" id="quiz-container">
    <div id="quiz"></div>
</div>

<div class="card">
  <button class="main-btn" onclick="calc()">查看結果 / View Results</button>
</div>

<div class="card" id="result" style="display:none"></div>

</div>

<script>
let lang="zh";
// 分數初始化
let score={W:0,T:0,G:0,S:0,P:0};
let attract={A:0,B:0,C:0};

const text={
zh:{
title:"愛的語言測驗",
desc:"這是一個幫助你了解自己在親密關係中如何感受與表達愛的心理測驗。",
qPrefix: "問題",
optPrefix: "選項",
types:{W:"肯定的言語",T:"精心時刻",G:"禮物",S:"行動服務",P:"身體接觸"},
analysis:{
W:"你對語言高度敏感，被理解與肯定會直接影響你的安全感。",
T:"你透過陪伴與投入時間感受連結，缺乏相處會讓你感到疏離。",
G:"你重視象徵與記憶，具體的心意會讓你感到被放在心上。",
S:"你相信行動勝過承諾，被實際支持會讓你最有感。",
P:"你透過肢體距離與溫度建立親密與信任。"
},
risk:{
W:"若肯定不足，容易自我懷疑。",
T:"若時間被忽略，會感到被冷落。",
G:"若心意缺席，會誤解為不被在乎。",
S:"若付出失衡，容易感到疲累。",
P:"若接觸不足，可能感到疏離。"
},
advice:{
W:"練習直接說出你的需要，也主動給予肯定。",
T:"把相處時間視為必要，而不是附加選項。",
G:"讓對方知道你重視的是心意而非價格。",
S:"學會請求回饋，不必凡事自己扛。",
P:"清楚表達界線，讓親密是安全的。"
},
attract:{
A:"你容易被外在與氣質吸引。",
B:"你重視心靈與對話連結。",
C:"你重視安全感與穩定度。"
}
},
en:{
title:"Love Language Test",
desc:"This test helps you understand how you give and receive love in relationships.",
qPrefix: "Question",
optPrefix: "Option",
types:{W:"Words of Affirmation",T:"Quality Time",G:"Receiving Gifts",S:"Acts of Service",P:"Physical Touch"},
analysis:{
W:"You feel loved through verbal affirmation and clarity.",
T:"You connect through shared time and presence.",
G:"You value symbolic gestures and meaning.",
S:"You trust actions more than promises.",
P:"You build intimacy through physical closeness."
},
risk:{
W:"Lack of affirmation may cause self-doubt.",
T:"Lack of time may feel like distance.",
G:"Missing gestures may feel like neglect.",
S:"Over-giving may cause burnout.",
P:"Lack of touch may feel isolating."
},
advice:{
W:"Express needs clearly and affirm others.",
T:"Protect quality time intentionally.",
G:"Clarify meaning matters more than price.",
S:"Allow others to support you.",
P:"Communicate boundaries for safety."
},
attract:{
A:"You are drawn to appearance and vibe.",
B:"You prioritize mental connection.",
C:"You value safety and stability."
}
}
};

// 題目數據：[選項1代碼, 選項2代碼]
const questions=[
["W","T"],["W","T"],["G","S"],["G","S"],["P","T"],
["P","T"],["W","S"],["W","S"],["G","P"],["G","P"],
["T","P"],["G","T"],["A","B"],["B","C"],["A","C"] // 最後三題為吸引力測試
];

// 初始化渲染題目
function renderQuiz(){
    const quizDiv = document.getElementById("quiz");
    quizDiv.innerHTML = "";
    questions.forEach((q, index) => {
        const qHtml = `
            <div class="q-item">
                <span class="q-title">${text[lang].qPrefix} ${index + 1}</span>
                <label class="opt-label">
                    <input type="radio" name="q${index}" value="${q[0]}" required> ${text[lang].optPrefix} A: ${text[lang].types[q[0]] || text[lang].attract[q[0]] || q[0]}
                </label>
                <label class="opt-label">
                    <input type="radio" name="q${index}" value="${q[1]}"> ${text[lang].optPrefix} B: ${text[lang].types[q[1]] || text[lang].attract[q[1]] || q[1]}
                </label>
            </div>
        `;
        quizDiv.innerHTML += qHtml;
    });
}

function setLang(l){
    lang=l;
    document.getElementById("title").innerText=text[l].title;
    document.getElementById("desc").innerText=text[l].desc;
    renderQuiz(); // 切換語言時重新渲染
}

function calc(){
    // 重置分數
    score={W:0,T:0,G:0,S:0,P:0};
    attract={A:0,B:0,C:0};

    // 收集答案
    const inputs = document.querySelectorAll('input[type="radio"]:checked');
    if(inputs.length < questions.length){
        alert(lang === 'zh' ? "請回答所有問題再查看結果！" : "Please answer all questions!");
        return;
    }

    inputs.forEach(input => {
        const val = input.value;
        if(score.hasOwnProperty(val)) score[val]++;
        if(attract.hasOwnProperty(val)) attract[val]++;
    });

    // 計算最高分
    let top = Object.keys(score).sort((a,b)=>score[b]-score[a])[0];
    let att = Object.keys(attract).sort((a,b)=>attract[b]-attract[a])[0];

    // 顯示結果
    const resDiv = document.getElementById("result");
    resDiv.style.display="block";
    resDiv.innerHTML=`
        <h2>${text[lang].types[top]}</h2>
        <div class="resultBox">
            <h3>${lang==="zh"?"核心分析":"Analysis"}</h3>
            <p>${text[lang].analysis[top]}</p>
        </div>
        <div class="resultBox">
            <h3>${lang==="zh"?"可能盲點":"Blind Spot"}</h3>
            <p>${text[lang].risk[top]}</p>
        </div>
        <div class="resultBox">
            <h3>${lang==="zh"?"給你的建議":"Advice"}</h3>
            <p>${text[lang].advice[top]}</p>
        </div>
        <div class="resultBox">
            <h3>${lang==="zh"?"伴侶吸引偏好":"Attraction Preference"}</h3>
            <p>${text[lang].attract[att]}</p>
        </div>
        <button onclick="window.scrollTo(0,0)" style="margin-top:10px; background:none; border:1px dashed #6ea8ff; color:#6ea8ff; padding:5px 10px; cursor:pointer">↑ 回到頂部</button>
    `;
    resDiv.scrollIntoView({behavior: "smooth"});
}

// 頁面加載後執行
renderQuiz();
</script>
</body>
</html>

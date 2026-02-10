<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Hisab Website</title>

<style>
body{
    margin:0;
    background:#000;
    color:#fff;
    font-family:Segoe UI, Arial;
    min-height:100vh;
    display:flex;
    justify-content:center;
    align-items:center;
}
.card{
    width:360px;
    background:#111;
    padding:20px;
    border-radius:16px;
    box-shadow:0 0 25px #facc15;
}
h2{
    text-align:center;
    color:#facc15;
    margin-bottom:10px;
}
input, textarea{
    width:100%;
    padding:12px;
    margin:8px 0;
    border:none;
    border-radius:8px;
    outline:none;
}
textarea{ resize:none; }
button{
    width:100%;
    padding:12px;
    background:#facc15;
    border:none;
    border-radius:8px;
    font-weight:bold;
    margin-top:6px;
    cursor:pointer;
}
.small{ background:#333; color:#fff; }
.del{ background:#c1121f; color:#fff; }
.entry{
    background:#1c1c1c;
    padding:10px;
    margin-top:8px;
    border-radius:8px;
    font-size:13px;
}
.hidden{ display:none; }
.note{
    font-size:12px;
    color:#aaa;
    text-align:center;
}
</style>
</head>

<body>

<!-- 🔐 MOBILE + OTP LOGIN -->
<div class="card" id="loginBox">
    <h2>Mobile Login</h2>
    <input id="loginMobile" placeholder="Mobile Number">
    <button onclick="sendOTP()">Send OTP</button>
    <div class="note" id="otpNote"></div>

    <div id="otpBox" class="hidden">
        <input id="otpInput" placeholder="Enter OTP">
        <button onclick="verifyOTP()">Verify OTP</button>
    </div>
</div>

<!-- 🧾 HISAB FORM -->
<div class="card hidden" id="formBox">
    <h2>Hisab</h2>

    <input id="userName" placeholder="Naam">
    <input id="mobile" placeholder="Mobile Number">

    <input type="date" id="workDate">
    <input type="time" id="workTime">

    <textarea id="detail" rows="3" placeholder="Kaam Detail"></textarea>
    <input id="amount" placeholder="Paise">

    <!-- 🔍 QUICK HISTORY SEARCH (NEW FEATURE) -->
    <input id="searchMobile" placeholder="History dekhne ke liye Mobile Number">
    <button class="small" onclick="searchHistory()">Search History</button>

    <button onclick="saveAndSend()">Send on WhatsApp</button>
    <button class="small" onclick="openHistory()">View My History</button>
</div>

<!-- 📜 HISTORY -->
<div class="card hidden" id="historyBox">
    <h2>History</h2>
    <div id="historyList"></div>
    <button class="del" onclick="deleteAll()">Delete All History</button>
    <button class="small" onclick="backToForm()">Back</button>
</div>

<script>
let generatedOTP = "";

/* 📩 SEND OTP */
function sendOTP(){
    let mobile = loginMobile.value.trim();
    if(mobile.length !== 10){
        alert("10 digit mobile number dalo");
        return;
    }
    generatedOTP = Math.floor(100000 + Math.random() * 900000);
    sessionStorage.setItem("otp", generatedOTP);
    sessionStorage.setItem("mobile", mobile);
    otpNote.innerText = "DEMO OTP: " + generatedOTP;
    otpBox.classList.remove("hidden");
}

/* ✅ VERIFY OTP */
function verifyOTP(){
    if(otpInput.value == sessionStorage.getItem("otp")){
        loginBox.classList.add("hidden");
        formBox.classList.remove("hidden");
        mobile.value = sessionStorage.getItem("mobile");
        sessionStorage.removeItem("otp");
    }else{
        alert("Wrong OTP");
    }
}

/* 💾 SAVE + 📲 WHATSAPP */
function saveAndSend(){
    let userName = userNameInput();
    let mobileNo = mobile.value.trim();

    if(mobileNo.length !== 10){
        alert("10 digit mobile number dalo");
        return;
    }

    let data = {
        id: Date.now(),
        name: userName,
        mobile: mobileNo,
        date: workDate.value,
        time: workTime.value,
        detail: detail.value,
        amount: amount.value
    };

    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all.push(data);
    localStorage.setItem("hisab", JSON.stringify(all));

    let msg = `HISAB
Naam: ${data.name}
Mobile: ${data.mobile}
Tarikh: ${data.date}
Time: ${data.time}
Detail: ${data.detail}
Paise: ${data.amount}`;

    let url = "https://api.whatsapp.com/send?phone=91"+data.mobile+"&text="+encodeURIComponent(msg);
    window.location.href = url;
}

function userNameInput(){
    let n = userName.value.trim();
    if(n===""){
        alert("Naam bharo");
        throw "";
    }
    return n;
}

/* 📜 OPEN MY HISTORY */
function openHistory(){
    loadHistory(sessionStorage.getItem("mobile"));
    formBox.classList.add("hidden");
    historyBox.classList.remove("hidden");
}

/* 🔍 SEARCH HISTORY BY ANY NUMBER */
function searchHistory(){
    let searchNo = searchMobile.value.trim();
    if(searchNo.length !== 10){
        alert("10 digit mobile number dalo");
        return;
    }
    loadHistory(searchNo);
    formBox.classList.add("hidden");
    historyBox.classList.remove("hidden");
}

/* 📜 LOAD HISTORY */
function loadHistory(number){
    historyList.innerHTML="";
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    let filtered = all.filter(e => e.mobile === number);

    if(filtered.length === 0){
        historyList.innerHTML = "<div class='entry'>No history found</div>";
        return;
    }

    filtered.forEach(e=>{
        historyList.innerHTML += `
        <div class="entry">
            ${e.date} ${e.time}<br>
            ${e.detail}<br>
            ₹${e.amount}
            <button class="del" onclick="deleteOne(${e.id})">Delete</button>
        </div>`;
    });
}

/* ❌ DELETE ONE */
function deleteOne(id){
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all = all.filter(e => e.id !== id);
    localStorage.setItem("hisab", JSON.stringify(all));
    loadHistory(searchMobile.value || sessionStorage.getItem("mobile"));
}

/* ❌ DELETE ALL (CURRENT NUMBER) */
function deleteAll(){
    if(!confirm("All history delete karni hai?")) return;
    let num = searchMobile.value || sessionStorage.getItem("mobile");
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all = all.filter(e => e.mobile !== num);
    localStorage.setItem("hisab", JSON.stringify(all));
    loadHistory(num);
}

/* 🔙 BACK */
function backToForm(){
    historyBox.classList.add("hidden");
    formBox.classList.remove("hidden");
}
</script>

</body>
</html><!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Hisab Website</title>

<style>
body{
    margin:0;
    background:#000;
    color:#fff;
    font-family:Segoe UI, Arial;
    min-height:100vh;
    display:flex;
    justify-content:center;
    align-items:center;
}
.card{
    width:360px;
    background:#111;
    padding:20px;
    border-radius:16px;
    box-shadow:0 0 25px #facc15;
}
h2{
    text-align:center;
    color:#facc15;
    margin-bottom:10px;
}
input, textarea{
    width:100%;
    padding:12px;
    margin:8px 0;
    border:none;
    border-radius:8px;
    outline:none;
}
textarea{ resize:none; }
button{
    width:100%;
    padding:12px;
    background:#facc15;
    border:none;
    border-radius:8px;
    font-weight:bold;
    margin-top:6px;
    cursor:pointer;
}
.small{ background:#333; color:#fff; }
.del{ background:#c1121f; color:#fff; }
.entry{
    background:#1c1c1c;
    padding:10px;
    margin-top:8px;
    border-radius:8px;
    font-size:13px;
}
.hidden{ display:none; }
.note{
    font-size:12px;
    color:#aaa;
    text-align:center;
}
</style>
</head>

<body>

<!-- 🔐 MOBILE + OTP LOGIN -->
<div class="card" id="loginBox">
    <h2>Mobile Login</h2>
    <input id="loginMobile" placeholder="Mobile Number">
    <button onclick="sendOTP()">Send OTP</button>
    <div class="note" id="otpNote"></div>

    <div id="otpBox" class="hidden">
        <input id="otpInput" placeholder="Enter OTP">
        <button onclick="verifyOTP()">Verify OTP</button>
    </div>
</div>

<!-- 🧾 HISAB FORM -->
<div class="card hidden" id="formBox">
    <h2>Hisab</h2>

    <input id="userName" placeholder="Naam">
    <input id="mobile" placeholder="Mobile Number">

    <input type="date" id="workDate">
    <input type="time" id="workTime">

    <textarea id="detail" rows="3" placeholder="Kaam Detail"></textarea>
    <input id="amount" placeholder="Paise">

    <!-- 🔍 QUICK HISTORY SEARCH (NEW FEATURE) -->
    <input id="searchMobile" placeholder="History dekhne ke liye Mobile Number">
    <button class="small" onclick="searchHistory()">Search History</button>

    <button onclick="saveAndSend()">Send on WhatsApp</button>
    <button class="small" onclick="openHistory()">View My History</button>
</div>

<!-- 📜 HISTORY -->
<div class="card hidden" id="historyBox">
    <h2>History</h2>
    <div id="historyList"></div>
    <button class="del" onclick="deleteAll()">Delete All History</button>
    <button class="small" onclick="backToForm()">Back</button>
</div>

<script>
let generatedOTP = "";

/* 📩 SEND OTP */
function sendOTP(){
    let mobile = loginMobile.value.trim();
    if(mobile.length !== 10){
        alert("10 digit mobile number dalo");
        return;
    }
    generatedOTP = Math.floor(100000 + Math.random() * 900000);
    sessionStorage.setItem("otp", generatedOTP);
    sessionStorage.setItem("mobile", mobile);
    otpNote.innerText = "DEMO OTP: " + generatedOTP;
    otpBox.classList.remove("hidden");
}

/* ✅ VERIFY OTP */
function verifyOTP(){
    if(otpInput.value == sessionStorage.getItem("otp")){
        loginBox.classList.add("hidden");
        formBox.classList.remove("hidden");
        mobile.value = sessionStorage.getItem("mobile");
        sessionStorage.removeItem("otp");
    }else{
        alert("Wrong OTP");
    }
}

/* 💾 SAVE + 📲 WHATSAPP */
function saveAndSend(){
    let userName = userNameInput();
    let mobileNo = mobile.value.trim();

    if(mobileNo.length !== 10){
        alert("10 digit mobile number dalo");
        return;
    }

    let data = {
        id: Date.now(),
        name: userName,
        mobile: mobileNo,
        date: workDate.value,
        time: workTime.value,
        detail: detail.value,
        amount: amount.value
    };

    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all.push(data);
    localStorage.setItem("hisab", JSON.stringify(all));

    let msg = `HISAB
Naam: ${data.name}
Mobile: ${data.mobile}
Tarikh: ${data.date}
Time: ${data.time}
Detail: ${data.detail}
Paise: ${data.amount}`;

    let url = "https://api.whatsapp.com/send?phone=91"+data.mobile+"&text="+encodeURIComponent(msg);
    window.location.href = url;
}

function userNameInput(){
    let n = userName.value.trim();
    if(n===""){
        alert("Naam bharo");
        throw "";
    }
    return n;
}

/* 📜 OPEN MY HISTORY */
function openHistory(){
    loadHistory(sessionStorage.getItem("mobile"));
    formBox.classList.add("hidden");
    historyBox.classList.remove("hidden");
}

/* 🔍 SEARCH HISTORY BY ANY NUMBER */
function searchHistory(){
    let searchNo = searchMobile.value.trim();
    if(searchNo.length !== 10){
        alert("10 digit mobile number dalo");
        return;
    }
    loadHistory(searchNo);
    formBox.classList.add("hidden");
    historyBox.classList.remove("hidden");
}

/* 📜 LOAD HISTORY */
function loadHistory(number){
    historyList.innerHTML="";
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    let filtered = all.filter(e => e.mobile === number);

    if(filtered.length === 0){
        historyList.innerHTML = "<div class='entry'>No history found</div>";
        return;
    }

    filtered.forEach(e=>{
        historyList.innerHTML += `
        <div class="entry">
            ${e.date} ${e.time}<br>
            ${e.detail}<br>
            ₹${e.amount}
            <button class="del" onclick="deleteOne(${e.id})">Delete</button>
        </div>`;
    });
}

/* ❌ DELETE ONE */
function deleteOne(id){
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all = all.filter(e => e.id !== id);
    localStorage.setItem("hisab", JSON.stringify(all));
    loadHistory(searchMobile.value || sessionStorage.getItem("mobile"));
}

/* ❌ DELETE ALL (CURRENT NUMBER) */
function deleteAll(){
    if(!confirm("All history delete karni hai?")) return;
    let num = searchMobile.value || sessionStorage.getItem("mobile");
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all = all.filter(e => e.mobile !== num);
    localStorage.setItem("hisab", JSON.stringify(all));
    loadHistory(num);
}

/* 🔙 BACK */
function backToForm(){
    historyBox.classList.add("hidden");
    formBox.classList.remove("hidden");
}
</script>

</body>
</html>

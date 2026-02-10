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
}
input, textarea{
    width:100%;
    padding:12px;
    margin:8px 0;
    border:none;
    border-radius:8px;
}
textarea{resize:none;}
button{
    width:100%;
    padding:12px;
    background:#facc15;
    border:none;
    border-radius:8px;
    font-weight:bold;
    margin-top:6px;
}
.small{
    background:#333;
    color:#fff;
}
.entry{
    background:#1c1c1c;
    padding:10px;
    margin-top:8px;
    border-radius:8px;
    font-size:13px;
}
.del{
    background:#c1121f;
    margin-top:5px;
}
.hidden{display:none;}
.note{
    font-size:12px;
    color:#aaa;
    text-align:center;
}
</style>
</head>

<body>

<!-- 🔐 LOGIN -->
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

    <input id="name" placeholder="Naam">
    <input id="mobile" placeholder="Mobile Number">

    <input type="date" id="date">
    <input type="time" id="time">

    <textarea id="detail" rows="3" placeholder="Kaam Detail"></textarea>
    <input id="amount" placeholder="Paise">

    <button onclick="saveAndSend()">Send on WhatsApp</button>
    <button class="small" onclick="openHistory()">View History</button>
</div>

<!-- 📜 HISTORY -->
<div class="card hidden" id="historyBox">
    <h2>History</h2>
    <div id="historyList"></div>
    <button class="del" onclick="deleteAll()">Delete All History</button>
    <button class="small" onclick="backToForm()">Back</button>
</div>

<script>
let otp = "";

// 📩 SEND OTP (DEMO – HAR BAAR NAYA)
function sendOTP(){
    let m = loginMobile.value.trim();
    if(m.length !== 10){
        alert("10 digit mobile number dalo");
        return;
    }
    otp = Math.floor(100000 + Math.random() * 900000);
    sessionStorage.setItem("otp", otp);
    sessionStorage.setItem("mobile", m);
    otpNote.innerText = "DEMO OTP: " + otp;
    otpBox.classList.remove("hidden");
}

// ✅ VERIFY OTP
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

// 💾 SAVE + 📲 WHATSAPP
function saveAndSend(){
    let data = {
        id: Date.now(),
        name:name.value,
        mobile:mobile.value,
        date:date.value,
        time:time.value,
        detail:detail.value,
        amount:amount.value
    };

    // ✅ BASIC VALIDATION
    if(data.mobile.length !== 10){
        alert("10 digit mobile number dalo");
        return;
    }

    // 💾 SAVE HISTORY
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all.push(data);
    localStorage.setItem("hisab", JSON.stringify(all));

    // 📲 WHATSAPP MESSAGE
    let msg = `HISAB
Naam: ${data.name}
Mobile: ${data.mobile}
Tarikh: ${data.date}
Time: ${data.time}
Detail: ${data.detail}
Paise: ${data.amount}`;

    // 🔥 USER KE NUMBER PAR HI MESSAGE JAYEGA
    let whatsappNumber = "91" + data.mobile;

    let url =
      "https://api.whatsapp.com/send?phone=" +
      whatsappNumber +
      "&text=" +
      encodeURIComponent(msg);

    // ✅ MOST RELIABLE REDIRECT
    window.location.href = url;
}

// 📜 OPEN HISTORY
function openHistory(){
    formBox.classList.add("hidden");
    historyBox.classList.remove("hidden");
    loadHistory();
}

// 📜 LOAD HISTORY
function loadHistory(){
    historyList.innerHTML="";
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    let m = sessionStorage.getItem("mobile");

    let filtered = all.filter(e => e.mobile === m);

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

// ❌ DELETE ONE
function deleteOne(id){
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all = all.filter(e => e.id !== id);
    localStorage.setItem("hisab", JSON.stringify(all));
    loadHistory();
}

// ❌ DELETE ALL (MOBILE WISE)
function deleteAll(){
    if(!confirm("All history delete karni hai?")) return;

    let m = sessionStorage.getItem("mobile");
    let all = JSON.parse(localStorage.getItem("hisab") || "[]");
    all = all.filter(e => e.mobile !== m);
    localStorage.setItem("hisab", JSON.stringify(all));
    loadHistory();
}

// 🔙 BACK
function backToForm(){
    historyBox.classList.add("hidden");
    formBox.classList.remove("hidden");
}
</script>

</body>
</html>        <button onclick="sendWhatsApp()">Send</button>
    </div>

</div>

<script>
let USER = "";
let PASS = "";

function showLogin(){
    document.getElementById("register").classList.add("hidden");
    document.getElementById("login").classList.remove("hidden");
}

function showRegister(){
    document.getElementById("login").classList.add("hidden");
    document.getElementById("register").classList.remove("hidden");
}

function doRegister(){
    let u = document.getElementById("rUser").value;
    let p = document.getElementById("rPass").value;
    let c = document.getElementById("rConf").value;

    if(u=="" || p=="" || c==""){
        alert("Sab fields bharo");
        return;
    }
    if(p !== c){
        alert("Password match nahi ho raha");
        return;
    }
    USER = u;
    PASS = p;
    alert("Register successful");
    showLogin();
}

function doLogin(){
    let u = document.getElementById("lUser").value;
    let p = document.getElementById("lPass").value;

    if(u === USER && p === PASS){
        document.getElementById("login").classList.add("hidden");
        document.getElementById("hisab").classList.remove("hidden");
    }else{
        alert("Galat username ya password");
    }
}

function sendWhatsApp(){
    let n = document.getElementById("name").value;
    let num = document.getElementById("number").value;
    let d = document.getElementById("date").value;
    let w = document.getElementById("work").value;
    let a = document.getElementById("amount").value;

    if(n==""||num==""||d==""||w==""||a==""){
        alert("Sab details bharo");
        return;
    }

    if(num.length === 10){
        num = "91" + num;
    }

    let msg =
        "Naam: "+n+"\n"+
        "Tarikh: "+d+"\n"+
        "Kaam: "+w+"\n"+
        "Paise: "+a;

    window.location.href =
        "https://api.whatsapp.com/send?phone="+num+
        "&text="+encodeURIComponent(msg);
}
</script>

</body>
</html>

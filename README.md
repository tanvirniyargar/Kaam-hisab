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
.small{background:#333;color:#fff;}
.del{background:#c1121f;color:#fff;}
.entry{
    background:#1c1c1c;
    padding:10px;
    margin-top:8px;
    border-radius:8px;
    font-size:13px;
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

<!-- LOGIN -->
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

<!-- FORM -->
<div class="card hidden" id="formBox">
    <h2>Hisab</h2>

    <input id="userName" placeholder="Naam">
    <input id="mobile" placeholder="Mobile Number">

    <input type="date" id="workDate">
    <input type="time" id="workTime">

    <textarea id="detail" placeholder="Kaam Detail"></textarea>
    <input id="amount" placeholder="Paise">

    <button onclick="saveAndSend()">Send on WhatsApp</button>
    <button class="small" onclick="openMyHistory()">View My History</button>
</div>

<!-- HISTORY -->
<div class="card hidden" id="historyBox">
    <h2>History</h2>
    <div id="historyList"></div>
    <button class="del" onclick="deleteAll()">Delete All History</button>
    <button class="small" onclick="backToForm()">Back</button>
</div>

<script>
let currentMobile="";

/* OTP DEMO */
function sendOTP(){
    let m=loginMobile.value.trim();
    if(m.length!==10){alert("10 digit mobile number dalo");return;}
    let otp=Math.floor(100000+Math.random()*900000);
    sessionStorage.setItem("otp",otp);
    sessionStorage.setItem("mobile",m);
    otpNote.innerText="DEMO OTP: "+otp;
    otpBox.classList.remove("hidden");
}
function verifyOTP(){
    if(otpInput.value==sessionStorage.getItem("otp")){
        loginBox.classList.add("hidden");
        formBox.classList.remove("hidden");
        currentMobile=sessionStorage.getItem("mobile");
        mobile.value=currentMobile;
        sessionStorage.removeItem("otp");
    }else alert("Wrong OTP");
}

/* SAVE + WHATSAPP + FULL HISTORY LINK */
function saveAndSend(){
    let name=userName.value.trim();
    let mob=mobile.value.trim();
    if(name==""||mob.length!==10){alert("Naam aur valid mobile dalo");return;}

    let data={
        id:Date.now(),
        name,mobile:mob,
        date:workDate.value,
        time:workTime.value,
        detail:detail.value,
        amount:amount.value
    };

    let all=JSON.parse(localStorage.getItem("hisab")||"[]");
    all.push(data);
    localStorage.setItem("hisab",JSON.stringify(all));

    let historyLink=
        window.location.origin+
        window.location.pathname+
        "#history="+mob;

    let msg=`HISAB DETAILS
Naam: ${data.name}
Mobile: ${data.mobile}
Tarikh: ${data.date}
Time: ${data.time}
Detail: ${data.detail}
Paise: ${data.amount}

📜 Puri hisab history yahan dekhein:
${historyLink}`;

    let url="https://api.whatsapp.com/send?phone=91"+mob+"&text="+encodeURIComponent(msg);
    window.location.href=url;
}

/* HISTORY */
function loadHistory(num){
    historyList.innerHTML="";
    let all=JSON.parse(localStorage.getItem("hisab")||"[]");
    let f=all.filter(e=>e.mobile===num);
    if(f.length===0){historyList.innerHTML="<div class='entry'>No history</div>";return;}
    f.forEach(e=>{
        historyList.innerHTML+=`
        <div class="entry">
        ${e.date} ${e.time}<br>
        ${e.detail}<br>
        ₹${e.amount}
        <button class="del" onclick="deleteOne(${e.id})">Delete</button>
        </div>`;
    });
}
function openMyHistory(){
    formBox.classList.add("hidden");
    historyBox.classList.remove("hidden");
    loadHistory(currentMobile);
}
function deleteOne(id){
    let all=JSON.parse(localStorage.getItem("hisab")||"[]");
    all=all.filter(e=>e.id!==id);
    localStorage.setItem("hisab",JSON.stringify(all));
    loadHistory(currentMobile);
}
function deleteAll(){
    if(!confirm("All history delete karni hai?"))return;
    let all=JSON.parse(localStorage.getItem("hisab")||"[]");
    all=all.filter(e=>e.mobile!==currentMobile);
    localStorage.setItem("hisab",JSON.stringify(all));
    loadHistory(currentMobile);
}
function backToForm(){
    historyBox.classList.add("hidden");
    formBox.classList.remove("hidden");
}

/* AUTO OPEN HISTORY FROM WHATSAPP LINK */
window.onload=function(){
    if(location.hash.startsWith("#history=")){
        let num=location.hash.split("=")[1];
        if(num){
            loginBox.classList.add("hidden");
            formBox.classList.add("hidden");
            historyBox.classList.remove("hidden");
            loadHistory(num);
        }
    }
};
</script>

</body>
</html>

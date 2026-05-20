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
    let otp=Math.floor(1+Math.random(9);
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
<!DOCTYPE html>
<html>
<head>

<meta charset="UTF-8">
<title>Kaam Hisab</title>

<style>

body{
background:#dfe8de;
font-family:Arial;
padding:20px;
}

.box{

width:260px;

margin:auto;

}

input{

width:100%;

height:30px;

padding:6px;

font-size:11px;

margin:4px 0;

border-radius:6px;

border:1px solid #999;

}

button{

width:100%;

height:32px;

font-size:11px;

background:#138b50;

color:white;

border:none;

border-radius:6px;

cursor:pointer;

}

</style>

</head>

<body>

<div class="box">

<input
id="name"
placeholder="Naam">

<input
id="number"
placeholder="Mobile Number">

<input
id="hisab"
placeholder="Hisab">

<input
id="money"
placeholder="Paise">

<button
onclick="send()">

WhatsApp

</button>

</div>

<script>

function send(){

let n=
document
.getElementById("name")
.value

let num=
document
.getElementById("number")
.value

let h=
document
.getElementById("hisab")
.value

let m=
document
.getElementById("money")
.value

if(
!n||
!num
){

alert(
"Naam aur Number likho"
)

return

}

let time=
new Date()
.toLocaleString()

let msg=
encodeURIComponent(

`Naam:
${n}

Hisab:
${h}

Paise:
₹${m}

Time:
${time}

`

)

window.location=
`https://wa.me/91${num}?text=${msg}`

}

</script>

</body>

</html>

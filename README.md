<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>paint Kaam Hisab</title>

<style>
body{
    margin:0;
    font-family: Arial, sans-serif;
    background:#0f0f0f;
    height:100vh;
    display:flex;
    justify-content:center;
    align-items:center;
    color:#fff;
}

.container{
    width:360px;
    background:#1c1c1c;
    padding:25px;
    border-radius:18px;
    box-shadow:0 0 20px gold;
}

h2{
    text-align:center;
    color:gold;
}

input, textarea{
    width:100%;
    padding:12px;
    margin:8px 0;
    border:none;
    border-radius:8px;
}

textarea{
    resize:none;
    height:70px;
}

button{
    width:100%;
    padding:12px;
    background:gold;
    border:none;
    border-radius:10px;
    font-size:16px;
    font-weight:bold;
    cursor:pointer;
}

button:active{
    transform:scale(0.98);
}

.link{
    text-align:center;
    font-size:13px;
    margin-top:10px;
}

.link span{
    color:gold;
    cursor:pointer;
}

.hidden{
    display:none;
}
</style>
</head>

<body>

<div class="container">

    <!-- REGISTER -->
    <div id="register">
        <h2>Register</h2>
        <input id="rUser" type="text" placeholder="Username">
        <input id="rPass" type="password" placeholder="Password">
        <input id="rConf" type="password" placeholder="Confirm Password">
        <button onclick="doRegister()">Register</button>
        <div class="link">
            Already have account? <span onclick="showLogin()">Login</span>
        </div>
    </div>

    <!-- LOGIN -->
    <div id="login" class="hidden">
        <h2>Login</h2>
        <input id="lUser" type="text" placeholder="Username">
        <input id="lPass" type="password" placeholder="Password">
        <button onclick="doLogin()">Login</button>
        <div class="link">
            New user? <span onclick="showRegister()">Register</span>
        </div>
    </div>

    <!-- KAAM HISAB -->
    <div id="hisab" class="hidden">
        <h2> Kaam Hisab</h2>
        <input id="name" type="text" placeholder="Naam">
        <input id="number" type="tel" placeholder="Mobile Number">
        <input id="date" type="date">
        <textarea id="work" placeholder="Kaam Detail"></textarea>
        <input id="amount" type="number" placeholder="Paise">
        <button onclick="sendWhatsApp()">Send</button>
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

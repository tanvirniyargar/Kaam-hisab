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

</html>t

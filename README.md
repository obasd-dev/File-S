<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Vertex Messenger</title>

<style>
*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:Segoe UI,Arial,sans-serif;
}

body{
height:100vh;
display:flex;
background:#17212b;
overflow:hidden;
}

/* Sidebar */

.sidebar{
width:320px;
background:#202c33;
display:flex;
flex-direction:column;
border-right:1px solid rgba(255,255,255,.08);
}

.logo{
padding:20px;
font-size:24px;
font-weight:bold;
color:white;
background:#293742;
}

.search{
padding:15px;
}

.search input{
width:100%;
padding:12px;
border:none;
border-radius:10px;
background:#36454f;
color:white;
outline:none;
}

.chat-list{
flex:1;
overflow:auto;
}

.chat{
display:flex;
gap:12px;
padding:15px;
cursor:pointer;
transition:.3s;
}

.chat:hover{
background:#2d3b45;
}

.avatar{
width:50px;
height:50px;
border-radius:50%;
background:#4da6ff;
display:flex;
align-items:center;
justify-content:center;
font-size:22px;
color:white;
}

.chat-info h4{
color:white;
}

.chat-info p{
color:#9fb3c8;
font-size:14px;
}

/* Chat */

.main{
flex:1;
display:flex;
flex-direction:column;
background:url('https://images.unsplash.com/photo-1518770660439-4636190af475?auto=format&fit=crop&w=1400&q=80');
background-size:cover;
}

.header{
height:70px;
background:#293742;
display:flex;
align-items:center;
padding:20px;
color:white;
gap:15px;
backdrop-filter:blur(8px);
}

.messages{
flex:1;
padding:20px;
overflow:auto;
display:flex;
flex-direction:column;
gap:12px;
}

.msg{
max-width:70%;
padding:12px 15px;
border-radius:18px;
white-space:pre-wrap;
animation:pop .25s ease;
word-wrap:break-word;
}

.sent{
align-self:flex-end;
background:#4da6ff;
color:white;
border-bottom-right-radius:6px;
}

.received{
align-self:flex-start;
background:#2d3b45;
color:white;
border-bottom-left-radius:6px;
}

.msg img{
width:100%;
border-radius:12px;
margin-top:8px;
}

.file{
display:flex;
align-items:center;
gap:12px;
padding:12px;
background:rgba(255,255,255,.1);
border-radius:12px;
margin-top:8px;
}

.file a{
color:white;
text-decoration:none;
font-weight:bold;
}

.input-area{
padding:15px;
background:#293742;
display:flex;
align-items:center;
gap:10px;
}

textarea{
flex:1;
resize:none;
height:50px;
padding:12px;
border:none;
border-radius:15px;
outline:none;
font-size:15px;
}

button{
background:#4da6ff;
color:white;
border:none;
padding:14px 22px;
border-radius:14px;
cursor:pointer;
font-weight:bold;
transition:.3s;
}

button:hover{
background:#2d8cff;
}

.attach{
font-size:24px;
cursor:pointer;
color:white;
}

#file{
display:none;
}

@keyframes pop{
from{
transform:scale(.9);
opacity:0;
}
to{
transform:scale(1);
opacity:1;
}
}

::-webkit-scrollbar{
width:6px;
}
::-webkit-scrollbar-thumb{
background:#4da6ff;
border-radius:10px;
}
</style>

</head>
<body>

<div class="sidebar">

<div class="logo">💬 Vertex Messenger</div>

<div class="search">
<input placeholder="Search chats">
</div>

<div class="chat-list">

<div class="chat">
<div class="avatar">A</div>
<div class="chat-info">
<h4>Alex Johnson</h4>
<p>Let's review the website...</p>
</div>
</div>

<div class="chat">
<div class="avatar" style="background:#ff7b54;">S</div>
<div class="chat-info">
<h4>Sarah Team</h4>
<p>Files uploaded successfully</p>
</div>
</div>

<div class="chat">
<div class="avatar" style="background:#8b5cf6;">D</div>
<div class="chat-info">
<h4>Design Group</h4>
<p>Pixel perfect completed</p>
</div>
</div>

</div>

</div>

<div class="main">

<div class="header">
<div class="avatar">A</div>
<div>
<h3>Alex Johnson</h3>
<p style="font-size:13px;color:#9fb3c8;">Online</p>
</div>
</div>

<div class="messages" id="messages">

<div class="msg received">
Welcome to Vertex Messenger!

Send messages, images, or large files.
Image quality stays original because the original file is shared.
</div>

</div>

<div class="input-area">

<label class="attach" for="file">📎</label>
<input type="file" id="file">

<textarea id="text" placeholder="Write a message..."></textarea>

<button onclick="send()">Send</button>

</div>

</div>

<script>
const messages=document.getElementById("messages");
const text=document.getElementById("text");
const file=document.getElementById("file");

function createMessage(content,type="text",mine=true){

const msg=document.createElement("div");
msg.className="msg "+(mine?"sent":"received");

if(type==="text"){

msg.textContent=content;

}else if(type==="image"){

const img=document.createElement("img");
img.src=content.url;

const cap=document.createElement("div");
cap.textContent=content.name;

msg.appendChild(cap);
msg.appendChild(img);

}else if(type==="file"){

const box=document.createElement("div");
box.className="file";

box.innerHTML=`
<div style="font-size:28px;">📁</div>
<div>
<a href="${content.url}" download="${content.name}">
${content.name}
</a>
<div style="font-size:12px;">
${content.size}
</div>
</div>
`;

msg.appendChild(box);

}

messages.appendChild(msg);
messages.scrollTop=messages.scrollHeight;
}

function send(){

const value=text.value.trim();

if(value!==""){
createMessage(value);
text.value="";
}

if(file.files.length){

const f=file.files[0];
const url=URL.createObjectURL(f);

if(f.type.startsWith("image")){

createMessage({
url:url,
name:f.name
},"image");

}else{

createMessage({
url:url,
name:f.name,
size:(f.size/1024/1024).toFixed(2)+" MB"
},"file");

}

file.value="";
}

}

text.addEventListener("keydown",e=>{

if(e.key==="Enter" && !e.shiftKey){

e.preventDefault();
send();

}

});
</script>

</body>
</html>

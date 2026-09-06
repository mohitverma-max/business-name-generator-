<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Business Name Generator</title>

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">

<style>
*{margin:0;padding:0;box-sizing:border-box}
body{
font-family:'Inter',sans-serif;
background:#E8F7F1;
padding:32px;
color:#14332B;
}
.container{
max-width:1320px;
margin:auto;
background:rgba(255,255,255,.82);
backdrop-filter:blur(18px);
border-radius:26px;
padding:30px;
box-shadow:0 18px 45px rgba(0,0,0,.06);
}
.topbar{
display:flex;
justify-content:flex-end;
margin-bottom:24px;
}
.badge{
background:#DDF7EC;
padding:12px 18px;
border-radius:999px;
font-weight:600;
color:#0F5132;
}
.grid{
display:grid;
grid-template-columns:1fr 1fr;
gap:18px;
}
.full{grid-column:1/-1}
label{
display:block;
font-size:13px;
font-weight:700;
margin-bottom:8px;
}
input,textarea{
width:100%;
padding:15px;
border-radius:14px;
border:1px solid #D8E8E2;
font-size:14px;
outline:none;
background:#fff;
}
textarea{height:120px;resize:none}
.section-title{
font-size:20px;
font-weight:700;
margin-bottom:12px;
}
.pills{
display:flex;
flex-wrap:wrap;
gap:10px;
}
.pill{
padding:11px 16px;
border-radius:999px;
border:1px solid #D8E8E2;
cursor:pointer;
background:white;
}
.pill.active{
background:#22C58B;
color:white;
border-color:#22C58B;
}
button{
margin-top:26px;
width:100%;
padding:18px;
border:none;
border-radius:999px;
background:linear-gradient(90deg,#34D399,#2DD4BF);
font-size:20px;
font-weight:800;
cursor:pointer;
color:#073B2F;
}
.results{
margin-top:30px;
display:none;
}
.results h2{
font-size:24px;
margin-bottom:16px;
}
.tabs{
display:flex;
gap:10px;
margin-bottom:18px;
flex-wrap:wrap;
}
.tab{
padding:10px 16px;
border-radius:999px;
border:1px solid #D8E8E2;
cursor:pointer;
background:#fff;
font-weight:600;
}
.tab.active{
background:#22C58B;
color:#fff;
border-color:#22C58B;
}
.names{
display:grid;
grid-template-columns:1fr 1fr;
gap:12px;
}
.name-card{
background:white;
border:1px solid #E5EFEB;
padding:16px;
border-radius:14px;
display:flex;
justify-content:space-between;
align-items:center;
font-weight:700;
}
.copy{
cursor:pointer;
opacity:.6;
}
.copy:hover{opacity:1}
@media(max-width:900px){
body{padding:18px}
.grid,.names{grid-template-columns:1fr}
.topbar{justify-content:center}
}
</style>
</head>

<body>

<div class="container">

<div class="topbar">
<div class="badge">💡 Turn your idea into a memorable brand</div>
</div>

<div class="grid">

<div>
<label>Your Name</label>
<input id="owner" placeholder="Mohit">
</div>

<div>
<label>Website (Optional)</label>
<input id="website" placeholder="roadtotop.com">
</div>

<div class="full">
<label>What's your business idea?</label>
<textarea id="idea" placeholder="AI SEO Agency helping businesses rank on Google"></textarea>
</div>

<div>
<div class="section-title">Brand Tone</div>
<div class="pills" id="tones">
<div class="pill active">Bold</div>
<div class="pill">Luxury</div>
<div class="pill">Minimal</div>
<div class="pill">Techy</div>
<div class="pill">Friendly</div>
<div class="pill">Playful</div>
</div>
</div>

<div>
<label>Target Audience</label>
<input id="audience" placeholder="Founders, SMEs, Startups">
</div>

<div>
<label>Industry</label>
<input id="industry" placeholder="SEO, SaaS, Fashion">
</div>

<div>
<label>Location</label>
<input id="location" placeholder="India / Global">
</div>

<div class="full">
<label>Other Preferences</label>
<input id="pref" placeholder="Short names, .com friendly, premium">
</div>

</div>

<button onclick="generateNames()">Generate 50 Business Names →</button>

<div class="results" id="results">

<h2>50 Business Name Ideas</h2>

<div class="tabs">
<div class="tab active" onclick="showCategory('brandable',this)">Brandable</div>
<div class="tab" onclick="showCategory('premium',this)">Premium</div>
<div class="tab" onclick="showCategory('modern',this)">Modern</div>
<div class="tab" onclick="showCategory('ai',this)">AI First</div>
</div>

<div class="names" id="nameList"></div>

</div>

</div>

<script>
// ===== Tone selection (max 2) =====
document.querySelectorAll('#tones .pill').forEach(p=>{
p.onclick=()=>{
if(p.classList.contains('active')){
p.classList.remove('active');
return;
}
if(document.querySelectorAll('#tones .active').length<2){
p.classList.add('active');
}
}
});

let generated={};

// ===== Dictionaries =====
const industries={
seo:{
roots:["Rank","Search","SERP","Crawl","Index","Organic","Authority","Topical","Growth","Visibility","Keyword","Traffic"],
brand:["Pilot","Forge","Mint","Peak","Flow","Works","Studio","Axis","Nest","Point","Labs","Prime"]
},
saas:{
roots:["Cloud","Logic","Sync","Stack","Scale","Flow","Core","Launch","Orbit","Nova"],
brand:["OS","Labs","Works","Pilot","Base","Hub","Suite","One","AI","Edge"]
},
fashion:{
roots:["Aura","Mode","Silk","Thread","Loom","Velvet","Muse"],
brand:["Studio","Label","Collective","Wear","House","Co"]
},
cafe:{
roots:["Bean","Roast","Brew","Mocha","Cup","Espresso"],
brand:["House","Roastery","Corner","Cafe","Co"]
}
};

const premiumWords=["Prime","Elite","Prestige","Maison","Royal","Aure","Elevate","Signature"];
const modernWords=["Neo","Nova","Opti","Hyper","Pixel","Quantum","Smart","Shift"];
const aiWords=["Neural","Cortex","Vector","AI","Logic","Intelli","Data","Vision"];

function detectIndustry(text){
text=text.toLowerCase();
if(text.includes("seo")||text.includes("google")||text.includes("ranking")) return "seo";
if(text.includes("saas")||text.includes("software")) return "saas";
if(text.includes("fashion")||text.includes("clothing")) return "fashion";
if(text.includes("cafe")||text.includes("coffee")) return "cafe";
return "seo";
}

function pick(arr){
return arr[Math.floor(Math.random()*arr.length)];
}

function uniqueNames(builder){
const set=new Set();
while(set.size<50){
set.add(builder());
}
return [...set];
}

function generateNames(){

const idea=document.getElementById("idea").value;
const industryInput=document.getElementById("industry").value;
const owner=document.getElementById("owner").value.trim();
const location=document.getElementById("location").value.trim();

const key=detectIndustry(idea+" "+industryInput);
const dict=industries[key];

generated.brandable=uniqueNames(()=>{
return pick(dict.roots)+pick(dict.brand);
});

generated.premium=uniqueNames(()=>{
return pick(premiumWords)+pick(dict.roots);
});

generated.modern=uniqueNames(()=>{
return pick(modernWords)+pick(dict.roots);
});

generated.ai=uniqueNames(()=>{
let base=pick(aiWords)+pick(dict.roots);
if(location && Math.random()>.7) base+=location.replace(/\s/g,'');
if(owner && Math.random()>.8) base=owner+pick(dict.roots);
return base;
});

showCategory('brandable',document.querySelector('.tab'));

document.getElementById("results").style.display="block";
window.scrollTo({top:document.body.scrollHeight,behavior:"smooth"});
}

function showCategory(cat,el){

document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
el.classList.add('active');

const wrap=document.getElementById("nameList");
wrap.innerHTML="";

generated[cat].forEach(name=>{
const card=document.createElement("div");
card.className="name-card";
card.innerHTML=`<span>${name}</span><span class="copy">📋</span>`;
card.onclick=()=>navigator.clipboard.writeText(name);
wrap.appendChild(card);
});

}
</script>

</body>
</html>

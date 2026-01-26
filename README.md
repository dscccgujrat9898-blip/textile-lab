<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<title>DS Digital Textile Lab</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
body{margin:0;font-family:Arial;background:#f1f1f1}
header{background:#263238;color:#fff;padding:14px;text-align:center;font-size:22px}
nav{display:flex;background:#37474f}
nav button{
 flex:1;padding:10px;border:none;
 background:#37474f;color:#fff;font-size:15px;cursor:pointer
}
nav button.active{background:#009688}
.section{display:none;padding:15px}
.box{max-width:1100px;margin:auto;background:#fff;padding:15px;border-radius:8px}
input,select,button{width:100%;padding:8px;margin:6px 0}
canvas{width:100%;border:1px solid #bbb;margin-top:10px}
.flex{display:flex;gap:10px;flex-wrap:wrap}
.small{flex:1}
button.main{background:#009688;color:#fff;border:none;border-radius:6px}
label{font-weight:bold;font-size:14px}
footer{text-align:center;font-size:13px;color:#666;margin:10px}
</style>
</head>

<body>

<header>DS DIGITAL TEXTILE LAB</header>

<nav>
<button class="active" onclick="openTab('enhancer',this)">Image Enhancer</button>
<button onclick="openTab('repeat',this)">Seamless Repeat</button>
<button onclick="openTab('fabric',this)">Fabric Preview</button>
<button onclick="openTab('ai',this)">AI Super Resolution</button>
</nav>

<!-- IMAGE ENHANCER -->
<div id="enhancer" class="section" style="display:block">
<div class="box">
<h3>🎨 Textile Image Enhancer</h3>

<input type="file" id="imgUpload" accept="image/*">

<label>Brightness</label>
<input type="range" id="bright" min="0.6" max="1.5" step="0.05" value="1">

<label>Contrast</label>
<input type="range" id="cont" min="0.6" max="1.8" step="0.05" value="1">

<label>Saturation</label>
<input type="range" id="sat" min="0.6" max="1.8" step="0.05" value="1">

<label>Sharpen</label>
<input type="range" id="sharp" min="0" max="3" step="1" value="0">

<div class="flex">
<div class="small"><label>Width (inch)</label><input type="number" id="ew" value="10"></div>
<div class="small"><label>Height (inch)</label><input type="number" id="eh" value="10"></div>
<div class="small">
<label>DPI</label>
<select id="edpi"><option>150</option><option>300</option><option>600</option></select>
</div>
</div>

<canvas id="ec"></canvas>
<button class="main" onclick="download(ec,'Enhanced')">Download</button>
</div>
</div>

<!-- SEAMLESS REPEAT -->
<div id="repeat" class="section">
<div class="box">
<h3>🔁 Seamless Repeat Generator</h3>

<input type="file" id="rUpload" accept="image/*">

<label>Spacing</label>
<input type="range" id="rspace" min="0" max="200" value="20">

<label>Rotation</label>
<input type="range" id="rangle" min="-180" max="180" value="0">

<div class="flex">
<div class="small"><label>Width (inch)</label><input type="number" id="rw" value="12"></div>
<div class="small"><label>Height (inch)</label><input type="number" id="rh" value="12"></div>
<div class="small">
<label>DPI</label>
<select id="rdpi"><option>150</option><option>300</option><option>600</option></select>
</div>
</div>

<canvas id="rc"></canvas>
<button class="main" onclick="download(rc,'Repeat')">Download</button>
</div>
</div>

<!-- FABRIC -->
<div id="fabric" class="section">
<div class="box">
<h3>🧵 Fabric Preview</h3>

<select id="fabricType">
<option value="cotton">Cotton</option>
<option value="silk">Silk</option>
<option value="poly">Polyester</option>
</select>

<label>Ink Absorption</label>
<input type="range" id="ink" min="0.8" max="1.3" step="0.05" value="1">

<canvas id="fc" width="800" height="500"></canvas>
</div>
</div>

<!-- AI -->
<div id="ai" class="section">
<div class="box">
<h3>🤖 AI Super Resolution</h3>

<input type="file" id="aiUpload" accept="image/*">

<select id="aiscale">
<option value="2">2× Upscale</option>
<option value="4">4× Upscale</option>
</select>

<div class="flex">
<div class="small"><label>Width (inch)</label><input type="number" id="aiw" value="10"></div>
<div class="small"><label>Height (inch)</label><input type="number" id="aih" value="10"></div>
<div class="small">
<label>DPI</label>
<select id="aidpi"><option>150</option><option>300</option><option>600</option></select>
</div>
</div>

<canvas id="aic"></canvas>
<button class="main" onclick="download(aic,'AI_Textile')">Download</button>
</div>
</div>

<footer>© DS Computer Classes – Digital Textile Lab</footer>

<script>
function openTab(id,btn){
 document.querySelectorAll('.section').forEach(s=>s.style.display='none');
 document.getElementById(id).style.display='block';
 document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
 btn.classList.add('active');
}

/* IMAGE ENHANCER */
const ec=document.getElementById("ec");
const ectx=ec.getContext("2d");
let img=new Image();

imgUpload.onchange=e=>{
 img.src=URL.createObjectURL(e.target.files[0]);
 img.onload=drawEnhancer;
};

["bright","cont","sat","sharp","ew","eh","edpi"].forEach(id=>{
 document.getElementById(id).oninput=drawEnhancer;
});

function drawEnhancer(){
 if(!img.src) return;

 let dpi=+edpi.value;
 ec.width=ew.value*dpi;
 ec.height=eh.value*dpi;

 ectx.clearRect(0,0,ec.width,ec.height);
 ectx.filter=`brightness(${bright.value}) contrast(${cont.value}) saturate(${sat.value})`;
 ectx.drawImage(img,0,0,ec.width,ec.height);
 ectx.filter="none";

 if(sharp.value>0){
  ectx.globalAlpha=0.25;
  ectx.drawImage(ec,0,0);
  ectx.globalAlpha=1;
 }
}

/* REPEAT */
const rc=document.getElementById("rc");
const rctx=rc.getContext("2d");
let rimg=new Image();

rUpload.onchange=e=>{
 rimg.src=URL.createObjectURL(e.target.files[0]);
 rimg.onload=drawRepeat;
};

["rspace","rangle","rw","rh","rdpi"].forEach(id=>{
 document.getElementById(id).oninput=drawRepeat;
});

function drawRepeat(){
 if(!rimg.src) return;

 let dpi=+rdpi.value;
 rc.width=rw.value*dpi;
 rc.height=rh.value*dpi;

 let stepX=rimg.width + +rspace.value;
 let stepY=rimg.height + +rspace.value;

 for(let y=0;y<rc.height;y+=stepY){
  for(let x=0;x<rc.width;x+=stepX){
   rctx.save();
   rctx.translate(x+rimg.width/2,y+rimg.height/2);
   rctx.rotate(rangle.value*Math.PI/180);
   rctx.drawImage(rimg,-rimg.width/2,-rimg.height/2);
   rctx.restore();
  }
 }
}

/* FABRIC */
const fc=document.getElementById("fc");
const fctx=fc.getContext("2d");

function drawFabric(){
 fctx.fillStyle="#eee";
 fctx.fillRect(0,0,fc.width,fc.height);
 for(let i=0;i<25000;i++){
  let a=fabricType.value=="silk"?0.15:0.3;
  fctx.fillStyle=`rgba(0,0,0,${a*(ink.value-0.7)})`;
  fctx.fillRect(Math.random()*fc.width,Math.random()*fc.height,1,1);
 }
}
fabricType.onchange=drawFabric;
ink.oninput=drawFabric;
drawFabric();

/* AI */
const aic=document.getElementById("aic");
const aictx=aic.getContext("2d");
let aiimg=new Image();

aiUpload.onchange=e=>{
 aiimg.src=URL.createObjectURL(e.target.files[0]);
 aiimg.onload=drawAI;
};

function drawAI(){
 if(!aiimg.src) return;

 let dpi=+aidpi.value;
 aic.width=aiw.value*dpi;
 aic.height=aih.value*dpi;

 aictx.imageSmoothingEnabled=true;
 aictx.imageSmoothingQuality="high";
 aictx.drawImage(aiimg,0,0,aic.width,aic.height);
}

/* DOWNLOAD */
function download(c,name){
 let a=document.createElement("a");
 a.download="DS_"+name+".png";
 a.href=c.toDataURL("image/png");
 a.click();
}
</script>

</body>
</html>

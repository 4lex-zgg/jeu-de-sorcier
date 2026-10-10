[eldoria_code_source.html](https://github.com/user-attachments/files/33279253/eldoria_code_source.html)# jeu-de-sorcier
copie puis met ça dans la barre de recherche
[Uploa<!DOCTYPE html>
<html lang="fr"><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<title>Eldoria — L'Héritage des Arcanes</title>
<style>
:root{--bg:#0d0a1a;--fg:#f1ead8;--panel:rgba(16,12,34,.8);--gold:#e8b94a;--line:#6a52b8;box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
html,body{height:100%;margin:0;background:var(--bg);color:var(--fg);font:14px Georgia,'Times New Roman',serif;overflow:hidden}
canvas{display:block;width:100%;height:100%}
.p{position:fixed;background:var(--panel);border:1px solid var(--line);border-radius:6px;padding:8px 10px;backdrop-filter:blur(4px);box-shadow:0 0 12px rgba(106,82,184,.35)}
#st{top:8px;left:8px;width:210px}
.bar{height:11px;background:#1d1830;border:1px solid #3a2f66;border-radius:3px;margin:4px 0;overflow:hidden}.bar i{display:block;height:100%;width:100%;transition:width .15s;box-shadow:inset 0 -4px 6px rgba(0,0,0,.25),inset 0 3px 3px rgba(255,255,255,.25)}
#qb{top:8px;right:8px;max-width:250px;line-height:1.4}
#sp{bottom:12px;left:50%;transform:translateX(-50%);display:flex;gap:8px;background:none;border:0}
.sk{width:88px;height:82px;position:relative;background:var(--panel);border:1px solid var(--line);border-radius:6px;text-align:center;font-size:11px;padding-top:6px;box-sizing:border-box;overflow:hidden}
.sk b{display:block;font-size:20px}.sk u{position:absolute;left:0;bottom:0;width:100%;background:rgba(0,0,0,.65);height:0}
#msg{bottom:110px;left:50%;transform:translateX(-50%);max-width:80%;text-align:center;display:none;font-size:15px}
#boss{top:64px;left:50%;transform:translateX(-50%);width:46%;display:none;text-align:center}
#qd{min-width:440px}#inv,#tr,#sh,#qd{top:50%;left:50%;transform:translate(-50%,-50%);display:none;min-width:290px;line-height:1.6}
#ov{inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:12px;text-align:center;border-radius:0;z-index:5;background:rgba(10,7,22,.92)}
#ov h1{margin:0;font-size:44px;letter-spacing:.06em;font-weight:400;color:var(--gold)}
button{background:#5a3fc0;color:#fff;border:0;border-radius:5px;padding:9px 18px;font:15px Georgia,serif;cursor:pointer}
button:focus-visible{outline:2px solid var(--gold)}
#x{top:50%;left:50%;width:6px;height:6px;margin:-3px;border-radius:50%;background:#fff8;padding:0;border:0}
</style></head><body>
<canvas id="c"></canvas>
<div id="st" class="p"><div id="lv"></div><div class="bar"><i id="hp" style="background:#c93a3a"></i></div><div class="bar"><i id="mp" style="background:#3a7ae0"></i></div><div class="bar"><i id="xp" style="background:var(--gold)"></i></div><div id="ex"></div></div>
<div id="qb" class="p"></div>
<div id="boss" class="p">Varkhûn, dragon du feu<div class="bar"><i id="bh" style="background:#f06a1a"></i></div></div>
<div id="sp" class="p"></div>
<div id="msg" class="p"></div>
<div id="x" class="p"></div>
<div id="inv" class="p"></div><div id="tr" class="p"></div><div id="sh" class="p"></div><div id="qd" class="p"></div><div id="boss2" class="p" style="top:64px;left:50%;transform:translateX(-50%);width:46%;display:none;text-align:center"><span id="bn2"></span><div class="bar"><i id="bh2" style="background:#9a4aff"></i></div></div>
<div id="ov" class="p">
<h1>ELDORIA</h1><div>L'Héritage des Arcanes — prototype</div>
<div style="max-width:540px;line-height:1.6">ZQSD / WASD : bouger (Maj : courir) · Souris : caméra (ou clic droit + glisser)<br>Clic gauche ou 1 : 🔥 Feu · 2 : ❄️ Nova de glace · 3 : ⚡ Foudre<br>4 : 🌀 Téléportation · T : compétences · E : parler / ouvrir / boutique · H : potion · I : inventaire · K : sauvegarder<br>Combo : le feu inflige +50 % aux ennemis gelés.</div>
<div id="cls"></div><div id="cd" style="max-width:560px;line-height:1.6"></div><div><button id="play">Jouer</button> <button id="new" style="background:#3a3350">Nouvelle partie</button></div>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
const $=i=>document.getElementById(i),T=THREE,hyp=Math.hypot,rnd=(a,b)=>a+Math.random()*(b-a),clamp=(v,a,b)=>Math.max(a,Math.min(b,v));
const scene=new T.Scene(),cam=new T.PerspectiveCamera(65,1,.1,300),ren=new T.WebGLRenderer({canvas:$('c'),antialias:true});
ren.setPixelRatio(Math.min(devicePixelRatio,2));ren.shadowMap.enabled=true;ren.shadowMap.type=T.PCFSoftShadowMap;
function rs(){ren.setSize(innerWidth,innerHeight,false);cam.aspect=innerWidth/innerHeight;cam.updateProjectionMatrix()}
addEventListener('resize',rs);rs();
const sun=new T.DirectionalLight(0xfff2cc,1),amb=new T.HemisphereLight(0xaaccff,0x445533,.6);sun.castShadow=true;sun.shadow.mapSize.set(2048,2048);Object.assign(sun.shadow.camera,{left:-45,right:45,top:45,bottom:-45,near:1,far:200});sun.shadow.bias=-.0006;scene.add(sun,sun.target,amb);
const cN=new T.Color(0x0a0a22),cD=new T.Color(0x88aadd),cs=new T.Color(0x88aadd);scene.background=cs;scene.fog=new T.Fog(0x88aadd,50,210);
const M=(c,e)=>new T.MeshLambertMaterial({color:c,emissive:e||0});
function mesh(g,c,x,y,z,e){const m=new T.Mesh(g,M(c,e));m.position.set(x,y,z);m.castShadow=m.receiveShadow=true;scene.add(m);return m}
const box=(w,h,d,c,x,y,z)=>mesh(new T.BoxGeometry(w,h,d),c,x,y,z),col=[];
function wall(w,d,c,x,z,h){h=h||6;box(w,h,d,c,x,h/2,z);col.push({x,z,w:w/2,d:d/2})}
// Monde
const gc=document.createElement('canvas');gc.width=gc.height=256;const gx=gc.getContext('2d');gx.fillStyle='#fff';gx.fillRect(0,0,256,256);for(let i=0;i<2500;i++){gx.fillStyle=`rgba(${Math.random()<.5?'0,40,0':'255,255,200'},${rnd(.03,.12)})`;gx.fillRect(Math.random()*256,Math.random()*256,rnd(2,7),rnd(2,7))}
const gt=new T.CanvasTexture(gc);gt.wrapS=gt.wrapT=T.RepeatWrapping;gt.repeat.set(90,90);mesh(new T.PlaneGeometry(520,520).rotateX(-Math.PI/2),0x4b8a3f,0,0,0).material.map=gt;
const patch=(r,c,x,z)=>mesh(new T.CircleGeometry(r,32).rotateX(-Math.PI/2),c,x,.02,z);
patch(20,0x8a7552,0,0);patch(48,0x24542a,68,8);box(40,.2,40,0x2a2a35,0,.1,-90);
for(let i=0;i<28;i++){const a=i/28*Math.PI*2+rnd(-.1,.1),r=rnd(150,170),h=rnd(30,70),m=new T.Mesh(new T.ConeGeometry(rnd(20,34),h,6),M(i%3?0x4a4a66:0x5c5c78));m.position.set(Math.cos(a)*r,h/2-2,Math.sin(a)*r);scene.add(m)}
const stars=new T.Points(new T.BufferGeometry().setFromPoints(Array.from({length:700},()=>new T.Vector3(rnd(-1,1),rnd(.05,1),rnd(-1,1)).normalize().multiplyScalar(250))),new T.PointsMaterial({color:0xffffff,size:2,sizeAttenuation:false,transparent:true,fog:false,opacity:0}));scene.add(stars);
{const g=new T.InstancedMesh(new T.ConeGeometry(.1,.7,4),M(0xffffff),3000),f=new T.InstancedMesh(new T.SphereGeometry(.12,6,6),M(0xffffff,0x222222),500),d=new T.Object3D(),c=new T.Color();
for(let i=0;i<3500;i++){const fl=i>=3000;let x=rnd(-130,130),z=rnd(-130,130);if(hyp(x,z)<20||hyp(x+100,z)<36||hyp(x-85,z+105)<38||hyp(x,z-105)<38||hyp(x+50,z-55)<26||hyp(x+75,z+100)<45||hyp(x-95,z-110)<32||hyp(x+95,z-105)<32||(z<-66&&Math.abs(x)<24)){x=rnd(40,100);z=rnd(-40,60)}d.position.set(x,fl?.5:.3,z);d.rotation.set(rnd(-.2,.2),0,rnd(-.2,.2));d.scale.setScalar(fl?1:rnd(.7,1.6));d.updateMatrix();(fl?f:g).setMatrixAt(fl?i-3000:i,d.matrix);(fl?f:g).setColorAt(fl?i-3000:i,fl?c.setHSL(rnd(0,1),.8,.65):c.setHSL(rnd(.22,.33),.55,rnd(.22,.4)))}scene.add(g,f)}
[[-10,-8],[-13,6],[10,11],[-4,-15],[14,-9]].forEach(([x,z])=>{wall(6,6,0xb59a6a,x,z,4);const r=mesh(new T.ConeGeometry(5,3,4),0x7a2e22,x,5.5,z);r.rotation.y=Math.PI/4;mesh(new T.BoxGeometry(1.2,1.2,.1),0x222222,x-1.5,2.4,z+3.02,0xffcc55);mesh(new T.BoxGeometry(1.2,2.2,.1),0x4a2e1a,x+1.2,1.1,z+3.02);mesh(new T.BoxGeometry(.8,2.5,.8),0x6b5a4a,x+1.5,5.8,z)});
for(let i=0;i<110;i++){const x=i<80?rnd(30,110):rnd(-62,-25),z=i<80?rnd(-50,70):rnd(-50,32),s=rnd(.8,1.5),gr=[0x1f6b34,0x2a7a3a,0x185a30][i%3];mesh(new T.CylinderGeometry(.3*s,.4*s,3*s),0x5a3b22,x,1.5*s,z);for(let k=0;k<2;k++)mesh(new T.ConeGeometry((2.5-k*.8)*s,(3.4-k*.4)*s,8),gr,x,(3.2+k*1.9)*s,z);col.push({x,z,w:.5,d:.5})}
wall(16,2,0x555566,-12,-70,8);wall(16,2,0x555566,12,-70,8);wall(40,2,0x555566,0,-110,8);wall(2,40,0x555566,-20,-90,8);wall(2,40,0x555566,20,-90,8);
const tl=[];[[-10,-100],[10,-100],[-10,-80],[10,-80]].forEach(([x,z])=>{wall(1.5,1.5,0x444455,x,z,6);mesh(new T.SphereGeometry(.5,8,8),0xff8a30,x,6.4,z,0xff6a00);if(z<-90){const l=new T.PointLight(0xff7a30,1.2,40);l.position.set(x,7,z);scene.add(l);tl.push(l)}});
const chest=box(1.2,.8,.8,0xb8860b,70,.4,-15);
const rn=new T.Points(new T.BufferGeometry().setAttribute('position',new T.BufferAttribute(new Float32Array(4500).map(()=>rnd(-30,30)),3)),new T.PointsMaterial({color:0xaaccff,size:.25,transparent:true,opacity:0}));rn.frustumCulled=false;rn.visible=false;scene.add(rn);let wet=0,wetT=40,wf=0;
patch(34,0x4b4770,-100,0);box(70,.1,4,0x8a7552,-52,.05,0);
wall(8,8,0x7a74a8,-100,-22,22);mesh(new T.ConeGeometry(7,8,8),0x4a2a8a,-100,26,-22);const crys=mesh(new T.OctahedronGeometry(1.4),0xaa66ff,-100,32.5,-22,0x8a44ff);
[[-124,-24],[-76,-24],[-124,24],[-76,24]].forEach(([x,z])=>{wall(5,5,0x6a6490,x,z,12);mesh(new T.ConeGeometry(4.5,5,8),0x4a2a8a,x,14.5,z)});
[[-114,14,10],[-86,14,10],[-114,-6,8],[-86,-6,8]].forEach(([x,z,w])=>{wall(w,8,0x8a7a68,x,z,5);const r=mesh(new T.ConeGeometry(w*.75,3.5,4),0x4a2a8a,x,6.7,z);r.rotation.y=Math.PI/4;mesh(new T.BoxGeometry(1.2,1.2,.1),0x222222,x-1.5,2.6,z+4.02,0xffcc55)});
[[-94,-8],[-106,-8],[-94,20],[-106,20],[-100,4]].forEach(([x,z])=>{mesh(new T.CylinderGeometry(.12,.12,3),0x333344,x,1.5,z);mesh(new T.SphereGeometry(.3,8,8),0xffcc66,x,3.2,z,0xffaa33)});
const arc=person(0x1a6a6a,0xd0d0ff,-94,4),sel=person(0x8a2a8a,0xffffff,-100,-14),mar=person(0x7a2020,0x888888,-106,6);
const RC=[0x3b2a8f,0x8a1a2a,0x1a7a4a,0xb8861a,0x2a2a2a,0x1a6aa8],RN=['Améthyste','Pourpre','Émeraude','Or','Nuit','Azur'];
// Terres du Brasier
patch(36,0x2e1a18,85,-105);
[[70,-80,5],[100,-85,6],[65,-125,6],[105,-115,5]].forEach(([x,z,r])=>mesh(new T.CircleGeometry(r,20).rotateX(-Math.PI/2),0xff5a10,x,.05,z,0xff3a00));
mesh(new T.ConeGeometry(18,30,10),0x3a2a28,118,15,-128);mesh(new T.CylinderGeometry(5,7,3,10),0xff6a20,118,29,-128,0xff3a00);
wall(2,30,0x4a3a38,70,-105,8);wall(2,30,0x4a3a38,100,-105,8);wall(30,2,0x4a3a38,85,-120,8);wall(10,2,0x4a3a38,75,-90,8);wall(10,2,0x4a3a38,95,-90,8);
[[70,-90],[100,-90],[70,-120],[100,-120]].forEach(([x,z])=>{wall(5,5,0x5a4a46,x,z,12);mesh(new T.ConeGeometry(4,4,6),0x7a1a10,x,14,z)});
wall(10,10,0x5a4a46,85,-112,10);mesh(new T.ConeGeometry(8,6,4),0x7a1a10,85,13,-112).rotation.y=Math.PI/4;
[[80,-96],[90,-96]].forEach(([x,z])=>{mesh(new T.CylinderGeometry(.5,.4,1.5),0x333333,x,.75,z);mesh(new T.SphereGeometry(.5,8,8),0xff8a30,x,1.8,z,0xff6a00)});
// Royaume des Glaces
patch(36,0xdde8f5,0,105);patch(9,0x8fd0ff,-14,92);
for(let i=0;i<26;i++){const a=rnd(0,6.28),r=rnd(12,34),h=rnd(3,9);mesh(new T.ConeGeometry(rnd(.8,1.8),h,5),0xa8dcf0,Math.cos(a)*r,h/2,105+Math.sin(a)*r*.9,0x1a4a6a)}
wall(14,10,0xbfe0f5,0,124,12);mesh(new T.ConeGeometry(11,8,4),0x6ab0e0,0,16,124).rotation.y=Math.PI/4;
[[-11,124],[11,124]].forEach(([x,z])=>{wall(4,4,0xa8d0ea,x,z,16);mesh(new T.ConeGeometry(3.5,7,6),0x6ab0e0,x,19.5,z)});
for(let i=0;i<8;i++){const a=i*.785,x=Math.sin(a)*7,z=106+Math.cos(a)*7;mesh(new T.CylinderGeometry(.5,.5,5),0xcfe8ff,x,2.5,z,0x224466);col.push({x,z,w:.5,d:.5})}
mesh(new T.OctahedronGeometry(1.2),0x88ddff,0,6,112,0x44aaff);
// Château d'Arcanor
patch(24,0x77777f,-50,55);
wall(30,2,0x8a8aa0,-50,70,10);wall(2,30,0x8a8aa0,-65,55,10);wall(2,30,0x8a8aa0,-35,55,10);wall(10,2,0x8a8aa0,-59,40,10);wall(10,2,0x8a8aa0,-41,40,10);
[[-65,40],[-35,40],[-65,70],[-35,70]].forEach(([x,z])=>{wall(5,5,0x7a7a92,x,z,15);mesh(new T.ConeGeometry(4.2,5,8),0x2a4a9a,x,17.5,z)});
wall(12,10,0x9a9ab0,-50,62,14);mesh(new T.ConeGeometry(9,6,4),0x2a4a9a,-50,17,62).rotation.y=Math.PI/4;
[-53,-47].forEach(x=>mesh(new T.BoxGeometry(1.5,4,.1),0x2a4a9a,x,9,56.9,0x112266));
// Îles du Firmament
const isl=(x,z,r,y)=>{mesh(new T.CylinderGeometry(r,r,3,14),0x6a9a5a,x,y,z);mesh(new T.ConeGeometry(r,r*.9,14).rotateX(Math.PI),0x6b5a48,x,y-1.5-r*.45,z)};
isl(-110,-105,15,28.5);isl(-135,-122,6,22);isl(-84,-126,7,34);isl(-134,-84,5,38);
for(let i=0;i<6;i++){const a=i*1.047;mesh(new T.CylinderGeometry(.5,.5,6),0xe8e0d0,-110+Math.cos(a)*8,33,-105+Math.sin(a)*8)}
mesh(new T.ConeGeometry(10,3,6),0xc8b8a0,-110,37.5,-105);mesh(new T.OctahedronGeometry(1.2),0xffe08a,-110,33,-105,0xffcc55);
for(let i=0;i<14;i++){const c=mesh(new T.SphereGeometry(rnd(4,8),8,6),0xffffff,-110+rnd(-70,70),rnd(15,45),-105+rnd(-60,60),0x555566);c.scale.y=.4;c.castShadow=false}
const sc=document.createElement('canvas');sc.width=sc.height=128;const sx=sc.getContext('2d'),gr=sx.createRadialGradient(64,64,4,64,64,64);gr.addColorStop(0,'#ffffff');gr.addColorStop(.5,'#b48cff');gr.addColorStop(1,'#5a2aff');sx.fillStyle=gr;sx.fillRect(0,0,128,128);sx.strokeStyle='rgba(255,255,255,.75)';sx.lineWidth=4;for(let k=0;k<4;k++){sx.beginPath();for(let t=0;t<60;t++){const a=t*.2+k*1.57,r=t*1.05;sx.lineTo(64+Math.cos(a)*r,64+Math.sin(a)*r)}sx.stroke()}
const swt=new T.CanvasTexture(sc),sw=[];
const portal=(x,y,z)=>{mesh(new T.TorusGeometry(2,.3,8,24),0x8a5aff,x,y+2.2,z,0x6a3aff);
 const d=new T.Mesh(new T.CircleGeometry(1.8,24),new T.MeshBasicMaterial({map:swt,transparent:true,opacity:.85,side:2}));d.position.set(x,y+2.2,z);scene.add(d);sw.push(d);
 const bm=new T.Mesh(new T.CylinderGeometry(1.6,1.6,26,16,1,true),new T.MeshBasicMaterial({color:0xaa88ff,transparent:true,opacity:.16,side:2}));bm.position.set(x,y+13,z);scene.add(bm)};
portal(-88,0,-16);portal(-110,30,-93);
function warp(){if(P.pc>0||P.dead)return;
 if(!P.fl&&hyp(P.x+88,P.z+16)<2){P.fl=1;P.x=-110;P.z=-100;P.y=30;P.pc=2;say('Le portail t\'emporte vers les Îles du Firmament…',3000)}
 else if(P.fl&&hyp(P.x+110,P.z+93)<2){P.fl=0;P.x=-88;P.z=-11;P.y=0;P.pc=2;say('De retour à Noctheris.',2500)}}
const kal=person(0x7a2a1a,0xaaaaaa,85,-97),ysa=person(0x4a8ac0,0xe0f4ff,0,106),gar=person(0x2a8aaa,0xffffff,-104,-106),orr=person(0x1a3a8a,0xffd060,-50,50);gar.position.y=30;
const LR=[['Commandant Kaldor',`Les Terres du Brasier ne pardonnent pas, voyageur. Prends cette lame de braise.`,'Lame de braise'],['Sage Ysolde',`Le Royaume des Glaces dort depuis mille ans… Prends cet éclat de givre.`,'Éclat de givre'],['Gardien céleste',`Les Îles du Firmament flottent grâce à un fragment d'arcane. Prends cette plume d'astre.`,`Plume d'astre`]];
function lore(i){const l=LR[i];if(P.lr[i]){say(`${l[0]} : « Que les arcanes te guident. »`,3500);return}P.lr[i]=1;P.gold+=150;P.sp++;P.inv.push(l[2]);gain(200);say(`${l[0]} : « ${l[1]} » (+150 or, +1 point de compétence)`,7000)}
// Académie Arcanum
patch(16,0x8a8a96,-75,-86);
const tw=(x,z,r,h,rc,c)=>{mesh(new T.CylinderGeometry(r,r*1.1,h,12),c||0x8b8f99,x,h/2,z);mesh(new T.ConeGeometry(r*1.35,r*2.4,12),rc||0x2b3a6a,x,h+r*1.2,z);col.push({x,z,w:r,d:r});mesh(new T.BoxGeometry(.7,1.4,.1),0x222222,x,h*.65,z+r+.02,0xffcc55)};
const rf=mesh(new T.ConeGeometry(8.5,7,4).rotateY(Math.PI/4),0x3a3f5a,-75,17.5,-102.5);rf.scale.set(2.5,1,1.3);
wall(12,10,0x7d8190,-95,-108,12);wall(12,10,0x7d8190,-55,-108,12);
tw(-92,-96,4,32);tw(-58,-96,3.5,26);tw(-75,-110,3.2,30,0x6a2a3a);tw(-100,-92,3,24);tw(-52,-90,3,20,0x2a5a3a);tw(-62,-114,2.5,38,0x2b3a6a,0x9a9eaa);tw(-88,-114,2.4,36,0x6a2a3a);
mesh(new T.CircleGeometry(2,20),0xfff0c0,-92,26,-91.9,0xffe08a);
wall(13,2,0x7d8190,-88.5,-78,8);wall(13,2,0x7d8190,-61.5,-78,8);tw(-82,-78,2.5,16);tw(-68,-78,2.5,16);wall(2,17,0x7d8190,-95,-86.5,8);wall(2,17,0x7d8190,-55,-86.5,8);
[-82,-68].forEach(x=>mesh(new T.SphereGeometry(.4,8,8),0xff8a30,x,5,-75.4,0xff6a00));
box(6,7,1.5,0x7d8190,-75,10.5,-95.75);[-77.6,-72.4].forEach(x=>box(.6,7,1.2,0x4a2e1a,x,3.5,-95.4));[0xaa2222,0x228844,0x2255aa,0xddaa22].forEach((c,i)=>mesh(new T.BoxGeometry(1.8,5,.1),c,[-86,-81,-69,-64][i],9,-94.9));[-84,-80,-70,-66].forEach(x=>mesh(new T.BoxGeometry(1.2,2.4,.1),0x222222,x,3,-94.9,0xffcc55));
const cand=[];for(let i=0;i<30;i++){const m=new T.Mesh(new T.CylinderGeometry(.08,.08,.7,6),new T.MeshBasicMaterial({color:0xfff2c0}));m.userData.y=rnd(5,9);m.position.set(-75+rnd(-14,14),m.userData.y,-86+rnd(-6,6));scene.add(m);cand.push(m)}
box(61,.05,6,0x3a7ac0,-109.5,.04,-68);box(49,.05,6,0x3a7ac0,-46.5,.04,-68);col.push({x:-109.5,z:-68,w:30.5,d:3},{x:-46.5,z:-68,w:24.5,d:3});
box(8,.4,9,0x6a4a2a,-75,.2,-68);[-78.5,-71.5].forEach(x=>[-71.5,-64.5].forEach(z=>box(.4,4.5,.4,0x4a2e1a,x,2.25,z)));box(8.4,.4,9.4,0x5a3a22,-75,4.6,-68);
[[-46,-98],[-46,-108],[-40,-103]].forEach(([x,z])=>{const g=new T.Mesh(new T.SphereGeometry(4,12,8,0,6.28,0,1.57),new T.MeshBasicMaterial({color:0x99ddbb,transparent:true,opacity:.4,side:2}));g.position.set(x,0,z);scene.add(g);col.push({x,z,w:3.5,d:3.5});mesh(new T.SphereGeometry(.6,8,6),0x2a8a3a,x,.6,z)});
patch(11,0x5a9a4a,-33,-122);[-4,0,4].forEach(dx=>[-9,9].forEach(dz=>{mesh(new T.CylinderGeometry(.15,.15,8),0xcaa86a,-33+dx,4,-122+dz);mesh(new T.TorusGeometry(1.3,.12,6,16),0xe8c040,-33+dx,8.5,-122+dz,0x886600)}));
wall(5,5,0x6a4a2a,-112,-78,3);mesh(new T.ConeGeometry(4.2,2.5,4),0x4a2e1a,-112,4.25,-78).rotation.y=Math.PI/4;mesh(new T.BoxGeometry(.7,2,.7),0x6b5a4a,-110.5,4.5,-78);
for(let i=0;i<18;i++){const x=rnd(-136,-116),z=rnd(-126,-86),s=rnd(.9,1.6);mesh(new T.CylinderGeometry(.35*s,.45*s,3*s),0x3a2a1a,x,1.5*s,z);for(let k=0;k<2;k++)mesh(new T.ConeGeometry((2.4-k*.8)*s,(3.4-k*.4)*s,8),0x123a22,x,(3.2+k*1.9)*s,z);col.push({x,z,w:.5,d:.5})}
const dir=person(0x5a1a7a,0xffd060,-75,-86),gk=person(0x5a3a1a,0x333333,-112,-73);
LR.push([`Directrice Aldara`,`Bienvenue à l'Académie Arcanum, jeune mage. Les cours t'attendent… et ce grimoire d'étude aussi.`,`Grimoire d'étude`],[`Garde-chasse Hamond`,`Ne t'aventure pas dans le bois sombre à la nuit tombée. Prends cette amulette de protection.`,`Amulette du garde`]);
// Intérieur de la Grande Salle
let camD=8;
box(28,.15,13.5,0x6a6a78,-75,.08,-102.5);box(3,.18,13,0x7a1a1a,-75,.1,-102.5);box(30,.5,15,0x4a3a2c,-75,14.2,-102.5);
wall(12,1.5,0x7d8190,-84,-95.75,14);wall(12,1.5,0x7d8190,-66,-95.75,14);wall(1.5,15,0x7d8190,-89.25,-102.5,14);wall(1.5,15,0x7d8190,-60.75,-102.5,14);wall(30,1.5,0x7d8190,-75,-109.25,14);
[-84,-79,-71,-66].forEach(x=>{box(1.6,1,9,0x5a3a22,x,.5,-102);col.push({x,z:-102,w:.8,d:4.5})});
[-88,-62].forEach(x=>[-99,-103,-107].forEach(z=>{box(.8,5,3,0x4a2e1a,x,2.5,z);box(.9,.3,2.6,0xaa8844,x,4,z);col.push({x,z,w:.4,d:1.5})}));
[0xaa2222,0x228844,0x2255aa,0xddaa22].forEach((c,i)=>mesh(new T.BoxGeometry(2,7,.1),c,[-85,-81,-69,-65][i],8,-108.4));
for(let i=0;i<26;i++){const m=new T.Mesh(new T.CylinderGeometry(.08,.08,.7,6),new T.MeshBasicMaterial({color:0xfff2c0}));m.userData.y=rnd(9.5,12.8);m.position.set(rnd(-87,-63),m.userData.y,rnd(-108,-97));scene.add(m);cand.push(m)}
[[-80,-100],[-70,-104]].forEach(([x,z])=>{const l=new T.PointLight(0xffcc88,1.1,26);l.position.set(x,9,z);scene.add(l)});
const hm=person(0x7a2a7a,0xffffff,-75,-103);
LR.push([`Professeur Merrin`,`Tu es dans la Grande Salle, où chaque étudiant reçoit la bénédiction des arcanes. Mange, repose-toi, et prends ce parchemin.`,`Parchemin de la Grande Salle`]);
function castle(){P.hp=P.mhp;P.mp=P.mmp;P.rc=((P.rc|0)+1)%RC.length;pm.children[0].material.color.setHex(RC[P.rc]);save(1);say(`Intendant Orrick : « Bienvenue chez vous. Repos complet, partie sauvegardée — tenue ${RN[P.rc]}. »`,4500)}
function push(o,r){col.forEach(c=>{const dx=o.x-c.x,dz=o.z-c.z,ox=c.w+r-Math.abs(dx),oz=c.d+r-Math.abs(dz);if(ox>0&&oz>0){if(ox<oz)o.x+=dx<0?-ox:ox;else o.z+=dz<0?-oz:oz}})}
function person(robe,hat,x,z){const g=new T.Group(),a=(geo,c,y)=>{const m=new T.Mesh(geo,M(c));m.position.y=y;m.castShadow=true;g.add(m);return m};
a(new T.ConeGeometry(.6,1.6,10),robe,.8);a(new T.SphereGeometry(.28,10,8),0xf0c8a0,1.8);a(new T.ConeGeometry(.35,.7,8),hat,2.25);g.position.set(x,0,z);scene.add(g);return g}
const elder=person(0x3a3aa0,0xe0e0ff,6,-4),pm=person(0x3b2a8f,0x2a1a6e,0,8),merch=person(0x8a3a1a,0xe8c040,-5,-3);
{const s=new T.Mesh(new T.CylinderGeometry(.04,.04,2),M(0x6b4a2a));s.position.set(.6,1,0);const o=new T.Mesh(new T.SphereGeometry(.12,8,8),M(0,0x66aaff));o.position.set(.6,2.1,0);pm.add(s,o);const pl=new T.PointLight(0x6699ff,.8,12);pl.position.set(.6,2.1,0);pm.add(pl);const cp_=new T.Mesh(new T.BoxGeometry(.9,1.5,.08),M(0x6a1b9a));cp_.position.set(0,1.1,.45);cp_.rotation.x=.15;cp_.castShadow=true;pm.add(cp_)}
// État
const P0=()=>({hp:100,mhp:100,mp:100,mmp:100,xp:0,lv:1,gold:0,pot:2,x:0,z:8,quest:0,kills:0,frag:0,inv:[],bd:0,ch:0,sp:0,up:[0,0,0,0],eq:[0,0,0],q2:0,k2:0,lr:[0,0,0],rc:0,cl:0,qs:[],qp:[],bk:[0,0],ivs:0,sh:0,sht:0,pc:0,y:0,fl:0,yaw:0,pitch:.35,dead:0});
let P=P0(),run=0,inv=0,ck=25,hudT=0,wasLock=0,mt;const K={},KEYS=['hp','mhp','mp','mmp','xp','lv','gold','pot','x','z','quest','kills','frag','inv','bd','ch','sp','up','eq','q2','k2','lr','rc','cl','qs','qp','bk'];
function save(s){if(P.dead)return;try{const o={};KEYS.forEach(k=>o[k]=P[k]);localStorage.setItem('eldoria1',JSON.stringify(o));if(!s)say('Partie sauvegardée.',1500)}catch(e){}}
function load(){try{const o=JSON.parse(localStorage.getItem('eldoria1'));if(o){Object.assign(P,o);return 1}}catch(e){}return 0}
function say(t,ms){const m=$('msg');m.textContent=t;m.style.display='block';clearTimeout(mt);mt=setTimeout(()=>m.style.display='none',ms||3500)}
const need=()=>40+P.lv*30;
function gain(x){P.xp+=x;while(P.xp>=need()){P.xp-=need();P.lv++;P.sp++;P.mhp+=15;P.mmp+=10;P.hp=P.mhp;P.mp=P.mmp;say('Niveau '+P.lv+' ! Vie et mana augmentées. +1 point de compétence (T).')}}
function hurtP(d){if(P.dead)return;if(P.sh>0){const a=Math.min(P.sh,d);P.sh-=a;d-=a}P.hp-=d;if(P.hp<=0){P.dead=1;say('Tu es tombé… Renaissance au village.',2500);setTimeout(()=>{P.hp=P.mhp/2;P.x=0;P.z=8;P.gold=Math.floor(P.gold*.8);P.dead=0},2500)}}
// Ennemis
const mons=[];
function spawn(x,z){const g=new T.Group(),b=new T.Mesh(new T.SphereGeometry(.8,10,8),M(0x3a2a55,0x1a0a30));b.scale.y=1.3;b.position.y=1.1;b.castShadow=true;g.add(b);const tail=new T.Mesh(new T.ConeGeometry(.7,1.2,8),M(0x2a1a45,0x120822));tail.rotation.x=Math.PI;tail.position.y=.5;g.add(tail);
[-.25,.25].forEach(e=>{const y=new T.Mesh(new T.SphereGeometry(.1,6,6),M(0,0x33ff88));y.position.set(e,1.4,-.7);g.add(y)});scene.add(g);
mons.push({x,z,hx:x,hz:z,hp:40,mhp:40,g,cd:0,slow:0,dead:0,rs:0,w:rnd(0,6)})}
for(let i=0;i<9;i++)spawn(rnd(36,92),rnd(-30,50));[[-10,-82],[10,-82],[0,-90]].forEach(p=>spawn(p[0],p[1]));for(let i=0;i<8;i++){const a=rnd(0,6.28),r=rnd(38,48);spawn(clamp(-100+Math.cos(a)*r,-138,-62),Math.sin(a)*r)}for(let i=0;i<6;i++)spawn(rnd(55,115),rnd(-130,-85));for(let i=0;i<6;i++)spawn(rnd(-30,30),rnd(80,125));for(let i=0;i<6;i++)spawn(rnd(-135,-115),rnd(-125,-85));for(let i=0;i<8;i++)spawn(rnd(32,100),rnd(-45,60));for(let i=0;i<4;i++)spawn(rnd(60,110),rnd(-125,-85));for(let i=0;i<4;i++)spawn(rnd(-30,30),rnd(80,125));
function hit(m,d){m.hp-=d*(m.arm>0?.4:1);if(m.hp<=0){m.dead=1;m.rs=m.boss?1e9:25;m.g.visible=false;if(m.boss)bossDown(m);qkill(m.k);gain(m.xp||25);P.gold+=Math.floor(rnd(3,9)*(m.gm||1));if(P.quest==1&&++P.kills>=5){P.quest=2;say('Quête : retourne voir Aldric au village (E).',5000)}if(P.q2==1&&++P.k2>=6){P.q2=2;say('Quête : retourne voir la capitaine Mara à Noctheris (E).',5000)}}}
function monUpd(m,dt){
 if(m.dead){m.rs-=dt;if(m.rs<=0){m.dead=0;m.hp=m.mhp;m.x=m.hx;m.z=m.hz;m.g.visible=true}return}
 m.cd-=dt;m.slow-=dt;const dx=P.x-m.x,dz=P.z-m.z,d=hyp(dx,dz),sp=m.slow>0?1.2:3.6;
 if(d<14&&!P.dead&&!(P.ivs>0)&&hyp(m.x-m.hx,m.z-m.hz)<35){if(d>1.8){m.x+=dx/d*sp*dt;m.z+=dz/d*sp*dt}else if(m.cd<=0){m.cd=1.1;hurtP(8)}m.g.rotation.y=Math.atan2(-dx,-dz)}
 else{m.w+=dt;const hx=m.hx-m.x,hz=m.hz-m.z,hd=hyp(hx,hz);if(hd>6){m.x+=hx/hd*2*dt;m.z+=hz/hd*2*dt}else{m.x+=Math.cos(m.w)*dt;m.z+=Math.sin(m.w)*dt}}
 push(m,.7);m.g.position.set(m.x,Math.sin(ck*3+m.w)*.15,m.z)}
// Boss : Varkhûn
const B={x:0,z:-100,hp:600,mhp:600,t:3,s:'i',ph:1,g:new T.Group(),dead:0,tx:0,tz:0,h:0},bm=c=>M(c,0x220600),ba=(w,h,d,c,x,y,z)=>{const m=new T.Mesh(new T.BoxGeometry(w,h,d),bm(c));m.position.set(x,y,z);m.castShadow=true;B.g.add(m);return m};
ba(3,2.5,5,0x8a1c10,0,2,0);ba(1.8,1.6,2,0x9a2412,0,3.2,-3);ba(.4,.3,.3,0xffdd00,-.5,3.5,-4.05).material.emissive.set(0xffcc00);ba(.4,.3,.3,0xffdd00,.5,3.5,-4.05).material.emissive.set(0xffcc00);ba(.8,.8,4,0x7a1810,0,1.5,4);
[-1,1].forEach(s=>{const w=ba(5,.15,3,0x5a0e08,s*3.5,3.2,.3);w.rotation.z=s*.35;const h=new T.Mesh(new T.ConeGeometry(.2,1,6),bm(0xddd0b0));h.position.set(s*.6,4.3,-3);B.g.add(h)});
B.g.scale.setScalar(1.4);B.g.position.set(B.x,0,B.z);const bl=new T.PointLight(0xff4400,1.4,30);bl.position.set(0,3,0);B.g.add(bl);scene.add(B.g);
const ring=new T.Mesh(new T.RingGeometry(11.5,12,48).rotateX(-Math.PI/2),new T.MeshBasicMaterial({color:0xff4400,transparent:true,opacity:.7,side:2}));ring.visible=false;scene.add(ring);
function hitB(d){B.hp-=d;if(B.hp<=0&&!B.dead){B.dead=1;P.bd=1;B.g.visible=false;ring.visible=false;$('boss').style.display='none';gain(200);P.gold+=100;P.frag++;P.inv.push('Fragment du Cœur d\'Eldoria #1');P.quest=4;qkill('v');say('Varkhûn est vaincu ! Fragment du Cœur obtenu (1/7).',6000);save(1)}}
function bossUpd(dt){
 if(B.dead)return;const dx=P.x-B.x,dz=P.z-B.z,d=hyp(dx,dz),act=d<42&&!P.dead;$('boss').style.display=act?'block':'none';
 if(!act){B.s='i';ring.visible=false;return}
 $('bh').style.width=Math.max(0,B.hp/B.mhp*100)+'%';
 if(B.ph==1&&B.hp<B.mhp/2){B.ph=2;say('Varkhûn entre en fureur !',3000)}
 const f=B.ph==2?1.5:1;B.t-=dt;
 if(B.s=='i'){B.g.rotation.y=Math.atan2(-dx,-dz);if(d>14){B.x+=dx/d*4*f*dt;B.z+=dz/d*4*f*dt}
  if(B.t<=0){const r=Math.random();
   if(r<.4){const n=B.ph==2?7:5,a0=Math.atan2(dx,dz);for(let i=0;i<n;i++){const a=a0+(i-(n-1)/2)*.28;proj(B.x,3,B.z,Math.sin(a)*16,Math.cos(a)*16,12,'e')}B.t=3/f}
   else if(r<.75){B.s='s';B.t=1.2;ring.visible=true;ring.position.set(B.x,.1,B.z)}
   else{B.s='c';B.t=.7;B.tx=dx/d;B.tz=dz/d;B.h=0}}}
 else if(B.s=='s'){ring.material.opacity=.3+.4*Math.abs(Math.sin(B.t*12));if(B.t<=0){ring.visible=false;if(d<12)hurtP(30);B.s='i';B.t=2/f}}
 else{B.x+=B.tx*28*dt;B.z+=B.tz*28*dt;if(!B.h&&d<4){B.h=1;hurtP(25)}if(B.t<=0){B.s='i';B.t=2/f}}
 B.x=clamp(B.x,-17,17);B.z=clamp(B.z,-108,-73);B.g.position.set(B.x,0,B.z)}
// Projectiles & effets
const pr=[],fx=[];
function proj(x,y,z,vx,vz,d,o){const m=new T.Mesh(new T.SphereGeometry(o=='p'?.4:.5,8,8),new T.MeshBasicMaterial({color:o=='p'?0xff7a1a:0xff3300}));m.position.set(x,y,z);m.add(new T.Mesh(new T.SphereGeometry(.9,8,8),new T.MeshBasicMaterial({color:o=='p'?0xff5a00:0xff2200,transparent:true,opacity:.3})));scene.add(m);pr.push({m,vx,vz,d,o,l:2.5,tr:0})}
function prUpd(dt){for(let i=pr.length-1;i>=0;i--){const p=pr[i];p.m.position.x+=p.vx*dt;p.m.position.z+=p.vz*dt;p.l-=dt;let h=p.l<=0;if((p.tr-=dt)<=0){p.tr=.04;const q=new T.Mesh(new T.SphereGeometry(.3,6,6),new T.MeshBasicMaterial({color:p.col||(p.o=='p'?0xffaa33:0xff4400),transparent:true}));q.position.copy(p.m.position);ef(q,.35,-.8)}const x=p.m.position.x,z=p.m.position.z;
 if(!h){if(p.o=='p'){for(const m of mons)if(!m.dead&&hyp(m.x-x,m.z-z)<1.3*m.g.scale.x){impact(p,m);h=1;break}
   if(!h&&!B.dead&&hyp(B.x-x,B.z-z)<4){hitB(p.d);h=1}}
  else if(hyp(P.x-x,P.z-z)<1){hurtP(p.d);h=1}
  if(!h)for(const c of col)if(Math.abs(x-c.x)<c.w&&Math.abs(z-c.z)<c.d){h=1;break}}
 if(h){const q=new T.Mesh(new T.SphereGeometry(.8,8,8),new T.MeshBasicMaterial({color:p.col||(p.o=='p'?0xffcc55:0xff5500),transparent:true}));q.position.copy(p.m.position);ef(q,.3,2.5);scene.remove(p.m);pr.splice(i,1)}}}
function ef(m,l,s){scene.add(m);fx.push({m,l,s,t:l})}
function fxUpd(dt){for(let i=fx.length-1;i>=0;i--){const e=fx[i];e.t-=dt;const k=1-e.t/e.l;if(e.s)e.m.scale.setScalar(1+k*e.s);e.m.material.opacity=1-k;if(e.t<=0){scene.remove(e.m);fx.splice(i,1)}}}
// Sorts
const SP=[{n:'Feu',k:'🔥',c:10,cd:.5},{n:'Glace',k:'❄️',c:25,cd:4},{n:'Foudre',k:'⚡',c:20,cd:2},{n:'Téléport',k:'🌀',c:15,cd:3}],cds=[0,0,0,0];
$('sp').innerHTML=SP.map((s,i)=>`<div class="sk"><b>${s.k}</b>${i+1} ${s.n}<br>${s.c} mana<u></u></div>`).join('');
function aim(){const fx_=-Math.sin(P.yaw),fz=-Math.cos(P.yaw),t=mons.filter(m=>!m.dead);if(!B.dead)t.push(B);let best=null,bd=1e9;
 for(const m of t){const dx=m.x-P.x,dz=m.z-P.z,d=hyp(dx,dz);if(d<40&&(dx*fx_+dz*fz)/d>.93&&d<bd){bd=d;best=[dx/d,dz/d]}}return best||[fx_,fz]}
function cast(i){if(!run||inv||P.dead||cds[i]>0||P.mp<SP[i].c)return;const L=(1+(P.lv-1)*.15)*(1+.25*P.up[i])*(P.eq[1]?1.2:1);
 if(i==0){const[a,b]=aim();proj(P.x+a,1.5,P.z+b,a*30,b*30,Math.round(25*L),'p')}
 else if(i==1){mons.forEach(m=>{if(!m.dead&&hyp(m.x-P.x,m.z-P.z)<9){m.slow=3;hit(m,Math.round(20*L))}});if(!B.dead&&hyp(B.x-P.x,B.z-P.z)<12)hitB(Math.round(20*L));
  const r=new T.Mesh(new T.RingGeometry(.8,1,32).rotateX(-Math.PI/2),new T.MeshBasicMaterial({color:0x77ddff,transparent:true,side:2}));r.position.set(P.x,.3,P.z);ef(r,.5,8)}
 else if(i==3){const dist=10+3*P.up[3],a=-Math.sin(P.yaw),b=-Math.cos(P.yaw);for(let s=0;s<dist;s++){const q={x:P.x+a,z:P.z+b};push(q,.6);if(hyp(q.x-P.x-a,q.z-P.z-b)>.01)break;P.x=q.x;P.z=q.z}
  const r=new T.Mesh(new T.RingGeometry(.5,1,24).rotateX(-Math.PI/2),new T.MeshBasicMaterial({color:0xaa77ff,transparent:true,side:2}));r.position.set(P.x,.3,P.z);ef(r,.4,3)}
 else{const t=mons.filter(m=>!m.dead&&hyp(m.x-P.x,m.z-P.z)<20).sort((a,b)=>hyp(a.x-P.x,a.z-P.z)-hyp(b.x-P.x,b.z-P.z)).slice(0,3);if(!B.dead&&hyp(B.x-P.x,B.z-P.z)<26)t.push(B);if(!t.length)return;
  t.forEach(m=>{const pts=[];for(let k=0;k<6;k++){const j=k%5?rnd(-.6,.6):0;pts.push(new T.Vector3(m.x+j,12-k*2.2,m.z+j))}
   ef(new T.Line(new T.BufferGeometry().setFromPoints(pts),new T.LineBasicMaterial({color:0xffff88,transparent:true})),.25,0);
   if(m===B)hitB(Math.round(30*L));else hit(m,Math.round(30*L))})}
 P.mp-=SP[i].c;cds[i]=SP[i].cd}
function pot(){if(P.pot>0&&P.hp<P.mhp&&!P.dead){P.pot--;P.hp=Math.min(P.mhp,P.hp+60);if(inv)invUI()}}
// Quête & interactions
function talk(){const q=P.quest;
 if(q==0){P.quest=1;P.kills=0;say(`Aldric : « Des spectres rôdent dans la Forêt des Murmures, à l'est. Élimines-en cinq et reviens me voir. »`,6000)}
 else if(q==1)say(`Aldric : « Il reste ${5-P.kills} spectres à vaincre. »`);
 else if(q==2){P.quest=3;P.gold+=50;P.pot+=2;P.mmp+=30;P.inv.push('Grimoire des Braises (+30 mana max)');gain(60);say(`Aldric : « Bravo ! Prends ce grimoire. Varkhûn garde un fragment du Cœur d'Eldoria dans le donjon, au nord. »`,7000)}
 else if(q==3)say(`Aldric : « Le donjon est au nord. N'oublie pas tes potions (H). »`);
 else say(`Aldric : « Un fragment retrouvé… il en reste six. Le voyage ne fait que commencer. »`,6000)}
function act(){if(inv||P.dead)return;if(HUB.some(h=>hyp(P.x-h[0],P.z-h[1])<3.2)){qh=HUB.findIndex(h=>hyp(P.x-h[0],P.z-h[1])<3.2);panel('q')}else if(hyp(P.x-6,P.z+4)<5)talk();else if(hyp(P.x+5,P.z+3)<4){shp=0;panel('s')}else if(hyp(P.x+94,P.z-4)<4){shp=1;panel('s')}else if(hyp(P.x+100,P.z+14)<4)acad();else if(hyp(P.x+106,P.z-6)<4)guild();else if(hyp(P.x+75,P.z+103)<3.5){lore(5);P.hp=P.mhp;P.mp=P.mmp}else if(hyp(P.x+75,P.z+86)<4)lore(3);else if(hyp(P.x+112,P.z+73)<4)lore(4);else if(hyp(P.x-85,P.z+97)<4)lore(0);else if(hyp(P.x,P.z-106)<4)lore(1);else if(P.fl&&hyp(P.x+104,P.z+106)<4)lore(2);else if(hyp(P.x+50,P.z-50)<4)castle();else if(!P.fl&&hyp(P.x+88,P.z+16)<3.5){P.fl=1;P.x=-110;P.z=-100;P.y=30;say('Le portail vous emporte vers les Îles du Firmament…')}else if(P.fl&&hyp(P.x+110,P.z+93)<3.5){P.fl=0;P.x=-88;P.z=-12;P.y=0;say('De retour à Noctheris.')}else if(!P.ch&&hyp(P.x-70,P.z+15)<4){P.ch=1;chest.visible=false;P.gold+=40;P.pot++;P.inv.push('Anneau runique (trésor de la forêt)');say('Trésor trouvé : 40 or, 1 potion, un anneau runique.')}}
const place=()=>P.fl?'Îles du Firmament':(P.x>-90&&P.x<-60&&P.z<-95&&P.z>-110)?'Grande Salle':hyp(P.x+78,P.z+95)<40?'Académie Arcanum':hyp(P.x-85,P.z+105)<42?'Terres du Brasier':P.z>70&&Math.abs(P.x)<45?'Royaume des Glaces':hyp(P.x+50,P.z-55)<26?`Château d'Arcanor`:hyp(P.x+100,P.z)<40?'Noctheris, cité des sorciers':P.z<-68&&Math.abs(P.x)<22?'Donjon du Brasier':P.x>28?'Forêt des Murmures':'Village de Brumeval';
let pan=0;function panel(k){pan=pan==k?0:k;inv=!!pan;['inv','tr','sh'].forEach(i=>$(i).style.display='none');if(pan){$({i:'inv',t:'tr',s:'sh'}[pan]).style.display='block';({i:invUI,t:trUI,s:shUI})[pan]()}}
function trUI(){$('tr').innerHTML=`<b>Compétences</b> — points : ${P.sp}<hr>`+SP.map((s,i)=>`${i+1} · ${s.k} ${s.n} — niveau ${P.up[i]}/5`).join('<br>')+`<hr><small>Touches 1 à 4 : améliorer (1 point) · T pour fermer</small>`}
function upg(i){if(P.sp>0&&P.up[i]<5){P.sp--;P.up[i]++;trUI()}}
const SH=[['Potion de soin',15],['Cristal de mana (+10 mana max)',40],['Élixir de vie (+20 PV max)',50]];
function shUI(){$('sh').innerHTML=`<b>Orin, marchand</b> — or : ${P.gold}<hr>`+SH.map((s,i)=>`${i+1} · ${s[0]} — ${s[1]} or`).join('<br>')+`<hr><small>Touches 1 à 3 : acheter · E pour fermer</small>`}
function buy(i){const s=SH[i];if(!s)return;if(P.gold<s[1]){say('Pas assez d\'or.',1500);return}P.gold-=s[1];if(i==0)P.pot++;else if(i==1)P.mmp+=10;else{P.mhp+=20;P.hp+=20}shUI()}
const EQ=[[`Robe de l'Académie (+25 PV max)`,90],['Bâton runique (+20 % dégâts)',140],['Grimoire de Noctheris (+25 mana max)',70]];let shp=0;
function shUI(){const L=shp?EQ:SH;$('sh').innerHTML=`<b>${shp?'Veylan, arcaniste':'Orin, marchand'}</b> — or : ${P.gold}<hr>`+L.map((s,i)=>`${i+1} · ${s[0]} — ${shp&&P.eq[i]?'acheté':s[1]+' or'}`).join('<br>')+`<hr><small>Touches 1 à 3 : acheter · E pour fermer</small>`}
function buy(i){const L=shp?EQ:SH,s=L[i];if(!s)return;if(shp&&P.eq[i]){say('Déjà acheté.',1500);return}if(P.gold<s[1]){say(`Pas assez d'or.`,1500);return}P.gold-=s[1];
 if(shp){P.eq[i]=1;if(i==0){P.mhp+=25;P.hp+=25}else if(i==2)P.mmp+=25}else if(i==0)P.pot++;else if(i==1)P.mmp+=10;else{P.mhp+=20;P.hp+=20}shUI()}
function guild(){const q=P.q2;if(q==0){P.q2=1;P.k2=0;say(`Capitaine Mara : « Des spectres cristallins rôdent aux abords de Noctheris. Élimines-en six pour la Guilde. »`,6000)}else if(q==1)say(`Capitaine Mara : « Il en reste ${6-P.k2}. »`);else if(q==2){P.q2=3;P.gold+=120;P.pot+=2;P.inv.push('Insigne de la Guilde de Noctheris');gain(150);say(`Capitaine Mara : « Bien joué ! Va voir la Maîtresse Sélène à l'Académie. »`,6000)}else say(`Capitaine Mara : « La Guilde te doit une faveur. »`)}
function acad(){const q=P.q2;if(q==3){P.q2=4;P.sp+=2;P.inv.push(`Sceau de l'Académie`);say(`Sélène : « Tu portes l'insigne de la Guilde. Voici deux points de savoir arcanique (T). Le Cœur d'Eldoria a sept fragments… le prochain repose sous les cryptes de Morvath. »`,8000)}else say(q<3?`Sélène : « Prouve ta valeur auprès de la Guilde, puis reviens. »`:`Sélène : « La crypte de Morvath s'est ouverte, au sud-est. Consulte le tableau de quêtes. »`,5000)}
const CL=[
 {n:'Kaël',s:'Mage des arcanes — téléportation et temps',rb:0x3b2a8f,c:0x66aaff,sp:[{n:'Trait arcanique',k:'✨',c:10,cd:.5,t:'p',d:25,col:0x66aaff},{n:'Distorsion',k:'⏳',c:25,cd:4,t:'n',d:12,sl:5,r:10,col:0x88ddff},{n:'Foudre',k:'⚡',c:20,cd:2,t:'s',d:30,m:3,col:0xffff88},{n:'Téléport',k:'🌀',c:15,cd:3,t:'b',dist:10,col:0xaa77ff}]},
 {n:'Nyx',s:`Sorcière de l'ombre — invisibilité et rapidité`,rb:0x3a1a4a,c:0xaa44ff,sp:[{n:`Lame d'ombre`,k:'🗡️',c:8,cd:.35,t:'p',d:20,v:42,col:0xaa44ff},{n:'Voile',k:'👤',c:30,cd:8,t:'v',col:0xaa44ff},{n:'Frappe fantôme',k:'💀',c:22,cd:2.5,t:'s',d:45,m:2,col:0xaa44ff},{n:`Pas d'ombre`,k:'💨',c:8,cd:1.5,t:'b',dist:7,col:0xaa44ff}]},
 {n:'Draven',s:'Mage du feu — explosions et phénix',rb:0x8a2a10,c:0xff6a1a,sp:[{n:'Boule de feu',k:'🔥',c:12,cd:.6,t:'p',d:30,ex:1,col:0xff7a1a},{n:'Nova ardente',k:'🌋',c:25,cd:4,t:'n',d:35,r:9,col:0xff5a10},{n:'Météores',k:'☄️',c:28,cd:3,t:'s',d:50,m:3,col:0xff6600},{n:'Phénix',k:'🦅',c:35,cd:12,t:'h',d:1,col:0xff6a1a}]},
 {n:'Eryon',s:'Mage de glace — givre et bouclier',rb:0x1a5a8a,c:0x77ddff,sp:[{n:'Éclat de glace',k:'❄️',c:10,cd:.5,t:'p',d:22,sl:2,col:0x99ddff},{n:'Tempête de neige',k:'🌨️',c:28,cd:5,t:'n',d:25,sl:4,r:12,col:0xddeeff},{n:'Lance de givre',k:'🧊',c:20,cd:2,t:'s',d:38,m:2,sl:3,col:0x88ccff},{n:'Armure de givre',k:'🛡️',c:25,cd:10,t:'a',col:0x99ddff}]}];
function impact(p,m){const f=!p.sl&&m.slow>0?1.5:1;hit(m,p.d*f);if(p.sl)m.slow=p.sl;if(p.ex)mons.forEach(o=>{if(o!==m&&!o.dead&&hyp(o.x-m.x,o.z-m.z)<4)hit(o,Math.round(p.d*.5))})}
function rng(c,r){const m=new T.Mesh(new T.RingGeometry(.8,1,32).rotateX(-Math.PI/2),new T.MeshBasicMaterial({color:c,transparent:true,side:2}));m.position.set(P.x,P.y+.3,P.z);ef(m,.5,r-1)}
function zap(x,z,c){const pts=[];for(let k=0;k<6;k++){const j=k%5?rnd(-.6,.6):0;pts.push(new T.Vector3(x+j,12-k*2.2,z+j))}ef(new T.Line(new T.BufferGeometry().setFromPoints(pts),new T.LineBasicMaterial({color:c,transparent:true})),.25,0);const q=new T.Mesh(new T.SphereGeometry(1.2,8,8),new T.MeshBasicMaterial({color:c,transparent:true}));q.position.set(x,1,z);ef(q,.35,2)}
function cast(i){if(!run||inv||P.dead||cds[i]>0||P.mp<SP[i].c)return;const s=SP[i],L=(1+(P.lv-1)*.15)*(1+.25*P.up[i])*(P.eq[1]?1.2:1),dm=Math.round((s.d||0)*L);
 if(s.t=='p'){const[a,b]=aim(),v=s.v||30;proj(P.x+a,1.5+P.y,P.z+b,a*v,b*v,dm,'p');const q=pr[pr.length-1];q.col=s.col;q.sl=s.sl;q.ex=s.ex;q.m.material.color.setHex(s.col);q.m.children[0].material.color.setHex(s.col)}
 else if(s.t=='n'){mons.forEach(m=>{if(!m.dead&&hyp(m.x-P.x,m.z-P.z)<s.r){if(s.sl)m.slow=s.sl;hit(m,dm)}});if(!B.dead&&hyp(B.x-P.x,B.z-P.z)<s.r+3)hitB(dm);rng(s.col,s.r)}
 else if(s.t=='s'){const t=mons.filter(m=>!m.dead&&hyp(m.x-P.x,m.z-P.z)<20).sort((a,b)=>hyp(a.x-P.x,a.z-P.z)-hyp(b.x-P.x,b.z-P.z)).slice(0,s.m);if(!B.dead&&hyp(B.x-P.x,B.z-P.z)<26)t.push(B);if(!t.length)return;t.forEach(m=>{zap(m.x,m.z,s.col);if(m===B)hitB(dm);else{hit(m,dm);if(s.sl)m.slow=s.sl}})}
 else if(s.t=='b'){const dist=s.dist+3*P.up[i],a=-Math.sin(P.yaw),b=-Math.cos(P.yaw);for(let k=0;k<dist;k++){const q={x:P.x+a,z:P.z+b};push(q,.6);if(hyp(q.x-P.x-a,q.z-P.z-b)>.01)break;P.x=q.x;P.z=q.z}rng(s.col,3)}
 else if(s.t=='v'){P.ivs=5;say(`Tu te fonds dans l'ombre…`,1500);rng(s.col,3)}
 else if(s.t=='h'){P.hp=Math.min(P.mhp,P.hp+Math.round(P.mhp*.35));mons.forEach(m=>{if(!m.dead&&hyp(m.x-P.x,m.z-P.z)<8)hit(m,Math.round(25*L))});if(!B.dead&&hyp(B.x-P.x,B.z-P.z)<11)hitB(Math.round(25*L));rng(s.col,8)}
 else if(s.t=='a'){P.sh=40+10*P.lv;P.sht=8;rng(s.col,2)}
 P.mp-=s.c;cds[i]=s.cd}
function setCl(i,r){P.cl=i;const c=CL[i];if(r)P.rc=0;RC[0]=c.rb;SP.length=0;SP.push(...c.sp);cds.fill(0);$('sp').innerHTML=SP.map((s,k)=>`<div class="sk"><b>${s.k}</b>${k+1} ${s.n}<br>${s.c} mana<u></u></div>`).join('');pm.children[0].material.color.setHex(RC[P.rc|0]);pm.children[6].material.color.setHex(c.rb);pm.children[4].material.emissive.setHex(c.c);pm.children[5].color.setHex(c.c)}
function clUI(){$('cls').innerHTML=CL.map((c,i)=>`<button data-c="${i}" style="background:${i==P.cl?'#5a3fc0':'#3a3350'}">${c.n}</button>`).join(' ');$('cd').innerHTML=`<b>${CL[P.cl].n}</b> — ${CL[P.cl].s}<br>`+CL[P.cl].sp.map((s,k)=>`${k+1} ${s.k} ${s.n}`).join(' · ')}
$('cls').onclick=e=>{const c=e.target.dataset.c;if(c!=null){setCl(+c,1);clUI()}};
function mtype(k){return {w:{hp:40,sp:3.6,dm:8,cd:1.1,xp:25,gm:1,ag:14,sc:1,hv:0},l:{hp:55,sp:6.2,dm:10,cd:.9,xp:35,gm:1.3,ag:18,sc:1,hv:0},s:{hp:65,sp:3,dm:13,cd:1.3,xp:40,gm:1.5,ag:15,sc:1,hv:0},g:{hp:150,sp:2.6,dm:20,cd:1.8,xp:90,gm:3,ag:16,sc:2.2,hv:0},y:{hp:130,sp:2.8,dm:17,cd:1.6,xp:80,gm:2.5,ag:16,sc:2,hv:0},b:{hp:75,sp:5,dm:12,cd:1.1,xp:50,gm:2,ag:20,sc:1.2,hv:1.4}}[k]}
function pick(x,z){const r=Math.random();if(hyp(x-95,z-110)<30)return r<.8?'s':'w';if(hyp(x+95,z-105)<30)return r<.7?'g':'b';if(z>75&&Math.abs(x)<50)return r<.6?'y':'w';if(x>55&&z<-65)return r<.6?'g':(r<.8?'b':'w');if((x<-110&&z<-80)||(z<-68&&Math.abs(x)<22))return r<.7?'s':'w';if(hyp(x+100,z)<60)return r<.4?'b':(r<.7?'l':'w');if(x>28)return r<.45?'l':(r<.55?'s':'w');return 'w'}
function mk(k){const g=new T.Group(),p=(geo,c,x,y,z,e)=>{const m=new T.Mesh(geo,M(c,e));m.position.set(x,y,z);m.castShadow=true;g.add(m);return m},BX=T.BoxGeometry,S=T.SphereGeometry,C=T.ConeGeometry,Y=T.CylinderGeometry;
 if(k=='w'){p(new S(.8,10,8),0x3a2a55,0,1.1,0,0x1a0a30).scale.y=1.3;p(new C(.7,1.2,8),0x2a1a45,0,.5,0,0x120822).rotation.x=Math.PI;[-.25,.25].forEach(e=>p(new S(.1,6,6),0,e,1.4,-.7,0x33ff88));const a=p(new S(1.1,10,8),0x66ffcc,0,1.1,0,0x33aa88);a.material.transparent=true;a.material.opacity=.18;a.castShadow=false}
 else if(k=='l'){p(new BX(.7,.8,1.5),0x55555e,0,.9,0);p(new BX(.5,.5,.6),0x66666e,0,1.15,-.95);[-.18,.18].forEach(x=>p(new C(.12,.3,4),0x44444c,x,1.5,-.9));[[-.25,-.5],[.25,-.5],[-.25,.5],[.25,.5]].forEach(([x,z])=>p(new Y(.09,.07,.7,5),0x333338,x,.35,z));p(new BX(.15,.15,.7),0x55555e,0,1,.95).rotation.x=.5;[-.14,.14].forEach(x=>p(new S(.06,6,6),0,x,1.2,-1.22,0xff2200))}
 else if(k=='s'){p(new S(.3,8,8),0xe8e4d0,0,1.9,0);p(new BX(.5,.9,.25),0xd8d4c0,0,1.2,0);[-.4,.4].forEach(x=>{p(new Y(.06,.06,.8,5),0xd8d4c0,x,1.2,0);p(new Y(.07,.07,.8,5),0xd8d4c0,x*.45,.4,0)});[-.1,.1].forEach(x=>p(new S(.06,6,6),0,x,1.95,-.26,0x66ff66));p(new Y(.03,.03,1.4,5),0x6b4a2a,.55,1,-.3)}
 else if(k=='g'){p(new BX(1.5,1.6,1.1),0x3a2a28,0,1.6,0);p(new BX(.9,.9,.9),0x4a3430,0,2.8,0);[-1,1].forEach(s=>{p(new BX(.5,1.5,.5),0x3a2a28,s,1.7,0);p(new BX(.55,1.1,.55),0x3a2a28,s*.5,.55,0)});p(new BX(1,.25,.05),0xff6a20,0,1.7,-.58,0xff4400);p(new BX(.15,1,.05),0xff6a20,.3,1.5,-.58,0xff4400);[-.2,.2].forEach(x=>p(new S(.1,6,6),0,x,2.85,-.46,0xff8800))}
 else if(k=='y'){p(new BX(1.4,1.6,1),0xe6eef8,0,1.5,0);p(new S(.55,10,8),0xf2f6fb,0,2.7,0);[-1,1].forEach(s=>{p(new BX(.5,1.6,.5),0xdbe6f2,s*.95,1.4,0);p(new BX(.5,.9,.5),0xdbe6f2,s*.4,.45,0)});[-.2,.2].forEach(x=>p(new S(.08,6,6),0,x,2.75,-.5,0x33aaff))}
 else{p(new S(.6,8,8),0x6a6a74,0,1.6,0);p(new S(.35,8,8),0x7a7a84,0,2.3,-.2);[-1,1].forEach(s=>{p(new BX(1.4,.06,.8),0x555560,s*1.1,1.9,.2).rotation.z=s*.5;p(new C(.1,.4,4),0x444450,s*.2,2.7,-.2)});[-.12,.12].forEach(x=>p(new S(.05,6,6),0,x,2.35,-.5,0xff3333))}
 return g}
function mkBar(sc){const g=new T.Group(),a=new T.Mesh(new T.PlaneGeometry(1.4,.16),new T.MeshBasicMaterial({color:0x111111,transparent:true,opacity:.7})),b=new T.Mesh(new T.PlaneGeometry(1.4,.16).translate(.7,0,0),new T.MeshBasicMaterial({color:0xdd3333}));b.position.set(-.7,0,.01);g.add(a,b);g.scale.setScalar(sc*.9);g.visible=false;g.userData.f=b;scene.add(g);return g}
function spawn(x,z){const k=pick(x,z),t=mtype(k),g=mk(k);g.scale.setScalar(t.sc);g.position.set(x,t.hv,z);scene.add(g);mons.push({x,z,hx:x,hz:z,hp:t.hp,mhp:t.hp,g,cd:0,slow:0,dead:0,rs:0,w:rnd(0,6),k,sp:t.sp,dm:t.dm,ct:t.cd,xp:t.xp,gm:t.gm,ag:t.ag,r:1.8+(t.sc-1)*.8,hv:t.hv,hh:2.8*t.sc+t.hv,bar:mkBar(t.sc)})}
function monUpd(m,dt){
 if(m.boss&&!m.dead){bossAI(m,dt);return}if(m.dead){m.bar.visible=false;m.rs-=dt;if(m.rs<=0){m.dead=0;m.hp=m.mhp;m.x=m.hx;m.z=m.hz;m.g.visible=true}return}
 m.cd-=dt;m.slow-=dt;const dx=P.x-m.x,dz=P.z-m.z,d=hyp(dx,dz),sp=m.slow>0?m.sp*.35:m.sp;
 if(d<m.ag&&!P.dead&&!(P.ivs>0)&&hyp(m.x-m.hx,m.z-m.hz)<35){if(d>m.r){m.x+=dx/d*sp*dt;m.z+=dz/d*sp*dt}else if(m.cd<=0){m.cd=m.ct;hurtP(m.dm)}m.g.rotation.y=Math.atan2(-dx,-dz)}
 else{m.w+=dt;const hx=m.hx-m.x,hz=m.hz-m.z,hd=hyp(hx,hz);if(hd>6){m.x+=hx/hd*2*dt;m.z+=hz/hd*2*dt}else{m.x+=Math.cos(m.w)*dt;m.z+=Math.sin(m.w)*dt}}
 push(m,.7*m.g.scale.x);m.g.position.set(m.x,Math.sin(ck*3+m.w)*.15+m.hv,m.z);
 const b=m.bar;b.visible=m.hp<m.mhp&&d<35;if(b.visible){b.position.set(m.x,m.hh,m.z);b.quaternion.copy(cam.quaternion);b.userData.f.scale.x=Math.max(.01,m.hp/m.mhp)}}
function dress(g,beard){const rb=g.children[0].material,sk=g.children[1].material,ht=g.children[2].material,dk=M(0x111111);
 const add=(geo,mat,x,y,z,rz)=>{const m=new T.Mesh(geo,mat);m.position.set(x,y,z);if(rz)m.rotation.z=rz;m.castShadow=true;g.add(m)};
 add(new T.CylinderGeometry(.11,.09,.8,6),rb,-.45,1.1,0,-.3);add(new T.CylinderGeometry(.11,.09,.8,6),rb,.45,1.1,0,.3);add(new T.SphereGeometry(.1,6,6),sk,-.57,.7,0);add(new T.SphereGeometry(.1,6,6),sk,.57,.7,0);
 add(new T.SphereGeometry(.035,6,6),dk,-.09,1.85,-.25);add(new T.SphereGeometry(.035,6,6),dk,.09,1.85,-.25);add(new T.CylinderGeometry(.27,.27,.1,12),M(0x4a3020),0,.9,0);add(new T.CylinderGeometry(.5,.5,.04,16),ht,0,1.95,0);
 if(beard)add(new T.ConeGeometry(.2,.55,8).rotateX(Math.PI),M(0xeeeeee),0,1.5,-.12)}
const sunM=new T.Mesh(new T.SphereGeometry(12,16,12),new T.MeshBasicMaterial({color:0xfff2b0,fog:false})),moonM=new T.Mesh(new T.SphereGeometry(8,16,12),new T.MeshBasicMaterial({color:0xdfe8ff,fog:false}));scene.add(sunM,moonM);
const cld=[];for(let i=0;i<22;i++){const c=new T.Mesh(new T.SphereGeometry(rnd(8,16),8,6),new T.MeshBasicMaterial({color:0xffffff,transparent:true,opacity:.8,fog:false}));c.scale.y=.35;c.position.set(rnd(-220,220),rnd(70,100),rnd(-220,220));scene.add(c);cld.push(c)}
for(let i=0;i<80;i++){const x=rnd(-135,135),z=rnd(-135,135),r=rnd(.4,1.4);if(hyp(x,z)<22||hyp(x+100,z)<38||hyp(x-85,z+105)<40||hyp(x,z-105)<40||hyp(x+50,z-55)<28||hyp(x+78,z+95)<45||(z<-66&&Math.abs(x)<24))continue;mesh(new T.DodecahedronGeometry(r),0x777780,x,r*.4,z)}
// Crypte de Morvath et ruines d'Azhur
patch(30,0x1f2a22,95,110);for(let i=0;i<36;i++){const a=rnd(0,6.28),r=rnd(10,27),x=95+Math.cos(a)*r,z=110+Math.sin(a)*r;mesh(new T.BoxGeometry(.8,1.4,.3),0x777a82,x,.7,z);col.push({x,z,w:.4,d:.2})}
for(let i=0;i<12;i++){const a=i*.5236,x=95+Math.cos(a)*20,z=114+Math.sin(a)*20;if(z>136)continue;mesh(new T.CylinderGeometry(.8,.9,7,8),0x6a6e78,x,3.5,z);col.push({x,z,w:.8,d:.8})}
wall(18,8,0x555a66,95,132,10);mesh(new T.ConeGeometry(11,5,4),0x2a3a3a,95,12.5,132).rotation.y=Math.PI/4;[-8,8].forEach(x=>mesh(new T.SphereGeometry(.5,8,8),0x66ff99,95+x,3,127.5,0x33ff77));
patch(30,0x6a6a60,-95,105);for(let i=0;i<10;i++){const a=i*.628,x=-95+Math.cos(a)*18,z=105+Math.sin(a)*18,h=rnd(5,9);mesh(new T.BoxGeometry(2,h,2),0x8a8a82,x,h/2,z);col.push({x,z,w:1,d:1})}
for(let i=0;i<6;i++)spawn(95+rnd(-18,18),110+rnd(-18,18));for(let i=0;i<5;i++)spawn(-95+rnd(-18,18),105+rnd(-18,18));
function mkB(k){const g=new T.Group(),p=(geo,c,x,y,z,e)=>{const m=new T.Mesh(geo,M(c,e));m.position.set(x,y,z);m.castShadow=true;g.add(m);return m},S=T.SphereGeometry,C=T.ConeGeometry,BX=T.BoxGeometry,Y=T.CylinderGeometry;
 if(k=='m'){p(new C(1.3,3.2,10),0x1a1a2a,0,1.6,0,0x0a0a14);p(new S(.45,10,8),0xe8e4d0,0,3.5,0);[-.35,-.17,0,.17,.35].forEach(x=>p(new C(.09,.45,4),0xd8b040,x,4.05,0,0x442200));[-.15,.15].forEach(x=>p(new S(.07,6,6),0,x,3.55,-.4,0x66ff88));[-1,1].forEach(s=>p(new Y(.13,.1,1.3,6),0x1a1a2a,s*.9,2.4,0).rotation.z=s*.5);p(new Y(.06,.06,3.4,6),0x3a2a1a,1.3,1.7,-.3);p(new S(.3,8,8),0x66ff88,1.3,3.5,-.3,0x33ff77)}
 else{p(new BX(2,2.6,1.6),0x7a7a72,0,2.8,0);p(new BX(1.3,1.2,1.3),0x8a8a82,0,4.7,0);[-1,1].forEach(s=>{p(new BX(.9,2.6,.9),0x727268,s*1.6,2.9,0);p(new BX(1,2,1),0x727268,s*.6,1,0);p(new BX(1.1,1.1,1.1),0x6a6a60,s*1.6,1.1,-.4)});[-.3,.3].forEach(x=>p(new S(.12,6,6),0,x,4.75,-.68,0x44aaff));p(new BX(1.2,.2,.05),0x44aaff,0,2.9,-.82,0x2288ff)}
 return g}
const BD=[{nm:'Morvath, roi nécromancien',k:'m',hp:900,sp:3.5,sc:1.6,cx:95,cz:114,x:95,z:118,rr:11,xp:900,col:0x66ff88,item:`Fragment du Cœur d'Eldoria #2`},{nm:'Azhur, colosse de pierre',k:'a',hp:1200,sp:2.8,sc:1.8,cx:-95,cz:105,x:-95,z:105,rr:12,xp:1100,col:0xffaa44,item:`Fragment du Cœur d'Eldoria #3`}];
function mkBosses(){BD.forEach((b,i)=>{if(P.bk[i])return;const g=mkB(b.k);g.scale.setScalar(b.sc);g.position.set(b.x,0,b.z);scene.add(g);const ring=new T.Mesh(new T.RingGeometry(b.rr-.5,b.rr,48).rotateX(-Math.PI/2),new T.MeshBasicMaterial({color:b.col,transparent:true,opacity:.7,side:2}));ring.visible=false;scene.add(ring);mons.push({boss:1,bi:i,nm:b.nm,k:b.k,x:b.x,z:b.z,hx:b.x,hz:b.z,hp:b.hp,mhp:b.hp,g,t:3,s:'i',cd:0,slow:0,dead:0,rs:0,w:0,xp:b.xp,gm:20,item:b.item,sp:b.sp,cx:b.cx,cz:b.cz,cr:16,rr:b.rr,ring,arm:0,hv:0,tx:0,tz:0,h:0,bar:{visible:false,userData:{}}})})}
function bshot(m,a,v,dm,col,sz){proj(m.x,2.5,m.z,Math.sin(a)*v,Math.cos(a)*v,dm,'e');const q=pr[pr.length-1];q.col=col;q.m.material.color.setHex(col);q.m.children[0].material.color.setHex(col);if(sz)q.m.scale.setScalar(sz)}
function bossDown(m){P.frag++;P.inv.push(m.item);P.gold+=200;P.sp+=2;P.bk[m.bi]=1;m.ring.visible=false;say(`${m.nm} est vaincu ! Fragment du Cœur obtenu (${P.frag}/7). +2 points de compétence.`,7000);save(1)}
function bossAI(m,dt){const dx=P.x-m.x,dz=P.z-m.z,d=hyp(dx,dz),act=d<42&&!P.dead;m.act=act;
 if(!act){m.s='i';m.ring.visible=false;m.arm=0;return}
 const f=m.hp<m.mhp/2?1.5:1;if(f>1&&!m.p2){m.p2=1;say(`${m.nm} entre en fureur !`,3000)}
 m.t-=dt;m.arm-=dt;m.g.rotation.y=Math.atan2(-dx,-dz);const mg=m.k=='m';
 if(m.s=='i'){const w=mg?13:7;if(d>w+2){m.x+=dx/d*m.sp*f*dt;m.z+=dz/d*m.sp*f*dt}else if(mg&&d<w-4){m.x-=dx/d*m.sp*dt;m.z-=dz/d*m.sp*dt}
  if(m.t<=0){const r=Math.random();
   if(mg){if(r<.4){const n=m.p2?5:3,a0=Math.atan2(dx,dz);for(let i=0;i<n;i++)bshot(m,a0+(i-(n-1)/2)*.25,14,13,0x66ff88);m.t=2.6/f}
    else if(r<.7){let c=0;for(const o of mons)if(!o.dead&&!o.boss&&hyp(o.x-m.x,o.z-m.z)<30)c++;if(c<5){for(let i=0;i<(m.p2?3:2);i++)spawn(m.x+rnd(-6,6),m.z+rnd(-6,6));say('Morvath relève des squelettes !',2000)}m.t=3/f}
    else{m.s='s';m.t=1.2;m.ring.visible=true;m.ring.position.set(m.x,.1,m.z)}}
   else{if(r<.35){const a0=Math.atan2(dx,dz),n=m.p2?3:1;for(let i=0;i<n;i++)bshot(m,a0+(i-(n-1)/2)*.3,13,22,0x998877,1.6);m.t=2.4/f}
    else if(r<.7){m.s='s';m.t=1.4;m.ring.visible=true;m.ring.position.set(m.x,.1,m.z)}
    else{m.s='c';m.t=.8;m.tx=dx/d;m.tz=dz/d;m.h=0}
    if(m.p2&&m.arm<=-6){m.arm=5;say('Azhur durcit sa pierre !',2000)}}}}
 else if(m.s=='s'){m.ring.material.opacity=.3+.4*Math.abs(Math.sin(m.t*12));if(m.t<=0){m.ring.visible=false;if(d<m.rr)hurtP(mg?25:32);m.s='i';m.t=2/f}}
 else{m.x+=m.tx*24*dt;m.z+=m.tz*24*dt;if(!m.h&&d<4.5){m.h=1;hurtP(28)}if(m.t<=0){m.s='i';m.t=2/f}}
 const ex=m.x-m.cx,ez=m.z-m.cz,er=hyp(ex,ez);if(er>m.cr){m.x=m.cx+ex/er*m.cr;m.z=m.cz+ez/er*m.cr}
 m.g.position.set(m.x,m.hv,m.z)}
function bar2(){const b=mons.find(m=>m.boss&&!m.dead&&m.act),e=$('boss2');if(!b){e.style.display='none';return}e.style.display='block';$('bn2').textContent=b.nm;$('bh2').style.width=Math.max(0,b.hp/b.mhp*100)+'%'}
// Quêtes
const HUB=[[4,7],[-98,8],[-82,-84],[78,-98],[6,103],[-44,50]],HN=['Village de Brumeval','Noctheris','Académie Arcanum','Terres du Brasier','Royaume des Glaces',`Château d'Arcanor`];let qh=0;
HUB.forEach(([x,z])=>{box(.2,2.6,.2,0x4a2e1a,x-1,1.3,z);box(.2,2.6,.2,0x4a2e1a,x+1,1.3,z);box(2.4,1.4,.15,0x8a6a3a,x,2,z);box(1.8,.9,.05,0xe8dcb0,x,2,z+.1,0x665533)});
const QD=[[0,'Chasseur de loups','k','l',5,80,120,0,1],[0,'Nettoyage du bois','k','*',8,120,200,1,3],[0,'Os et poussière','k','s',6,150,260,1,4],
[1,'Ailes de pierre','k','b',4,200,320,1,5],[1,'Chasse aux spectres','k','w',10,180,300,1,4],[1,'La crypte de Morvath','b','m',1,500,900,2,7],
[2,'Épreuve du bois sombre','k','s',6,220,380,1,5],[2,'Examen pratique','k','*',12,260,420,1,6],[2,`Le Colosse d'Azhur`,'b','a',1,600,1100,2,9],
[3,'Golems de lave','k','g',3,300,500,1,8],[3,'Forteresse assiégée','k','*',10,320,520,1,8],[3,'Le dragon de la forteresse','b','v',1,450,800,2,6],
[4,'Chasse aux yétis','k','y',4,300,520,1,8],[4,'Givre éternel','k','*',10,340,560,1,8],
[5,'Sécurité du royaume','k','*',15,400,650,1,10],[5,'Légende vivante','k','*',30,900,1500,3,12]],KN={l:'loups',s:'squelettes',w:'spectres',b:'gargouilles',g:'golems',y:'yétis'};
function qUI(){const L=QD.map((q,i)=>[q,i]).filter(([q])=>q[0]==qh);$('qd').innerHTML=`<b>Tableau de quêtes — ${HN[qh]}</b><hr>`+L.map(([q,i],k)=>{const s=P.qs[i]|0;return `${k+1} · <b>${q[1]}</b> (niv. ${q[8]}) — ${s==3?'✔ terminée':s==2?'à rendre !':s==1?(q[2]=='k'?`en cours ${P.qp[i]|0}/${q[4]}`:'en cours'):'à accepter'}<br><small>${q[2]=='k'?'Éliminer '+q[4]+' '+(q[3]=='*'?'monstres':KN[q[3]]):'Vaincre le boss'} · ${q[5]} or, ${q[6]} XP${q[7]?', +'+q[7]+' pt':''}</small>`}).join('<hr>')+`<hr><small>Touche du chiffre : accepter ou rendre · E pour fermer</small>`}
function qact(k){const i=QD.map((q,j)=>j).filter(j=>QD[j][0]==qh)[k];if(i==null)return;const q=QD[i],s=P.qs[i]|0;
 if(s==0){if(P.lv<q[8]){say(`Niveau ${q[8]} requis.`,1800);return}P.qs[i]=1;P.qp[i]=0;say(`Quête acceptée : ${q[1]}`,2200)}
 else if(s==2){P.qs[i]=3;P.gold+=q[5];P.sp+=q[7];gain(q[6]);say(`Quête terminée : ${q[1]} (+${q[5]} or, +${q[6]} XP)`,3500)}
 qUI()}
function qkill(k){QD.forEach((q,i)=>{if((P.qs[i]|0)!=1)return;if(q[2]=='k'&&(q[3]=='*'||q[3]==k)){P.qp[i]=(P.qp[i]|0)+1;if(P.qp[i]>=q[4]){P.qs[i]=2;say(`Quête prête : ${q[1]} — retourne au tableau !`,4000)}}else if(q[2]=='b'&&q[3]==k){P.qs[i]=2;say(`Quête prête : ${q[1]} — retourne au tableau !`,4000)}})}
function panel(k){pan=pan==k?0:k;inv=!!pan;['inv','tr','sh','qd'].forEach(i=>$(i).style.display='none');if(pan){$({i:'inv',t:'tr',s:'sh',q:'qd'}[pan]).style.display='block';({i:invUI,t:trUI,s:shUI,q:qUI})[pan]()}}
function invUI(){$('inv').innerHTML=`<b>Inventaire</b><br>Or : ${P.gold}<br>Potions de soin : ${P.pot} (touche H)<br>Fragments du Cœur : ${P.frag}/7<hr>${P.inv.length?P.inv.join('<br>'):'<i>Aucun objet pour l\'instant</i>'}<hr>Niveau ${P.lv} · ${P.mhp} PV · ${P.mmp} mana<br><small>I pour fermer · K pour sauvegarder</small>`}
function hud(){const Q=[`Parle à l'Ancien Aldric (E), au village.`,`Éliminer 5 spectres (${P.kills}/5)`,`Retourne voir Aldric (E).`,`Vaincre Varkhûn au donjon, au nord.`,`Fragment 1/7 obtenu ! Améliore tes sorts (T) et équipe-toi.`];
 $('lv').textContent=`Niveau ${P.lv} · Or ${P.gold} · Potions ${P.pot}`+(P.sh>0?` · 🛡${Math.ceil(P.sh)}`:'')+(P.sp?` · ✦ ${P.sp} pt (T)`:'');$('hp').style.width=Math.max(0,P.hp/P.mhp*100)+'%';$('mp').style.width=P.mp/P.mmp*100+'%';$('xp').style.width=P.xp/need()*100+'%';
 $('ex').textContent=place()+(HUB.some(h=>hyp(P.x-h[0],P.z-h[1])<3.2)?' · [E] Tableau de quêtes':hyp(P.x-6,P.z+4)<5?' · [E] Parler':hyp(P.x+5,P.z+3)<4||hyp(P.x+94,P.z-4)<4?' · [E] Boutique':hyp(P.x+100,P.z+14)<4||hyp(P.x+106,P.z-6)<4||hyp(P.x-85,P.z+97)<4||hyp(P.x,P.z-106)<4||hyp(P.x+75,P.z+86)<4||hyp(P.x+75,P.z+103)<3.5||hyp(P.x+112,P.z+73)<4||hyp(P.x+50,P.z-50)<4||(P.fl&&hyp(P.x+104,P.z+106)<4)?' · [E] Parler':hyp(P.x+88,P.z+16)<3.5||hyp(P.x+110,P.z+93)<3.5?' · Entre dans le portail':!P.ch&&hyp(P.x-70,P.z+15)<4?' · [E] Ouvrir':'');const Q2=[`Rends-toi à Noctheris, à l'ouest, et parle à la Guilde.`,`Éliminer 6 spectres cristallins (${P.k2}/6)`,`Retourne voir la capitaine Mara (E).`,`Va voir Sélène à l'Académie (E).`,`Noctheris : quête terminée. La crypte de Morvath t'attend, au sud-est.`];$('qb').innerHTML='<b>Quête</b><br>'+Q[P.quest]+(P.quest>=2?`<br><b>Noctheris</b><br>`+Q2[P.q2]:'');$('qb').innerHTML+=QD.map((q,i)=>(P.qs[i]|0)==1?`<br>• ${q[1]} ${q[2]=='k'?(P.qp[i]|0)+'/'+q[4]:''}`:(P.qs[i]|0)==2?`<br>✔ ${q[1]} (à rendre)`:'').join('');
 document.querySelectorAll('.sk').forEach((e,i)=>{e.lastChild.style.height=Math.min(100,Math.max(0,cds[i])/SP[i].cd*100)+'%';e.style.opacity=P.mp<SP[i].c?.5:1})}
// Entrées
addEventListener('keydown',e=>{K[e.code]=1;if(!run)return;const c=e.code;
 if(pan){if(/^Digit[1-4]$/.test(c)){if(pan=='t')upg(+c[5]-1);else if(pan=='s')buy(+c[5]-1);else if(pan=='q')qact(+c[5]-1)}else if(c=='KeyH')pot();else if(c=='KeyE'||(c=='KeyI'&&pan=='i')||(c=='KeyT'&&pan=='t'))panel(pan);return}
 if(c=='Digit1'||c=='Numpad1')cast(0);else if(c=='Digit2'||c=='Numpad2')cast(1);else if(c=='Digit3'||c=='Numpad3')cast(2);else if(c=='Digit4'||c=='Numpad4')cast(3);
 else if(c=='KeyE')act();else if(c=='KeyH')pot();else if(c=='KeyK')save();else if(c=='KeyI')panel('i');else if(c=='KeyT')panel('t')});
addEventListener('keyup',e=>K[e.code]=0);
addEventListener('mousemove',e=>{if(run&&(document.pointerLockElement||e.buttons&2)){P.yaw-=e.movementX*.0025;P.pitch=clamp(P.pitch+e.movementY*.0025,.05,1.2)}});
addEventListener('mousedown',e=>{if(run&&e.button==0)cast(0)});addEventListener('contextmenu',e=>e.preventDefault());
document.addEventListener('pointerlockchange',()=>{const l=!!document.pointerLockElement;if(wasLock&&!l){run=0;$('play').textContent='Reprendre';$('ov').style.display='flex'}wasLock=l});
$('play').onclick=()=>{run=1;$('ov').style.display='none';try{$('c').requestPointerLock()}catch(e){}};
$('new').onclick=()=>{try{localStorage.removeItem('eldoria1')}catch(e){}location.reload()};
// Initialisation
if(load())$('play').textContent='Continuer';
if(P.bd){B.dead=1;B.hp=0;B.g.visible=false}chest.visible=!P.ch;setCl(P.cl|0);clUI();mkBosses();[elder,merch,arc,sel,mar,kal,ysa,gar,orr,dir,gk,hm,pm].forEach(g=>dress(g,g==elder||g==gk||g==hm));
setInterval(()=>{if(run&&!inv)save(1)},30000);
// Boucle
let last=performance.now();
function step(dt){
 ck+=dt;const a=ck/90*Math.PI*2,day=Math.sin(a)*.5+.5;sun.position.set(P.x+Math.cos(a)*60,Math.abs(Math.sin(a))*60+15+P.y,P.z+30);sun.target.position.set(P.x,P.y,P.z);sun.intensity=.2+day*.9;amb.intensity=.25+day*.4;cs.copy(cN).lerp(cD,day);cs.multiplyScalar(1-.35*wf);scene.fog.color.copy(cs);stars.material.opacity=clamp(1.2-day*2,0,1);stars.position.set(P.x,0,P.z);sunM.position.set(P.x+Math.cos(a)*220,Math.sin(a)*220,P.z-60);moonM.position.set(P.x-Math.cos(a)*220,-Math.sin(a)*220,P.z-60);cld.forEach(c=>{c.position.x+=dt*3;if(c.position.x>230)c.position.x=-230;c.material.color.setScalar(.2+day*.8)});tl.forEach((l,i)=>l.intensity=1.1+Math.sin(ck*9+i*2)*.25);
 const kd=(a,b)=>K[a]||K[b],mx=(kd('KeyD','ArrowRight')?1:0)-(kd('KeyA','ArrowLeft')?1:0),mz=(kd('KeyW','ArrowUp')?1:0)-(kd('KeyS','ArrowDown')?1:0);
 P.mv=!!(mx||mz);if(mx||mz){const sp=(K.ShiftLeft?10:6)*dt/hyp(mx,mz),fx_=-Math.sin(P.yaw),fz=-Math.cos(P.yaw),rx=Math.cos(P.yaw),rz=-Math.sin(P.yaw);P.x+=(fx_*mz+rx*mx)*sp;P.z+=(fz*mz+rz*mx)*sp}
 P.x=clamp(P.x,-140,140);P.z=clamp(P.z,-140,140);push(P,.6);if(P.fl){const dx=P.x+110,dz=P.z+105,dd=hyp(dx,dz);if(dd>14.5){P.x=-110+dx/dd*14.5;P.z=-105+dz/dd*14.5}}P.y=P.fl?30:0;
 P.mp=Math.min(P.mmp,P.mp+(4+P.lv*.3)*dt);if(!P.dead&&hyp(P.x,P.z)<14)P.hp=Math.min(P.mhp,P.hp+5*dt);
 for(let i=0;i<4;i++)cds[i]-=dt;P.ivs-=dt;P.sht-=dt;if(P.sht<=0)P.sh=0;P.pc-=dt;warp();
 mons.forEach(m=>monUpd(m,dt));bossUpd(dt);bar2();prUpd(dt);fxUpd(dt);
 wetT-=dt;if(wetT<=0){wet=!wet;wetT=rnd(50,100);if(wet)say('La pluie se met à tomber…',2500)}wf+=((wet?1:0)-wf)*dt*.5;rn.visible=wf>.05;rn.material.opacity=.6*wf;scene.fog.near=50-30*wf;scene.fog.far=210-90*wf;
 if(rn.visible){const ar=rn.geometry.attributes.position.array;for(let i=1;i<ar.length;i+=3){ar[i]-=dt*28;if(ar[i]<-15)ar[i]+=30}rn.geometry.attributes.position.needsUpdate=true;rn.position.set(P.x,15,P.z)}
 hudT-=dt;if(hudT<=0){hudT=.12;hud()}}
function loop(now){requestAnimationFrame(loop);const dt=Math.min((now-last)/1000,.05);last=now;if(run&&!inv)step(dt);
 sw.forEach(d=>d.rotation.z=now/400);cand.forEach((m,i)=>m.position.y=m.userData.y+Math.sin(now/600+i)*.3);pm.position.set(P.x,P.y,P.z);pm.rotation.y=P.yaw;if(P.mv)pm.position.y+=Math.abs(Math.sin(now/110))*.1;pm.rotation.z=P.mv?Math.sin(now/110)*.05:0;pm.visible=!P.dead&&(P.ivs<=0||Math.sin(now/80)>0);elder.rotation.y=Math.atan2(-(P.x-6),-(P.z+4));merch.rotation.y=Math.atan2(-(P.x+5),-(P.z+3));crys.rotation.y=now/1000;crys.position.y=32.5+Math.sin(now/700)*.5;[[arc,-94,4],[sel,-100,-14],[mar,-106,6],[kal,85,-97],[ysa,0,106],[gar,-104,-106],[orr,-50,50],[dir,-75,-86],[gk,-112,-73],[hm,-75,-103]].forEach(([n,x,z])=>n.rotation.y=Math.atan2(-(P.x-x),-(P.z-z)));
 camD+=(((P.x>-90&&P.x<-60&&P.z<-95&&P.z>-110)?3.2:8)-camD)*.08;const d=camD,cp=Math.cos(P.pitch);cam.position.set(P.x+Math.sin(P.yaw)*d*cp,P.y+2+Math.sin(P.pitch)*d,P.z+Math.cos(P.yaw)*d*cp);cam.lookAt(P.x,P.y+2,P.z);ren.render(scene,cam)}
hud();requestAnimationFrame(loop);
</script></body></html>
ding eldoria_code_source.html…]()

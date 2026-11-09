// 直笛守護者：雲端同步（Netlify Functions + Netlify Blobs）
import { getStore } from "@netlify/blobs";
const H={"content-type":"application/json; charset=utf-8","cache-control":"no-store","access-control-allow-origin":"*","access-control-allow-methods":"GET,PUT,POST,OPTIONS","access-control-allow-headers":"content-type"};
const okCode=c=>typeof c==="string"&&/^[A-Za-z0-9_\-\u4e00-\u9fff]{4,40}$/.test(c);
const okPart=p=>typeof p==="string"&&/^(cfg|cls\/[A-Za-z0-9_\-]{1,40})$/.test(p);
// 同一班兩台平板同時上傳時：星星、最高分取較高，完成的任務合併，經驗值取較高
const P=x=>{try{return JSON.parse(x)}catch(e){return null}};
function mergeCls(o,n){const r=Object.assign({},o,n);
  for(const k of ["rgStars","rgBest"]){const a=P(o[k])||{},b=P(n[k])||{},m=Object.assign({},a,b);for(const x of Object.keys(a))m[x]=Math.max(+a[x]||0,+b[x]||0);r[k]=JSON.stringify(m)}
  const qa=P(o.rgMQ),qb=P(n.rgMQ);if(qa||qb)r.rgMQ=JSON.stringify(Object.assign({},qa,qb,{m:[...new Set([...(qa&&qa.m||[]),...(qb&&qb.m||[])])],s:[...new Set([...(qa&&qa.s||[]),...(qb&&qb.s||[])])]}));
  const pa=P(o.rgProf),pb=P(n.rgProf);if(pa&&pb&&(+pa.exp||0)>(+pb.exp||0)){pb.exp=pa.exp;r.rgProf=JSON.stringify(pb)}
  return r}
const J=(o,s)=>new Response(JSON.stringify(o),{status:s||200,headers:H});
export default async (req)=>{
  if(req.method==="OPTIONS")return new Response(null,{status:204,headers:H});
  const store=getStore({name:"rg-sync",consistency:"strong"});
  const url=new URL(req.url);
  if(req.method==="GET"){
    const code=url.searchParams.get("code");if(!okCode(code))return J({error:"code"},400);
    const {blobs}=await store.list({prefix:code+"/"});const out={cfg:null,cls:{},now:Date.now()};
    await Promise.all(blobs.map(async b=>{const v=await store.get(b.key,{type:"json"});const k=b.key.slice(code.length+1);if(!v)return;if(k==="cfg")out.cfg=v;else if(k.startsWith("cls/"))out.cls[k.slice(4)]=v}));
    return J(out)}
  if(req.method==="PUT"||req.method==="POST"){
    let b;try{b=await req.json()}catch(e){return J({error:"json"},400)}
    if(!okCode(b.code)||!okPart(b.part)||typeof b.d!=="object"||!b.d)return J({error:"bad"},400);
    const body=JSON.stringify(b.d);if(body.length>4e6)return J({error:"too big"},413);
    let d=b.d;
    if(b.part.startsWith("cls/")){const old=await store.get(b.code+"/"+b.part,{type:"json"});if(old&&old.d)d=mergeCls(old.d,d)}
    const t=Date.now();await store.setJSON(b.code+"/"+b.part,{t,d});return J({t,d})}
  return J({error:"method"},405)};
export const config={path:"/api/sync"};

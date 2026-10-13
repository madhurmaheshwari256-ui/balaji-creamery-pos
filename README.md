
# balaji-creamery-pos
<!doctype html><html><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Balaji Creamery POS v5</title>

<style>
*{box-sizing:border-box}body{margin:0;font:14px Arial;background:#f7f4f0;color:#29211f}header{background:#7b1e2b;color:#fff;padding:12px 18px;display:flex;justify-content:space-between;align-items:center}.brand{display:flex;align-items:center;gap:10px}.logo{width:44px;height:44px;border-radius:50%;background:#fff;color:#7b1e2b;display:grid;place-items:center;font-weight:900}.app{max-width:1450px;margin:auto;padding:15px}.tabs{display:flex;gap:7px;flex-wrap:wrap;margin-bottom:12px}button{border:0;border-radius:8px;padding:9px 12px;font-weight:700;cursor:pointer}.primary{background:#7b1e2b;color:#fff}.gold{background:#d9a441}.light{background:#eee7e1}.danger{background:#b42318;color:#fff}.panel{background:#fff;border-radius:14px;padding:15px;box-shadow:0 2px 10px #0001}.grid{display:grid;grid-template-columns:1fr 390px;gap:15px}.items{display:grid;grid-template-columns:repeat(auto-fill,minmax(155px,1fr));gap:8px}.item{background:#fff8f4;text-align:left;border:1px solid #eee1d9;min-height:75px}.item span{display:block;color:#7b1e2b;font-weight:bold;margin-top:5px}input,select{width:100%;padding:10px;border:1px solid #ddd;border-radius:8px}.row{display:flex;gap:7px}.row>*{flex:1}.cats{display:flex;gap:6px;overflow:auto;margin:8px 0}.cartrow{display:grid;grid-template-columns:1fr 90px 25px;gap:5px;padding:8px 0;border-bottom:1px solid #eee}.qty{display:flex;gap:4px}.line{display:flex;justify-content:space-between;margin:7px 0}.total{font-size:21px;font-weight:900}.hidden{display:none!important}.stats{display:grid;grid-template-columns:repeat(4,1fr);gap:9px}.stat{background:#fff8f4;padding:13px;border-radius:9px}.stat b{display:block;font-size:20px;margin-top:4px}table{width:100%;border-collapse:collapse}td,th{padding:8px;border-bottom:1px solid #eee;text-align:left;font-size:13px}.login{min-height:100vh;display:grid;place-items:center}.loginbox{background:#fff;padding:25px;border-radius:15px;width:min(420px,92%);box-shadow:0 5px 25px #0002}.badge{padding:4px 8px;border-radius:20px;background:#e7f5e8;color:#176b2c;font-size:11px}.warn{background:#fff4d6;color:#7a4f00;padding:10px;border-radius:8px;font-size:12px}.ok{background:#e7f5e8;color:#176b2c;padding:10px;border-radius:8px;font-size:12px}.modal{position:fixed;inset:0;background:#0008;display:grid;place-items:center}.modal>div{background:#fff;padding:20px;border-radius:14px;width:min(450px,92%)}@media(max-width:850px){.grid{grid-template-columns:1fr}.stats{grid-template-columns:repeat(2,1fr)}}
</style></head><body>
<div id="login" class="login"><div class="loginbox"><div class="logo" style="margin:auto">BC</div><h2 style="text-align:center;color:#7b1e2b">Balaji Creamery</h2><p style="text-align:center">Google Sheets POS</p><div id="cfgMsg" class="warn">Enter the Google Apps Script Web App URL and shared shop access code. Follow README_SETUP.txt to deploy the backend.</div><br><input id="gsUrl" placeholder="Google Apps Script Web App URL ending in /exec"><br><br><input id="gsToken" type="password" placeholder="Shared shop access code"><br><br><button class="light" style="width:100%" onclick="saveConfig()">Save Connection</button><hr><button class="primary" style="width:100%" onclick="login()">Connect & Open POS</button><p style="font-size:11px;color:#777">Use the same Web App URL and access code on every device. Sales are saved locally when offline and sync when connected.</p></div></div>
<div id="app" class="hidden"><header><div class="brand"><div class="logo">BC</div><div><b>Balaji Creamery</b><br><small id="session"></small></div></div><div><span id="syncBadge" class="badge">● Cloud</span> <button class="gold" onclick="syncNow()">Sync</button> <button class="gold" onclick="logout()">Logout</button></div></header>
<div class="app"><div class="tabs"><button class="primary" onclick="show('bill',this)">Billing</button><button onclick="show('dashboard',this)">Dashboard</button><button onclick="show('customers',this)">Customers</button><button onclick="show('invoices',this)">Invoices</button><button onclick="show('inventory',this)">Inventory</button></div>
<section id="bill"><div class="grid"><div class="panel"><input id="search" placeholder="Search menu..." oninput="menu()"><div id="cats" class="cats"></div><div id="items" class="items"></div></div>
<div class="panel"><h3>Current Bill</h3><div class="row"><input id="mob" maxlength="10" placeholder="Customer mobile" oninput="lookup()"><button class="light" onclick="addCustomer()">Add</button></div><input id="cname" placeholder="Customer name" style="margin-top:7px"><p id="loyal" style="font-size:12px;color:#777"></p><div id="cart"></div><hr><div class="line"><span>Subtotal</span><b id="sub">₹0</b></div><div class="row"><input id="disc" type="number" value="0" min="0" oninput="renderCart()"><select id="pay"><option>Cash</option><option>UPI</option><option>Card</option><option>Other</option></select></div><div class="line"><span>Loyalty earned</span><b id="pts">0</b></div><div class="line total"><span>Total</span><b id="tot">₹0</b></div><button class="primary" style="width:100%" onclick="saveAndPrint()">Save & Print Invoice</button><button class="gold" style="width:100%;margin-top:7px" onclick="saveAndWhatsAppPDF()">Save & Send PDF on WhatsApp</button><button class="light" style="width:100%;margin-top:7px" onclick="saveAndWhatsAppText()">Save & Send Text on WhatsApp</button><button class="light" style="width:100%;margin-top:7px" onclick="clearBill()">Clear</button></div></div></section>
<section id="dashboard" class="panel hidden"><h2>Sales Dashboard</h2><div class="row"><select id="period" onchange="dashboard()"><option value="today">Today</option><option value="7">Last 7 Days</option><option value="30">Last 30 Days</option><option value="all">All Time</option></select><button class="light" onclick="dashboard()">Refresh</button></div><br><div id="stats" class="stats"></div><h3>Items Sold</h3><div id="topitems"></div><h3>Recent Sales</h3><div id="recent"></div></section>
<section id="customers" class="panel hidden"><h2>Customers</h2><input id="cq" placeholder="Search mobile/name" oninput="customers()"><div id="ct"></div></section>
<section id="invoices" class="panel hidden"><h2>Invoices</h2><input id="iq" placeholder="Search invoice/customer" oninput="invoices()"><div id="it"></div></section>
<section id="inventory" class="panel hidden"><h2>Inventory</h2><p class="warn">Stock is optional. Set opening stock below; every saved invoice deducts the quantity sold. This is shared across devices.</p><div id="invtable"></div></section>
</div></div>
<div id="modal" class="modal hidden"><div><h3>Add Customer</h3><input id="am" placeholder="Mobile"><br><br><input id="an" placeholder="Name"><br><br><div class="row"><button class="light" onclick="closeM()">Cancel</button><button class="primary" onclick="saveC()">Save</button></div></div></div>
<script>
const M=[['Scoops','Vanilla Single',30],['Scoops','Vanilla Double',50],['Scoops','Chocolate Single',30],['Scoops','Chocolate Double',50],['Scoops','Butterscotch Single',40],['Scoops','Butterscotch Double',70],['Scoops','Mango Single',40],['Scoops','Mango Double',70],['Scoops','Tutti Fruiti Single',40],['Scoops','Tutti Fruiti Double',70],['Scoops','Kesar Pista Single',50],['Scoops','Kesar Pista Double',90],['Scoops','Paan Kulfi Single',50],['Scoops','Paan Kulfi Double',90],['Scoops','American Nuts Single',50],['Signature Sundaes','Choco Overload',90],['Signature Sundaes','Malwa Mithaas',110],['Signature Sundaes','Rainbow Magic',130],['Signature Sundaes','Royal Rajbhog Delight',130],['Signature Sundaes','American Crunch Sundae',130],['Signature Sundaes','Dry Fruit Anjeer Blast',140],['Signature Sundaes','Kulfi Falooda Special',150],['Classic Shakes','Classic Shake Small 250ml',60],['Classic Shakes','Classic Shake Medium 350ml',70],['Classic Shakes','Classic Shake Large 500ml',90],['Premium Shakes','Premium Shake Small 250ml',70],['Premium Shakes','Premium Shake Medium 350ml',90],['Premium Shakes','Premium Shake Large 500ml',110],['Extras','Add Scoop',20],['Winter Special','Snowman in a Hot Tub',110],['Winter Special','Hot Chocolate Oreo Blast',140],['Winter Special','Lava Lake Adventure',160]];
const money=n=>'₹'+Number(n||0).toLocaleString('en-IN');let gsUrl='',gsToken='',S=null,C=[],cat='All',syncing=false,opening=false;
let D={customers:{},invoices:[],inventory:{},queue:[]};
function loadLocal(){try{D=JSON.parse(localStorage.bc_gs_pos||'null')||D}catch(e){}}
function saveLocal(){localStorage.bc_gs_pos=JSON.stringify(D)}
loadLocal();
function setSync(t,ok=true){
 let b=document.getElementById('syncBadge'); if(!b)return;
 b.textContent=t;b.className='badge';b.style.background=ok?'#e7f5e8':'#fff0ef';b.style.color=ok?'#176b2c':'#b42318';
}
function configLoad(){
 try{let c=JSON.parse(localStorage.bc_gs_cfg||'null');if(c){gsUrl=c.url||'';gsToken=c.token||'';gsUrlInput.value=gsUrl;gsTokenInput.value=gsToken;cfgMsg.className='ok';cfgMsg.textContent='Connection details saved on this device.'}}catch(e){}
}
const gsUrlInput=document.getElementById('gsUrl'),gsTokenInput=document.getElementById('gsToken');
function saveConfig(){
 gsUrl=gsUrlInput.value.trim();gsToken=gsTokenInput.value.trim();
 if(!/^https:\/\/script\.google\.com\/macros\/s\/[^/]+\/exec\/?$/.test(gsUrl))return alert('Paste the deployed Google Apps Script Web App URL ending in /exec.');
 if(!gsToken)return alert('Enter the shared shop access code you set in Apps Script Project Settings.');
 localStorage.bc_gs_cfg=JSON.stringify({url:gsUrl,token:gsToken});
 cfgMsg.className='ok';cfgMsg.textContent='Connection details saved on this device.';
}
function jsonp(action,extra={}){
 return new Promise((resolve,reject)=>{
  if(!gsUrl||!gsToken)return reject(new Error('Google Sheets connection is not configured.'));
  const cb='bc_cb_'+Date.now()+'_'+Math.floor(Math.random()*100000);
  const s=document.createElement('script');let done=false;
  const cleanup=()=>{if(done)return;done=true;clearTimeout(timer);delete window[cb];s.remove()};
  const timer=setTimeout(()=>{cleanup();reject(new Error('No response from Google Apps Script. Check deployment access, URL and internet.'))},12000);
  window[cb]=(data)=>{cleanup();if(!data||data.ok===false)reject(new Error(data?.error||'Google Sheets request failed'));else resolve(data)};
  const params=new URLSearchParams({action,token:gsToken,callback:cb,...extra});
  s.src=gsUrl+'?'+params.toString();s.onerror=()=>{cleanup();reject(new Error('Could not reach Google Apps Script.'))};
  document.body.appendChild(s);
 });
}
async function login(){
 try{
  if(!gsUrl||!gsToken)saveConfig();
  if(!gsUrl||!gsToken)return;
  const r=await jsonp('ping');
  if(!r.ok)throw new Error(r.error||'Connection check failed');
  S={email:'Balaji Creamery shared login'};
  await openApp();
 }catch(e){setSync('Connection failed',false);alert('Could not connect to Google Sheets.\\n\\n'+e.message+'\\n\\nFollow README_SETUP.txt and confirm the Web App is deployed for access by anyone with the link.')}
}
async function openApp(){
 if(opening)return;opening=true;
 document.getElementById('login').classList.add('hidden');document.getElementById('app').classList.remove('hidden');
 document.getElementById('session').textContent='Shared shop access';
 menu();renderCart();
 try{await syncNow()}finally{dashboard();opening=false}
}
function logout(){S=null;document.getElementById('app').classList.add('hidden');document.getElementById('login').classList.remove('hidden')}
async function postEvent(item){
 // Apps Script form POST avoids cross-origin JSON preflight. Polling JSONP confirms whether the event was committed.
 const form=document.createElement('form');form.method='POST';form.action=gsUrl;form.target='bc_post_frame';form.style.display='none';
 const add=(name,value)=>{const i=document.createElement('input');i.name=name;i.value=value;form.appendChild(i)};
 add('token',gsToken);add('payload',JSON.stringify(item));
 let frame=document.getElementById('bc_post_frame');
 if(!frame){frame=document.createElement('iframe');frame.name='bc_post_frame';frame.id='bc_post_frame';frame.style.display='none';document.body.appendChild(frame)}
 document.body.appendChild(form);form.submit();form.remove();
 for(let n=0;n<12;n++){
  await new Promise(r=>setTimeout(r,700));
  const st=await jsonp('status',{eventId:item.id});
  if(st.status==='done')return true;
  if(st.status==='error')throw new Error(st.message||'Backend rejected event');
 }
 throw new Error('Write was sent but not confirmed yet. It will remain queued and retry on next sync.');
}
async function syncNow(){
 if(syncing)return;if(!gsUrl||!gsToken){setSync('Cloud not configured',false);return}
 if(!navigator.onLine){setSync('Offline • '+D.queue.length+' pending',false);return}
 syncing=true;setSync('Syncing…',true);
 try{
  for(const item of [...D.queue]){
   await postEvent(item);
   D.queue=D.queue.filter(x=>x.id!==item.id);saveLocal();
  }
  const cloud=await jsonp('bootstrap');
  // Merge cloud rows with any still-pending local events. Cloud is the shared source of truth after each confirmed sync.
  D.invoices=cloud.invoices||[];
  D.customers=Object.fromEntries((cloud.customers||[]).map(x=>[String(x.mobile),{name:x.name||''}]));
  D.inventory=Object.fromEntries((cloud.products||[]).map(x=>[x.name,Number(x.stock||0)]));
  for(const item of D.queue){
   if(item.table==='invoices'&&!D.invoices.some(x=>x.id===item.row.id))D.invoices.unshift(item.row);
   if(item.table==='customers')D.customers[String(item.row.mobile)]={name:item.row.name||''};
   if(item.table==='products')D.inventory[item.row.name]=Number(item.row.stock||0);
  }
  saveLocal();setSync(D.queue.length?'Pending sync: '+D.queue.length:'Sheets synced • '+new Date().toLocaleTimeString('en-IN'),D.queue.length===0);
  dashboard();customers();invoices();
 }catch(e){console.error(e);setSync('Sync issue • '+e.message.slice(0,55),false);saveLocal()}
 finally{syncing=false}
}
function queue(table,row,conflict){
 const item={id:crypto.randomUUID(),table,row,conflict,created_at:new Date().toISOString()};
 D.queue.push(item);saveLocal();
 setSync(navigator.onLine?'Pending sync: '+D.queue.length:'Offline • '+D.queue.length+' pending',false);
 if(navigator.onLine)syncNow();
}
window.addEventListener('online',()=>syncNow());
window.addEventListener('offline',()=>setSync('Offline • '+D.queue.length+' pending',false));
setInterval(()=>{if(navigator.onLine&&S&&D.queue.length)syncNow()},15000);
configLoad();
function menu(){let q=(search.value||'').toLowerCase();cats.innerHTML=['All',...new Set(M.map(x=>x[0]))].map(c=>`<button class="${c===cat?'primary':'light'}" onclick="cat='${c}';menu()">${c}</button>`).join('');items.innerHTML=M.map((x,i)=>[x,i]).filter(([x])=>(cat==='All'||x[0]===cat)&&x[1].toLowerCase().includes(q)).map(([x,i])=>`<button class=item onclick="add(${i})"><b>${x[1]}</b><span>${money(x[2])}</span></button>`).join('')}
function add(i){let x=C.find(a=>a.i===i);x?x.q++:C.push({i,q:1});renderCart()}
function renderCart(){let s=C.reduce((a,x)=>a+M[x.i][2]*x.q,0),d=+disc.value||0,t=Math.max(0,s-d);cart.innerHTML=C.length?C.map((x,k)=>`<div class=cartrow><div>${M[x.i][1]}<br><small>${money(M[x.i][2])} × ${x.q}</small></div><div class=qty><button class=light onclick="chg(${k},-1)">−</button>${x.q}<button class=light onclick="chg(${k},1)">+</button></div><button class=danger onclick="C.splice(${k},1);renderCart()">×</button></div>`).join(''):'<p style="color:#777">No items.</p>';sub.textContent=money(s);tot.textContent=money(t);pts.textContent=Math.floor(t/20)}
function chg(k,d){C[k].q+=d;if(C[k].q<1)C.splice(k,1);renderCart()} function clearBill(){C=[];disc.value=0;mob.value='';cname.value='';loyal.textContent='';renderCart()}
async function lookup(){let c=D.customers[mob.value];cname.value=c?.name||'';if(c){let p=D.invoices.filter(i=>i.mobile===mob.value).reduce((a,i)=>a+Math.floor(i.total/20),0);let v=D.invoices.filter(i=>i.mobile===mob.value).length;let t=D.invoices.filter(i=>i.mobile===mob.value).reduce((a,i)=>a+i.total,0);loyal.textContent=`Points: ${p} • Visits: ${v} • Spend: ${money(t)}`}else loyal.textContent=''}
function addCustomer(){am.value=mob.value;an.value=cname.value;modal.classList.remove('hidden')}function closeM(){modal.classList.add('hidden')}
function saveC(){let m=am.value.trim(),n=an.value.trim();if(!/^\d{10}$/.test(m)||!n)return alert('Enter valid mobile/name');D.customers[m]={name:n};queue('customers',{mobile:m,name:n},'mobile');mob.value=m;cname.value=n;lookup();closeM()}
function makeInvoice(){if(!C.length)return alert('Add items first'),null;let m=mob.value.trim(),n=cname.value.trim();if(m&&!/^\d{10}$/.test(m))return alert('Invalid mobile'),null;let sub=C.reduce((a,x)=>a+M[x.i][2]*x.q,0),d=+disc.value||0,t=Math.max(0,sub-d),p=Math.floor(t/20);let i={id:crypto.randomUUID(),invoice_no:'BC-'+new Date().getFullYear()+'-'+Date.now().toString().slice(-7),created_at:new Date().toISOString(),mobile:m||null,customer_name:n||D.customers[m]?.name||'Walk-in',items:C.map(x=>({name:M[x.i][1],price:M[x.i][2],qty:x.q})),subtotal:sub,discount:d,total:t,payment_mode:pay.value,loyalty_points:p};if(m&&!D.customers[m]){D.customers[m]={name:n||'Customer'};queue('customers',{mobile:m,name:n||'Customer'},'mobile')}D.invoices.unshift(i);queue('invoices',i,'id');saveLocal();return i}
function invoiceText(i){return `*BALAJI CREAMERY* 🍦\nInvoice: ${i.invoice_no}\nDate: ${new Date(i.created_at).toLocaleString('en-IN')}\nCustomer: ${i.customer_name}${i.mobile?` (${i.mobile})`:''}\n\n`+i.items.map(x=>`• ${x.name} × ${x.qty} = ${money(x.price*x.qty)}`).join('\n')+`\n\nSubtotal: ${money(i.subtotal)}\nDiscount: ${money(i.discount)}\n*TOTAL: ${money(i.total)}*\nPayment: ${i.payment_mode}\n🎁 Loyalty earned: ${i.loyalty_points} points\n\nThank you for visiting Balaji Creamery!`}
function saveAndPrint(){let i=makeInvoice();if(!i)return;let w=window.open('','_blank');if(w){w.document.write(`<html><body style="font-family:Arial;width:72mm;margin:auto"><h2 style="text-align:center;color:#7b1e2b">Balaji Creamery</h2><p>Invoice: ${i.invoice_no}<br>${new Date(i.created_at).toLocaleString('en-IN')}</p><hr>${i.items.map(x=>`<p>${x.name} × ${x.qty} — ${money(x.price*x.qty)}</p>`).join('')}<hr><p>Subtotal: ${money(i.subtotal)}<br>Discount: ${money(i.discount)}<br><b>Total: ${money(i.total)}</b><br>Payment: ${i.payment_mode}<br>Loyalty earned: ${i.loyalty_points}</p><p style="text-align:center">Thank you for visiting!</p><script>window.print();<\/script></body></html>`);w.document.close()}clearBill()}
function makePdf(i){let e=s=>String(s??'').replace(/\\/g,'\\\\').replace(/\(/g,'\\(').replace(/\)/g,'\\)').replace(/\r?\n/g,' '),o=[],t=(x,y,n,v,b=false)=>o.push(`BT /${b?'F2':'F1'} ${n} Tf ${x} ${y} Td (${e(v)}) Tj ET`);o.push('0.48 0.12 0.17 rg 0 755 595 87 re f','1 1 1 rg');t(38,805,23,'Balaji Creamery',true);o.push('0 0 0 rg');t(38,730,15,'SALES INVOICE',true);t(38,710,10,`Invoice: ${i.invoice_no}`);t(38,694,10,`Date: ${new Date(i.created_at).toLocaleString('en-IN')}`);t(340,710,10,`Customer: ${i.customer_name}`);t(340,694,10,`Mobile: ${i.mobile||'-'}`);o.push('38 675 m 557 675 l S');let y=655;i.items.forEach(x=>{t(40,y,9,x.name);t(395,y,9,String(x.qty));t(455,y,9,money(x.price));t(510,y,9,money(x.price*x.qty));y-=19});t(390,y-12,10,'Subtotal');t(510,y-12,10,money(i.subtotal));t(390,y-30,10,'Discount');t(510,y-30,10,'-'+money(i.discount));t(390,y-58,12,'TOTAL',true);t(510,y-58,12,money(i.total),true);t(38,y-90,10,`Payment: ${i.payment_mode}`);t(38,y-108,10,`Loyalty earned: ${i.loyalty_points} points`);t(38,y-145,10,'Thank you for visiting Balaji Creamery!');let c=o.join('\n'),a=['<< /Type /Catalog /Pages 2 0 R >>','<< /Type /Pages /Kids [3 0 R] /Count 1 >>','<< /Type /Page /Parent 2 0 R /MediaBox [0 0 595 842] /Resources << /Font << /F1 5 0 R /F2 6 0 R >> >> /Contents 4 0 R >>',`<< /Length ${c.length} >>\nstream\n${c}\nendstream`,'<< /Type /Font /Subtype /Type1 /BaseFont /Helvetica >>','<< /Type /Font /Subtype /Type1 /BaseFont /Helvetica-Bold >>'],p='%PDF-1.4\n',off=[0];a.forEach((v,n)=>{off[n+1]=p.length;p+=(n+1)+' 0 obj\n'+v+'\nendobj\n'});let x=p.length;p+='xref\n0 7\n0000000000 65535 f \n';for(let n=1;n<7;n++)p+=String(off[n]).padStart(10,'0')+' 00000 n \n';return new Blob([p+`trailer\n<< /Size 7 /Root 1 0 R >>\nstartxref\n${x}\n%%EOF`],{type:'application/pdf'})}
async function saveAndWhatsAppPDF(){let i=makeInvoice();if(!i)return;if(!i.mobile)return alert('Enter customer mobile number to send WhatsApp.');let f=new File([makePdf(i)],i.invoice_no+'.pdf',{type:'application/pdf'});try{if(navigator.share&&(!navigator.canShare||navigator.canShare({files:[f]})))await navigator.share({title:'Balaji Creamery Invoice '+i.invoice_no,files:[f]});else{let a=document.createElement('a');a.href=URL.createObjectURL(f);a.download=i.invoice_no+'.pdf';a.click();alert('PDF downloaded. Attach it in WhatsApp.')}}catch(e){if(e.name!=='AbortError')alert('PDF sharing was cancelled; the invoice remains saved.')}clearBill()}
function saveAndWhatsAppText(){let i=makeInvoice();if(!i)return;if(!i.mobile)return alert('Enter customer mobile number to send WhatsApp.');window.open('https://wa.me/91'+i.mobile+'?text='+encodeURIComponent(invoiceText(i)),'_blank');clearBill()}
function show(id,b){document.querySelectorAll('section').forEach(x=>x.classList.add('hidden'));document.getElementById(id).classList.remove('hidden');document.querySelectorAll('.tabs button').forEach(x=>x.classList.remove('primary'));b.classList.add('primary');if(id==='dashboard')dashboard();if(id==='customers')customers();if(id==='invoices')invoices();if(id==='inventory')inventory()}
function periodInvoices(){let p=period.value||'today',now=Date.now();if(p==='all')return D.invoices;if(p==='today'){let d=new Date().toDateString();return D.invoices.filter(x=>new Date(x.created_at).toDateString()===d)}let ms=+p*86400000;return D.invoices.filter(x=>now-new Date(x.created_at).getTime()<=ms)}
function dashboard(){let a=periodInvoices(),sales=a.reduce((z,x)=>z+x.total,0),qty=a.reduce((z,x)=>z+x.items.reduce((q,i)=>q+i.qty,0),0),cust=new Set(a.filter(x=>x.mobile).map(x=>x.mobile)).size;stats.innerHTML=`<div class=stat>Sales<b>${money(sales)}</b></div><div class=stat>Bills<b>${a.length}</b></div><div class=stat>Items Sold<b>${qty}</b></div><div class=stat>Customers<b>${cust}</b></div>`;let m={};a.forEach(x=>x.items.forEach(i=>{m[i.name]=(m[i.name]||0)+i.qty}));let rows=Object.entries(m).sort((a,b)=>b[1]-a[1]);topitems.innerHTML=`<table><tr><th>Item</th><th>Qty</th></tr>${rows.map(x=>`<tr><td>${x[0]}</td><td>${x[1]}</td></tr>`).join('')}</table>`;recent.innerHTML=`<table><tr><th>Invoice</th><th>Date</th><th>Customer</th><th>Total</th></tr>${a.slice(0,30).map(x=>`<tr><td>${x.invoice_no}</td><td>${new Date(x.created_at).toLocaleString('en-IN')}</td><td>${x.customer_name}</td><td>${money(x.total)}</td></tr>`).join('')}</table>`}
function customers(){let q=(cq.value||'').toLowerCase(),a=Object.entries(D.customers).filter(([m,c])=>(m+(c.name||'')).toLowerCase().includes(q));ct.innerHTML=`<table><tr><th>Mobile</th><th>Name</th><th>Points</th><th>Visits</th><th>Spend</th></tr>${a.map(([m,c])=>{let ii=D.invoices.filter(x=>x.mobile===m),pts=ii.reduce((z,x)=>z+x.loyalty_points,0),sp=ii.reduce((z,x)=>z+x.total,0);return `<tr><td>${m}</td><td>${c.name||''}</td><td>${pts}</td><td>${ii.length}</td><td>${money(sp)}</td></tr>`}).join('')}</table>`}
function invoices(){let q=(iq.value||'').toLowerCase(),a=D.invoices.filter(x=>(x.invoice_no+x.customer_name+(x.mobile||'')).toLowerCase().includes(q));it.innerHTML=`<table><tr><th>Invoice</th><th>Date</th><th>Customer</th><th>Total</th><th>Payment</th></tr>${a.map(x=>`<tr><td>${x.invoice_no}</td><td>${new Date(x.created_at).toLocaleString('en-IN')}</td><td>${x.customer_name}<br>${x.mobile||''}</td><td>${money(x.total)}</td><td>${x.payment_mode}</td></tr>`).join('')}</table>`}
async function inventory(){let rows=M.map(x=>x[1]).map(n=>[n,D.inventory[n]??0]);invtable.innerHTML=`<table><tr><th>Item</th><th>Stock</th><th>Save</th></tr>${rows.map(([n,s],i)=>`<tr><td>${n}</td><td><input id="stk${i}" type="number" min="0" value="${s}"></td><td><button class="primary" onclick="setStock(${i})">Save</button></td></tr>`).join('')}</table>`}
function setStock(i){let n=M[i][1],v=Math.max(0,+document.getElementById('stk'+i).value||0);D.inventory[n]=v;queue('products',{name:n,price:M[i][2],stock:v},'name');alert('Stock saved and queued for cloud sync.')}
menu();renderCart();
</script></body></html>

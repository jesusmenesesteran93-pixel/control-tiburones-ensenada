

<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#071426">
<title>Control de Academia | Tiburones Ensenada</title>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<style>
:root{--bg:#071426;--panel:#10243d;--muted:#a9bdd2;--blue:#168bff}
*{box-sizing:border-box}
body{margin:0;background:radial-gradient(circle at top,#183b60,#071426 60%);color:#fff;font-family:Arial,sans-serif;min-height:100vh}
.container{width:min(100% - 26px,900px);margin:auto;padding:20px 0 45px}
header{text-align:center;padding:15px 5px 22px}
.logo{width:105px;height:105px;border-radius:25px;display:grid;place-items:center;margin:auto auto 12px;background:linear-gradient(145deg,#168bff,#06366c);font-size:52px}
h1{font-size:clamp(22px,5vw,32px);margin:4px 0}
.subtitle{font-size:12px;color:var(--muted);letter-spacing:3px}
.panel{background:#10243df5;border:1px solid #29445f;border-radius:18px;padding:19px;margin-bottom:16px}
.login{max-width:430px;margin:10px auto}
label{display:block;color:var(--muted);font-size:13px;margin:13px 0 6px}
input,select{width:100%;padding:13px;border:1px solid #34516f;border-radius:11px;background:#091a2e;color:white}
.btn{border:0;border-radius:11px;padding:13px 16px;color:white;font-weight:bold;background:var(--blue);cursor:pointer}
.full{width:100%;margin-top:18px}
.gray{background:#344b65}
.message{font-size:13px;color:#ffd27b;margin-top:12px}
.hidden{display:none!important}
.userbar{display:flex;justify-content:space-between;align-items:center;gap:12px}
.modules{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px}
.module{min-height:120px;border:1px solid #ffffff20;border-radius:17px;padding:16px;color:white;text-align:left;cursor:pointer}
.module span{display:block;font-size:29px;margin-bottom:10px}
.module small{display:block;color:#ffffffbd;margin-top:6px}
.blue{background:linear-gradient(135deg,#0878e9,#06417f)}
.green{background:linear-gradient(135deg,#08ae91,#075c65)}
.purple{background:linear-gradient(135deg,#8950e9,#482c91)}
.gold{background:linear-gradient(135deg,#d99a18,#80500b)}
.pink{background:linear-gradient(135deg,#dc48a6,#812c82);grid-column:1/-1}
.view{display:none}
.view.active{display:block}
.back{background:#344b65;margin-bottom:15px}
.notice,.item{background:#0a1b30;border:1px solid #294663;border-radius:12px;padding:13px;margin-top:10px}
.notice{color:var(--muted);font-size:13px;line-height:1.5}
.item small{display:block;color:var(--muted);margin-top:5px}
.empty{text-align:center;color:var(--muted);padding:20px}
.footer{text-align:center;color:#71869e;font-size:11px;margin-top:25px}
@media(max-width:380px){.modules{grid-template-columns:1fr}.pink{grid-column:auto}}
</style>
</head>
<body>
<main class="container">
<header>
<div class="logo">🦈</div>
<h1>CONTROL DE ACADEMIA</h1>
<div class="subtitle">TIBURONES ENSENADA</div>
</header>

<section id="login" class="panel login">
<h2 style="text-align:center">Acceso a la academia</h2>
<label>Correo electrónico</label>
<input id="email" type="email" autocomplete="username" placeholder="Tu correo">
<label>Contraseña</label>
<input id="password" type="password" autocomplete="current-password" placeholder="Tu contraseña">
<button class="btn full" id="loginBtn" onclick="login()">ENTRAR</button>
<div id="loginMessage" class="message"></div>
</section>

<section id="app" class="hidden">
<div class="panel userbar">
<div>
<strong id="welcome">¡Bienvenido!</strong>
<div id="role" class="subtitle"></div>
</div>
<button class="btn gray" onclick="logout()">Salir</button>
</div>

<section id="menu">
<h2>MENÚ PRINCIPAL</h2>
<div class="modules">
<button class="module blue" onclick="openView('students')"><span>👥</span><b>ALUMNOS</b><small>Familias, alumnos y grupos</small></button>
<button class="module green" onclick="openView('payments')"><span>💳</span><b>PAGOS</b><small>Mensualidades y pedidos</small></button>
<button class="module purple" onclick="openView('training')"><span>🏀</span><b>ENTRENAMIENTOS</b><small>Asistencia y evaluaciones</small></button>
<button class="module gold" onclick="openView('mvp')"><span>🏆</span><b>CONTROL DEPORTIVO</b><small>Seguimiento y reconocimientos</small></button>
<button class="module pink" onclick="openView('stats')"><span>📊</span><b>ESTADÍSTICAS</b><small>Ingresos, gastos y resultados</small></button>
</div>
</section>

<section id="view-students" class="panel view">
<button class="btn back" onclick="backMenu()">← Regresar</button>
<h2>👥 Familias y alumnos</h2>
<div class="notice">Registra primero la familia y después agrega sus hijos. Cada alumno puede tener una cuota mensual diferente, incluso $0.</div>

<h3>Nueva familia</h3>
<label>Apellido o nombre de familia</label>
<input id="familyName" placeholder="Ej. Familia Pérez">
<label>Responsable</label>
<input id="responsible" placeholder="Nombre del responsable">
<label>Teléfono</label>
<input id="phone" placeholder="Teléfono (opcional)">
<button class="btn full" onclick="saveFamily()">GUARDAR FAMILIA</button>
<div id="familyMessage" class="message"></div>

<hr>
<h3>Agregar alumno</h3>
<label>Familia</label>
<select id="studentFamily"><option value="">Seleccionar familia</option></select>
<label>Nombre completo</label>
<input id="studentName" placeholder="Nombre completo">
<label>Grupo</label>
<select id="studentGroup">
<option value="">Seleccionar grupo</option>
<option>Inicial</option>
<option>Infantil</option>
<option>Juvenil</option>
<option>Matutino</option>
<option>4 a 5 años</option>
</select>
<label>Cuota mensual individual (MXN)</label>
<input id="studentFee" type="number" min="0" step="0.01" placeholder="Cuota acordada">
<button class="btn full" onclick="saveStudent()">GUARDAR ALUMNO</button>
<div id="studentMessage" class="message"></div>

<h3>Familias registradas</h3>
<div id="familiesList" class="empty">Cargando...</div>
<h3>Alumnos registrados</h3>
<div id="studentsList" class="empty">Cargando...</div>
</section>

<section id="view-payments" class="panel view">
<button class="btn back" onclick="backMenu()">← Regresar</button>
<h2>💳 Pagos</h2>
<div class="notice">Módulo pendiente de desarrollo: mensualidades, abonos, saldos y pedidos.</div>
</section>

<section id="view-training" class="panel view">
<button class="btn back" onclick="backMenu()">← Regresar</button>
<h2>🏀 Entrenamientos</h2>
<div class="notice">Módulo pendiente de desarrollo: sesiones, asistencia, observaciones y evaluaciones.</div>
</section>

<section id="view-mvp" class="panel view">
<button class="btn back" onclick="backMenu()">← Regresar</button>
<h2>🏆 Control deportivo</h2>
<div class="notice">Módulo pendiente de desarrollo: seguimiento mensual y reconocimientos.</div>
</section>

<section id="view-stats" class="panel view">
<button class="btn back" onclick="backMenu()">← Regresar</button>
<h2>📊 Estadísticas</h2>
<div class="notice">Módulo pendiente de desarrollo: ingresos, gastos, mensualidades y ventas.</div>
</section>
</section>

<footer class="footer">ACADEMIA TIBURONES ENSENADA · CONTROL ADMINISTRATIVO</footer>
</main>

<script>
// Solo clave publicable. Nunca usar una clave Secret o service_role en el navegador.
const SUPABASE_URL='https://wlgnstujytirsnefaryf.supabase.co';
const SUPABASE_ANON_KEY='sb_publishable_D0o1VUjkG_i067MZ__XaRA_X5EokdSF';

let db=null,currentUser=null,currentProfile=null;

function configureDatabase(){
if(!window.supabase){
document.getElementById('loginMessage').textContent='No se pudo cargar la biblioteca de conexión.';
return false;
}
db=window.supabase.createClient(SUPABASE_URL,SUPABASE_ANON_KEY);
return true;
}

async function login(){
const email=document.getElementById('email').value.trim();
const password=document.getElementById('password').value;
const btn=document.getElementById('loginBtn');
if(!email||!password){
document.getElementById('loginMessage').textContent='Escribe tu correo y contraseña.';
return;
}
if(!db&&!configureDatabase())return;
btn.disabled=true;
btn.textContent='VERIFICANDO...';
try{
const {data,error}=await db.auth.signInWithPassword({email,password});
if(error)throw error;
currentUser=data.user;
const result=await db.from('profiles').select('full_name,role,active').eq('id',currentUser.id).single();
if(result.error)throw result.error;
if(!result.data||!result.data.active)throw new Error('Cuenta inactiva o perfil no configurado.');
currentProfile=result.data;
showApp();
}catch(e){
document.getElementById('loginMessage').textContent=e.message||'No fue posible iniciar sesión.';
await db.auth.signOut();
}finally{
btn.disabled=false;
btn.textContent='ENTRAR';
}
}

function showApp(){
document.getElementById('login').classList.add('hidden');
document.getElementById('app').classList.remove('hidden');
document.getElementById('welcome').textContent='¡Bienvenido, '+currentProfile.full_name+'!';
const coach=currentProfile.role==='ADMIN_GENERAL';
document.getElementById('role').textContent=coach?'Administrador general':'Administración de pagos';
document.querySelectorAll('.module.purple,.module.gold,.module.pink').forEach(x=>x.classList.toggle('hidden',!coach));
loadFamilies();
loadStudents();
}

async function logout(){
if(db)await db.auth.signOut();
currentUser=null;
currentProfile=null;
document.getElementById('app').classList.add('hidden');
document.getElementById('login').classList.remove('hidden');
document.getElementById('password').value='';
}

function openView(n){
document.getElementById('menu').classList.add('hidden');
document.querySelectorAll('.view').forEach(x=>x.classList.remove('active'));
document.getElementById('view-'+n).classList.add('active');
}
function backMenu(){
document.querySelectorAll('.view').forEach(x=>x.classList.remove('active'));
document.getElementById('menu').classList.remove('hidden');
}
function msg(id,t){document.getElementById(id).textContent=t;}

async function saveFamily(){
if(currentProfile.role!=='ADMIN_GENERAL'){
msg('familyMessage','Solo el Coach puede registrar familias.');
return;
}
const family_name=document.getElementById('familyName').value.trim();
const responsible_name=document.getElementById('responsible').value.trim();
const phone=document.getElementById('phone').value.trim();
if(!family_name){msg('familyMessage','Escribe el nombre de la familia.');return;}
const {error}=await db.from('families').insert({family_name,responsible_name,phone});
if(error){msg('familyMessage','Error: '+error.message);return;}
['familyName','responsible','phone'].forEach(id=>document.getElementById(id).value='');
msg('familyMessage','Familia registrada correctamente.');
loadFamilies();
}

async function loadFamilies(){
if(!db)return;
const list=document.getElementById('familiesList');
const sel=document.getElementById('studentFamily');
const {data,error}=await db.from('families').select('id,family_name,responsible_name,phone').eq('active',true).order('family_name');
if(error){list.textContent='No se pudieron cargar las familias: '+error.message;return;}
sel.innerHTML='<option value="">Seleccionar familia</option>';
list.replaceChildren();
if(!data.length){list.textContent='Todavía no hay familias registradas.';return;}
data.forEach(f=>{
const o=document.createElement('option');
o.value=f.id;o.textContent=f.family_name;sel.appendChild(o);
const card=document.createElement('div');card.className='item';
const b=document.createElement('b');b.textContent=f.family_name;
const s=document.createElement('small');
s.textContent=(f.responsible_name||'Sin responsable')+' · '+(f.phone||'Sin teléfono');
card.append(b,s);list.appendChild(card);
});
}

async function saveStudent(){
if(currentProfile.role!=='ADMIN_GENERAL'){
msg('studentMessage','Solo el Coach puede registrar alumnos.');
return;
}
const family_id=document.getElementById('studentFamily').value;
const full_name=document.getElementById('studentName').value.trim();
const groupName=document.getElementById('studentGroup').value;
const feeText=document.getElementById('studentFee').value;
if(!family_id||!full_name||!groupName||feeText===''){
msg('studentMessage','Completa familia, nombre, grupo y cuota.');
return;
}
const monthly_fee=Number(feeText);
if(!Number.isFinite(monthly_fee)||monthly_fee<0){
msg('studentMessage','La cuota debe ser válida e igual o mayor a cero.');
return;
}
const g=await db.from('groups').select('id').eq('name',groupName).maybeSingle();
if(g.error||!g.data){msg('studentMessage','No se encontró el grupo configurado.');return;}
const {error}=await db.from('students').insert({
family_id,group_id:g.data.id,full_name,monthly_fee,
fee_effective_from:new Date().toISOString().slice(0,10)
});
if(error){msg('studentMessage','Error: '+error.message);return;}
document.getElementById('studentName').value='';
document.getElementById('studentFee').value='';
msg('studentMessage','Alumno registrado correctamente.');
loadStudents();
}

async function loadStudents(){
if(!db)return;
const list=document.getElementById('studentsList');
const {data,error}=await db.from('students').select('id,full_name,monthly_fee,status,families(family_name),groups(name)').order('full_name');
if(error){list.textContent='No se pudieron cargar los alumnos: '+error.message;return;}
list.replaceChildren();
if(!data.length){list.textContent='Todavía no hay alumnos registrados.';return;}
data.forEach(s=>{
const card=document.createElement('div');card.className='item';
const b=document.createElement('b');b.textContent=s.full_name;
const small=document.createElement('small');
small.textContent=(s.families?.family_name||'Familia')+' · '+(s.groups?.name||'Sin grupo')+' · Cuota: $'+Number(s.monthly_fee).toFixed(2)+' MXN';
card.append(b,small);list.appendChild(card);
});
}

async function restoreSession(){
if(!configureDatabase())return;
const {data}=await db.auth.getSession();
if(!data.session)return;
currentUser=data.session.user;
const {data:profile,error}=await db.from('profiles').select('full_name,role,active').eq('id',currentUser.id).single();
if(!error&&profile&&profile.active){
currentProfile=profile;
showApp();
}else{
await db.auth.signOut();
}
}
restoreSession();
</script>
</body>
</html>

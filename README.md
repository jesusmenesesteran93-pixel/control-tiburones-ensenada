
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Control de Academia Tiburones Ensenada</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;background:#07182d;color:white;
 font-family:Arial,sans-serif;min-height:100vh;
 display:flex;align-items:center;justify-content:center;
 padding:20px
}
.container{
 width:100%;max-width:430px;background:#102944;
 border:1px solid #24547c;border-radius:22px;
 padding:28px;box-shadow:0 15px 45px #0008
}
.logo{font-size:55px;text-align:center}
h1{text-align:center;font-size:23px;margin:8px 0}
.sub{text-align:center;color:#9fc6e8;margin-bottom:25px}
label{display:block;margin:15px 0 7px}
input{
 width:100%;padding:15px;border-radius:10px;
 border:1px solid #42627f;background:#07182d;
 color:white;font-size:16px
}
button{
 width:100%;padding:15px;margin-top:20px;
 border:0;border-radius:10px;background:#087de0;
 color:white;font-size:16px;font-weight:bold
}
button:disabled{opacity:.5}
#mensaje{
 margin-top:15px;padding:12px;border-radius:9px;
 line-height:1.4;display:none;overflow-wrap:anywhere
}
.error{display:block!important;background:#54232b;color:#ffd6d6}
.ok{display:block!important;background:#174b3b;color:#d5ffeb}
.panel{display:none}
.modulo{
 background:#173b5d;padding:17px;border-radius:12px;
 margin-top:12px
}
small{color:#a9c7df}
</style>
</head>
<body>
<div class="container">
 <div class="logo">🦈</div>
 <h1>CONTROL DE ACADEMIA</h1>
 <div class="sub">TIBURONES ENSENADA</div>

 <section id="login">
  <label>Correo electrónico</label>
  <input id="correo" type="email" placeholder="Ingresa tu correo" autocomplete="username">

  <label>Contraseña</label>
  <input id="clave" type="password" placeholder="Ingresa tu contraseña" autocomplete="current-password">

  <button id="entrar">INICIAR SESIÓN</button>
  <div id="mensaje"></div>
 </section>

 <section id="panel" class="panel">
  <h2 id="bienvenida">Bienvenido</h2>
  <p id="rol"></p>
  <div class="modulo">🔵 <b>ALUMNOS</b><br><small>Consulta de alumnos y familias</small></div>
  <div class="modulo">🟢 <b>PAGOS</b><br><small>Control de pagos de la academia</small></div>
  <div class="modulo" id="deportivo">🟣 <b>ENTRENAMIENTOS</b></div>
  <div class="modulo" id="mvp">🏆 <b>CONTROL DEPORTIVO</b></div>
  <div class="modulo" id="estadisticas">📊 <b>ESTADÍSTICAS</b></div>
  <button id="salir">CERRAR SESIÓN</button>
 </section>
</div>

<script type="module">
import { createClient } from
'https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/+esm';

const SUPABASE_URL = 'https://wlgnstujytirsnefaryf.supabase.co';
const SUPABASE_KEY = 'sb_publishable_D0o1VUjkG_i067MZ__XaRA_X5EokdSF';

const supabase = createClient(SUPABASE_URL, SUPABASE_KEY);

const login = document.getElementById('login');
const panel = document.getElementById('panel');
const mensaje = document.getElementById('mensaje');
const entrar = document.getElementById('entrar');

function mostrarError(texto){
 mensaje.className='error';
 mensaje.textContent=texto;
}
function mostrarOk(texto){
 mensaje.className='ok';
 mensaje.textContent=texto;
}

async function abrirPanel(user){
 const {data:perfil,error} = await supabase
  .from('profiles')
  .select('full_name,role,active')
  .eq('id',user.id)
  .maybeSingle();

 if(error){
  mostrarError('No se pudo consultar tu perfil: '+error.message);
  return;
 }

 if(!perfil){
  mostrarError('Tu usuario inició sesión, pero no tiene perfil en la tabla profiles. Pide revisar tu registro.');
  return;
 }

 if(perfil.active === false){
  mostrarError('Tu cuenta está desactivada. Contacta al administrador.');
  return;
 }

 login.style.display='none';
 panel.style.display='block';
 document.getElementById('bienvenida').textContent=
  '¡Bienvenido, '+(perfil.full_name || 'Coach')+'!';
 document.getElementById('rol').textContent=
  'Acceso: '+perfil.role;

 if(perfil.role !== 'ADMIN_GENERAL'){
  document.getElementById('deportivo').style.display='none';
  document.getElementById('mvp').style.display='none';
  document.getElementById('estadisticas').style.display='none';
 }
}

entrar.addEventListener('click',async()=>{
 const email=document.getElementById('correo').value.trim();
 const password=document.getElementById('clave').value;

 mensaje.className='';
 mensaje.style.display='none';

 if(!email || !password){
  mostrarError('Escribe tu correo y contraseña.');
  return;
 }

 entrar.disabled=true;
 entrar.textContent='VERIFICANDO...';

 try{
  const {data,error}=await supabase.auth.signInWithPassword({
   email,
   password
  });

  if(error){
   mostrarError('No se pudo iniciar sesión: '+error.message);
  }else if(data.user){
   await abrirPanel(data.user);
  }
 }catch(e){
  mostrarError('Error de conexión: '+e.message);
 }finally{
  entrar.disabled=false;
  entrar.textContent='INICIAR SESIÓN';
 }
});

document.getElementById('salir').addEventListener('click',async()=>{
 await supabase.auth.signOut();
 panel.style.display='none';
 login.style.display='block';
 document.getElementById('clave').value='';
 mensaje.className='';
});

async function revisarSesion(){
 const {data:{session}}=await supabase.auth.getSession();
 if(session?.user) await abrirPanel(session.user);
}

revisarSesion();
</script>
</body>
</html>

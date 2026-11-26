# come-rico-sabe-que-comes
Especial periodístico sobre alimentación juvenil y oferta gastronómica en Bochalema y Cachipay, Cali.
// NavegaciÃ³n mÃ³vil y asistente Gemini
const menuButton=document.querySelector('.menu-toggle');
const nav=document.querySelector('.nav');
if(menuButton&&nav){menuButton.addEventListener('click',()=>{const open=nav.classList.toggle('open');menuButton.setAttribute('aria-expanded',String(open));});nav.querySelectorAll('a').forEach(a=>a.addEventListener('click',()=>{nav.classList.remove('open');menuButton.setAttribute('aria-expanded','false');}));}

// IMPORTANTE: pega aquÃ­ la URL pÃºblica de tu Cloudflare Worker cuando lo despliegues.
const GEMINI_CHAT_ENDPOINT = 'PEGA_AQUI_LA_URL_DE_TU_WORKER';
const launcher=document.getElementById('chatLauncher');
const panel=document.getElementById('chatPanel');
const closeChat=document.getElementById('chatClose');
const form=document.getElementById('chatForm');
const input=document.getElementById('chatInput');
const messages=document.getElementById('chatMessages');
const history=[];
function addMessage(text,who='bot',extra=''){const el=document.createElement('div');el.className=`chat-bubble ${who} ${extra}`.trim();el.textContent=text;messages.appendChild(el);messages.scrollTop=messages.scrollHeight;return el;}
function toggleChat(show){panel.hidden=!show;if(show)input.focus();}
launcher?.addEventListener('click',()=>toggleChat(true));
closeChat?.addEventListener('click',()=>toggleChat(false));
form?.addEventListener('submit',async(event)=>{event.preventDefault();const question=input.value.trim();if(!question)return;addMessage(question,'user');input.value='';if(!GEMINI_CHAT_ENDPOINT.startsWith('https://')){addMessage('El chat ya estÃ¡ instalado en la pÃ¡gina, pero falta conectarlo al servicio seguro de Gemini. Sigue la guÃ­a CONFIGURAR-GEMINI.md para activarlo.','bot','error');return;}const pending=addMessage('Estoy consultandoâ€¦','bot');const contents=[...history,{role:'user',parts:[{text:question}]}];try{const response=await fetch(GEMINI_CHAT_ENDPOINT,{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({contents})});const data=await response.json();if(!response.ok)throw new Error(data.error||'No se pudo conectar con Gemini.');const answer=data.reply||'No recibÃ­ una respuesta. Intenta de nuevo.';pending.textContent=answer;history.push({role:'user',parts:[{text:question}]},{role:'model',parts:[{text:answer}]});if(history.length>12)history.splice(0,2);}catch(error){pending.textContent='No pude conectarme en este momento. '+error.message;pending.classList.add('error');}});

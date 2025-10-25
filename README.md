<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>ZONARUN — Catálogo</title>
<style>
body {background:#0b0d10;color:#e7eef2;font-family:Arial,Helvetica,sans-serif;margin:0;padding:0;}
header {background:#111417;padding:1rem;display:flex;justify-content:space-between;align-items:center;}
.logo {font-weight:700;color:#ff6b00;font-size:1.5rem;}
.container {display:flex;flex-wrap:wrap;gap:1rem;padding:1rem;justify-content:center;}
.card {background:#1a1d22;border-radius:10px;padding:1rem;max-width:250px;text-align:center;box-shadow:0 0 8px rgba(0,0,0,0.3);}
.card img {width:100%;border-radius:10px;}
button {background:#ff6b00;border:none;padding:0.5rem 1rem;color:#fff;border-radius:6px;cursor:pointer;}
#adminForm {background:#111417;padding:1rem;border-radius:10px;margin:1rem auto;max-width:400px;}
input,textarea {width:100%;margin-bottom:0.5rem;padding:0.5rem;border:none;border-radius:6px;}
.cart {background:#1a1d22;padding:1rem;margin:1rem;border-radius:10px;}
</style>
</head>
<body>
<header>
  <div class="logo">ZONARUN</div>
  <button onclick="toggleAdmin()">➕ Añadir producto</button>
</header>
<section id="adminForm" style="display:none;">
  <h3>Agregar nuevo producto</h3>
  <input id="name" placeholder="Nombre del producto">
  <textarea id="desc" placeholder="Descripción"></textarea>
  <input id="price" placeholder="Precio" type="number">
  <input id="image" type="file" accept="image/*">
  <button onclick="addProduct()">Guardar</button>
</section>
<section class="container" id="catalog"></section>
<section class="cart">
  <h3>Carrito</h3>
  <ul id="cartList"></ul>
  <p><strong>Total:</strong> <span id="total">$0.00</span></p>
  <button onclick="checkout()">Finalizar compra por WhatsApp</button>
</section>
<script>
const WHATSAPP_NUMBER = "584126051158";
let products = JSON.parse(localStorage.getItem("zonarun_products") || "[]");
let cart = JSON.parse(localStorage.getItem("zonarun_cart") || "[]");

function saveProducts(){localStorage.setItem("zonarun_products",JSON.stringify(products));}
function saveCart(){localStorage.setItem("zonarun_cart",JSON.stringify(cart));}

function render(){
  const c = document.getElementById("catalog");
  c.innerHTML="";
  products.forEach((p,i)=>{
    const div=document.createElement("div");
    div.className="card";
    div.innerHTML=`<img src="${p.img||''}" alt=""><h4>${p.name}</h4><p>${p.desc}</p><strong>$${p.price}</strong><br><button onclick='addToCart(${i})'>Añadir</button>`;
    c.appendChild(div);
  });
  renderCart();
}
function renderCart(){
  const cl=document.getElementById("cartList");
  cl.innerHTML="";
  let total=0;
  cart.forEach((item,i)=>{
    const li=document.createElement("li");
    li.textContent=`${item.name} x${item.qty} - $${(item.price*item.qty).toFixed(2)}`;
    cl.appendChild(li);
    total+=item.price*item.qty;
  });
  document.getElementById("total").textContent="$"+total.toFixed(2);
}
function addToCart(i){
  const p=products[i];
  const found=cart.find(x=>x.name===p.name);
  if(found)found.qty++;else cart.push({...p,qty:1});
  saveCart();renderCart();
}
function addProduct(){
  const name=document.getElementById("name").value;
  const desc=document.getElementById("desc").value;
  const price=parseFloat(document.getElementById("price").value||0);
  const file=document.getElementById("image").files[0];
  if(!name)return alert("Falta nombre");
  if(file){
    const reader=new FileReader();
    reader.onload=e=>{products.push({name,desc,price,img:e.target.result});saveProducts();render();toggleAdmin();};
    reader.readAsDataURL(file);
  }else{
    products.push({name,desc,price,img:""});saveProducts();render();toggleAdmin();
  }
}
function toggleAdmin(){
  const f=document.getElementById("adminForm");
  f.style.display=f.style.display==="none"?"block":"none";
}
function checkout(){
  if(cart.length===0)return alert("Tu carrito está vacío");
  let msg="Hola, quiero comprar:%0A";
  let total=0;
  cart.forEach(p=>{msg+=`- ${p.name} x${p.qty}%0A`;total+=p.price*p.qty;});
  msg+=`Total: $${total.toFixed(2)}`;
  window.open(`https://wa.me/${WHATSAPP_NUMBER}?text=${msg}`,"_blank");
}
render();
</script>
</body>
</html>

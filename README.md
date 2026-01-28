# APLIKAI-JUAL-PAKAIAN
[Uploading aplikasi jual pakaian.html…]()
<script>
const adminPassword = "admin123"; // GANTI PASSWORD

function adminLogin(){
  let pass = prompt("Masukkan Password Admin:");
  if(pass === adminPassword){
    location.hash = "#admin";
    location.reload();
  }else{
    alert("Password salah!");
  }
}
</script>
<button onclick="adminLogin()">🔐 Admin Login</button>
<button onclick="exportExcel()">📊 Export Excel</button>
<script>
function exportExcel(){
  let data = JSON.parse(localStorage.getItem("laporan")) || [];
  let csv = "Produk,Ukuran,Harga\n";
  data.forEach(d=>{
    csv += `${d.produk},${d.ukuran},${d.harga}\n`;
  });

  let blob = new Blob([csv], { type: "text/csv" });
  let url = URL.createObjectURL(blob);
  let a = document.createElement("a");
  a.href = url;
  a.download = "laporan_order.csv";
  a.click();
}
</script>
<script>
if("Notification" in window){
  Notification.requestPermission();
}
</script>

<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>URBAN FASHION</title>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">

<style>
:root{
  --primary:#ff6a00;
  --dark:#111;
  --bg:#f4f4f4;
}
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:Poppins;background:var(--bg)}
.container{max-width:1200px;margin:auto;padding:14px}

/* HEADER */
header{
  background:linear-gradient(135deg,#000,#222);
  color:#fff;
  padding:14px;
  border-radius:16px;
  display:flex;
  justify-content:space-between;
  align-items:center;
  animation:slideDown .6s;
}
.brand{display:flex;gap:10px;align-items:center}
.brand img{width:46px;height:46px;border-radius:50%}
nav a{color:#fff;font-size:14px;margin:0 6px;text-decoration:none}

/* KATEGORI */
.category{
  display:grid;
  grid-template-columns:repeat(3,1fr);
  gap:10px;
  margin:16px 0;
}
.cat{
  background:#fff;
  border-radius:14px;
  padding:14px;
  text-align:center;
  font-size:14px;
  transition:.3s;
}
.cat:hover{transform:scale(1.08)}
.cat i{font-size:22px;color:var(--primary)}

/* PRODUK */
.products{
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:14px;
}
.product{
  background:#fff;
  border-radius:16px;
  padding:10px;
  animation:pop .4s;
}
.product img{
  width:100%;
  height:160px;
  object-fit:cover;
  border-radius:12px;
}
.product h4{font-size:15px;margin:6px 0}
.price{color:var(--primary);font-weight:700}
select{
  width:100%;
  padding:8px;
  margin:4px 0;
  border-radius:10px;
}
button{
  width:100%;
  height:44px;
  border:none;
  border-radius:12px;
  background:var(--dark);
  color:#fff;
  font-size:14px;
}

/* CART */
.cart{
  background:#fff;
  margin-top:16px;
  padding:14px;
  border-radius:16px;
}
.cart-item{display:flex;justify-content:space-between;font-size:14px}
.total{font-weight:700;margin-top:6px}

/* PAY */
.pay button{margin-top:8px}
.qris{background:#000}
.ewallet{background:var(--primary)}
.wa{background:#25D366}

/* ADMIN */
#admin{
  display:none;
  background:#fff;
  padding:16px;
  border-radius:16px;
  margin-top:20px;
}
table{width:100%;font-size:14px;border-collapse:collapse}
td,th{border-bottom:1px solid #ddd;padding:8px}

/* QRIS MODAL */
#qrisBox{
  position:fixed;
  inset:0;
  background:rgba(0,0,0,.6);
  display:none;
  align-items:center;
  justify-content:center;
}
#qrisBox img{
  width:260px;
  border-radius:16px;
  background:#fff;
  padding:12px;
}

/* STICKY WA */
.sticky-wa{
  position:fixed;
  bottom:12px;
  left:50%;
  transform:translateX(-50%);
  background:#25D366;
  color:#fff;
  padding:14px 26px;
  border-radius:30px;
  text-decoration:none;
  animation:bounce 1.5s infinite;
}

/* ANIMASI */
@keyframes slideDown{from{transform:translateY(-30px);opacity:0}}
@keyframes pop{from{transform:scale(.9);opacity:0}}
@keyframes bounce{
  0%,100%{transform:translate(-50%,0)}
  50%{transform:translate(-50%,-6px)}
}
</style>
</head>

<body>
<div class="container">

<header>
  <div class="brand">
    <img src="QRIS.png">
    <strong>THAMAM361 FASHION</strong>
  </div>
  <nav>
    <a>Home</a><a>Produk</a><a>Tentang</a><a>Kontak</a>
  </nav>
</header>

<div class="category">
  <div class="cat"><i class="fa fa-shirt"></i><br>Kaos</div>
  <div class="cat"><i class="fa fa-person-military-jacket"></i><br>Jaket</div>
  <div class="cat"><i class="fa fa-user"></i><br>Celana</div>
  <div class="cat"><i class="fa fa-hoodie"></i><br>Hoodie</div>
  <div class="cat"><i class="fa fa-person-dress"></i><br>Dress</div>
  <div class="cat"><i class="fa fa-gem"></i><br>Aksesoris</div>
</div>

<div class="products" id="products"></div>

<div class="cart">
  <h3>🛒 Keranjang</h3>
  <div class="cart-item"><span>Item</span><span id="qty">0</span></div>
  <div class="cart-item total"><span>Total</span><span id="total">Rp 0</span></div>

  <div class="pay">
    <button class="qris" onclick="showQRIS()">Bayar QRIS</button>
    <button class="ewallet">Dana / OVO</button>
    <button class="wa" onclick="checkout()">WhatsApp</button>
  </div>
</div>

<!-- ADMIN -->
<div id="admin">
  <h3>📈 Laporan Order</h3>
  <table>
    <thead><tr><th>Produk</th><th>Ukuran</th><th>Harga</th></tr></thead>
    <tbody id="report"></tbody>
  </table>
</div>

</div>

<!-- QRIS -->
<div id="qrisBox" onclick="this.style.display='none'">
  <img src="QRIS.png">
</div>

<a class="sticky-wa" href="https://wa.me/085123622237">💬 Chat WhatsApp</a>

<script>
const produk=[
 {nama:"Kaos Oversize",harga:75000,foto:"kaos.jpg",stok:{S:5,M:8,L:6,XL:4}},
 {nama:"Hoodie Street",harga:120000,foto:"hodi.jpg",stok:{S:5,M:8,L:6,XL:4}},
 {nama:"Jaket Denim",harga:170000,foto:"hodi.jpg",stok:{S:5,M:8,L:6,XL:4}},
 {nama:"Celana Panjang",harga:150000,foto:"celana panjang.jpg",stok:{S:5,M:8,L:6,XL:4}},
 {nama:"Celana Pendek",harga:80000,foto:"celana pendek.jpg",stok:{S:5,M:8,L:6,XL:4}},
 {nama:"Topi",harga:50000,foto:"topi.jpg",stok:{S:5,M:8,L:6,XL:4}},
];

let total=0,qty=0,laporan=JSON.parse(localStorage.getItem("laporan"))||[];

const list=document.getElementById("products");
produk.forEach(p=>{
  let opt="";
  for(let s in p.stok){opt+=`<option>${s} (${p.stok[s]})</option>`}
  list.innerHTML+=`
  <div class="product">
    <img src="${p.foto}">
    <h4>${p.nama}</h4>
    <div class="price">Rp ${p.harga.toLocaleString()}</div>
    <select id="${p.nama}">${opt}</select>
    <button onclick="add('${p.nama}',${p.harga})">Tambah</button>
  </div>`;
});

function add(n,h){
  let u=document.getElementById(n).value;
  qty++; total+=h;
  document.getElementById("qty").innerText=qty;
  document.getElementById("total").innerText="Rp "+total.toLocaleString();
  laporan.push({produk:n,ukuran:u,harga:h});
  localStorage.setItem("laporan",JSON.stringify(laporan));
}

function checkout(){
  window.open(`https://wa.me/085123622237?text=Order:${qty}%0ATotal:Rp ${total}`);
}

function showQRIS(){
  document.getElementById("qrisBox").style.display="flex";
}

/* ADMIN MODE */
if(location.hash=="#admin"){
  document.getElementById("admin").style.display="block";
  let r=document.getElementById("report");
  laporan.forEach(l=>{
    r.innerHTML+=`<tr><td>${l.produk}</td><td>${l.ukuran}</td><td>Rp ${l.harga}</td></tr>`;
  });
}
</script>

</body>
</html>

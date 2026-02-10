
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Shiva Fitness</title>

<style>
body{margin:0;font-family:Arial;background:#fff8d2;}
header{background:linear-gradient(45deg,#ffcc00,#ffdf4d);padding:25px;text-align:center;font-size:32px;font-weight:bold;}
nav{background:#ffcc00;padding:12px;display:flex;justify-content:center;gap:15px;flex-wrap:wrap;}
nav button{padding:10px 20px;background:#000;color:#ffcc00;border:none;border-radius:6px;cursor:pointer;font-weight:bold;}
section{padding:20px;display:none;}
.active{display:block;}
.card{background:white;padding:20px;border-radius:10px;margin-bottom:20px;box-shadow:0 0 10px #0003;}
table{width:100%;border-collapse:collapse;background:white;}
table th,table td{padding:10px;border:1px solid #ccc;text-align:center;}
table th{background:#ffcc00;}
input,select{padding:10px;width:95%;border-radius:6px;border:1px solid #aaa;margin-bottom:10px;}
.btn{padding:10px 15px;background:#ffcc00;border:none;font-weight:bold;border-radius:6px;}
.delete-btn{background:red;color:white;border:none;padding:6px 12px;border-radius:6px;cursor:pointer;}
#loginPage{display:flex;justify-content:center;align-items:center;height:100vh;background:#fff2a8;}
#loginBox{background:white;padding:25px;border-radius:10px;width:300px;text-align:center;}
</style>
</head>

<body>

<div id="loginPage">
  <div id="loginBox">
    <h2>Admin Login</h2>
    <input type="password" id="adminPass" placeholder="Enter Password">
    <button class="btn" onclick="login()">Login</button>
  </div>
</div>

<div id="app" style="display:none;">

<header>
  <img src="logo.png" height="60" style="vertical-align:middle;" alt='x'>
  SHIVA FITNESS
</header>

<nav>
  <button onclick="showPage('intro')">Gym Intro</button>
  <button onclick="showPage('members')">Members</button>
  <button onclick="showPage('media')">Media</button>
  <button onclick="showPage('settings')">Settings</button>
  <button onclick="logout()">Logout</button>
</nav>

<section id="intro" class="active">
  <div class="card">
    <h2>Welcome to Shiva Fitness Gym</h2>
    <p>Transform your fitness journey with strength and training.</p>
  </div>
</section>

<section id="members">
  <div class="card">
    <h2>Add Member</h2>
    <input id="name" placeholder="Member Name">
    <input id="email" placeholder="Email ID">
    <input id="phone" placeholder="WhatsApp Number">
    <input id="admission" type="date">
    <select id="plan">
      <option>Monthly</option>
      <option>3 Months</option>
      <option>6 Months</option>
      <option>1 Year</option>
    </select>
    <input id="fees" type="number" placeholder="Fees ₹">
    <button type="button" class="btn" onclick="addMember()">Add </button>
  </div>

  <div class="card">
    <h2>Members List</h2>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Name</th>
          <th>Expiry</th>
          <th>Plan</th>
          <th>Fees</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody id="memberTable"></tbody>
    </table>
  </div>
</section>

<section id="media">
  <div class="card">
    <h2>Photos</h2>
    <img src="https://i.ibb.co/bMzMvLQj/1770574994743.jpg" height="200">
  </div>
</section>

<section id="settings">
  <div class="card">
    <h2>Change Admin Password</h2>
    <input type="password" id="oldPass" placeholder="Old Password">
    <input type="password" id="newPass" placeholder="New Password">
    <input type="password" id="confirmPass" placeholder="Confirm Password">
    <button class="btn" onclick="changePassword()">Update</button>
  </div>
</section>

</div>

<script>
/* ===== CHANGE THESE LATER ===== */
const OWNER_EMAIL = "yourgymemail@gmail.com";
const OWNER_WHATSAPP = "919999999999";
/* ============================= */

let PASSWORD = localStorage.getItem("gymPassword") || "0908";
let members = JSON.parse(localStorage.getItem("members")) || [];
let id = members.length ? members[members.length-1].id + 1 : 1;

function login(){
  if(adminPass.value === PASSWORD){
    loginPage.style.display="none";
    app.style.display="block";
    loadMembers();
    checkExpiry();
  } else alert("Wrong Password");
}

function logout(){location.reload();}

function showPage(p){
  document.querySelectorAll("section").forEach(s=>s.classList.remove("active"));
  document.getElementById(p).classList.add("active");
}

function saveMembers(){
  localStorage.setItem("members", JSON.stringify(members));
}

function getExpiryDate(admission, plan){
  let d = new Date(admission);
  if(plan==="Monthly") d.setMonth(d.getMonth()+1);
  if(plan==="3 Months") d.setMonth(d.getMonth()+3);
  if(plan==="6 Months") d.setMonth(d.getMonth()+6);
  if(plan==="1 Year") d.setFullYear(d.getFullYear()+1);
  return d.toISOString().split("T")[0];
}

function addMember(){
  alert("Add button clicked"); // temporary test

  const nameVal = document.getElementById("name").value.trim();
  const emailVal = document.getElementById("email").value.trim();
  const phoneVal = document.getElementById("phone").value.trim();
  const feesVal = document.getElementById("fees").value.trim();
  const admissionVal = document.getElementById("admission").value;
  const planVal = document.getElementById("plan").value;

  if(!nameVal || !emailVal || !phoneVal || !feesVal || !admissionVal){
    alert("Fill all fields");
    return;
  }

  let m = {
    id,
    name: nameVal,
    email: emailVal,
    phone: phoneVal,
    plan: planVal,
    fees: feesVal,
    expiry: getExpiryDate(admissionVal, planVal),
    notified:false
  };

  members.push(m);
  id++;
  saveMembers();
  loadMembers();
  }

function deleteMember(i){
  members = members.filter(m=>m.id!==i);
  saveMembers();
  loadMembers();
}

function checkExpiry(){
  let today = new Date().toISOString().split("T")[0];
  members.forEach(m=>{
    if(m.expiry < today && !m.notified){
      let msg = `Hi ${m.name}, your Shiva Fitness subscription has expired.`;
      window.open(`https://wa.me/${m.phone}?text=${encodeURIComponent(msg)}`);
      window.open(`https://wa.me/${OWNER_WHATSAPP}?text=${encodeURIComponent("Expired member: "+m.name)}`);
      console.log("Email sent to:", m.email, OWNER_EMAIL);
      m.notified=true;
    }
  });
  saveMembers();
}

function changePassword(){
  if(oldPass.value!==PASSWORD){alert("Wrong password");return;}
  if(newPass.value!==confirmPass.value){alert("Mismatch");return;}
  PASSWORD=newPass.value;
  localStorage.setItem("gymPassword",PASSWORD);
  alert("Password Updated");
}
</script>

</body>
<footer style="text-align:center;padding:15px;background:#ffcc00;font-weight:bold;">
  © 2026 Shiva Fitness. All Rights Reserved.by <b>><i>ADITYA CHATURVEDI</i>></b>

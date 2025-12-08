<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Aditya Fitness Gym</title>
  <style>
    body { margin:0; font-family:Arial; background:#fff8d2; }
    header { background:linear-gradient(45deg,#ffcc00,#ffdf4d); padding:25px; text-align:center; font-size:32px; font-weight:bold; color:#000; }
    nav { background:#ffcc00; padding:12px; display:flex; justify-content:center; gap:20px; flex-wrap:wrap; }
    nav button { padding:10px 20px; background:#000; color:#ffcc00; border:none; border-radius:6px; cursor:pointer; font-weight:bold; }
    section { padding:20px; display:none; }
    .active { display:block; }
    .card { background:white; padding:20px; border-radius:10px; margin-bottom:20px; box-shadow:0 0 10px #0003; }
    table { width:100%; border-collapse:collapse; background:white; }
    table th, table td { padding:10px; border:1px solid #ccc; text-align:center; }
    table th { background:#ffcc00; font-weight:bold; }
    input, select { padding:10px; width:95%; border-radius:6px; border:1px solid #aaa; margin-bottom:10px; }
    .btn { padding:10px 15px; background:#ffcc00; border:none; cursor:pointer; font-weight:bold; border-radius:6px; }
    .delete-btn { background:red; color:white; border:none; padding:6px 12px; border-radius:6px; cursor:pointer; }
    #loginPage { display:flex; justify-content:center; align-items:center; height:100vh; background:#fff2a8; }
    #loginBox { background:white; padding:25px; border-radius:10px; width:300px; box-shadow:0 0 10px #0004; text-align:center; }
    @media(max-width:600px){ header{font-size:24px;} nav button{padding:8px 12px;} }
  </style>
</head>
<body>

<!-- LOGIN PAGE -->
<div id="loginPage">
  <div id="loginBox">
    <h2>Admin Login</h2>
    <input type="password" id="adminPass" placeholder="Enter Password" />
    <button class="btn" onclick="login()">Login</button>
  </div>
</div>

<!-- MAIN APP -->
<div id="app" style="display:none;">
<header>ADITYA FITNESS GYM</header>
<nav>
  <button onclick="showPage('intro')">Gym Intro</button>
  <button onclick="showPage('members')">Members</button>
  <button onclick="showPage('media')">Media</button>
  <button onclick="showPage('settings')">Settings</button>
  <button onclick="logout()">Logout</button>
</nav>

<section id="intro" class="active">
  <div class="card">
    <h2>Welcome to Aditya Fitness Gym</h2>
    <p>Transform your fitness journey with strength, cardio, training programs and premium equipment.</p>
  </div>
</section>

<section id="members">
  <div class="card">
    <h2>Add Member</h2>
    <input id="name" placeholder="Member Name" />
    <input id="admission" type="date" />
    <select id="plan">
      <option value="Monthly">Monthly</option>
      <option value="3 Months">3 Months</option>
      <option value="6 Months">6 Months</option>
      <option value="1 Year">1 Year</option>
    </select>
    <input id="fees" placeholder="Fees ₹" />
    <button class="btn" onclick="addMember()">Add</button>
  </div>

  <div class="card">
    <h2>Members List</h2>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Name</th>
          <th>Admission</th>
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
  <div class="card"><h2>Photos</h2><div style="height:150px;background:#ffe98a;border-radius:8px;"></div></div>
  <div class="card"><h2>Videos</h2><div style="height:150px;background:#ffe98a;border-radius:8px;"></div></div>
</section>

<!-- PASSWORD SETTINGS -->
<section id="settings">
  <div class="card">
    <h2>Change Admin Password</h2>

  <input type="password" id="oldPass" placeholder="Old Password" />
    <input type="password" id="newPass" placeholder="New Password" />
    <input type="password" id="confirmPass" placeholder="Confirm New Password" />

   <button class="btn" onclick="changePassword()">Update Password</button>
  </div>
</section>

</div>

<script>
let PASSWORD = localStorage.getItem("gymPassword") || "0908";
let id = 1;
let members = JSON.parse(localStorage.getItem("members")) || [];

function login(){
  let pass = document.getElementById('adminPass').value;
  if(pass === PASSWORD){
    document.getElementById('loginPage').style.display='none';
    document.getElementById('app').style.display='block';
    loadMembers();
  } else alert('Wrong Password');
}

function logout(){ location.reload(); }

function showPage(page){
  document.querySelectorAll('section').forEach(s=>s.classList.remove('active'));
  document.getElementById(page).classList.add('active');
}

function saveMembers(){ localStorage.setItem("members", JSON.stringify(members)); }

function loadMembers(){
  let table = document.getElementById('memberTable');
  table.innerHTML = "";
  members.forEach(m=>{ table.innerHTML += rowHTML(m); });
  if(members.length>0) id = members[members.length-1].id + 1;
}

function rowHTML(m){
  return `<tr>
    <td>${m.id}</td>
    <td>${m.name}</td>
    <td>${m.admission}</td>
    <td>${m.plan}</td>
    <td>₹${m.fees}</td>
    <td><button class='delete-btn' onclick='deleteMember(${m.id})'>Delete</button></td>
  </tr>`;
}

function addMember(){
  let name=document.getElementById('name').value;
  let admission=document.getElementById('admission').value;
  let plan=document.getElementById('plan').value;
  let fees=document.getElementById('fees').value;
  if(!name||!admission||!fees){ alert('Fill all fields'); return; }
  let m={ id, name, admission, plan, fees };
  members.push(m);
  saveMembers();
  loadMembers();
  id++;
  document.getElementById('name').value="";
  document.getElementById('fees').value="";
}

function deleteMember(mid){
  members = members.filter(m=>m.id !== mid);
  saveMembers();
  loadMembers();
}

// CHANGE PASSWORD
function changePassword(){
  let oldP = document.getElementById('oldPass').value;
  let newP = document.getElementById('newPass').value;
  let conP = document.getElementById('confirmPass').value;

  if(oldP !== PASSWORD){ alert('Old password is incorrect'); return; }
  if(newP.length < 4){ alert('Password must be at least 4 characters'); return; }
  if(newP !== conP){ alert('New passwords do not match'); return; }

  PASSWORD = newP;
  localStorage.setItem("gymPassword", newP);
  alert('Password updated successfully!');

  document.getElementById('oldPass').value="";
  document.getElementById('newPass').value="";
  document.getElementById('confirmPass').value="";
}
</script>

</body>
</html>

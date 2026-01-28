# python3
https://github.com/9672955-glitch/gamerai-plaza.git

<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   <title>GamerAI | Master Plaza</title>
   <script src="https://cdn.tailwindcss.com"></script>
   <style>
       body { background-color: #020617; color: white; font-family: 'Inter', sans-serif; }
       .glass { background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(16px); border: 1px solid rgba(255, 255, 255, 0.08); }
       .gold-star { color: #fbbf24; text-shadow: 0 0 8px rgba(251, 191, 36, 0.5); }
       .suggestion-card { border-left: 4px solid #334155; transition: all 0.3s; }
       .suggestion-gold { border-left: 4px solid #fbbf24; background: rgba(251, 191, 36, 0.05); }
       .status-bar { height: 4px; border-radius: 2px; transition: width 0.5s ease; }
       .hidden { display: none; }
   </style>
</head>
<body class="p-4">

   <div id="authOverlay" class="fixed inset-0 bg-black z-[100] flex items-center justify-center p-6">
       <div class="glass p-10 rounded-[2rem] w-full max-w-md border-cyan-500/20 border-2 text-center">
           <h1 class="text-4xl font-black text-white mb-2 tracking-tighter">GAMER<span class="text-cyan-400">AI</span></h1>
           <p class="text-[10px] text-slate-500 uppercase tracking-widest mb-6">Plaza Entry Terminal</p>
           <input id="authUser" type="text" placeholder="Username" class="w-full p-4 mb-4 rounded-xl bg-slate-900 border border-slate-800 text-white outline-none">
           <input id="authPass" type="password" placeholder="Password" class="w-full p-4 mb-6 rounded-xl bg-slate-900 border border-slate-800 text-white outline-none">
           <button onclick="handleAuth()" class="w-full bg-cyan-600 py-4 rounded-xl font-black uppercase hover:bg-cyan-500 transition">Enter Hub</button>
           <p id="authMsg" class="mt-4 text-red-500 text-xs"></p>
       </div>
   </div>

   <div id="mainContent" class="hidden max-w-6xl mx-auto pt-8">
       <header class="flex justify-between items-center mb-6">
           <h1 class="text-2xl font-black italic text-cyan-400">PLAZA OS</h1>
           <div class="flex gap-4 items-center">
               <button id="adminBtn" onclick="toggleAdmin()" class="hidden bg-yellow-500 text-black px-4 py-1 rounded-full text-[10px] font-black">SYSTEM CORE</button>
               <span id="userBadge" class="text-xs font-bold text-slate-400"></span>
           </div>
       </header>

       <section id="adminPanel" class="hidden glass border-2 border-yellow-500/50 rounded-3xl p-6 mb-8">
           <h2 class="text-yellow-500 font-bold uppercase italic mb-4">Master Console: Samry778</h2>
           <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
               <div class="bg-black/40 p-4 rounded-xl border border-white/5">
                   <h3 class="text-xs font-bold mb-3 text-slate-400 uppercase">Manage Users & Gold Stars</h3>
                   <div id="userLogs" class="space-y-2 max-h-48 overflow-y-auto pr-2"></div>
               </div>
               <div class="bg-black/40 p-4 rounded-xl border border-white/5">
                   <h3 class="text-xs font-bold mb-3 text-cyan-400 uppercase">Master "Remind Me" & Progress</h3>
                   <div id="remindMeList" class="text-[10px] space-y-3 max-h-48 overflow-y-auto pr-2"></div>
               </div>
           </div>
       </section>

       <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
           <div class="lg:col-span-2 space-y-8">
               <div class="glass p-6 rounded-[2rem] border border-white/5">
                   <h3 class="font-bold mb-4 text-sm uppercase text-slate-400">Developer Suggestion Box</h3>
                   <div class="flex gap-2">
                       <input id="sugInput" type="text" placeholder="I suggest adding..." class="flex-1 bg-slate-900 p-3 rounded-xl border border-slate-800 text-sm outline-none focus:border-cyan-500">
                       <button onclick="submitSuggestion()" class="bg-cyan-600 px-6 rounded-xl font-bold">Submit</button>
                   </div>
               </div>

               <div>
                   <h3 class="text-xs font-black uppercase text-slate-500 mb-4 tracking-widest">Public Progress Feed</h3>
                   <div id="suggestionFeed" class="space-y-4"></div>
               </div>
           </div>

           <div class="space-y-8">
               <div class="glass p-6 rounded-[2rem]">
                   <h3 class="font-bold mb-4 text-xs uppercase text-slate-400 tracking-widest">Global Chat</h3>
                   <div id="plazaChat" class="h-64 overflow-y-auto mb-4 space-y-2 text-xs pr-2"></div>
                   <input id="plazaInput" type="text" placeholder="Type..." class="w-full bg-slate-900 p-2 rounded-lg text-xs outline-none border border-slate-800">
               </div>
           </div>
       </div>
   </div>

   <script>
       let currentUser = null;
       let isAdmin = false;

       function handleAuth() {
           const user = document.getElementById('authUser').value.trim();
           const pass = document.getElementById('authPass').value;
           const adminPass = localStorage.getItem('adminPass') || '$aM';

           if (user === 'Samry778' && pass === adminPass) {
               isAdmin = true;
               login(user);
           } else if (user.length >= 3) {
               login(user);
           } else {
               document.getElementById('authMsg').innerText = "Enter a username with at least 3 characters.";
           }
       }

       function login(name) {
           currentUser = name;
           document.getElementById('authOverlay').classList.add('hidden');
           document.getElementById('mainContent').classList.remove('hidden');
           document.getElementById('userBadge').innerText = `@${name}`;

           let users = JSON.parse(localStorage.getItem('user_data') || '{}');
           if (!users[name]) users[name] = { friends: [], isMod: false };
           if (!users['Samry778']) users['Samry778'] = { friends: [], isMod: true };
           localStorage.setItem('user_data', JSON.stringify(users));

           if (isAdmin) document.getElementById('adminBtn').classList.remove('hidden');
           renderSuggestions();
           refreshAdmin();
       }

       function submitSuggestion() {
           const text = document.getElementById('sugInput').value.trim();
           if (!text) return;
           const sugs = JSON.parse(localStorage.getItem('site_suggestions') || '[]');
           sugs.push({
               user: currentUser || 'Anonymous',
               text: text,
               id: Date.now(),
               progress: 0,
               statusText: "Pending Review"
           });
           localStorage.setItem('site_suggestions', JSON.stringify(sugs));
           document.getElementById('sugInput').value = "";
           renderSuggestions();
       }

       function updateProgress(id) {
           let sugs = JSON.parse(localStorage.getItem('site_suggestions') || '[]');
           let s = sugs.find(x => x.id == id);
           if (!s) return;
           if (s.progress === 0) { s.progress = 50; s.statusText = "In Progress"; }
           else if (s.progress === 50) { s.progress = 100; s.statusText = "Finished!"; }
           else { s.progress = 0; s.statusText = "Pending Review"; }

           localStorage.setItem('site_suggestions', JSON.stringify(sugs));
           renderSuggestions();
       }

       function renderSuggestions() {
           const sugs = JSON.parse(localStorage.getItem('site_suggestions') || '[]');
           const users = JSON.parse(localStorage.getItem('user_data') || '{}');
           const feed = document.getElementById('suggestionFeed');

           const sorted = sugs.slice().sort((a, b) => {
               const aStar = users['Samry778']?.friends.includes(a.user);
               const bStar = users['Samry778']?.friends.includes(b.user);
               return (bStar === aStar) ? b.id - a.id : (bStar ? 1 : -1);
           });

           feed.innerHTML = sorted.map(s => {
               const isStar = users['Samry778']?.friends.includes(s.user);
               const barColor = s.progress === 100 ? 'bg-green-500' : (s.progress === 50 ? 'bg-cyan-500' : 'bg-slate-700');

               return `
                   <div class="glass p-4 rounded-2xl suggestion-card ${isStar ? 'suggestion-gold' : ''}">
                       <div class="flex justify-between items-center mb-2">
                           <span class="text-[9px] font-black uppercase ${isStar ? 'text-yellow-500' : 'text-slate-500'}">
                               ${s.user} ${isStar ? '★' : ''}
                           </span>
                           <span class="text-[8px] font-bold uppercase ${s.progress === 100 ? 'text-green-400' : 'text-slate-400'}">${s.statusText}</span>
                       </div>
                       <p class="text-sm mb-3">${s.text}</p>
                       <div class="w-full bg-black/50 rounded-full h-1">
                           <div class="status-bar ${barColor}" style="width: ${s.progress}%"></div>
                       </div>
                       ${isAdmin ? `<div class="mt-3 flex gap-2">
                           <button onclick="updateProgress(${s.id})" class="text-[8px] bg-cyan-900/50 p-1 rounded px-2">Update Stage</button>
                           <button onclick="deleteSug(${s.id})" class="text-[8px] bg-red-900/50 p-1 rounded px-2">Wipe</button>
                       </div>` : ''}
                   </div>`;
           }).join('');
       }

       function deleteSug(id) {
           let sugs = JSON.parse(localStorage.getItem('site_suggestions') || '[]');
           localStorage.setItem('site_suggestions', JSON.stringify(sugs.filter(s => s.id != id)));
           renderSuggestions();
       }

       function sendFriendRequest(target) {
           if (currentUser !== 'Samry778') return;
           let users = JSON.parse(localStorage.getItem('user_data') || '{}');
           if (!users[target]) users[target] = { friends: [], isMod: false };
           if (!users[target].friends.includes('Samry778')) {
               users[target].friends.push('Samry778');
               users['Samry778'].friends.push(target);
               localStorage.setItem('user_data', JSON.stringify(users));
               refreshAdmin();
               renderSuggestions();
           }
       }

       function refreshAdmin() {
           const users = JSON.parse(localStorage.getItem('user_data') || '{}');
           const log = document.getElementById('userLogs');
           log.innerHTML = Object.keys(users).map(u => {
               if (u === 'Samry778') return "";
               const isStar = users['Samry778']?.friends.includes(u);
               return `
                   <div class="bg-white/5 p-2 rounded flex justify-between items-center text-[10px]">
                       <span>${u} ${isStar ? '★' : ''}</span>
                       <button onclick="sendFriendRequest('${u}')" class="bg-slate-700 px-2 py-1 rounded">
                           ${isStar ? 'GOLD STAR' : '+ Add Star'}
                       </button>
                   </div>`;
           }).join('');
       }

       function toggleAdmin() { document.getElementById('adminPanel').classList.toggle('hidden'); }
   </script>
</body>
</html>

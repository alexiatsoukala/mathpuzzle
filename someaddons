(function(){
  // === 1. CYBERPUNK ENERGY RING ===
  const ringSize = 110;
  let energyRing = document.createElement('div');
  energyRing.innerHTML = `
    <svg width="${ringSize}" height="${ringSize}" style="position:absolute;top:-35px;left:-35px;pointer-events:none;z-index:10">
      <circle cx="${ringSize/2}" cy="${ringSize/2}" r="48" 
        stroke="url(#cyberGlow)" stroke-width="8" fill="none" stroke-dasharray="301" stroke-dashoffset="301" id="cybEnergy"/>
      <defs>
        <linearGradient id="cyberGlow" x1="0" y1="0" x2="1" y2="1">
          <stop offset="0%" stop-color="#39ff14"/>
          <stop offset="50%" stop-color="#00eaff"/>
          <stop offset="100%" stop-color="#ff00cc"/>
        </linearGradient>
      </defs>
    </svg>
  `;
  energyRing.style.position = "absolute";
  energyRing.style.left = "40%";
  energyRing.style.top = "1.3em";
  energyRing.style.pointerEvents = "none";
  energyRing.style.zIndex = 5;
  let mainCard = document.querySelector('.main-card');
  if(mainCard) mainCard.appendChild(energyRing);

  // Animate the energy ring on every puzzle
  window.updateEnergyRing = function(progress) {
    let c = document.getElementById('cybEnergy');
    if(c) c.setAttribute('stroke-dashoffset', 301 - 301 * progress);
  };
  // Call this from your main script: updateEnergyRing(GAME.score/GAME.maxLevel);

  // === 2. CYBER CHALLENGE ROUNDS ===
  // Every 5 correct in a row, trigger a challenge round!
  let origNextPuzzle = window.nextPuzzle;
  window.nextPuzzle = function(correct) {
    if(window.GAME && window.GAME.streak % 5 === 0 && correct && window.GAME.streak) {
      cyberChallengeRound();
    }
    origNextPuzzle(correct);
    if(window.GAME) window.updateEnergyRing(window.GAME.score/window.GAME.maxLevel);
  };

  function cyberChallengeRound() {
    let puzzleBox = document.getElementById('puzzleBox');
    if(!puzzleBox) return;
    let msg = document.createElement('div');
    msg.innerHTML = `<span style="color:#ffe700;font-size:1.3em;text-shadow:0 0 16px #ff00cc">CYBER CHALLENGE!<br>Answer within 10 seconds for bonus points!</span>`;
    msg.style.position = "absolute";
    msg.style.left = "0"; msg.style.top = "0";
    msg.style.width = "100%";
    msg.style.height = "100%";
    msg.style.background = "rgba(10,12,30,0.88)";
    msg.style.display = "flex";
    msg.style.alignItems = "center";
    msg.style.justifyContent = "center";
    msg.style.zIndex = 88;
    msg.id = "cyberChallengeMsg";
    puzzleBox.appendChild(msg);

    // Remove after 2 seconds, then start timer
    setTimeout(()=>{
      msg.remove();
      let timer = 10; // seconds
      let interval = setInterval(()=>{
        if(timer <= 0) {
          clearInterval(interval);
          document.getElementById('resultBox').textContent = "⏱️ Challenge expired!";
          document.getElementById('resultBox').className = "result-message wrong";
        }
        timer--;
      },1000);

      // Patch answer submission for this round
      let origSubmit = window.checkAnswer;
      window.checkAnswer = function(ans){
        clearInterval(interval);
        window.checkAnswer = origSubmit;
        let res = origSubmit(ans);
        if(res) {
          document.getElementById('resultBox').textContent = "💎 Cyber Challenge Bonus!";
          window.GAME.score += 2;
          window.unlockAchievement && window.unlockAchievement("Cyber Challenge Winner!");
        }
        return res;
      };
    },1700);
  }

  // === 3. FLASH ROUND MINI-GAME ===
  // Randomly inject a "flash" round (very quick arithmetic) for energy bonus
  setInterval(function(){
    if(Math.random() < 0.07 && window.GAME && window.GAME.streak > 2) {
      flashRound();
    }
  }, 30000);

  function flashRound() {
    let puzzleBox = document.getElementById('puzzleBox');
    if(!puzzleBox) return;
    let a = Math.floor(Math.random()*40+10), b = Math.floor(Math.random()*9+2);
    let ans = a * b;
    let msg = document.createElement('div');
    msg.innerHTML = `<span style="color:#39ff14;font-size:1.1em;">FLASH ROUND: What is ${a} × ${b}? <span id="flashTimer" style="color:#ff00cc">7</span>s</span><br>
      <input id="flashInput" style="margin-top:1em;font-size:1.1em;background:#222;color:#ffe700;border-radius:7px;padding:0.4em;" autocomplete="off"/>
      <button id="flashSubmit" style="font-size:1em;background:#00eaff;color:#181818;border:none;padding:0.4em 1.2em;border-radius:7px;margin-left:1em;">GO</button>
    `;
    msg.style.position = "absolute";
    msg.style.left = "0"; msg.style.top = "0";
    msg.style.width = "100%";
    msg.style.height = "100%";
    msg.style.background = "rgba(20,32,92,0.97)";
    msg.style.display = "flex";
    msg.style.flexDirection = "column";
    msg.style.alignItems = "center";
    msg.style.justifyContent = "center";
    msg.style.zIndex = 99;
    msg.id = "flashMsg";
    puzzleBox.appendChild(msg);
    document.getElementById('flashInput').focus();
    let t = 7;
    let flashInt = setInterval(()=>{
      t--;
      if(document.getElementById('flashTimer')) document.getElementById('flashTimer').textContent = t;
      if(t <= 0) {
        clearInterval(flashInt);
        msg.remove();
      }
    },1000);

    document.getElementById('flashSubmit').onclick = function(){
      clearInterval(flashInt);
      let val = document.getElementById('flashInput').value.trim();
      if(val == ans) {
        window.GAME.score++;
        window.unlockAchievement && window.unlockAchievement("Flash Math Winner!");
        msg.innerHTML = `<span style="color:#39ff14;font-size:1.2em;">✔️ Correct! +1 Energy!</span>`;
      } else {
        msg.innerHTML = `<span style="color:#ff00cc;font-size:1.2em;">❌ Missed!</span>`;
      }
      setTimeout(()=>msg.remove(),1200);
    };
  }

  // === 4. KONAMI CODE EASTER EGG ===
  // Up Up Down Down Left Right Left Right B A
  let kBuffer = [];
  const kCode = [38,38,40,40,37,39,37,39,66,65];
  window.addEventListener('keydown', function(e){
    kBuffer.push(e.keyCode);
    if(kBuffer.length > kCode.length) kBuffer.shift();
    if(kBuffer.join() === kCode.join()) {
      cyberKonami();
    }
  });
  function cyberKonami() {
    let body = document.body;
    body.style.background = "linear-gradient(135deg,#ff00cc 0%,#ffe700 100%)";
    let t = document.createElement('div');
    t.innerHTML = `<span style="color:#181818;font-size:2em;font-family:Orbitron,monospace;text-shadow:0 0 25px #fff;z-index:1000;">💽 CYBERBEE ULTRA MODE 💽</span>`;
    t.style.position = "fixed";t.style.top="45%";t.style.left="0";t.style.width="100%";
    t.style.textAlign="center";t.style.zIndex=2000;
    body.appendChild(t);
    setTimeout(()=>t.remove(),2600);
    window.unlockAchievement && window.unlockAchievement("Konami Coder!");
  }

  // === 5. SETTINGS IMPROVEMENTS ===
  // Add a "Theme Preview" in settings
  setTimeout(()=>{
    let sPanel = document.getElementById('settingsPanel');
    if(!sPanel) return;
    let preview = document.createElement('div');
    preview.innerHTML = `<div style="margin-top:1.1em;text-align:center;">
      <span style="display:inline-block;width:22px;height:22px;background:linear-gradient(90deg,#ff00cc,#ffe700,#00eaff);border-radius:50%;margin-right:0.5em;"></span>
      <span style="color:#ffe700;font-family:Orbitron,monospace;">Cyberpunk Theme Preview</span>
    </div>`;
    sPanel.querySelector('.settings-content').appendChild(preview);
  },600);

  // === 6. OPTIONAL: API for main game to call enhancements ===
  window.cyberbeeEnhancer = {
    energyRing,
    updateEnergyRing,
    addMissedPuzzle,
    addFavoritePuzzle
  };

})();

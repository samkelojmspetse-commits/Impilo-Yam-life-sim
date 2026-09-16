
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Impilo Yami — Life Simulator</title>
<style>
  :root{
    --bg:#151210; --bg2:#1e1a16; --panel:#26211c; --panel2:#2f2922; --line:#3c342a;
    --gold:#d4a017; --gold-dim:#8a6c1f; --teal:#3a7d78; --red:#b5443a; --green:#5a8f3c;
    --text:#f1ebe0; --text-dim:#b8ab97; --radius:10px; --bg-grad-start:#241f19;
  }
  html[data-theme="light"]{
    --bg:#f7f3e8; --bg2:#ffffff; --panel:#ffffff; --panel2:#f1ede0; --line:#e2ddc9;
    --gold:#2f9e5c; --gold-dim:#1f7a45; --teal:#2f8f8f; --red:#c0392b; --green:#2f9e5c;
    --text:#1c1c1a; --text-dim:#6b6763; --bg-grad-start:#ffffff;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;height:100%;}
  body{background:radial-gradient(circle at 20% 0%, var(--bg-grad-start) 0%, var(--bg) 55%);color:var(--text);
    font-family:'Segoe UI',system-ui,-apple-system,sans-serif;overflow:hidden;transition:background .2s,color .2s;}
  #app{position:relative;width:100vw;height:100vh;max-width:520px;margin:0 auto;background:var(--bg);
       box-shadow:0 0 40px rgba(0,0,0,0.6); display:flex;flex-direction:column;}
  .screen{display:none;flex-direction:column;height:100%;width:100%;}
  .screen.active{display:flex;}
  h1,h2,h3{font-family:'Georgia',serif;font-weight:700;margin:0;}
  ::-webkit-scrollbar{width:6px;}
  ::-webkit-scrollbar-thumb{background:var(--gold-dim);border-radius:4px;}
  .theme-toggle{position:absolute;top:10px;right:10px;width:34px;height:34px;border-radius:50%;
    background:var(--panel2);border:1px solid var(--line);color:var(--text);cursor:pointer;font-size:1em;z-index:20;}

  #screen-welcome{align-items:center;justify-content:center;text-align:center;padding:30px;
     background:linear-gradient(160deg,#1c1712 0%,#0e0c0a 100%);}
  .brandmark{font-size:2.6em;letter-spacing:1px;color:var(--gold);text-shadow:0 2px 12px rgba(212,160,23,0.35);}
  .brandsub{color:var(--text-dim);margin-top:6px;font-size:0.95em;letter-spacing:2px;}
  .loadbar-track{width:80%;height:8px;background:var(--panel);border-radius:6px;margin-top:36px;overflow:hidden;border:1px solid var(--line);}
  .loadbar-fill{height:100%;width:0%;background:linear-gradient(90deg,var(--gold-dim),var(--gold));transition:width .15s linear;}
  .loadtext{margin-top:14px;color:var(--text-dim);font-size:0.85em;min-height:1.2em;}
  .btn{background:linear-gradient(180deg,var(--gold) 0%, var(--gold-dim) 100%);color:#1a1509;border:none;
    padding:14px 26px;border-radius:8px;font-size:1em;font-weight:700;cursor:pointer;letter-spacing:0.3px;}
  .btn:active{transform:translateY(1px);}
  .btn.secondary{background:var(--panel2);color:var(--text);border:1px solid var(--line);}
  .btn.small{padding:8px 14px;font-size:0.85em;border-radius:6px;}
  .btn.block{width:100%;}
  .btn:disabled{opacity:0.4;cursor:not-allowed;}
  .btn.danger{background:linear-gradient(180deg,#c25a4e,#8f382f);color:#fff;}

  #screen-create{padding:24px;overflow-y:auto;}
  .create-title{color:var(--gold);font-size:1.4em;margin-bottom:4px;}
  .create-sub{color:var(--text-dim);font-size:0.85em;margin-bottom:22px;}
  .field{margin-bottom:18px;}
  .field label{display:block;font-size:0.8em;color:var(--text-dim);margin-bottom:6px;letter-spacing:0.4px;}
  .field input, .field select{width:100%;padding:12px;border-radius:8px;border:1px solid var(--line);
    background:var(--panel);color:var(--text);font-size:1em;}
  .choice-row{display:flex;gap:10px;}
  .choice-row .choice{flex:1;padding:12px;border-radius:8px;border:1px solid var(--line);
     background:var(--panel);text-align:center;cursor:pointer;color:var(--text-dim);}
  .choice-row .choice.selected{border-color:var(--gold);color:var(--gold);background:var(--panel2);}
  .stat-preview{display:flex;flex-wrap:wrap;gap:8px;margin-top:6px;}
  .stat-chip{font-size:0.75em;background:var(--panel);padding:6px 10px;border-radius:20px;color:var(--text-dim);border:1px solid var(--line);}

  #screen-game{height:100%;}
  .topbar{padding:10px 14px 6px;background:var(--bg2);border-bottom:1px solid var(--line);order:1;position:relative;}
  .topbar-row1{display:flex;justify-content:space-between;align-items:baseline;}
  .topbar-row1 .who{font-weight:700;color:var(--gold);font-size:1.05em;}
  .topbar-row1 .age{font-size:0.85em;color:var(--text-dim);}
  .money{font-size:1.15em;font-weight:700;color:#c9e6a8;margin-top:2px;}
  .money.negative{color:#e39a8e;}
  .prison-banner{background:#3a1414;border:1px solid #6b2020;color:#ffb4b4;font-size:0.72em;padding:4px 8px;border-radius:6px;margin-top:6px;}
  .hud-badges{display:flex;gap:5px;flex-wrap:wrap;margin-top:5px;}
  .hud-badge{font-size:0.64em;padding:2px 7px;border-radius:10px;background:var(--panel2);border:1px solid var(--line);color:var(--text-dim);}
  .hud-badge.warn{border-color:var(--red);color:#e39a8e;}
  .hud-badge.good{border-color:var(--gold);color:var(--gold);}
  .stats-grid{display:grid;grid-template-columns:repeat(5,1fr);gap:6px;margin-top:10px;}
  .stat-box{text-align:center;}
  .stat-label{font-size:0.62em;color:var(--text-dim);text-transform:uppercase;letter-spacing:0.5px;}
  .stat-bar{height:6px;background:var(--panel);border-radius:4px;margin-top:4px;overflow:hidden;}
  .stat-bar-fill{height:100%;border-radius:4px;}
  .fill-happiness{background:#e0b64a;} .fill-health{background:#5a8f3c;} .fill-smarts{background:#4a90c9;}
  .fill-looks{background:#c9679e;} .fill-connections{background:#3a7d78;} .fill-fame{background:#caa4e0;}

  .tabs{display:flex;overflow-x:auto;background:var(--bg2);border-top:1px solid var(--line);order:3;}
  .tab{flex:0 0 auto;min-width:58px;padding:8px 8px 6px;font-size:0.62em;color:var(--text-dim);cursor:pointer;
       white-space:nowrap;text-align:center;border-top:2px solid transparent;display:flex;flex-direction:column;align-items:center;gap:2px;}
  .tab .tab-icon{font-size:1.25em;line-height:1;}
  .tab.active{color:var(--gold);border-top-color:var(--gold);}

  .content{flex:1;overflow-y:auto;padding:14px 14px 30px;order:2;}
  .age-fab{position:absolute;left:50%;transform:translateX(-50%);bottom:56px;width:60px;height:60px;border-radius:50%;
    background:linear-gradient(180deg,var(--gold),var(--gold-dim));color:#1a1509;border:3px solid var(--bg2);
    font-size:1.5em;display:flex;align-items:center;justify-content:center;cursor:pointer;
    box-shadow:0 4px 14px rgba(0,0,0,0.45);z-index:15;}
  .age-fab:active{transform:translateX(-50%) scale(0.94);}
  .card{background:var(--panel);border:1px solid var(--line);border-radius:var(--radius);padding:12px 14px;margin-bottom:10px;}
  .card-title{font-weight:700;color:var(--text);font-size:0.95em;}
  .card-sub{font-size:0.78em;color:var(--text-dim);margin-top:2px;}
  .row-between{display:flex;justify-content:space-between;align-items:center;gap:10px;}
  .section-title{font-size:0.72em;color:var(--gold);letter-spacing:1px;margin:16px 0 8px;text-transform:uppercase;}
  .section-title:first-child{margin-top:0;}
  .log-entry{font-size:0.85em;padding:8px 0;border-bottom:1px dashed var(--line);color:var(--text-dim);}
  .log-entry b{color:var(--text);}
  .empty-note{color:var(--text-dim);font-size:0.85em;padding:14px 0;text-align:center;}
  .pill{display:inline-block;font-size:0.68em;background:var(--panel2);border:1px solid var(--line);
        border-radius:20px;padding:2px 9px;color:var(--text-dim);}
  .pill.owned{border-color:var(--green);color:#a9d18e;}
  .pill.verified{border-color:#4a90c9;color:#9cc7ea;}
  .pill.warn{border-color:var(--red);color:#f0a99c;}
  .cat-filter{display:flex;gap:6px;overflow-x:auto;margin-bottom:10px;padding-bottom:2px;}
  .cat-filter span{flex:0 0 auto;font-size:0.72em;padding:5px 10px;border-radius:20px;background:var(--panel);
      border:1px solid var(--line);color:var(--text-dim);cursor:pointer;}
  .cat-filter span.active{color:var(--gold);border-color:var(--gold);}
  .app-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;}
  .app-icon{background:var(--panel);border:1px solid var(--line);border-radius:14px;padding:14px 4px;text-align:center;cursor:pointer;}
  .app-icon .emoji{font-size:1.6em;}
  .app-icon .lbl{font-size:0.62em;color:var(--text-dim);margin-top:6px;}
  .app-icon:active{transform:scale(0.96);}
  .locked-panel{text-align:center;padding:40px 20px;color:var(--text-dim);}
  .locked-panel .emoji{font-size:2.4em;display:block;margin-bottom:10px;}
  .meter{height:8px;background:var(--panel2);border-radius:4px;overflow:hidden;margin-top:6px;}
  .meter-fill{height:100%;background:var(--teal);}
  .toast{position:absolute;left:50%;bottom:18px;transform:translateX(-50%);background:#000c;color:#fff;
     padding:10px 18px;border-radius:20px;font-size:0.82em;z-index:50;max-width:88%;text-align:center;
     border:1px solid var(--gold-dim);}
  .footer-btn-wrap{padding:10px 14px;background:var(--bg2);border-top:1px solid var(--line);display:flex;gap:8px;}
  .btn-row{display:flex;gap:8px;flex-wrap:wrap;margin-top:8px;}

  #screen-gameover{align-items:center;justify-content:center;text-align:center;padding:30px;background:#100d0b;overflow-y:auto;}
  #screen-gameover .rip{font-size:2em;color:var(--gold);margin-bottom:6px;}
  #go-summary{background:var(--panel);border:1px solid var(--line);border-radius:var(--radius);
     padding:16px;margin:18px 0;text-align:left;font-size:0.85em;color:var(--text-dim);width:100%;}
  #go-summary b{color:var(--text);}
</style>
</head>
<body>
<div id="app">

  <!-- SCREEN 1: WELCOME / LOADING -->
  <section id="screen-welcome" class="screen active">
    <button class="theme-toggle" id="theme-toggle-welcome" aria-label="Toggle light/dark theme">🌓</button>
    <div class="brandmark">IMPILO YAMI</div>
    <div class="brandsub">A LIFE SIMULATOR</div>
    <button class="btn" style="margin-top:40px;display:none;" id="btn-continue-life">Continue Your Life</button>
    <button class="btn secondary" style="margin-top:12px;" id="btn-new-life">Start New Life</button>
    <div class="loadbar-track" id="loadWrap" style="display:none;">
      <div class="loadbar-fill" id="loadFill"></div>
    </div>
    <div class="loadtext" id="loadText"></div>
    <div class="btn-row" style="margin-top:24px;justify-content:center;">
      <button class="btn secondary small" id="btn-export-save">⬇ Export Save</button>
      <button class="btn secondary small" id="btn-import-save">⬆ Import Save</button>
      <input type="file" id="import-file-input" accept="application/json" style="display:none;">
    </div>
  </section>

  <!-- SCREEN 2: CHARACTER CREATION -->
  <section id="screen-create" class="screen">
    <div class="create-title">Before you're born...</div>
    <div class="create-sub">Set the stage for a brand new life, starting from zero.</div>
    <div class="field">
      <label>Name your character</label>
      <input type="text" id="cc-name" placeholder="e.g. Lindiwe Mokoena" maxlength="22">
    </div>
    <div class="field">
      <label>Gender</label>
      <div class="choice-row" id="cc-gender">
        <div class="choice" data-val="Female">Female</div>
        <div class="choice" data-val="Male">Male</div>
        <div class="choice" data-val="Non-binary">Non-binary</div>
      </div>
    </div>
    <div class="field">
      <label>Birthplace</label>
      <select id="cc-place">
        <option>Soweto, Johannesburg</option><option>Alexandra, Johannesburg</option>
        <option>Khayelitsha, Cape Town</option><option>Umlazi, Durban</option>
        <option>Mamelodi, Pretoria</option><option>Sandton, Johannesburg</option>
        <option>Polokwane, Limpopo</option><option>Mthatha, Eastern Cape</option>
      </select>
    </div>
    <div class="field">
      <label>Starting family fortune</label>
      <div class="choice-row" id="cc-fortune">
        <div class="choice selected" data-val="0">Rags</div>
        <div class="choice" data-val="1">Modest</div>
        <div class="choice" data-val="2">Comfortable</div>
      </div>
      <div class="card-sub" style="margin-top:6px;">Everyone starts life as a baby with nothing to their name — this only reflects the household you're born into.</div>
    </div>
    <div class="field">
      <label>Newborn stats (randomly rolled)</label>
      <div class="stat-preview" id="cc-stat-preview"></div>
      <button class="btn secondary small" style="margin-top:10px;" id="cc-reroll">🎲 Re-roll stats</button>
    </div>
    <button class="btn block" id="btn-start-life" style="margin-top:10px;">Begin Life</button>
  </section>

  <!-- SCREEN 3: MAIN GAME -->
  <section id="screen-game" class="screen">
    <div class="topbar">
      <button class="theme-toggle" id="theme-toggle-game" aria-label="Toggle light/dark theme" style="top:8px;right:8px;width:30px;height:30px;font-size:0.9em;">🌓</button>
      <div class="topbar-row1">
        <span class="who" id="hud-name">—</span>
        <span class="age" id="hud-age">Age 0</span>
      </div>
      <div class="money" id="hud-money">R 0</div>
      <div class="networth" id="hud-networth" style="font-size:0.72em;color:var(--text-dim);"></div>
      <div id="hud-badges" class="hud-badges"></div>
      <div id="hud-prison" style="display:none;" class="prison-banner"></div>
      <div class="stats-grid" id="hud-stats-grid">
        <div class="stat-box"><div class="stat-label">Happy</div><div class="stat-bar"><div class="stat-bar-fill fill-happiness" id="bar-happiness"></div></div></div>
        <div class="stat-box"><div class="stat-label">Health</div><div class="stat-bar"><div class="stat-bar-fill fill-health" id="bar-health"></div></div></div>
        <div class="stat-box"><div class="stat-label">Smarts</div><div class="stat-bar"><div class="stat-bar-fill fill-smarts" id="bar-smarts"></div></div></div>
        <div class="stat-box"><div class="stat-label">Looks</div><div class="stat-bar"><div class="stat-bar-fill fill-looks" id="bar-looks"></div></div></div>
        <div class="stat-box"><div class="stat-label">Conn.</div><div class="stat-bar"><div class="stat-bar-fill fill-connections" id="bar-connections"></div></div></div>
      </div>
    </div>

    <div class="tabs" id="tabs">
      <div class="tab active" data-tab="life"><span class="tab-icon">🏠</span>Life</div>
      <div class="tab" data-tab="job"><span class="tab-icon">💼</span>Career</div>
      <div class="tab" data-tab="fame"><span class="tab-icon">⭐</span>Fame</div>
      <div class="tab" data-tab="family"><span class="tab-icon">👪</span>Family</div>
      <div class="tab" data-tab="shop"><span class="tab-icon">🛍️</span>Shop</div>
      <div class="tab" data-tab="biz"><span class="tab-icon">🏢</span>Biz</div>
      <div class="tab" data-tab="bank"><span class="tab-icon">🏦</span>Bank</div>
      <div class="tab" data-tab="social"><span class="tab-icon">📱</span>Social</div>
      <div class="tab" data-tab="phone"><span class="tab-icon">📞</span>Phone</div>
      <div class="tab" data-tab="computer"><span class="tab-icon">🖥️</span>PC</div>
    </div>

    <div class="content" id="content"></div>
    <button class="age-fab" id="btn-age-up" aria-label="Age up one year">🎂</button>
  </section>

  <!-- SCREEN 4: GAME OVER -->
  <section id="screen-gameover" class="screen">
    <div class="rip">✝ Rest in Peace</div>
    <div id="go-name" style="color:var(--text-dim);"></div>
    <div id="go-summary"></div>
    <div class="btn-row" id="go-buttons" style="justify-content:center;"></div>
  </section>

</div>

<script>
/* =====================================================================
   IMPILO YAMI — full client-side life simulator (v2)
   ===================================================================== */

/* ---------------------------------------------------------------------
   NAME POOLS
--------------------------------------------------------------------- */
const FEMALE_NAMES = ["Lindiwe","Thandiwe","Nomvula","Precious","Zanele","Buhle","Ayanda","Karabo","Naledi","Palesa","Bongiwe","Nomsa","Thato","Amahle","Refilwe","Lerato","Zodwa","Nokuthula","Mbali","Kagiso"];
const MALE_NAMES = ["Sipho","Thabo","Bongani","Lucky","Sizwe","Katlego","Tebogo","Mandla","Sabelo","Nkosana","Themba","Vusi","Kagiso","Lwazi","Siyabonga","Andile","Bhekizizwe","Ntando","Given","Tumelo"];
const SURNAMES = ["Mokoena","Dlamini","Nkosi","Khumalo","Mahlangu","Zulu","Sithole","Mabaso","Ndlovu","Radebe","Mthembu","Tshabalala","Molefe","Sibiya","Motaung"];
function randOf(arr){ return arr[Math.floor(Math.random()*arr.length)]; }
function genFirstName(gender){ return gender==='Male' ? randOf(MALE_NAMES) : (gender==='Female' ? randOf(FEMALE_NAMES) : randOf(Math.random()<0.5?MALE_NAMES:FEMALE_NAMES)); }
function deriveSurname(fullName){
  const parts = fullName.trim().split(/\s+/);
  return parts.length>1 ? parts[parts.length-1] : randOf(SURNAMES);
}

/* ---------------------------------------------------------------------
   DATA: 100 shopping assets
--------------------------------------------------------------------- */
const SHOP_RAW = [
["Bicycle","Vehicles",300,1,0],["Scooter","Vehicles",1500,2,1],["Golf Cart","Vehicles",4000,2,1],
["Motorbike","Vehicles",18000,3,2],["Toyota Starlet","Vehicles",25000,4,2],["Datsun Go","Vehicles",45000,4,2],
["VW Polo","Vehicles",60000,5,3],["Toyota Corolla","Vehicles",90000,5,3],["Toyota Hilux","Vehicles",150000,6,3],
["Ford Ranger","Vehicles",180000,6,3],["Quantum Taxi","Vehicles",200000,5,2],["VW Golf GTI","Vehicles",280000,6,5],
["BMW 3 Series","Vehicles",350000,7,5],["Mercedes C-Class","Vehicles",400000,7,6],["Audi Q5","Vehicles",500000,8,6],
["Ford Mustang","Vehicles",650000,8,7],["Range Rover","Vehicles",900000,9,8],["Porsche 911","Vehicles",1800000,10,9],
["Bentley Continental","Vehicles",2800000,11,9],["Ferrari","Vehicles",3500000,12,10],["Rolls-Royce Phantom","Vehicles",4200000,13,10],
["Lamborghini","Vehicles",4500000,13,10],["Bugatti Chiron","Vehicles",9000000,15,10],
["Backroom","Housing",500,1,0],["Container Home","Housing",60000,3,0],["Student Res Room","Housing",15000,2,0],
["RDP House","Housing",80000,4,0],["2-Bedroom Flat, Soweto","Housing",250000,5,1],["Townhouse, Roodepoort","Housing",650000,6,2],
["House in Midrand","Housing",1200000,7,2],["Beach House, Umhlanga","Housing",2500000,9,3],["House in Sandton","Housing",4000000,10,4],
["Penthouse, Sandton City","Housing",7000000,12,6],["Game Farm, Limpopo","Housing",9000000,11,3],["Mansion, Bishopscourt","Housing",15000000,14,7],
["Fake Gold Chain","Jewelry",100,1,1],["Silver Ring","Jewelry",300,1,1],["Casio Watch","Jewelry",500,1,1],
["Ankle Bracelet","Jewelry",1500,1,2],["Grillz","Jewelry",2000,2,2],["Gold Chain","Jewelry",5000,3,3],
["Diamond Earrings","Jewelry",8000,3,3],["Diamond Ring","Jewelry",20000,4,4],["Tennis Bracelet","Jewelry",35000,5,5],
["Cuban Link Chain","Jewelry",60000,6,6],["Rolex","Jewelry",150000,7,7],["Cartier Watch","Jewelry",250000,8,8],
["Old Nokia","Electronics",200,1,0],["Basic Radio","Electronics",400,1,0],["Air Fryer","Electronics",1200,2,0],
["Fridge","Electronics",5000,2,0],["Washing Machine","Electronics",6000,2,0],["Samsung TV","Electronics",8000,3,0],
["Home Theatre","Electronics",12000,3,0],["PlayStation 5","Electronics",15000,4,0],["Xbox Series X","Electronics",15000,4,0],
["Gaming PC","Electronics",35000,5,1],["Drone","Electronics",25000,3,0],["Smart Home Setup","Electronics",40000,4,1],
["Second Hand Clothes","Clothing",50,1,1],["Sunglasses","Clothing",600,1,1],["Sneakers","Clothing",800,2,2],
["Streetwear Hoodie","Clothing",900,1,2],["Air Force Ones","Clothing",1500,2,2],["Leather Jacket","Clothing",2500,2,3],
["Traditional Attire","Clothing",3000,3,2],["Gucci Belt","Clothing",6000,2,4],["Designer Suit","Clothing",12000,3,5],
["Tuxedo","Clothing",15000,3,5],["Louis Vuitton Bag","Clothing",25000,4,6],["Wedding Dress","Clothing",40000,5,4],
["Goldfish","Pets",50,1,0],["Stray Cat","Pets",0,2,0],["Township Dog","Pets",100,3,0],["Tortoise","Pets",500,1,0],
["Chicken","Pets",150,1,0],["Rabbit","Pets",300,2,0],["Goat","Pets",800,2,0],["Parrot","Pets",2000,3,0],
["Exotic Snake","Pets",8000,2,0],["Cow","Pets",15000,3,0],["Purebred Boerboel","Pets",5000,4,0],["Horse","Pets",25000,5,0],
["Plastic Chair","Furniture",100,1,0],["Bookshelf","Furniture",1500,1,0],["Braai Stand","Furniture",1200,2,0],
["Dining Table","Furniture",2000,2,0],["Wardrobe","Furniture",2500,2,0],["Home Office Desk","Furniture",3500,2,0],
["Bed","Furniture",3000,3,0],["Sofa Set","Furniture",6000,3,0],["Pool Table","Furniture",15000,4,0],
["Home Gym","Furniture",20000,4,0],["Wine Cellar","Furniture",30000,4,0],["Art Collection","Furniture",50000,5,2],
["Guitar","Leisure",1500,3,0],["Trampoline","Leisure",2500,2,0],["Gaming Chair","Leisure",3000,2,0],
["Telescope","Leisure",4000,2,0],["Piano","Leisure",8000,4,0],["VR Headset","Leisure",9000,3,0],
["DJ Decks","Leisure",12000,4,0],["Home Cinema","Leisure",60000,5,0],["Caravan","Leisure",90000,5,0],
["Swimming Pool","Leisure",120000,6,0],["Jet Ski","Leisure",150000,6,0],["Boat","Leisure",300000,7,0],
["Helicopter","Luxury",6000000,12,6],["Yacht","Luxury",12000000,14,7],["Private Jet","Luxury",40000000,18,9]
];
const SHOP = SHOP_RAW.map((r,i)=>({id:'shop'+i,name:r[0],cat:r[1],price:r[2],happiness:r[3],looks:r[4]}));

/* ---------------------------------------------------------------------
   DATA: 100 businesses
--------------------------------------------------------------------- */
const BIZ_RAW = [
["Food Truck","Food & Beverage",20000,15000],["Spaza Shop","Food & Beverage",30000,20000],["Shisanyama","Food & Beverage",50000,35000],
["Bakery","Food & Beverage",80000,50000],["Coffee Shop","Food & Beverage",150000,90000],["Catering Company","Food & Beverage",200000,120000],
["Fast Food Franchise","Food & Beverage",500000,280000],["Restaurant","Food & Beverage",800000,400000],["Brewery","Food & Beverage",2000000,900000],
["Wine Farm","Food & Beverage",5000000,2000000],
["Mall Kiosk","Retail",25000,18000],["Sneaker Store","Retail",60000,40000],["Cosmetics Shop","Retail",90000,55000],
["Bookstore","Retail",70000,35000],["Clothing Boutique","Retail",150000,90000],["Pharmacy","Retail",400000,220000],
["Hardware Store","Retail",500000,250000],["Furniture Store","Retail",600000,280000],["Electronics Store","Retail",900000,420000],
["Supermarket","Retail",3000000,1200000],
["Car Wash","Services",20000,15000],["Barbershop","Services",25000,18000],["Hair Salon","Services",40000,28000],
["Tailoring Shop","Services",30000,20000],["Laundromat","Services",60000,35000],["Cleaning Service","Services",80000,45000],
["Driving School","Services",100000,55000],["Event Planning Co.","Services",150000,80000],["Security Company","Services",600000,300000],
["Funeral Parlour","Services",1000000,500000],
["Scrapyard","Transport",40000,25000],["Taxi Rank Stake","Transport",80000,55000],["Panel Beater Shop","Transport",120000,70000],
["Car Rental Fleet","Transport",500000,250000],["Courier Service","Transport",200000,110000],["Uber Fleet","Transport",350000,190000],
["Bus Service","Transport",800000,400000],["Trucking Company","Transport",1500000,700000],["Fuel Station","Transport",3000000,1400000],
["Toll Logistics Co.","Transport",4000000,1800000],
["Beekeeping","Agriculture",15000,10000],["Vegetable Farm","Agriculture",60000,40000],["Plant Nursery","Agriculture",40000,25000],
["Chicken Farm","Agriculture",100000,65000],["Piggery","Agriculture",150000,90000],["Fishery","Agriculture",200000,110000],
["Maize Farm","Agriculture",500000,250000],["Dairy Farm","Agriculture",800000,420000],["Cattle Ranch","Agriculture",1500000,700000],
["Wine Estate","Agriculture",6000000,2400000],
["Internet Cafe","Tech",40000,28000],["Web Design Agency","Tech",60000,45000],["E-commerce Store","Tech",80000,55000],
["Drone Services Co.","Tech",150000,90000],["IT Support Co.","Tech",200000,120000],["App Startup","Tech",300000,180000],
["Game Studio","Tech",500000,260000],["Cybersecurity Firm","Tech",800000,420000],["Fintech Startup","Tech",1500000,750000],
["AI Consultancy","Tech",2500000,1200000],
["Photography Studio","Entertainment",30000,20000],["Arcade","Entertainment",80000,50000],["Tavern","Entertainment",100000,65000],
["Recording Studio","Entertainment",200000,110000],["Talent Agency","Entertainment",250000,130000],["Cinema","Entertainment",800000,400000],
["Nightclub","Entertainment",1000000,500000],["Event Venue","Entertainment",1500000,700000],["Radio Station","Entertainment",3000000,1400000],
["Amusement Park","Entertainment",8000000,3500000],
["Brick Factory","Manufacturing",100000,60000],["Shoe Factory","Manufacturing",150000,90000],["Textile Factory","Manufacturing",300000,170000],
["Paint Factory","Manufacturing",400000,220000],["Plastic Recycling Plant","Manufacturing",500000,270000],["Bottling Plant","Manufacturing",600000,320000],
["Furniture Factory","Manufacturing",700000,380000],["Auto Parts Plant","Manufacturing",900000,470000],["Steel Works","Manufacturing",2000000,950000],
["Cement Plant","Manufacturing",5000000,2200000],
["Rental Flats","Real Estate",500000,250000],["Student Housing Block","Real Estate",700000,350000],["Guest House","Real Estate",900000,450000],
["Warehouse","Real Estate",1200000,550000],["Office Park","Real Estate",3000000,1300000],["Retirement Village","Real Estate",6000000,2600000],
["Shopping Complex","Real Estate",5000000,2200000],["Hotel","Real Estate",8000000,3500000],["Industrial Park","Real Estate",10000000,4200000],
["Gated Estate","Real Estate",15000000,6000000],
["Stokvel Fund","Finance",30000,22000],["Mashonisa Loan Shop","Finance",40000,30000],["Pawn Shop","Finance",60000,40000],
["Accounting Firm","Finance",150000,90000],["Insurance Agency","Finance",300000,170000],["Forex Trading Desk","Finance",800000,400000],
["Microfinance Co.","Finance",500000,280000],["Investment Firm","Finance",1000000,520000],["Crypto Exchange","Finance",2000000,900000],
["Bank Franchise","Finance",20000000,8000000]
];
const BIZ = BIZ_RAW.map((r,i)=>({id:'biz'+i,name:r[0],cat:r[1],price:r[2],income:r[3]}));

/* ---------------------------------------------------------------------
   DATA: 60 careers x 5 promotion levels
--------------------------------------------------------------------- */
const CAREERS_RAW = [
["Cashier",50000],["Waiter",48000],["Petrol Attendant",50000],["Domestic Worker",46000],["Cleaner",47000],
["Farm Worker",52000],["Security Guard",65000],["Call Centre Agent",70000],["Factory Worker",75000],["Construction Worker",80000],
["Taxi Driver",90000],["Bus Driver",95000],["Truck Driver",110000],["Mechanic",130000],["Hair Stylist",120000],
["Chef",180000],["Correctional Officer",135000],["Police Officer",150000],["Soldier",140000],["Firefighter",145000],
["Paramedic",160000],["Bank Teller",150000],["Journalist",190000],["Graphic Designer",210000],["Librarian",180000],
["Real Estate Agent",190000],["Sales Representative",180000],["Teacher",240000],["Social Worker",200000],["IT Technician",220000],
["Nurse",230000],["Auditor",280000],["Accountant",300000],["Data Analyst",340000],["Financial Analyst",350000],
["Psychologist",330000],["Physiotherapist",340000],["Veterinarian",380000],["Pharmacist",400000],["HR Manager",380000],
["Marketing Manager",400000],["Software Developer",450000],["Civil Engineer",420000],["Mechanical Engineer",420000],["Electrical Engineer",430000],
["Architect",460000],["Lawyer",480000],["Professor",500000],["Dentist",550000],["Doctor",620000],
["Miner",200000],["Air Hostess",220000],["Pilot",480000],["Geologist",350000],["Statistician",360000],
["Actuary",500000],["Optometrist",400000],["Land Surveyor",340000],["Urban Planner",330000],["Diplomat",420000],
["Radiographer",280000],["Speech Therapist",260000],["Air Traffic Controller",500000],["Quantity Surveyor",360000],["Environmental Scientist",310000],
["Data Scientist",480000],["Cybersecurity Analyst",440000],["Judge",650000],["Prosecutor",420000],["Marine Biologist",300000]
];
const LEVEL_MULT = [1, 1.4, 1.9, 2.6, 3.5];
const LEVEL_PREFIX = ["Trainee","Junior","","Senior","Head"];
const DEGREE_THRESHOLD = 40; // careers requiring this much base reqSmarts prefer a degree
const CAREERS = CAREERS_RAW.map((r,i)=>{
  const [name, base] = r;
  const reqSmarts = Math.min(92, 18 + Math.floor(base/15000));
  const levels = LEVEL_PREFIX.map((p,li)=>({
    title: p ? (p+" "+name) : name,
    salary: Math.round(base*LEVEL_MULT[li]/100)*100
  }));
  return {id:'car'+i, name, reqSmarts, levels};
});
const BOARD_EXAM_CAREERS = ["Doctor","Lawyer","Judge","Prosecutor","Pilot","Air Traffic Controller","Actuary","Dentist"];
function needsBoardExam(car){ return BOARD_EXAM_CAREERS.includes(car.name); }

/* ---------------------------------------------------------------------
   DATA: 10 special (fame) careers
--------------------------------------------------------------------- */
const SPECIAL_CAREERS = [
  {id:'sp0',name:"Musician",stat:"looks",risky:false,base:15000,mult:[1,4,15,60,250],
   levels:["Bedroom Producer","Local Artist","Radio Hit Maker","National Star","Global Icon"]},
  {id:'sp1',name:"Actor",stat:"looks",risky:false,base:20000,mult:[1,4,14,55,220],
   levels:["Extra","Soapie Actor","Lead Actor","Award-Winning Actor","International Star"]},
  {id:'sp2',name:"Professional Footballer",stat:"health",risky:false,base:25000,mult:[1,3,10,40,180],
   levels:["Amateur Player","ABC Motsepe League","PSL Player","Bafana Bafana Star","World Cup Legend"]},
  {id:'sp3',name:"Professional Boxer",stat:"health",risky:false,base:20000,mult:[1,3,12,45,200],
   levels:["Amateur Boxer","Provincial Champion","National Champion","African Champion","World Champion"]},
  {id:'sp4',name:"Content Creator",stat:"connections",risky:false,base:10000,mult:[1,5,20,80,350],
   levels:["Nobody Online","Micro-Influencer","Verified Creator","Viral Sensation","Global Influencer"]},
  {id:'sp5',name:"Politician",stat:"connections",risky:false,base:60000,mult:[1,3,8,20,60],
   levels:["Ward Committee Member","Councillor","Member of Parliament","Minister","President"]},
  {id:'sp6',name:"Pastor",stat:"connections",risky:false,base:30000,mult:[1,3,10,35,150],
   levels:["Sunday School Teacher","Youth Pastor","Church Pastor","Bishop","Televangelist"]},
  {id:'sp7',name:"Comedian",stat:"looks",risky:false,base:15000,mult:[1,4,14,50,200],
   levels:["Open Mic Comedian","Club Comedian","TV Comedian","Headline Comedian","International Comedy Star"]},
  {id:'sp8',name:"Underworld Hustler",stat:"connections",risky:true,base:20000,mult:[1,4,16,60,220],
   levels:["Petty Thief","Street Hustler","Gang Member","Syndicate Boss","Crime Kingpin"]},
  {id:'sp9',name:"Entrepreneur",stat:"smarts",risky:false,base:40000,mult:[1,3,10,35,150],
   levels:["Side Hustler","Small Business Owner","Company Founder","CEO","Business Mogul"]},
  {id:'sp10',name:"Celebrity Chef",stat:"smarts",risky:false,base:18000,mult:[1,4,13,50,200],
   levels:["Line Cook","Head Chef","Restaurant Owner","TV Chef","Global Celebrity Chef"]},
  {id:'sp11',name:"Fashion Designer & Model",stat:"looks",risky:false,base:16000,mult:[1,4,15,60,240],
   levels:["Local Model","Runway Regular","Fashion Week Feature","Global Supermodel","Fashion Icon"]},
  {id:'sp12',name:"Astronaut",stat:"smarts",risky:false,base:35000,mult:[1,3,9,30,120],
   levels:["Aspiring Cadet","SANSA Trainee","Mission Specialist","ISS Astronaut","Space Program Legend"]}
];

/* ---------------------------------------------------------------------
   DATA: phones / computers / apps / software / social
--------------------------------------------------------------------- */
const PHONES = [["Nokia 3310",500],["Basic Android",1500],["SmartFoni X",3500],["iZulu 10",8000],["iZulu Pro Max",20000]];
const COMPUTERS = [["Refurbished Desktop",2000],["Budget Laptop",6000],["Gaming Laptop",18000],["MacBook-style Ultra",30000],["Workstation Rig",60000]];

const PHONE_APPS = [
  {id:'msg',name:"Messages",emoji:"💬",desc:"Text your circle to stay close.",action:'app_messages'},
  {id:'soc',name:"Buzzr",emoji:"📱",desc:"Jump to your social feeds.",action:'app_social'},
  {id:'bank',name:"iBank",emoji:"🏦",desc:"Jump to your banking.",action:'app_bank'},
  {id:'jobs',name:"WorkFinder",emoji:"💼",desc:"Browse the job board.",action:'app_jobs'},
  {id:'cam',name:"Camera",emoji:"📸",desc:"Snap a selfie — might go viral.",action:'app_camera'},
  {id:'maps',name:"Maps",emoji:"🗺️",desc:"Explore the city for opportunities.",action:'app_maps'},
  {id:'date',name:"Mzansi Match",emoji:"❤️",desc:"Swipe and look for love (18+).",action:'app_dating'},
  {id:'music',name:"Vibes",emoji:"🎧",desc:"Stream amapiano to lift your mood.",action:'app_music'},
  {id:'weather',name:"Weathr",emoji:"☀️",desc:"Check the forecast.",action:'app_weather'},
  {id:'ride',name:"HailaRide",emoji:"🚕",desc:"Book a discounted ride for your next trip.",action:'app_ride'},
  {id:'recipe',name:"KitchenKing",emoji:"🍳",desc:"Try a new recipe.",action:'app_recipe'},
  {id:'news',name:"Mzansi News",emoji:"📰",desc:"Catch up on world events and rivals.",action:'app_news'},
  {id:'fit',name:"FitTrack",emoji:"⌚",desc:"Log a workout.",action:'app_fit'},
  {id:'lingua',name:"LinguaBoost",emoji:"🗣️",desc:"Practice a new language.",action:'app_lingua'},
  {id:'invest',name:"InvestSight",emoji:"📈",desc:"Jump to your investments.",action:'app_invest'},
  {id:'zen',name:"ZenSpace",emoji:"🧘",desc:"Meditate and unwind.",action:'app_zen'},
  {id:'famcall',name:"FamCall",emoji:"📹",desc:"Video call someone close to you.",action:'app_famcall'},
  {id:'podcast',name:"PodPlay",emoji:"🎙️",desc:"Listen to a podcast.",action:'app_podcast'}
];

const COMPUTER_SOFTWARE = [
  {id:'word',name:"Word Processor",emoji:"📄",desc:"Write a book for royalties.",action:'pc_word'},
  {id:'sheet',name:"Spreadsheet",emoji:"📊",desc:"Budget your finances for guaranteed interest.",action:'pc_sheet'},
  {id:'browser',name:"Web Browser",emoji:"🌐",desc:"Research online to sharpen your mind.",action:'pc_browser'},
  {id:'code',name:"Code Editor",emoji:"💻",desc:"Build an app — big payouts if you're smart.",action:'pc_code'},
  {id:'design',name:"Design Studio",emoji:"🎨",desc:"Take a freelance design gig.",action:'pc_design'},
  {id:'video',name:"Video Editor",emoji:"🎬",desc:"Edit a vlog and grow your following.",action:'pc_video'},
  {id:'daw',name:"Music Studio",emoji:"🎹",desc:"Produce a beat — boosts music fame too.",action:'pc_daw'},
  {id:'crypto',name:"Crypto Exchange",emoji:"🪙",desc:"Gamble R1000 on the markets.",action:'pc_crypto'},
  {id:'av',name:"Antivirus Suite",emoji:"🛡️",desc:"Run a security scan for peace of mind.",action:'pc_av'},
  {id:'mail',name:"Email Client",emoji:"📧",desc:"Check for job leads and offers.",action:'pc_mail'},
  {id:'engine',name:"Pulse Engine",emoji:"🕹️",desc:"Develop an indie game — high risk, high reward.",action:'pc_engine'},
  {id:'legal',name:"Legal Research Suite",emoji:"⚖️",desc:"Study case law — helps if you're ever sued.",action:'pc_legal'},
  {id:'campaign',name:"Campaign Manager",emoji:"🗳️",desc:"Plan a political campaign.",action:'pc_campaign'},
  {id:'proptech',name:"PropTech Manager",emoji:"🏘️",desc:"Optimize your business logistics.",action:'pc_proptech'},
  {id:'translate',name:"Translator Pro",emoji:"🌍",desc:"Translate documents for practice.",action:'pc_translate'},
  {id:'print3d',name:"3D Print Studio",emoji:"🖨️",desc:"Design and sell novelty prints.",action:'pc_print3d'},
  {id:'geneal',name:"Genealogy Finder",emoji:"🌳",desc:"Dig into your family history.",action:'pc_geneal'},
  {id:'vr',name:"VR Suite",emoji:"🥽",desc:"Escape into virtual reality.",action:'pc_vr'},
  {id:'taxopt',name:"Tax Optimizer AI",emoji:"🧾",desc:"Legally reduce next year's tax bill.",action:'pc_taxopt'},
  {id:'podstudio',name:"Podcast Studio",emoji:"🎚️",desc:"Record an episode and grow your audience.",action:'pc_podstudio'},
  {id:'nft',name:"Blockchain Exchange",emoji:"🔗",desc:"Gamble R2000 on digital assets.",action:'pc_nft'}
];

const WORLD_EVENTS = [
  {name:"Global Recession", years:2, bizMult:0.82, stockDrift:-0.06, happinessAll:-2},
  {name:"Economic Boom", years:2, bizMult:1.22, stockDrift:0.06, happinessAll:2},
  {name:"Election Year Uncertainty", years:1, bizMult:0.95, happinessAll:0},
  {name:"Bafana Bafana World Cup Run", years:1, bizMult:1.0, happinessAll:4},
  {name:"Load Shedding Crisis (Stage 8)", years:2, bizMult:0.85, happinessAll:-3},
  {name:"Tech Investment Wave", years:2, bizMult:1.15, stockDrift:0.04, happinessAll:0},
  {name:"Currency Slump", years:2, bizMult:0.9, stockDrift:-0.03, happinessAll:-1},
  {name:"National Infrastructure Boom", years:2, bizMult:1.12, happinessAll:1}
];

const ROYAL_HOUSES = [
  {id:'rh0', kingdom:"Kingdom of Marisole", titleM:"Prince", titleF:"Princess"},
  {id:'rh1', kingdom:"Principality of Vaelburg", titleM:"Prince", titleF:"Princess"},
  {id:'rh2', kingdom:"Sultanate of Zahrain", titleM:"Royal Son", titleF:"Royal Daughter"},
  {id:'rh3', kingdom:"Kingdom of Drakenhall", titleM:"Duke", titleF:"Duchess"}
];

const EMIGRATION_COUNTRIES = [
  "United Kingdom","United States","Australia","United Arab Emirates","Canada","New Zealand","Germany","Netherlands"
];
const NOBLE_TITLE_PLACES = ["Marisole","Vaelmoor","Ashenreach","Draketon","Silverholt","Windmere"];

const SOCIAL_PLATFORMS = [
  {id:'ig',name:"InstaSnap",emoji:"📷"}, {id:'tt',name:"TokTok",emoji:"🎵"}, {id:'tw',name:"Chirp",emoji:"🐦"},
  {id:'yt',name:"YouView",emoji:"▶️"}, {id:'fb',name:"Facebook",emoji:"👥"}
];

const LIFE_EVENTS = [
  {text:"Load shedding hit your area right when you needed power most.", h:-3},
  {text:"You helped a neighbour push-start their car.", c:2, h:1},
  {text:"You caught a nasty cold going around.", health:-4},
  {text:"You stayed up late studying and it paid off.", smarts:3},
  {text:"Your stokvel paid out a surprise bonus!", money:2000},
  {text:"You got mugged walking home at night.", money:-1200, h:-5},
  {text:"You won a small spot prize at the taxi rank.", money:500},
  {text:"Someone new took a real interest in you.", h:5},
  {text:"You had a blow-up argument with a family member.", h:-4, c:-2},
  {text:"You kept up a solid gym routine this year.", health:5, looks:2},
  {text:"You lived off fast food most of the year.", health:-3},
  {text:"You read every book you could get your hands on.", smarts:4},
  {text:"Data and airtime prices went up again.", h:-2},
  {text:"You helped organise a community braai.", c:5, h:3},
  {text:"You got food poisoning from a dodgy shisanyama.", health:-5},
  {text:"An old friend finally paid back what they owed you.", money:800},
  {text:"You danced a viral challenge with your cousins.", c:3, h:2},
  {text:"Eskom announced yet another stage of load shedding.", h:-3},
  {text:"You donated blood at a community drive.", health:-1, c:2, h:2},
  {text:"You won the local stokvel raffle.", money:1500},
  {text:"You spent a peaceful weekend at church.", h:3, c:2},
  {text:"You were in a minor taxi fender-bender.", health:-6},
  {text:"You threw a birthday party everyone still talks about.", h:6},
  {text:"You picked up a random odd job for quick cash.", money:600},
  {text:"You felt completely burnt out this year.", h:-5, health:-2}
];

const PRISON_EVENTS = [
  {text:"You kept your head down and stayed out of trouble.", h:1},
  {text:"A fight broke out in the yard and you got caught up in it.", health:-6, h:-4},
  {text:"You joined a rehabilitation program behind bars.", smarts:2, h:2},
  {text:"You earned good behaviour credit with the warders.", h:2},
  {text:"Conditions were rough this year — overcrowding and cold nights.", health:-3, h:-3},
  {text:"A cellmate looked out for you.", c:1, h:1},
  {text:"You got into it with a rival gang inside.", health:-5, h:-5}
];

const MAJORS = [
  {name:"Commerce", tuition:60000}, {name:"Engineering", tuition:120000}, {name:"Law", tuition:150000},
  {name:"Medicine", tuition:220000}, {name:"Computer Science", tuition:110000}, {name:"Education", tuition:45000},
  {name:"Arts", tuition:40000}, {name:"Social Sciences", tuition:50000}, {name:"Agriculture", tuition:70000},
  {name:"Fine Art", tuition:38000}
];

const STOCKS = [
  {id:'idx', name:"JSE Index Fund", volatility:0.10, drift:0.04},
  {id:'min', name:"Mining Co-op", volatility:0.28, drift:0.02},
  {id:'tec', name:"Tech Startup Inc.", volatility:0.45, drift:0.06}
];

const FAME_TIERS = [
  {min:0, label:"Unknown"}, {min:10, label:"Local Name"}, {min:25, label:"Rising Star"},
  {min:45, label:"Celebrity"}, {min:65, label:"National Icon"}, {min:85, label:"Global Superstar"}
];
const SECURITY_COST = {"Celebrity":30000, "National Icon":80000, "Global Superstar":200000};

/* ---------------------------------------------------------------------
   DATA: side hustles (stackable alongside a main career)
--------------------------------------------------------------------- */
const SIDE_HUSTLES = [
  {id:'sh0', name:"Weekend Uber Driving", base:14000, stat:'connections'},
  {id:'sh1', name:"Tutoring", base:12000, stat:'smarts'},
  {id:'sh2', name:"Selling Snacks/Airtime", base:8000, stat:'connections'},
  {id:'sh3', name:"Freelance Photography", base:15000, stat:'looks'},
  {id:'sh4', name:"Dog Walking", base:6000, stat:'health'},
  {id:'sh5', name:"Reselling Clothes Online", base:10000, stat:'smarts'},
  {id:'sh6', name:"Food Delivery", base:13000, stat:'health'},
  {id:'sh7', name:"Weekend Hairdressing", base:11000, stat:'looks'},
  {id:'sh8', name:"Car Washing", base:7000, stat:'health'},
  {id:'sh9', name:"Weekend DJing", base:16000, stat:'connections'}
];
function sideHustleIncome(id){
  const sh = SIDE_HUSTLES.find(x=>x.id===id);
  if(!sh) return 0;
  return Math.round(sh.base * (0.6 + (G.stats[sh.stat]/100)*0.8));
}
const VEHICLE_UPKEEP_RATE = 0.04; // 4% of purchase price per year in insurance/fuel/maintenance
function totalVehicleUpkeep(){
  return G.ownedAssets.reduce((sum,id)=>{
    const item = SHOP.find(s=>s.id===id);
    return item && item.cat==='Vehicles' ? sum + Math.round(item.price*VEHICLE_UPKEEP_RATE) : sum;
  }, 0);
}

const NICHES = ["Comedy","Beauty & Fashion","Finance Tips","Gaming","Music","Lifestyle & Vlogging","Sports"];

const HOBBIES = [
  {id:'hb0', name:"Reading", emoji:"📚", stat:'smarts'},
  {id:'hb1', name:"Gym Training", emoji:"🏋️", stat:'health'},
  {id:'hb2', name:"Gaming", emoji:"🎮", stat:'smarts'},
  {id:'hb3', name:"Painting", emoji:"🎨", stat:'looks'},
  {id:'hb4', name:"Soccer", emoji:"⚽", stat:'health'},
  {id:'hb5', name:"Cooking", emoji:"🍲", stat:'happiness'},
  {id:'hb6', name:"Singing", emoji:"🎤", stat:'connections'}
];

const DESTINATIONS_DOMESTIC = [
  {name:"Cape Town Weekend", cost:3500, h:8, c:2},
  {name:"Durban Beach Trip", cost:2500, h:7, c:2},
  {name:"Kruger National Park Safari", cost:5000, h:9, c:1},
  {name:"Drakensberg Hike", cost:1800, h:6, health:2},
  {name:"Sun City Getaway", cost:4500, h:8, c:3}
];
const DESTINATIONS_INTL = [
  {name:"Dubai", cost:25000, h:12, c:5, looks:2},
  {name:"London", cost:35000, h:11, c:6, looks:1},
  {name:"New York City", cost:40000, h:12, c:7, looks:2},
  {name:"Paris", cost:38000, h:13, c:5, looks:3},
  {name:"Bali", cost:28000, h:14, c:4, looks:2}
];

const AMBASSADOR_BRANDS = ["Mzansi Sportswear","Amanzi Beverages","Township Threads","Jozi Telecom","Savanna Cosmetics","Ubuntu Tech"];

const EVENT_DECK = [
  {id:'ev_bribe', minAge:18,
   title:"A shortcut appears",
   prompt:"A contact offers to fast-track something you need for a 'small favor' — nobody would know.",
   choices:[
     {label:"Take the shortcut", tag:"Short-term gain", immediate:[{money:5000}],
      deferred:{years:3, chance:0.45, log:"That old shortcut resurfaced and cost you your reputation.", effects:[{fame:-8},{stat:'happiness',delta:-6}]}},
     {label:"Do it properly", tag:"Long-term payoff", immediate:[{stat:'connections',delta:2}],
      deferred:{years:2, chance:0.6, log:"Your reputation for doing things properly paid off unexpectedly.", effects:[{stat:'connections',delta:6},{money:3000}]}}
   ]},
  {id:'ev_loyalty', minAge:20,
   title:"A rival offer",
   prompt:"A competitor offers you more money to jump ship immediately, no notice.",
   choices:[
     {label:"Take the money and run", tag:"Short-term gain", immediate:[{money:8000},{stat:'happiness',delta:3}],
      deferred:{years:2, chance:0.4, log:"Word got around about how you left — some doors quietly closed.", effects:[{stat:'connections',delta:-6}]}},
     {label:"Stay loyal", tag:"Long-term payoff", immediate:[{stat:'happiness',delta:-2}],
      deferred:{years:3, chance:0.55, log:"Your loyalty was remembered and rewarded.", effects:[{stat:'connections',delta:8},{money:10000}]}}
   ]},
  {id:'ev_family_event', minAge:16,
   title:"A family gathering clashes",
   prompt:"An important family event falls on the same day as a big opportunity.",
   choices:[
     {label:"Chase the opportunity", tag:"Short-term gain", immediate:[{stat:'smarts',delta:2}],
      deferred:{years:2, chance:0.5, log:"Family still remembers you missing that day — distance grew.", effects:[{stat:'happiness',delta:-6}]}},
     {label:"Show up for family", tag:"Long-term payoff", immediate:[{stat:'happiness',delta:4}],
      deferred:{years:3, chance:0.5, log:"Showing up all those years ago is still remembered fondly.", effects:[{stat:'happiness',delta:6},{stat:'connections',delta:4}]}}
   ]},
  {id:'ev_getrich', minAge:18,
   title:"A get-rich-quick pitch",
   prompt:"Someone pitches you an investment that 'can't lose' — but it smells a bit off.",
   choices:[
     {label:"Put money in", tag:"Short-term gain", immediate:[{money:4000}],
      deferred:{years:1, chance:0.55, log:"The scheme collapsed. That money — and more — is gone.", effects:[{money:-15000},{stat:'happiness',delta:-8}]}},
     {label:"Walk away", tag:"Long-term payoff", immediate:[{stat:'smarts',delta:1}],
      deferred:{years:2, chance:0.5, log:"Your caution back then is still saving you money now.", effects:[{money:6000}]}}
   ]},
  {id:'ev_symptom', minAge:25,
   title:"Something feels off",
   prompt:"You've noticed a nagging symptom for weeks. Get it checked, or hope it passes?",
   choices:[
     {label:"Ignore it, you're busy", tag:"Short-term gain", immediate:[{stat:'happiness',delta:2}],
      deferred:{years:2, chance:0.45, log:"What you ignored years ago turned into a real health scare.", effects:[{stat:'health',delta:-14}]}},
     {label:"Get it checked", tag:"Long-term payoff", immediate:[{money:-800}],
      deferred:{years:1, chance:0.6, log:"Catching that early years ago is still paying off — you're healthier for it.", effects:[{stat:'health',delta:8}]}}
   ]},
  {id:'ev_secret', minAge:18,
   title:"A secret worth keeping?",
   prompt:"You're holding onto information that could hurt someone close to you if it came out.",
   choices:[
     {label:"Keep it buried", tag:"Short-term gain", immediate:[{stat:'happiness',delta:2}],
      deferred:{years:3, chance:0.4, log:"The secret came out eventually — and the timing made it worse.", effects:[{stat:'connections',delta:-8},{stat:'happiness',delta:-6}]}},
     {label:"Come clean now", tag:"Long-term payoff", immediate:[{stat:'happiness',delta:-4}],
      deferred:{years:2, chance:0.6, log:"Coming clean years ago built a trust that's still holding.", effects:[{stat:'connections',delta:8}]}}
   ]},
  {id:'ev_riskypost', minAge:16,
   title:"A controversial take",
   prompt:"You've got a hot take that would definitely get attention online — but it's risky.",
   choices:[
     {label:"Post it anyway", tag:"Short-term gain", immediate:[{fame:5}],
      deferred:{years:1, chance:0.4, log:"That old post got dug up and dragged back into the spotlight.", effects:[{fame:-10},{stat:'happiness',delta:-5}]}},
     {label:"Keep it to yourself", tag:"Long-term payoff", immediate:[],
      deferred:{years:2, chance:0.45, log:"Staying out of past controversies is quietly paying off in trust.", effects:[{fame:4},{stat:'connections',delta:4}]}}
   ]},
  {id:'ev_stranger', minAge:14,
   title:"A stranger needs help",
   prompt:"Someone you don't know is struggling right in front of you. Get involved, or keep walking?",
   choices:[
     {label:"Keep walking", tag:"Short-term gain", immediate:[{stat:'happiness',delta:1}],
      deferred:{years:2, chance:0.35, log:"Word travels — people remember who does and doesn't help.", effects:[{stat:'connections',delta:-4}]}},
     {label:"Stop and help", tag:"Long-term payoff", immediate:[{money:-500}],
      deferred:{years:2, chance:0.6, log:"That small act of kindness came back around in a big way.", effects:[{stat:'connections',delta:6},{stat:'happiness',delta:4}]}}
   ]}
];
function applyEffect(e){
  if(e.money) G.money += e.money;
  if(e.fame) G.fame = clampStat(G.fame+e.fame);
  if(e.stat) G.stats[e.stat] = clampStat(G.stats[e.stat]+e.delta);
}
function triggerLifeEvent(){
  const eligible = EVENT_DECK.filter(ev=>G.age>=ev.minAge && !G.seenEvents.includes(ev.id));
  const pool = eligible.length ? eligible : EVENT_DECK.filter(ev=>G.age>=ev.minAge);
  if(!pool.length) return;
  const ev = randOf(pool);
  G.pendingLifeEvent = ev.id;
  if(!G.seenEvents.includes(ev.id)){ G.seenEvents.push(ev.id); if(G.seenEvents.length>EVENT_DECK.length) G.seenEvents=[ev.id]; }
}
function resolveLifeEvent(choiceIndex){
  const ev = EVENT_DECK.find(e=>e.id===G.pendingLifeEvent);
  if(!ev) return;
  const choice = ev.choices[choiceIndex];
  (choice.immediate||[]).forEach(applyEffect);
  if(choice.deferred){
    G.deferredConsequences.push({yearsLeft:choice.deferred.years, chance:choice.deferred.chance, log:choice.deferred.log, effects:choice.deferred.effects});
  }
  pushLog(`${ev.title}: chose to "${choice.label.toLowerCase()}".`);
  toast(`Decision made: ${choice.label}`);
  G.pendingLifeEvent = null;
  renderAll();
}

const ACHIEVEMENTS = [
  {id:'first_million', name:"First Million", check:g=>netWorth()>=1000000},
  {id:'first_ten_million', name:"Deca-Millionaire", check:g=>netWorth()>=10000000},
  {id:'billionaire', name:"Billionaire", check:g=>netWorth()>=1000000000},
  {id:'global_superstar', name:"Global Superstar", check:g=>getFameTier(g.fame).label==='Global Superstar'},
  {id:'president', name:"President of the Republic", check:g=>g.isPresident},
  {id:'married', name:"Happily Ever After", check:g=>g.spouse && g.spouse.married},
  {id:'parent', name:"Parenthood", check:g=>g.children.length>0},
  {id:'survived_prison', name:"Survived the Inside", check:g=>g.criminalRecord},
  {id:'degree', name:"Graduate", check:g=>g.education.hasDegree},
  {id:'globetrotter', name:"Globetrotter", check:g=>g.emigrationHistory.length>0},
  {id:'noble', name:"Nobility", check:g=>!!g.royal.title},
  {id:'business_empire', name:"Business Empire", check:g=>g.ownedBusinesses.length>=10},
  {id:'holding_co', name:"Conglomerate Builder", check:g=>g.holdingCompany},
  {id:'centenarian', name:"Centenarian", check:g=>g.age>=100}
];
function checkAchievements(){
  ACHIEVEMENTS.forEach(a=>{
    if(!G.achievements.includes(a.id) && a.check(G)){
      G.achievements.push(a.id);
      pushLog(`🏆 Achievement unlocked: ${a.name}!`);
      toast(`🏆 ${a.name}`);
    }
  });
}

/* ---------------------------------------------------------------------
   GAME STATE
--------------------------------------------------------------------- */
let G = null;
function freshState(){
  return {
    name:"", gender:"Female", place:"Soweto, Johannesburg", fortune:0, surname:"Mokoena",
    age:0, alive:true,
    stats:{happiness:70, health:80, smarts:50, looks:50, connections:30},
    money:0, debt:0, businessLoan:0,
    career:null, retired:false, pension:0, boss:null, raiseMultiplier:1,
    promotedThisYear:false,
    ownedAssets:[], ownedBusinesses:[],
    mortgages:[], // [{assetId, balance, rate}]
    marginLoan:0,
    achievements:[],
    phone:null, computer:null,
    social:{ig:0, tt:0, tw:0, yt:0, fb:0},
    usedThisYear:new Set(),
    hackShield:false,
    fame:0, criminalRecord:false,
    prison:{active:false, yearsLeft:0},
    education:{stage:'toddler', matric:null, matricResult:null, prelimsDone:false, uniYearsLeft:0, major:null, hasDegree:false, focus:null},
    family:{mother:null, father:null, siblings:[], grandparents:[], auntsUncles:[], cousins:[]},
    spouse:null, // {name, gender, relationship, dating, engaged, married}
    children:[],
    stepChildren:[], // kids a spouse brought into the marriage: [{name, gender, age}]
    exChildren:[], // kids lost in a custody battle: [{name, age, exSpouseName, support}]
    sideHustle:null, // {id}
    bizUpgrades:{}, bizInsurance:false, bizManager:false,
    holdingCompany:false, ipoTaken:false, boardExamsPassed:{},
    socialNiche:null, followerSnapshotStart:0,
    agent:false, merchLine:{active:false, income:0}, ambassadorDeals:[],
    passport:false, gymMembership:false, medicalAid:false,
    hobby:null, hobbyProgress:0, hobbyLevel:0,
    driversLicense:{learners:false, full:false}, dui:0,
    willBeneficiary:null,
    vices:{alcohol:0, smoking:0},
    bank:{savings:0, creditScore:600, stocks:{idx:0,min:0,tec:0}},
    stockPrices:{idx:100, min:60, tec:20},
    insurance:{active:false, premium:2500, payout:60000},
    rival:null, // {name, netWorth, fame}
    activeWorldEvent:null, worldEventYearsLeft:0,
    electionsWon:0, electionsLost:0, isPresident:false,
    lawsuits:{filed:0, won:0, lost:0}, hasLawyer:false,
    citizenship:'South African', visaStatus:'citizen', emigrationHistory:[],
    royal:{title:null, spouseIsRoyal:false, bloodlineBought:false},
    rideDiscount:false, legalPrepared:false, campaignBoost:false, taxOptimized:false,
    pendingLawsuit:null, pendingInterview:false, sponsorOffers:[], trendingBonus:null,
    pendingLifeEvent:null, deferredConsequences:[], seenEvents:[],
    log:[]
  };
}

/* ---------------------------------------------------------------------
   SAVE / LOAD
--------------------------------------------------------------------- */
const THEME_KEY = 'impiloYamiTheme';
function applyTheme(theme){
  document.documentElement.setAttribute('data-theme', theme);
  try{ localStorage.setItem(THEME_KEY, theme); }catch(e){}
}
function initTheme(){
  let theme = 'dark';
  try{ theme = localStorage.getItem(THEME_KEY) || 'dark'; }catch(e){}
  applyTheme(theme);
}
function toggleTheme(){
  const current = document.documentElement.getAttribute('data-theme') || 'dark';
  applyTheme(current==='dark' ? 'light' : 'dark');
}
initTheme();

const SAVE_KEY = 'impiloYamiSave_v4';
function serializeState(state){
  return {...state, usedThisYear: Array.from(state.usedThisYear||[])};
}
function deserializeState(obj){
  return {...freshState(), ...obj, usedThisYear: new Set(obj.usedThisYear||[])};
}
function saveGame(){
  if(!G) return;
  try{ localStorage.setItem(SAVE_KEY, JSON.stringify(serializeState(G))); }catch(e){ /* storage unavailable — fail silently */ }
}
function loadGame(){
  try{
    const raw = localStorage.getItem(SAVE_KEY);
    if(!raw) return null;
    return deserializeState(JSON.parse(raw));
  }catch(e){ return null; }
}
function clearSave(){
  try{ localStorage.removeItem(SAVE_KEY); }catch(e){}
}
function hasSave(){
  try{ return localStorage.getItem(SAVE_KEY) !== null; }catch(e){ return false; }
}

function rollStat(base, spread){ return Math.max(5, Math.min(95, Math.round(base + (Math.random()*spread*2 - spread)))); }
function rollNewbornStats(){
  return { happiness: rollStat(65,15), health: rollStat(80,12), smarts: rollStat(45,20), looks: rollStat(50,20), connections: rollStat(25,15) };
}
function money(n){
  const neg = n<0; const v = Math.abs(Math.round(n));
  return (neg?"-R ":"R ") + v.toLocaleString();
}
function clampStat(v){ return Math.max(0, Math.min(100, Math.round(v))); }
function toast(msg){
  const el = document.createElement('div');
  el.className='toast'; el.textContent=msg;
  document.getElementById('app').appendChild(el);
  setTimeout(()=>el.remove(), 2200);
}
function pushLog(msg){
  G.log.unshift({age:G.age, text:msg});
  if(G.log.length>200) G.log.pop();
}
function getFameTier(fame){ let t=FAME_TIERS[0]; for(const tier of FAME_TIERS){ if(fame>=tier.min) t=tier; } return t; }
function totalFollowers(){ return Object.values(G.social).reduce((a,b)=>a+b,0); }
function isAdult(){ return G.age>=18; }
function inPrison(){ return G.prison && G.prison.active; }
function netWorth(){
  let nw = G.money + G.bank.savings - G.debt - G.businessLoan - (G.marginLoan||0);
  (G.mortgages||[]).forEach(m=>{ nw -= m.balance; });
  STOCKS.forEach(st=>{ nw += Math.round((G.bank.stocks[st.id]||0)*G.stockPrices[st.id]); });
  G.ownedAssets.forEach(id=>{ const it = SHOP.find(s=>s.id===id); if(it) nw += Math.round(it.price*0.5); });
  G.ownedBusinesses.forEach(id=>{ const it = BIZ.find(b=>b.id===id); if(it) nw += Math.round(it.price*0.5*(1+0.25*(G.bizUpgrades[id]||0))); });
  return Math.round(nw);
}
function forbesStatus(){
  const nw = netWorth();
  const badges = [];
  if(nw>=1000000000) badges.push("Forbes Africa Billionaires List");
  else if(nw>=100000000) badges.push("Forbes Richest in Mzansi");
  else if(nw>=10000000) badges.push("Forbes Rising Wealth Watchlist");
  if(G.age<30 && nw>=1000000) badges.push("Forbes 30 Under 30");
  if(G.fame>=85) badges.push("Forbes Most Influential");
  return badges;
}

/* SARS-style progressive tax */
function computeTax(income){
  if(income<=0) return 0;
  const brackets = [
    [95750, 0.00], [237100, 0.18], [370500, 0.26], [512800, 0.31],
    [673000, 0.36], [857900, 0.39], [1817000, 0.41], [Infinity, 0.45]
  ];
  let tax=0, prev=0;
  for(const [cap, rate] of brackets){
    if(income>prev){
      const taxable = Math.min(income, cap) - prev;
      tax += taxable*rate;
      prev = cap;
    } else break;
  }
  return Math.round(tax);
}

/* ---------------------------------------------------------------------
   SCREEN 1: WELCOME / LOADING / CONTINUE / IMPORT-EXPORT
--------------------------------------------------------------------- */
document.getElementById('theme-toggle-welcome').addEventListener('click', toggleTheme);
document.getElementById('theme-toggle-game').addEventListener('click', toggleTheme);

(function initWelcomeBoot(){
  const saved = loadGame();
  if(saved){
    const btn = document.getElementById('btn-continue-life');
    btn.style.display='inline-block';
    btn.textContent = `▶ Continue Your Life (${saved.name}, age ${saved.age})`;
  }
})();
document.getElementById('btn-continue-life').addEventListener('click', ()=>{
  const saved = loadGame();
  if(!saved){ toast("No save found."); return; }
  G = saved;
  activeTab='life';
  switchScreen('screen-game');
  renderAll();
});
document.getElementById('btn-export-save').addEventListener('click', ()=>{
  const raw = G ? JSON.stringify(serializeState(G)) : localStorage.getItem(SAVE_KEY);
  if(!raw){ toast("No save data to export yet."); return; }
  const blob = new Blob([raw], {type:'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  const nameGuess = (()=>{ try{ return JSON.parse(raw).name; }catch(e){ return 'save'; } })();
  a.href = url; a.download = `impilo-yami-${nameGuess||'save'}.json`;
  document.body.appendChild(a); a.click(); document.body.removeChild(a);
  URL.revokeObjectURL(url);
  toast("Save exported!");
});
document.getElementById('btn-import-save').addEventListener('click', ()=>{
  document.getElementById('import-file-input').click();
});
document.getElementById('import-file-input').addEventListener('change', (e)=>{
  const file = e.target.files[0];
  if(!file) return;
  const reader = new FileReader();
  reader.onload = (ev)=>{
    try{
      const obj = JSON.parse(ev.target.result);
      if(!obj || typeof obj.name!=='string') throw new Error('bad save');
      const s = deserializeState(obj);
      G = s;
      saveGame();
      activeTab='life';
      switchScreen('screen-game');
      renderAll();
      toast("Save imported!");
    }catch(err){
      toast("Couldn't read that save file.");
    }
  };
  reader.readAsText(file);
  e.target.value='';
});

document.getElementById('btn-new-life').addEventListener('click', ()=>{
  clearSave();
  document.getElementById('btn-continue-life').style.display='none';
  document.getElementById('btn-new-life').style.display='none';
  const wrap = document.getElementById('loadWrap');
  const fill = document.getElementById('loadFill');
  const text = document.getElementById('loadText');
  wrap.style.display='block';
  const msgs = ["Rolling the dice on your destiny...","Choosing your birthplace...","Packing your baby bag...","Waking up the ancestors...","Loading load shedding schedule...","Almost there..."];
  let pct = 0;
  text.textContent = msgs[0];
  const iv = setInterval(()=>{
    pct += Math.random()*18 + 6;
    if(pct>=100){
      pct=100; fill.style.width='100%';
      clearInterval(iv);
      setTimeout(goToCreate, 400);
      return;
    }
    fill.style.width = pct+'%';
    text.textContent = msgs[Math.min(msgs.length-1, Math.floor(pct/100*msgs.length))];
  }, 220);
});
function goToCreate(){ switchScreen('screen-create'); renderStatPreview(); }

/* ---------------------------------------------------------------------
   SCREEN 2: CHARACTER CREATION
--------------------------------------------------------------------- */
let pendingStats = rollNewbornStats();
document.getElementById('cc-reroll').addEventListener('click', ()=>{ pendingStats = rollNewbornStats(); renderStatPreview(); });
function renderStatPreview(){
  const el = document.getElementById('cc-stat-preview');
  el.innerHTML = Object.entries(pendingStats).map(([k,v])=>`<span class="stat-chip">${cap(k)}: ${v}</span>`).join('');
}
function cap(s){ return s.charAt(0).toUpperCase()+s.slice(1); }

document.querySelectorAll('#cc-gender .choice').forEach(el=>{
  el.addEventListener('click', ()=>{
    document.querySelectorAll('#cc-gender .choice').forEach(c=>c.classList.remove('selected'));
    el.classList.add('selected');
  });
});
document.querySelector('#cc-gender .choice').classList.add('selected');
document.querySelectorAll('#cc-fortune .choice').forEach(el=>{
  el.addEventListener('click', ()=>{
    document.querySelectorAll('#cc-fortune .choice').forEach(c=>c.classList.remove('selected'));
    el.classList.add('selected');
  });
});

document.getElementById('btn-start-life').addEventListener('click', ()=>{
  const name = document.getElementById('cc-name').value.trim();
  if(!name){ toast("Give your character a name first."); return; }
  const gender = document.querySelector('#cc-gender .choice.selected').dataset.val;
  const place = document.getElementById('cc-place').value;
  const fortune = parseInt(document.querySelector('#cc-fortune .choice.selected').dataset.val,10);

  G = freshState();
  G.name = name; G.gender = gender; G.place = place; G.fortune = fortune;
  G.surname = deriveSurname(name);
  G.stats = {...pendingStats};
  G.money = fortune===0?0:(fortune===1?2000:15000);
  G.education.stage = 'toddler';
  generateFamily();
  generateRival();
  pushLog(`${name} was born in ${place}.`);
  switchScreen('screen-game');
  activeTab='life';
  renderAll();
});

function generateFamily(){
  const momGender='Female', dadGender='Male';
  G.family.mother = {name: genFirstName(momGender)+" "+G.surname, relationship:65, alive:true, ageOffset:26+Math.floor(Math.random()*8)};
  G.family.father = {name: genFirstName(dadGender)+" "+G.surname, relationship:60, alive:true, ageOffset:28+Math.floor(Math.random()*8)};
  const siblingCount = Math.floor(Math.random()*4); // 0-3
  G.family.siblings = [];
  for(let i=0;i<siblingCount;i++){
    const g = Math.random()<0.5?'Male':'Female';
    G.family.siblings.push({name: genFirstName(g)+" "+G.surname, gender:g, relationship:55+Math.floor(Math.random()*20), ageOffset: Math.floor(Math.random()*16)-8});
  }

  // Extended family: grandparents (some may already have passed before birth)
  G.family.grandparents = [];
  const grandDefs = [
    {side:'maternal', gender:'Female', label:"Grandmother"}, {side:'maternal', gender:'Male', label:"Grandfather"},
    {side:'paternal', gender:'Female', label:"Grandmother"}, {side:'paternal', gender:'Male', label:"Grandfather"}
  ];
  grandDefs.forEach(def=>{
    const alreadyGone = Math.random()<0.35;
    G.family.grandparents.push({
      name: genFirstName(def.gender)+" "+randOf(SURNAMES), side:def.side, label:def.label,
      relationship: alreadyGone?0:(50+Math.floor(Math.random()*25)),
      alive: !alreadyGone,
      ageOffset: 52+Math.floor(Math.random()*10)
    });
  });

  // Aunts, uncles, and their kids (cousins)
  G.family.auntsUncles = [];
  G.family.cousins = [];
  const auCount = Math.floor(Math.random()*4); // 0-3
  for(let i=0;i<auCount;i++){
    const g = Math.random()<0.5?'Male':'Female';
    const side = Math.random()<0.5?'maternal':'paternal';
    G.family.auntsUncles.push({name: genFirstName(g)+" "+randOf(SURNAMES), gender:g, side, relationship:45+Math.floor(Math.random()*30), ageOffset: Math.floor(Math.random()*20)-10});
    if(Math.random()<0.6){
      const cg = Math.random()<0.5?'Male':'Female';
      G.family.cousins.push({name: genFirstName(cg)+" "+randOf(SURNAMES), gender:cg, relationship:50+Math.floor(Math.random()*25), ageOffset: Math.floor(Math.random()*20)-10});
    }
  }
}
function maybeGenerateStepKids(){
  if(Math.random()<0.2){
    const count = 1+Math.floor(Math.random()*2);
    for(let i=0;i<count;i++){
      const g = Math.random()<0.5?'Male':'Female';
      G.stepChildren.push({name: genFirstName(g)+" "+randOf(SURNAMES), gender:g, age: Math.floor(Math.random()*10)+1});
    }
    pushLog(`${G.spouse.name} brought ${count>1?'children':'a child'} from a previous relationship into the picture.`);
    toast(`👪 You're now a step-parent to ${count} ${count>1?'kids':'kid'}.`);
  }
}
function generateRival(){
  const g = Math.random()<0.5?'Male':'Female';
  G.rival = {name: genFirstName(g)+" "+randOf(SURNAMES), netWorth: Math.round(500+Math.random()*2000), fame: Math.floor(Math.random()*5)};
}
function simulateRivalYearly(){
  if(!G.rival) return;
  const r = G.rival;
  const growth = 1 + (Math.random()*0.28 - 0.08);
  r.netWorth = Math.max(0, Math.round(r.netWorth*growth + Math.random()*3000));
  r.fame = clampStat(r.fame + Math.round(Math.random()*10-4));
  if(Math.random()<0.18){
    const myNW = netWorth();
    if(r.netWorth > myNW*1.5 && myNW>1000){
      pushLog(`Your rival ${r.name} is pulling ahead financially — now worth ${money(r.netWorth)}.`);
    } else if(myNW > r.netWorth*1.5 && r.netWorth>1000){
      pushLog(`You're leaving your rival ${r.name} in the dust financially.`);
    } else if(r.fame > G.fame+20){
      pushLog(`${r.name} is getting a lot more famous than you lately.`);
    } else if(G.fame > r.fame+20){
      pushLog(`You're far more famous than your rival ${r.name} these days.`);
    }
  }
}

/* ---------------------------------------------------------------------
   SCREEN SWITCH / TABS
--------------------------------------------------------------------- */
function switchScreen(id){
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}
let activeTab = 'life';
document.getElementById('tabs').addEventListener('click', (e)=>{
  const t = e.target.closest('.tab');
  if(!t) return;
  activeTab = t.dataset.tab;
  renderAll();
});

/* ---------------------------------------------------------------------
   HUD
--------------------------------------------------------------------- */
function renderHUD(){
  document.getElementById('hud-name').textContent = G.name;
  document.getElementById('hud-age').textContent = `Age ${G.age}`;
  const moneyEl = document.getElementById('hud-money');
  const netWorthLine = G.money - G.debt - G.businessLoan;
  moneyEl.textContent = money(G.money) + ((G.debt>0||G.businessLoan>0) ? `  (debt ${money(G.debt+G.businessLoan)})` : '');
  moneyEl.className = 'money' + (netWorthLine<0?' negative':'');
  document.getElementById('hud-networth').textContent = `Net worth: ${money(netWorth())}`;

  const badges = [];
  if(inPrison()) badges.push({label:`🔒 ${G.prison.yearsLeft}y left`, cls:'warn'});
  if(G.pendingLawsuit) badges.push({label:'⚖️ Lawsuit', cls:'warn'});
  if(G.pendingInterview) badges.push({label:'🎤 Interview', cls:'good'});
  if(G.pendingLifeEvent) badges.push({label:'❗ Decision', cls:'good'});
  if(G.activeWorldEvent) badges.push({label:`🌍 ${G.activeWorldEvent}`, cls:''});
  if(G.vices.alcohol>=60 || G.vices.smoking>=60) badges.push({label:'⚠️ Vices high', cls:'warn'});
  document.getElementById('hud-badges').innerHTML = badges.map(b=>`<span class="hud-badge ${b.cls}">${b.label}</span>`).join('');

  const prisonEl = document.getElementById('hud-prison');
  prisonEl.style.display = 'none';
  ['happiness','health','smarts','looks','connections'].forEach(k=>{
    document.getElementById('bar-'+k).style.width = G.stats[k]+'%';
  });
}

/* ---------------------------------------------------------------------
   MAIN CONTENT ROUTER
--------------------------------------------------------------------- */
function renderAll(){
  if(!G) return;
  renderHUD();
  document.querySelectorAll('.tab').forEach(t=>t.classList.toggle('active', t.dataset.tab===activeTab));
  const c = document.getElementById('content');
  c.innerHTML='';
  if(activeTab==='life') renderLifeTab(c);
  else if(activeTab==='job') renderJobTab(c);
  else if(activeTab==='fame') renderFameTab(c);
  else if(activeTab==='family') renderFamilyTab(c);
  else if(activeTab==='shop') renderShopTab(c);
  else if(activeTab==='biz') renderBizTab(c);
  else if(activeTab==='bank') renderBankTab(c);
  else if(activeTab==='social') renderSocialTab(c);
  else if(activeTab==='phone') renderPhoneTab(c);
  else if(activeTab==='computer') renderComputerTab(c);
  if(G.alive) saveGame();
}

/* ---------- LIFE TAB ---------- */
function eduStageLabel(){
  const e = G.education;
  if(e.stage==='toddler') return 'Not in school yet';
  if(e.stage==='primary') return 'Primary School';
  if(e.stage==='high') return 'High School';
  if(e.stage==='graduated_high') return `Matriculated (${e.matricResult||'Pass'}) — not enrolled in tertiary study`;
  if(e.stage==='dropped_out') return 'Dropped out of school';
  if(e.stage==='university') return `Studying ${e.major} (${e.uniYearsLeft} yr left)`;
  if(e.stage==='graduated_uni') return `Graduated with a degree in ${e.major}`;
  return 'Unknown';
}

function renderLifeTab(c){
  if(inPrison()){
    const p = document.createElement('div');
    p.className='card';
    p.innerHTML = `<div class="card-title">🔒 Behind Bars</div>
      <div class="card-sub">${G.prison.yearsLeft} year(s) remaining on your sentence.</div>
      <div class="card-sub" style="margin-top:6px;">Most of the outside world is off-limits until you're released.</div>`;
    c.appendChild(p);
  }

  if(G.pendingLawsuit){
    const suit = G.pendingLawsuit;
    const lawyerFee = Math.round(suit.amount*0.15);
    const lawsuitCard = document.createElement('div'); lawsuitCard.className='card';
    lawsuitCard.style.borderColor = 'var(--red)';
    lawsuitCard.innerHTML = `<div class="card-title">⚖️ ${suit.plaintiff} is suing you</div>
      <div class="card-sub">Claim amount: ${money(suit.amount)}</div>
      <div class="btn-row">
        <button class="btn small secondary" id="btn-suit-settle">Settle (${money(Math.round(suit.amount*0.5))})</button>
        <button class="btn small" id="btn-suit-lawyer">Hire a Lawyer (${money(lawyerFee)} fee)</button>
        <button class="btn small danger" id="btn-suit-self">Represent Yourself</button>
      </div>`;
    c.appendChild(lawsuitCard);
    document.getElementById('btn-suit-settle').addEventListener('click', ()=>resolveLawsuit('settle'));
    document.getElementById('btn-suit-lawyer').addEventListener('click', ()=>resolveLawsuit('lawyer'));
    document.getElementById('btn-suit-self').addEventListener('click', ()=>resolveLawsuit('self'));
  }

  if(G.pendingInterview){
    const intCard = document.createElement('div'); intCard.className='card';
    intCard.style.borderColor = 'var(--gold)';
    intCard.innerHTML = `<div class="card-title">🎤 A journalist wants an interview</div>
      <div class="card-sub">How do you want to handle it?</div>
      <div class="btn-row">
        <button class="btn small" id="btn-interview-honest">Be Refreshingly Honest</button>
        <button class="btn small secondary" id="btn-interview-safe">Stick to Safe Talking Points</button>
        <button class="btn small danger" id="btn-interview-savage">Go Savage on a Rival</button>
      </div>`;
    c.appendChild(intCard);
    document.getElementById('btn-interview-honest').addEventListener('click', ()=>resolveInterview('honest'));
    document.getElementById('btn-interview-safe').addEventListener('click', ()=>resolveInterview('safe'));
    document.getElementById('btn-interview-savage').addEventListener('click', ()=>resolveInterview('savage'));
  }

  if(G.pendingLifeEvent){
    const ev = EVENT_DECK.find(x=>x.id===G.pendingLifeEvent);
    if(ev){
      const evCard = document.createElement('div'); evCard.className='card';
      evCard.style.borderColor = 'var(--gold)';
      evCard.innerHTML = `<div class="card-title">❗ ${ev.title}</div>
        <div class="card-sub">${ev.prompt}</div>
        <div class="btn-row" style="margin-top:8px;">
          ${ev.choices.map((ch,i)=>`<button class="btn small ${i===0?'':'secondary'}" data-choice="${i}">${ch.label} <span class="card-sub">(${ch.tag})</span></button>`).join('')}
        </div>`;
      c.appendChild(evCard);
      evCard.querySelectorAll('[data-choice]').forEach(btn=>btn.addEventListener('click', ()=>resolveLifeEvent(parseInt(btn.dataset.choice,10))));
    }
  }

  const summary = document.createElement('div');
  summary.className='card';
  let careerLine = G.retired ? `Retired · Pension ${money(G.pension)}/yr` : 'Unemployed';
  if(G.career){
    if(G.career.kind==='normal'){
      const car = CAREERS.find(x=>x.id===G.career.refId);
      careerLine = `${car.levels[G.career.level].title} · ${money(currentCareerSalary(car,G.career.level))}/yr`;
    } else {
      const sp = SPECIAL_CAREERS.find(x=>x.id===G.career.refId);
      careerLine = `${sp.levels[G.career.level]} (${sp.name}) · ${money(specialSalary(sp,G.career.level))}/yr`;
    }
  }
  const fameTier = getFameTier(G.fame);
  const badges = forbesStatus();
  summary.innerHTML = `
    <div class="card-title">${G.name}, age ${G.age}</div>
    <div class="card-sub">${G.gender} · Born in ${G.place}</div>
    <div class="card-sub" style="margin-top:6px;">Career: ${careerLine}</div>
    <div class="card-sub">Education: ${eduStageLabel()}</div>
    <div class="card-sub">Fame: ${fameTier.label} (${G.fame}/100)</div>
    <div class="card-sub">Net worth: ${money(netWorth())}</div>
    <div class="card-sub">Assets: ${G.ownedAssets.length} · Businesses: ${G.ownedBusinesses.length}${G.holdingCompany?' (Holding Co.)':''}</div>
    <div class="card-sub">${G.spouse ? (G.spouse.married?`Married to ${G.spouse.name}`:(G.spouse.engaged?`Engaged to ${G.spouse.name}`:`Dating ${G.spouse.name}`)) : 'Single'} ${G.children.length?(' · '+G.children.length+' child'+(G.children.length>1?'ren':'')):''}</div>
    ${G.criminalRecord?'<div class="card-sub" style="color:#e39a8e;">Has a criminal record</div>':''}
    ${G.driversLicense.full?'<div class="card-sub">🪪 Licensed driver</div>':(G.driversLicense.learners?'<div class="card-sub">🪪 Learner license only</div>':'')}
    ${badges.length?`<div class="card-sub" style="color:var(--gold);margin-top:4px;">🏆 ${badges.join(' · ')}</div>`:''}
    ${G.royal.title?`<div class="card-sub">👑 ${G.royal.title}</div>`:''}
    ${G.achievements.length?`<div class="card-sub" style="margin-top:4px;">🎖️ Trophies (${G.achievements.length}/${ACHIEVEMENTS.length}): ${G.achievements.map(id=>ACHIEVEMENTS.find(a=>a.id===id).name).join(' · ')}</div>`:''}
  `;
  c.appendChild(summary);

  if(G.rival || G.activeWorldEvent){
    const worldCard = document.createElement('div'); worldCard.className='card';
    let wh = '';
    if(G.activeWorldEvent) wh += `<div class="card-title">🌍 ${G.activeWorldEvent}</div><div class="card-sub">${G.worldEventYearsLeft} year(s) remaining</div>`;
    if(G.rival) wh += `<div class="card-sub" style="margin-top:${G.activeWorldEvent?'6px':'0'};">Rival: ${G.rival.name} · Net worth ${money(G.rival.netWorth)} · Fame ${G.rival.fame}/100</div>`;
    worldCard.innerHTML = wh;
    c.appendChild(worldCard);
  }

  if(!inPrison()){
    const actTitle = document.createElement('div'); actTitle.className='section-title'; actTitle.textContent='Activities';
    c.appendChild(actTitle);
    const actCard = document.createElement('div'); actCard.className='card';
    let html = '<div class="btn-row">';
    if(G.age<6){
      html += btnHtml('act_play','🧸 Play', G.usedThisYear.has('act_play'));
      html += btnHtml('act_bond','👪 Bond with Family', G.usedThisYear.has('act_bond'));
    } else if(G.age>=6 && G.age<18){
      html += btnHtml('act_study','📚 Study Hard', G.usedThisYear.has('act_study'));
      if(G.education.stage==='high' && G.education.focus){
        html += btnHtml('extracurricular',`🏅 ${G.education.focus} Extracurricular`, G.usedThisYear.has('extracurricular'));
      }
    }
    if(G.age>=18 && (G.education.stage==='graduated_high') ){
      html += `<button class="btn small" id="btn-enroll-uni">🎓 Enroll in University</button>`;
    }
    html += '</div>';
    actCard.innerHTML = html;
    c.appendChild(actCard);
    wireActButtons(actCard);
    const enrollBtn = document.getElementById('btn-enroll-uni');
    if(enrollBtn) enrollBtn.addEventListener('click', ()=>{ activeTab='life'; showUniPicker(); });

    if(G.education.stage==='high' && !G.education.focus){
      const focusCard = document.createElement('div'); focusCard.className='card';
      focusCard.innerHTML = `<div class="card-title">Choose Your High School Focus</div>
        <div class="card-sub">This shapes your extracurricular activities.</div>
        <div class="btn-row">${['Academics','Sports','Arts','Social'].map(f=>`<button class="btn small secondary" data-focus="${f}">${f}</button>`).join('')}</div>`;
      c.appendChild(focusCard);
      focusCard.querySelectorAll('[data-focus]').forEach(btn=>btn.addEventListener('click', ()=>{
        G.education.focus = btn.dataset.focus;
        pushLog(`Chose to focus on ${btn.dataset.focus} in high school.`);
        renderAll();
      }));
    }

    if(G.age>=17 && !G.driversLicense.full){
      const dlCard = document.createElement('div'); dlCard.className='card';
      if(!G.driversLicense.learners){
        dlCard.innerHTML = `<div class="card-title">🪪 Learner's License (K53)</div>
          <div class="card-sub">Required before you can go for your full license.</div>
          <button class="btn small" id="btn-learners">Take Learner's Test (R250)</button>`;
      } else {
        dlCard.innerHTML = `<div class="card-title">🪪 Driver's License</div>
          <div class="card-sub">You've got your learner's — ready for the real test.</div>
          <button class="btn small" id="btn-full-license">Take Driver's Test (R800)</button>`;
      }
      c.appendChild(dlCard);
      if(document.getElementById('btn-learners')) document.getElementById('btn-learners').addEventListener('click', ()=>{
        if(G.money<250){ toast("Test costs R250."); return; }
        G.money -= 250;
        if(Math.random()<0.55+G.stats.smarts/400){
          G.driversLicense.learners = true;
          pushLog("Passed the learner's license test.");
          toast("🪪 Learner's license earned!");
        } else {
          pushLog("Failed the learner's license test — can retry next year.");
          toast("Didn't pass — try again next year.");
        }
        renderAll();
      });
      if(document.getElementById('btn-full-license')) document.getElementById('btn-full-license').addEventListener('click', ()=>{
        if(G.money<800){ toast("Test costs R800."); return; }
        G.money -= 800;
        if(Math.random()<0.5+G.stats.smarts/400){
          G.driversLicense.full = true;
          pushLog("Passed the driver's license test!");
          toast("🪪 Fully licensed to drive!");
        } else {
          pushLog("Failed the driver's test — can retry next year.");
          toast("Didn't pass — try again next year.");
        }
        renderAll();
      });
    }

    const healthTitle = document.createElement('div'); healthTitle.className='section-title'; healthTitle.textContent='Health & Wellness';
    c.appendChild(healthTitle);
    const healthCard = document.createElement('div'); healthCard.className='card';
    healthCard.innerHTML = `<div class="card-sub">Gym membership: ${G.gymMembership?'Active (R300/mo)':'Not active'}</div>
      <div class="btn-row">
        ${btnHtml('doctor_checkup','🩺 Doctor Checkup (R800)', G.usedThisYear.has('doctor_checkup'))}
        ${btnHtml('mental_health_day','🧘 Take a Mental Health Day', G.usedThisYear.has('mental_health_day'))}
        <button class="btn small secondary" id="btn-gym-toggle">${G.gymMembership?'Cancel Gym':'Join Gym (R3,600/yr)'}</button>
      </div>`;
    c.appendChild(healthCard);
    wireActButtons(healthCard);
    document.getElementById('btn-gym-toggle').addEventListener('click', ()=>{
      G.gymMembership = !G.gymMembership;
      pushLog(G.gymMembership ? "Signed up for a gym membership." : "Cancelled the gym membership.");
      toast(G.gymMembership ? "Gym membership active." : "Membership cancelled.");
      renderAll();
    });

    const hobbyTitle = document.createElement('div'); hobbyTitle.className='section-title'; hobbyTitle.textContent='Hobbies';
    c.appendChild(hobbyTitle);
    const hobbyCard = document.createElement('div'); hobbyCard.className='card';
    if(G.hobby){
      const hb = HOBBIES.find(h=>h.id===G.hobby);
      hobbyCard.innerHTML = `<div class="card-title">${hb.emoji} ${hb.name} <span class="pill">Level ${G.hobbyLevel+1}</span></div>
        <div class="meter"><div class="meter-fill" style="width:${(G.hobbyProgress/3)*100}%;"></div></div>
        <div class="btn-row" style="margin-top:8px;">
          ${btnHtml('hobby_practice','Practice', G.usedThisYear.has('hobby_practice'))}
          <button class="btn small secondary" id="btn-drop-hobby">Try Something New</button>
        </div>`;
    } else {
      hobbyCard.innerHTML = `<div class="card-sub">Pick a hobby to build up over time.</div>
        <div class="btn-row">${HOBBIES.map(h=>`<button class="btn small secondary" data-hobby="${h.id}">${h.emoji} ${h.name}</button>`).join('')}</div>`;
    }
    c.appendChild(hobbyCard);
    wireActButtons(hobbyCard);
    if(document.getElementById('btn-drop-hobby')) document.getElementById('btn-drop-hobby').addEventListener('click', ()=>{
      G.hobby=null; G.hobbyProgress=0; G.hobbyLevel=0; renderAll();
    });
    hobbyCard.querySelectorAll('[data-hobby]').forEach(btn=>btn.addEventListener('click', ()=>{
      G.hobby = btn.dataset.hobby; G.hobbyProgress=0; G.hobbyLevel=0;
      pushLog(`Took up a new hobby: ${HOBBIES.find(h=>h.id===btn.dataset.hobby).name}.`);
      renderAll();
    }));

    const travelTitle = document.createElement('div'); travelTitle.className='section-title'; travelTitle.textContent='Travel';
    c.appendChild(travelTitle);
    const travelUsed = G.usedThisYear.has('travel');
    const domCard = document.createElement('div'); domCard.className='card';
    domCard.innerHTML = `<div class="card-title">Domestic Trips</div>
      <div class="btn-row">${DESTINATIONS_DOMESTIC.map((d,i)=>`<button class="btn small secondary" ${travelUsed?'disabled':''} data-act="travel_dom_${i}">${d.name} (${money(d.cost)})</button>`).join('')}</div>`;
    c.appendChild(domCard);
    wireActButtons(domCard);
    if(!G.passport){
      const passCard = document.createElement('div'); passCard.className='card';
      passCard.innerHTML = `<div class="card-title">🛂 Get a Passport</div><div class="card-sub">Required for international travel.</div>
        <button class="btn small" id="btn-buy-passport">Apply for Passport (R1,500)</button>`;
      c.appendChild(passCard);
      document.getElementById('btn-buy-passport').addEventListener('click', ()=>{
        if(G.money<1500){ toast("Not enough cash."); return; }
        G.money -= 1500; G.passport = true;
        pushLog("Got a passport.");
        toast("Passport issued!");
        renderAll();
      });
    } else {
      const intlCard = document.createElement('div'); intlCard.className='card';
      intlCard.innerHTML = `<div class="card-title">International Trips</div>
        <div class="btn-row">${DESTINATIONS_INTL.map((d,i)=>`<button class="btn small secondary" ${travelUsed?'disabled':''} data-act="travel_intl_${i}">${d.name} (${money(d.cost)})</button>`).join('')}</div>`;
      c.appendChild(intlCard);
      wireActButtons(intlCard);

      const immCard = document.createElement('div'); immCard.className='card';
      if(G.visaStatus==='citizen'){
        immCard.innerHTML = `<div class="card-title">🛃 Emigrate</div>
          <div class="card-sub">Permanently relocate abroad. Costs a visa fee and comes with an adjustment period.</div>
          <div class="btn-row">${EMIGRATION_COUNTRIES.map((ctry,i)=>`<button class="btn small secondary" data-emigrate="${i}">${ctry}</button>`).join('')}</div>`;
      } else {
        immCard.innerHTML = `<div class="card-title">🛃 Living Abroad: ${G.place}</div>
          <div class="card-sub">Visa status: ${G.visaStatus}</div>
          <button class="btn small secondary" id="btn-return-home">Return to South Africa</button>`;
      }
      c.appendChild(immCard);
      immCard.querySelectorAll('[data-emigrate]').forEach(btn=>btn.addEventListener('click', ()=>{
        const ctry = EMIGRATION_COUNTRIES[btn.dataset.emigrate];
        const visaFee = 15000+Math.floor(Math.random()*10000);
        if(G.money<visaFee){ toast(`Visa and relocation costs ${money(visaFee)}.`); return; }
        G.money -= visaFee;
        G.emigrationHistory.push(G.place);
        G.place = ctry;
        G.visaStatus = 'resident';
        G.stats.happiness = clampStat(G.stats.happiness-5);
        G.stats.connections = clampStat(G.stats.connections-4);
        pushLog(`Emigrated to ${ctry}. It's a big adjustment, but a fresh start.`);
        toast(`🛃 Relocated to ${ctry}!`);
        renderAll();
      }));
      const returnBtn = document.getElementById('btn-return-home');
      if(returnBtn) returnBtn.addEventListener('click', ()=>{
        G.place = G.emigrationHistory.length ? G.emigrationHistory[0] : 'Soweto, Johannesburg';
        G.visaStatus = 'citizen';
        G.stats.happiness = clampStat(G.stats.happiness+5);
        pushLog(`Moved back home to ${G.place}.`);
        toast("🇿🇦 Welcome home!");
        renderAll();
      });
    }

    if(isAdult()){
      const nightTitle = document.createElement('div'); nightTitle.className='section-title'; nightTitle.textContent='Nightlife (18+)';
      c.appendChild(nightTitle);
      const nightCard = document.createElement('div'); nightCard.className='card';
      let nh = `<div class="card-sub">Vices — Alcohol: ${G.vices.alcohol}/100 · Smoking: ${G.vices.smoking}/100</div><div class="btn-row">`;
      nh += btnHtml('act_club','🎉 Go Clubbing (R500)', G.usedThisYear.has('act_club'));
      nh += btnHtml('act_casino','🎰 Casino Night (R2,000)', G.usedThisYear.has('act_casino'));
      nh += btnHtml('act_drink','🍺 Have a Drink', G.usedThisYear.has('act_drink'));
      nh += btnHtml('act_smoke','🚬 Light a Cigarette', G.usedThisYear.has('act_smoke'));
      if(G.vices.alcohol>=50 || G.vices.smoking>=50){
        nh += `<button class="btn small danger" id="btn-rehab">🏥 Rehab (R15,000)</button>`;
      }
      nh += '</div>';
      nightCard.innerHTML = nh;
      c.appendChild(nightCard);
      wireActButtons(nightCard);
      const rehabBtn = document.getElementById('btn-rehab');
      if(rehabBtn) rehabBtn.addEventListener('click', doRehab);
    }
  }

  const title = document.createElement('div');
  title.className='section-title'; title.textContent='Life Story';
  c.appendChild(title);
  if(G.log.length===0){
    const e = document.createElement('div'); e.className='empty-note'; e.textContent='Your story starts the moment you age up.';
    c.appendChild(e);
  } else {
    let lastAge = null;
    G.log.slice(0,80).forEach(entry=>{
      if(entry.age!==lastAge){
        const header = document.createElement('div');
        header.className='card-sub'; header.style.color='var(--gold)'; header.style.marginTop='10px'; header.style.fontWeight='700';
        header.textContent = `— Age ${entry.age} —`;
        c.appendChild(header);
        lastAge = entry.age;
      }
      const d = document.createElement('div');
      d.className='log-entry';
      d.textContent = entry.text;
      c.appendChild(d);
    });
  }
}
function btnHtml(id,label,used){
  return `<button class="btn small ${used?'secondary':''}" ${used?'disabled':''} data-act="${id}">${used?label+' ✓':label}</button>`;
}
function wireActButtons(scope){
  scope.querySelectorAll('[data-act]').forEach(btn=>{
    btn.addEventListener('click', ()=>doActivity(btn.dataset.act));
  });
}
function doActivity(id){
  if(G.usedThisYear.has(id)) return;
  switch(id){
    case 'act_play':
      G.usedThisYear.add(id);
      G.stats.happiness = clampStat(G.stats.happiness+4);
      G.stats.health = clampStat(G.stats.health+1);
      pushLog("Played and had a carefree day.");
      toast("+4 Happiness");
      break;
    case 'act_bond':
      G.usedThisYear.add(id);
      G.stats.connections = clampStat(G.stats.connections+3);
      pushLog("Spent quality time bonding with family.");
      toast("+3 Connections");
      break;
    case 'act_study': {
      G.usedThisYear.add(id);
      const gain = 3+Math.floor(Math.random()*4);
      G.stats.smarts = clampStat(G.stats.smarts+gain);
      pushLog(`Studied hard and grew smarter (+${gain} Smarts).`);
      toast(`+${gain} Smarts`);
      break;
    }
    case 'act_club': {
      if(G.money<500){ toast("Not enough cash for a night out."); return; }
      G.usedThisYear.add(id);
      G.money -= 500;
      G.stats.happiness = clampStat(G.stats.happiness+5);
      G.stats.connections = clampStat(G.stats.connections+2);
      G.stats.health = clampStat(G.stats.health-2);
      G.vices.alcohol = clampStat(G.vices.alcohol+6);
      pushLog("Went clubbing with friends.");
      toast("+5 Happiness, +2 Connections");
      const ownsCar = G.ownedAssets.some(id2=>{ const it = SHOP.find(s=>s.id===id2); return it && it.cat==='Vehicles'; });
      if(ownsCar && G.vices.alcohol>=50 && Math.random()<0.25){
        G.dui++;
        const fine = Math.round(2000+Math.random()*8000);
        G.money -= fine;
        G.stats.happiness = clampStat(G.stats.happiness-10);
        if(G.driversLicense.full && Math.random()<0.4){
          G.driversLicense.full = false;
          pushLog(`Drove home drunk, got pulled over, and lost your license — fined ${money(fine)}.`);
          toast("🚨 DUI! License suspended.");
        } else {
          G.stats.health = clampStat(G.stats.health-6);
          pushLog(`Drove home drunk and crashed — fined ${money(fine)} and got hurt.`);
          toast("🚨 DUI crash! That was dangerous.");
        }
      }
      break;
    }
    case 'act_casino': {
      if(G.money<2000){ toast("Not enough cash for the casino."); return; }
      G.usedThisYear.add(id);
      G.money -= 2000;
      if(Math.random()<0.45){
        const win = Math.round(2000+Math.random()*4000);
        G.money += win;
        pushLog(`Hit a win at the casino! +${money(win)}.`);
        toast(`🎰 Won ${money(win)}!`);
      } else {
        G.stats.happiness = clampStat(G.stats.happiness-2);
        pushLog("Lost big at the casino.");
        toast("Lost your R2,000 stake.");
      }
      break;
    }
    case 'act_drink':
      G.usedThisYear.add(id);
      G.stats.happiness = clampStat(G.stats.happiness+3);
      G.vices.alcohol = clampStat(G.vices.alcohol+8);
      pushLog("Had a few drinks with friends.");
      toast("+3 Happiness");
      break;
    case 'act_smoke':
      G.usedThisYear.add(id);
      G.stats.happiness = clampStat(G.stats.happiness+2);
      G.vices.smoking = clampStat(G.vices.smoking+10);
      G.stats.health = clampStat(G.stats.health-2);
      pushLog("Smoked to unwind.");
      toast("+2 Happiness, -2 Health");
      break;
    case 'engage_fans':
      G.usedThisYear.add(id);
      G.stats.connections = clampStat(G.stats.connections+3);
      G.stats.happiness = clampStat(G.stats.happiness+2);
      pushLog("Spent time replying to fans and comments.");
      toast("+3 Connections, +2 Happiness");
      break;
    case 'collab': {
      G.usedThisYear.add(id);
      const chance = 0.3 + G.fame/200 + G.stats.connections/300;
      if(Math.random()<chance && G.phone){
        const plat = SOCIAL_PLATFORMS[Math.floor(Math.random()*SOCIAL_PLATFORMS.length)];
        const gain = Math.round(800+Math.random()*4000*(1+G.fame/100));
        G.social[plat.id] += gain;
        G.followerGainThisYear = (G.followerGainThisYear||0) + gain;
        pushLog(`Collaborated with another creator — gained ${gain} followers on ${plat.name}.`);
        toast(`🤝 Collab worked! +${gain} followers`);
      } else {
        pushLog("Reached out for a collab but nobody bit this year.");
        toast("No collab partners this time.");
      }
      break;
    }
    case 'livestream': {
      G.usedThisYear.add(id);
      const power = (G.stats.looks + G.stats.smarts + G.stats.connections)/3;
      let total = 0;
      SOCIAL_PLATFORMS.forEach(p=>{
        const gain = Math.round((power/100)*(100+Math.random()*400));
        G.social[p.id] += gain;
        total += gain;
      });
      G.followerGainThisYear = (G.followerGainThisYear||0) + total;
      if(Math.random()<0.12){
        G.stats.happiness = clampStat(G.stats.happiness-6);
        pushLog("Went live and said something you regret — clips are already spreading.");
        toast("😬 That livestream did not go well.");
      } else {
        G.stats.happiness = clampStat(G.stats.happiness+3);
        pushLog(`Went live across all your platforms — gained ${total} followers combined.`);
        toast(`🔴 Livestream! +${total} followers total`);
      }
      break;
    }
    case 'extracurricular': {
      G.usedThisYear.add(id);
      const focus = G.education.focus;
      if(focus==='Academics'){ G.stats.smarts=clampStat(G.stats.smarts+5); toast("+5 Smarts"); }
      else if(focus==='Sports'){ G.stats.health=clampStat(G.stats.health+4); G.stats.connections=clampStat(G.stats.connections+2); toast("+4 Health, +2 Connections"); }
      else if(focus==='Arts'){ G.stats.looks=clampStat(G.stats.looks+4); G.stats.happiness=clampStat(G.stats.happiness+2); toast("+4 Looks, +2 Happiness"); }
      else if(focus==='Social'){ G.stats.connections=clampStat(G.stats.connections+5); G.stats.happiness=clampStat(G.stats.happiness+2); toast("+5 Connections, +2 Happiness"); }
      pushLog(`Took part in a ${focus} extracurricular activity.`);
      break;
    }
    case 'doctor_checkup':
      if(G.money<800){ toast("A checkup costs R800."); return; }
      G.usedThisYear.add(id);
      G.money -= 800;
      G.stats.health = clampStat(G.stats.health+3);
      pushLog("Went for a doctor's checkup.");
      toast("+3 Health");
      break;
    case 'mental_health_day':
      G.usedThisYear.add(id);
      G.stats.happiness = clampStat(G.stats.happiness+6);
      G.stats.health = clampStat(G.stats.health+1);
      pushLog("Took a day for yourself to rest and recharge.");
      toast("+6 Happiness");
      break;
    case 'hobby_practice': {
      G.usedThisYear.add(id);
      const hb = HOBBIES.find(h=>h.id===G.hobby);
      const boost = 2+G.hobbyLevel;
      if(hb.stat==='happiness') G.stats.happiness = clampStat(G.stats.happiness+boost+2);
      else G.stats[hb.stat] = clampStat(G.stats[hb.stat]+boost);
      G.hobbyProgress++;
      if(G.hobbyProgress>=3 && G.hobbyLevel<3){ G.hobbyLevel++; G.hobbyProgress=0; toast(`${hb.name} levelled up! (Level ${G.hobbyLevel+1})`); }
      pushLog(`Practiced ${hb.name}.`);
      toast(`+${boost} ${cap(hb.stat)}`);
      break;
    }
    case 'practice': {
      G.usedThisYear.add(id);
      if(G.career && G.career.kind==='special'){
        const sp = SPECIAL_CAREERS.find(x=>x.id===G.career.refId);
        const gain = 2+Math.floor(Math.random()*3);
        G.stats[sp.stat] = clampStat(G.stats[sp.stat]+gain);
        pushLog(`Trained hard to sharpen your ${sp.name.toLowerCase()} skills.`);
        toast(`+${gain} ${cap(sp.stat)}`);
      }
      break;
    }
    case 'impress_boss': {
      if(!G.boss) return;
      G.usedThisYear.add(id);
      const gain = 4+Math.floor(Math.random()*6);
      G.boss.relationship = clampStat(G.boss.relationship+gain);
      pushLog(`Went out of your way to impress ${G.boss.name}.`);
      toast(`+${gain} Boss Relationship`);
      break;
    }
    case 'negotiate_raise': {
      if(!G.boss || !G.career) return;
      G.usedThisYear.add(id);
      const chance = 0.25 + G.boss.relationship/250 + G.stats.smarts/400;
      if(Math.random() < Math.min(0.75, chance)){
        G.raiseMultiplier = (G.raiseMultiplier||1) * 1.08;
        pushLog(`Negotiated a raise with ${G.boss.name}!`);
        toast("💰 8% raise secured!");
      } else {
        G.boss.relationship = clampStat(G.boss.relationship-5);
        pushLog(`Asked ${G.boss.name} for a raise, but got turned down.`);
        toast("Raise denied — try again next year.");
      }
      break;
    }
    case 'acquire_competitor': {
      G.usedThisYear.add(id);
      const unowned = BIZ.filter(b=>!G.ownedBusinesses.includes(b.id));
      if(!unowned.length) return;
      const target = randOf(unowned);
      const price = Math.round(target.price*0.7);
      if(G.money<price){ toast(`You'd need ${money(price)} for this takeover.`); return; }
      G.money -= price;
      G.ownedBusinesses.push(target.id);
      pushLog(`Pulled off a hostile takeover of a ${target.name} for ${money(price)} — 30% below market.`);
      toast(`🤝 Acquired a ${target.name}!`);
      break;
    }
  }
  if(id.startsWith('travel_')){
    if(G.usedThisYear.has('travel')){ toast("You've already travelled this year."); return; }
    const list = id.startsWith('travel_intl_') ? DESTINATIONS_INTL : DESTINATIONS_DOMESTIC;
    const idx = parseInt(id.split('_').pop(),10);
    const dest = list[idx];
    const cost = G.rideDiscount ? Math.round(dest.cost*0.8) : dest.cost;
    if(G.money<cost){ toast("Not enough cash for this trip."); return; }
    G.usedThisYear.add('travel');
    G.money -= cost;
    const discounted = G.rideDiscount;
    G.rideDiscount = false;
    G.stats.happiness = clampStat(G.stats.happiness+(dest.h||0));
    if(dest.c) G.stats.connections = clampStat(G.stats.connections+dest.c);
    if(dest.health) G.stats.health = clampStat(G.stats.health+dest.health);
    if(dest.looks) G.stats.looks = clampStat(G.stats.looks+dest.looks);
    pushLog(`Travelled to ${dest.name}${discounted?' (discounted ride booked)':''}.`);
    toast(`✈️ Trip to ${dest.name}!`);
    renderAll();
    return;
  }
  renderAll();
}
function doRehab(){
  if(G.money<15000){ toast("Rehab costs R15,000 — come back when you can afford it."); return; }
  G.money -= 15000;
  G.vices.alcohol = Math.min(G.vices.alcohol, 15);
  G.vices.smoking = Math.min(G.vices.smoking, 15);
  G.stats.health = clampStat(G.stats.health+8);
  G.stats.happiness = clampStat(G.stats.happiness+5);
  pushLog("Completed a stint in rehab and came out healthier.");
  toast("Rehab complete — vices under control.");
  renderAll();
}

function showUniPicker(){
  const c = document.getElementById('content');
  c.innerHTML='';
  const card = document.createElement('div'); card.className='card';
  card.innerHTML = `<div class="card-title">Choose a Major</div><div class="card-sub">Pay tuition upfront or take out an NSFAS-style loan.</div>`;
  c.appendChild(card);
  MAJORS.forEach((m,i)=>{
    const row = document.createElement('div'); row.className='card';
    row.innerHTML = `<div class="row-between"><div><div class="card-title">${m.name}</div><div class="card-sub">Tuition: ${money(m.tuition)}</div></div>
      <div class="btn-row">
        <button class="btn small" data-pay="${i}">Pay Cash</button>
        <button class="btn small secondary" data-loan="${i}">NSFAS Loan</button>
      </div></div>`;
    c.appendChild(row);
  });
  const back = document.createElement('button'); back.className='btn secondary small'; back.textContent='Back'; back.style.marginTop='8px';
  back.addEventListener('click', renderAll);
  c.appendChild(back);
  c.querySelectorAll('[data-pay]').forEach(btn=>btn.addEventListener('click', ()=>{
    const m = MAJORS[btn.dataset.pay];
    if(G.money<m.tuition){ toast("Not enough cash — try the NSFAS loan."); return; }
    G.money -= m.tuition;
    enrollUniversity(m);
  }));
  c.querySelectorAll('[data-loan]').forEach(btn=>btn.addEventListener('click', ()=>{
    const m = MAJORS[btn.dataset.loan];
    G.debt += m.tuition;
    pushLog(`Took an NSFAS-style loan of ${money(m.tuition)} for tuition.`);
    enrollUniversity(m);
  }));
}
function enrollUniversity(m){
  G.education.stage='university'; G.education.major=m.name; G.education.uniYearsLeft=3;
  pushLog(`Enrolled in university to study ${m.name}.`);
  toast(`Enrolled in ${m.name}!`);
  renderAll();
}

/* ---------- JOB TAB ---------- */
function reqSmartsAdjusted(car){
  let req = car.reqSmarts;
  if(car.reqSmarts>=DEGREE_THRESHOLD){
    if(G.education.hasDegree) req = Math.max(20, req-15);
    if(G.education.matric===false) req += 15;
  }
  if(G.criminalRecord && car.reqSmarts>=DEGREE_THRESHOLD) req += 20;
  return req;
}
function renderJobTab(c){
  if(inPrison()){ c.innerHTML = lockedHtml("You can't work a job from behind bars."); return; }
  const cur = document.createElement('div'); cur.className='card';
  if(G.retired){
    cur.innerHTML = `<div class="card-title">Retired</div><div class="card-sub">Enjoying your pension of ${money(G.pension)}/yr.</div>`;
  } else if(G.career && G.career.kind==='normal'){
    const car = CAREERS.find(x=>x.id===G.career.refId);
    const lvl = G.career.level;
    const needsExam = needsBoardExam(car) && lvl===1 && !G.boardExamsPassed[car.id];
    cur.innerHTML = `
      <div class="card-title">${car.levels[lvl].title}</div>
      <div class="card-sub">${car.name} track · Level ${lvl+1} of 5 · ${money(currentCareerSalary(car,lvl))}/yr</div>
      ${G.boss?`<div class="card-sub">Boss: ${G.boss.name} · Relationship ${G.boss.relationship}%</div>`:''}
      <div class="btn-row">
      ${needsExam ? `<button class="btn small" id="btn-board-exam">📝 Take Board Exam</button>` :
        (lvl<4 ? `<button class="btn small" id="btn-promo">📈 Work hard for a promotion</button>` : `<span class="pill">Top of the ladder</span>`)}
      ${btnHtml('impress_boss','🤝 Impress Your Boss', G.usedThisYear.has('impress_boss'))}
      ${btnHtml('negotiate_raise','💰 Negotiate a Raise', G.usedThisYear.has('negotiate_raise'))}
      <button class="btn secondary small" id="btn-quit">Quit job</button>
      ${G.age>=60 ? `<button class="btn small" id="btn-retire">🌴 Retire</button>` : ''}
      </div>`;
  } else {
    cur.innerHTML = `<div class="card-title">Currently unemployed</div><div class="card-sub">Apply below — you need enough Smarts to qualify. A matric or degree helps with professional roles.</div>`;
  }
  c.appendChild(cur);
  if(document.getElementById('btn-promo')) document.getElementById('btn-promo').onclick = tryNormalPromotion;
  if(document.getElementById('btn-board-exam')) document.getElementById('btn-board-exam').onclick = tryBoardExam;
  if(document.getElementById('btn-quit')) document.getElementById('btn-quit').onclick = ()=>{ G.career=null; G.boss=null; renderAll(); };
  wireActButtons(cur);
  if(document.getElementById('btn-retire')) document.getElementById('btn-retire').onclick = ()=>{
    const car = CAREERS.find(x=>x.id===G.career.refId);
    G.pension = Math.round(currentCareerSalary(car,G.career.level)*0.25);
    G.career=null; G.retired=true;
    pushLog(`Retired with a pension of ${money(G.pension)}/yr.`);
    toast("You've retired!");
    renderAll();
  };

  if(G.age>=16){
    const shTitle = document.createElement('div'); shTitle.className='section-title'; shTitle.textContent='Side Hustle';
    c.appendChild(shTitle);
    const shCard = document.createElement('div'); shCard.className='card';
    if(G.sideHustle){
      const sh = SIDE_HUSTLES.find(x=>x.id===G.sideHustle);
      shCard.innerHTML = `<div class="card-title">${sh.name}</div>
        <div class="card-sub">Extra income: ${money(sideHustleIncome(sh.id))}/yr (stacks with your main career)</div>
        <button class="btn secondary small" style="margin-top:8px;" id="btn-drop-hustle">Stop hustling</button>`;
    } else {
      shCard.innerHTML = `<div class="card-sub">Pick a side hustle to earn extra cash alongside your main career.</div>
        <div class="btn-row">${SIDE_HUSTLES.map(sh=>`<button class="btn small secondary" data-hustle="${sh.id}">${sh.name} (~${money(sideHustleIncome(sh.id))}/yr)</button>`).join('')}</div>`;
    }
    c.appendChild(shCard);
    if(document.getElementById('btn-drop-hustle')) document.getElementById('btn-drop-hustle').addEventListener('click', ()=>{
      pushLog(`Stopped hustling on the side.`);
      G.sideHustle=null; renderAll();
    });
    shCard.querySelectorAll('[data-hustle]').forEach(btn=>btn.addEventListener('click', ()=>{
      G.sideHustle = btn.dataset.hustle;
      const sh = SIDE_HUSTLES.find(x=>x.id===btn.dataset.hustle);
      pushLog(`Started a side hustle: ${sh.name}.`);
      toast(`Side hustle: ${sh.name}`);
      renderAll();
    }));
  }

  const title = document.createElement('div');
  title.className='section-title'; title.textContent=`Job Board (${CAREERS.length} careers)`;
  c.appendChild(title);

  CAREERS.forEach(car=>{
    const req = reqSmartsAdjusted(car);
    const qualifies = G.age>=18 && G.stats.smarts>=req && !G.retired;
    const isCurrent = G.career && G.career.kind==='normal' && G.career.refId===car.id;
    const row = document.createElement('div');
    row.className='card';
    row.innerHTML = `
      <div class="row-between">
        <div>
          <div class="card-title">${car.name}</div>
          <div class="card-sub">Entry: ${money(car.levels[0].salary)}/yr · Requires ${req} Smarts</div>
        </div>
        <button class="btn small ${isCurrent?'secondary':''}" ${isCurrent||!qualifies?'disabled':''} data-apply="${car.id}">
          ${isCurrent?'Current':'Apply'}
        </button>
      </div>`;
    c.appendChild(row);
  });
  c.querySelectorAll('[data-apply]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      G.retired=false;
      G.career = {kind:'normal', refId:btn.dataset.apply, level:0};
      G.raiseMultiplier = 1;
      const car = CAREERS.find(x=>x.id===btn.dataset.apply);
      const bossGender = Math.random()<0.5?'Male':'Female';
      G.boss = {name: genFirstName(bossGender)+" "+randOf(SURNAMES), relationship: 45+Math.floor(Math.random()*20)};
      pushLog(`Got a job as a ${car.levels[0].title}. Your new boss is ${G.boss.name}.`);
      toast(`You're now a ${car.levels[0].title}!`);
      renderAll();
    });
  });
}
function tryNormalPromotion(){
  if(G.promotedThisYear){ toast("You already tried this year — wait for your next birthday."); return; }
  const car = CAREERS.find(x=>x.id===G.career.refId);
  const lvl = G.career.level;
  if(lvl>=4) return;
  G.promotedThisYear = true;
  const bossBonus = G.boss ? (G.boss.relationship-50)/300 : 0;
  const chance = 0.25 + (G.stats.smarts - car.reqSmarts)/150 + bossBonus + Math.random()*0.15;
  if(Math.random() < Math.max(0.1, Math.min(0.85, chance))){
    G.career.level++;
    pushLog(`Promoted to ${car.levels[G.career.level].title}!`);
    toast(`🎉 Promoted to ${car.levels[G.career.level].title}!`);
  } else {
    G.stats.happiness = clampStat(G.stats.happiness-2);
    pushLog(`Tried for a promotion at work but got passed over.`);
    toast("No promotion this time. Keep grinding.");
  }
  renderAll();
}
function tryBoardExam(){
  if(G.promotedThisYear){ toast("You already tried something career-related this year."); return; }
  const car = CAREERS.find(x=>x.id===G.career.refId);
  G.promotedThisYear = true;
  const chance = 0.35 + (G.stats.smarts-60)/150;
  if(Math.random() < Math.max(0.15, Math.min(0.9, chance))){
    G.boardExamsPassed[car.id] = true;
    pushLog(`Passed the board exam for ${car.name}!`);
    toast("📝 Board exam passed!");
  } else {
    G.stats.happiness = clampStat(G.stats.happiness-3);
    pushLog(`Failed the board exam for ${car.name} — can try again next year.`);
    toast("Didn't pass this time. Study up and retry next year.");
  }
  renderAll();
}

/* ---------- FAME TAB (fame meter + 10 special careers) ---------- */
function specialSalary(sp, level){ return Math.round(sp.base*sp.mult[level]/100)*100; }
function currentCareerSalary(car, level){ return Math.round(car.levels[level].salary * (G.raiseMultiplier||1)); }
function renderFameTab(c){
  if(inPrison()){ c.innerHTML = lockedHtml("Fame will have to wait until you're released."); return; }
  const tier = getFameTier(G.fame);
  const fameCard = document.createElement('div'); fameCard.className='card';
  fameCard.innerHTML = `
    <div class="card-title">Fame: ${tier.label}</div>
    <div class="meter"><div class="meter-fill fill-fame" style="width:${G.fame}%;"></div></div>
    <div class="card-sub" style="margin-top:8px;">${fameTierPerksText(tier.label)}</div>
  `;
  c.appendChild(fameCard);

  const cur = document.createElement('div'); cur.className='card';
  if(G.career && G.career.kind==='special'){
    const sp = SPECIAL_CAREERS.find(x=>x.id===G.career.refId);
    const lvl = G.career.level;
    const isPolitician = sp.id==='sp5';
    cur.innerHTML = `
      <div class="card-title">${sp.levels[lvl]}${G.isPresident?' 🇿🇦':''}</div>
      <div class="card-sub">${sp.name} path · Level ${lvl+1} of 5 · ${money(specialSalary(sp,lvl))}/yr</div>
      ${isPolitician?`<div class="card-sub">Election record: ${G.electionsWon} won · ${G.electionsLost} lost</div>`:''}
      <div class="btn-row">
      ${lvl<4 ? (isPolitician
          ? `<button class="btn small" id="btn-spec-promo">🗳️ Run for Election (campaign costs ${money((lvl+1)*15000)})</button>`
          : `<button class="btn small" id="btn-spec-promo">🌟 Chase your big break (costs ${money((lvl+1)*5000)})</button>`)
        : `<span class="pill">Reached the top</span>`}
      ${btnHtml('practice','💪 Practice/Train', G.usedThisYear.has('practice'))}
      <button class="btn secondary small" id="btn-spec-quit">Walk away</button>
      </div>`;
  } else {
    cur.innerHTML = `<div class="card-title">No fame path chosen</div><div class="card-sub">Pick a special career below. These pay based on fame, not corporate ladders — high risk, high reward.</div>`;
  }
  c.appendChild(cur);
  if(document.getElementById('btn-spec-promo')) document.getElementById('btn-spec-promo').onclick = trySpecialPromotion;
  if(document.getElementById('btn-spec-quit')) document.getElementById('btn-spec-quit').onclick = ()=>{ G.career=null; renderAll(); };
  wireActButtons(cur);

  if(G.fame>=10){
    const brandTitle = document.createElement('div'); brandTitle.className='section-title'; brandTitle.textContent='Personal Brand';
    c.appendChild(brandTitle);

    const agentCard = document.createElement('div'); agentCard.className='card';
    agentCard.innerHTML = `<div class="card-title">Agent / Manager</div>
      <div class="card-sub">Takes a 10% cut of your special-career income, but boosts your odds of a breakthrough.</div>
      <button class="btn small ${G.agent?'secondary':''}" id="btn-agent">${G.agent?'Drop Your Agent':'Hire an Agent (R20,000/yr)'}</button>`;
    c.appendChild(agentCard);
    document.getElementById('btn-agent').addEventListener('click', ()=>{
      G.agent = !G.agent;
      pushLog(G.agent ? "Hired an agent to manage your career." : "Parted ways with your agent.");
      toast(G.agent ? "Agent hired." : "Agent gone.");
      renderAll();
    });

    if(G.ambassadorDeals.length){
      const dealsCard = document.createElement('div'); dealsCard.className='card';
      dealsCard.innerHTML = `<div class="card-title">Active Ambassador Deals</div>` +
        G.ambassadorDeals.map(d=>`<div class="card-sub">${d.brand} · ${money(d.payout)}/yr · ${d.yearsLeft} yr(s) left</div>`).join('');
      c.appendChild(dealsCard);
    }
    if(G.ambassadorDeals.length<2){
      const signCard = document.createElement('div'); signCard.className='card';
      signCard.innerHTML = `<div class="card-title">Sign an Ambassador Deal</div>
        <div class="card-sub">A multi-year brand partnership paying out every year of the contract.</div>
        <button class="btn small" id="btn-sign-ambassador">Sign a Deal</button>`;
      c.appendChild(signCard);
      document.getElementById('btn-sign-ambassador').addEventListener('click', ()=>{
        const brand = randOf(AMBASSADOR_BRANDS);
        const years = 2+Math.floor(Math.random()*3);
        const payout = Math.round(5000*(1+G.fame/20));
        G.ambassadorDeals.push({brand, yearsLeft:years, payout});
        pushLog(`Signed a ${years}-year ambassador deal with ${brand}.`);
        toast(`🤝 Signed with ${brand}!`);
        renderAll();
      });
    }

    if(G.fame>=45){
      const merchCard = document.createElement('div'); merchCard.className='card';
      if(G.merchLine.active){
        merchCard.innerHTML = `<div class="card-title">Your Merch Line</div><div class="card-sub">Earning ${money(G.merchLine.income)}/yr passively.</div>`;
        c.appendChild(merchCard);
      } else {
        const cost = Math.round(30000*(1+G.fame/100));
        merchCard.innerHTML = `<div class="card-title">Launch Your Own Merch Line</div>
          <div class="card-sub">One-time investment: ${money(cost)}. Scales with your Fame.</div>
          <button class="btn small" id="btn-launch-merch">Launch Merch Line</button>`;
        c.appendChild(merchCard);
        document.getElementById('btn-launch-merch').addEventListener('click', ()=>{
          if(G.money<cost){ toast("Not enough capital yet."); return; }
          G.money -= cost;
          G.merchLine = {active:true, income: Math.round(cost*0.35)};
          pushLog("Launched your own merch line.");
          toast("👕 Merch line launched!");
          renderAll();
        });
      }
    }
  }

  const legalTitle = document.createElement('div'); legalTitle.className='section-title'; legalTitle.textContent='Legal';
  c.appendChild(legalTitle);
  const legalCard = document.createElement('div'); legalCard.className='card';
  legalCard.innerHTML = `<div class="card-title">File a Lawsuit</div>
    <div class="card-sub">Sue someone for damages — R5,000 filing fee, payout if you win.</div>
    ${btnHtml('sue_someone','⚖️ Sue Someone', G.usedThisYear.has('sue_someone'))}`;
  c.appendChild(legalCard);
  legalCard.querySelectorAll('[data-act="sue_someone"]').forEach(btn=>btn.addEventListener('click', trySueSomeone));

  const title = document.createElement('div');
  title.className='section-title'; title.textContent=`Special Careers (${SPECIAL_CAREERS.length})`;
  c.appendChild(title);
  SPECIAL_CAREERS.forEach(sp=>{
    const isCurrent = G.career && G.career.kind==='special' && G.career.refId===sp.id;
    const row = document.createElement('div'); row.className='card';
    row.innerHTML = `
      <div class="row-between">
        <div>
          <div class="card-title">${sp.name} ${sp.risky?'<span class="pill warn">risky</span>':''}</div>
          <div class="card-sub">Start: ${sp.levels[0]} · ${money(specialSalary(sp,0))}/yr · Driven by ${cap(sp.stat)}</div>
        </div>
        <button class="btn small ${isCurrent?'secondary':''}" ${isCurrent?'disabled':''} data-start="${sp.id}">${isCurrent?'Current':'Pursue'}</button>
      </div>`;
    c.appendChild(row);
  });
  c.querySelectorAll('[data-start]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      G.retired=false;
      G.career = {kind:'special', refId:btn.dataset.start, level:0};
      const sp = SPECIAL_CAREERS.find(x=>x.id===btn.dataset.start);
      pushLog(`Started chasing fame as a ${sp.levels[0]}.`);
      toast(`You're now a ${sp.levels[0]}!`);
      renderAll();
    });
  });
}
function fameTierPerksText(label){
  switch(label){
    case "Unknown": return "Nobody recognizes you yet — keep building.";
    case "Local Name": return "A few people around town know your name.";
    case "Rising Star": return "Brand deals are starting to come in.";
    case "Celebrity": return "Security upkeep now costs you yearly — the price of being recognized. Paparazzi are a real risk.";
    case "National Icon": return "You're a household name. Award nominations, bigger brand deals, and heavier security costs.";
    case "Global Superstar": return "Global recognition brings passive residual income, huge deals — but scandals and paparazzi hit hardest here.";
  }
  return "";
}
function trySpecialPromotion(){
  if(G.promotedThisYear){ toast("You already tried this year — wait for your next birthday."); return; }
  const sp = SPECIAL_CAREERS.find(x=>x.id===G.career.refId);
  const lvl = G.career.level;
  if(lvl>=4) return;
  if(sp.id==='sp5'){ runElection(sp, lvl); return; }
  const cost = (lvl+1)*5000;
  if(G.money < cost){ toast(`You need ${money(cost)} to chase this break.`); return; }
  G.promotedThisYear = true;
  G.money -= cost;
  const statVal = G.stats[sp.stat];
  const chance = 0.15 + statVal/220 + G.stats.connections/300 + (G.agent?0.1:0);
  if(Math.random() < Math.max(0.08, Math.min(0.75, chance))){
    G.career.level++;
    pushLog(`Big break! Now a ${sp.levels[G.career.level]}.`);
    toast(`🌟 Breakthrough! You're now a ${sp.levels[G.career.level]}!`);
  } else {
    pushLog(`Tried to break into the next level as a ${sp.name.toLowerCase()} but it didn't pan out.`);
    toast("Didn't work out this time — the spotlight's fickle.");
    if(sp.risky){
      if(Math.random()<0.20){
        G.prison.active = true; G.prison.yearsLeft = 1+Math.floor(Math.random()*4);
        G.criminalRecord = true; G.career = null;
        G.stats.happiness = clampStat(G.stats.happiness-15);
        G.stats.connections = clampStat(G.stats.connections-5);
        pushLog(`Busted by the police — sentenced to ${G.prison.yearsLeft} years.`);
        toast("🚓 You've been arrested!");
      } else if(Math.random()<0.35){
        const loss = Math.round(G.money*0.2);
        G.money -= loss;
        G.stats.happiness = clampStat(G.stats.happiness-8);
        G.stats.health = clampStat(G.stats.health-5);
        pushLog(`Things got messy in the underworld — lost ${money(loss)} and took a beating.`);
        toast("The streets took their cut. That was rough.");
      }
    }
  }
  renderAll();
}

function resolveInterview(mode){
  G.pendingInterview = false;
  if(mode==='honest'){
    if(Math.random()<0.7){
      G.fame = clampStat(G.fame+6);
      G.stats.connections = clampStat(G.stats.connections+3);
      pushLog("Gave a refreshingly honest interview that fans loved.");
      toast("🎤 Fans loved your honesty! +6 Fame");
    } else {
      G.fame = clampStat(G.fame-4);
      G.stats.happiness = clampStat(G.stats.happiness-4);
      pushLog("Your honest interview backfired and drew some backlash.");
      toast("😬 That honesty didn't land well.");
    }
  } else if(mode==='safe'){
    G.stats.happiness = clampStat(G.stats.happiness+1);
    pushLog("Gave a safe, forgettable interview.");
    toast("Nothing gained, nothing lost.");
  } else {
    if(G.rival && Math.random()<0.5){
      G.fame = clampStat(G.fame+10);
      G.stats.connections = clampStat(G.stats.connections-3);
      pushLog(`Took a public swing at ${G.rival.name} in the interview — it made headlines.`);
      toast("🔥 Savage! Fame spiked, but people noticed the pettiness.");
    } else {
      G.fame = clampStat(G.fame-6);
      G.stats.happiness = clampStat(G.stats.happiness-6);
      pushLog("Went savage in an interview and it read as bitter, not bold.");
      toast("That came across badly.");
    }
  }
  renderAll();
}
function resolveLawsuit(mode){
  const suit = G.pendingLawsuit;
  if(!suit) return;
  if(mode==='settle'){
    const pay = Math.round(suit.amount*0.5);
    G.money -= pay;
    G.stats.happiness = clampStat(G.stats.happiness-3);
    pushLog(`Settled the lawsuit with ${suit.plaintiff} for ${money(pay)}.`);
    toast(`Settled for ${money(pay)}.`);
  } else if(mode==='lawyer'){
    const fee = Math.round(suit.amount*0.15);
    G.money -= fee;
    const chance = 0.5 + G.stats.smarts/300 + G.stats.connections/400 + (G.legalPrepared?0.15:0);
    G.legalPrepared = false;
    if(Math.random() < Math.min(0.9, chance)){
      pushLog(`Your lawyer got the case against you from ${suit.plaintiff} dismissed.`);
      toast("⚖️ Case dismissed!");
    } else {
      G.money -= suit.amount;
      G.stats.happiness = clampStat(G.stats.happiness-6);
      pushLog(`Lost the case to ${suit.plaintiff} despite hiring a lawyer — paid ${money(suit.amount)}.`);
      toast("Lost in court.");
    }
  } else {
    const chance = 0.3 + G.stats.smarts/300 + (G.legalPrepared?0.15:0);
    G.legalPrepared = false;
    if(Math.random() < Math.min(0.75, chance)){
      pushLog(`Represented yourself against ${suit.plaintiff} and won!`);
      toast("⚖️ You won, representing yourself!");
    } else {
      G.money -= suit.amount;
      G.stats.happiness = clampStat(G.stats.happiness-10);
      pushLog(`Lost the case to ${suit.plaintiff} representing yourself — paid ${money(suit.amount)}.`);
      toast("Lost in court — should've hired a lawyer.");
    }
  }
  G.pendingLawsuit = null;
  renderAll();
}
function trySueSomeone(){
  if(G.usedThisYear.has('sue_someone')){ toast("You've already filed a lawsuit this year."); return; }
  const filingFee = 5000;
  if(G.money<filingFee){ toast(`Filing a lawsuit costs ${money(filingFee)}.`); return; }
  G.usedThisYear.add('sue_someone');
  G.money -= filingFee;
  const chance = 0.3 + G.stats.connections/300 + G.stats.smarts/400 + (G.legalPrepared?0.15:0);
  G.legalPrepared = false;
  if(Math.random() < Math.min(0.75, chance)){
    const payout = Math.round(filingFee*(3+Math.random()*8));
    G.money += payout;
    pushLog(`Won a lawsuit and were awarded ${money(payout)}.`);
    toast(`⚖️ Won ${money(payout)}!`);
  } else {
    G.stats.happiness = clampStat(G.stats.happiness-3);
    pushLog("Filed a lawsuit but the case was thrown out.");
    toast("Case dismissed — you're out the filing fee.");
  }
  renderAll();
}

function runElection(sp, lvl){
  const ELECTION_NAMES = ["Ward Committee Election","Council Election","Parliamentary Election","Cabinet Reshuffle Vote","Presidential Election"];
  const campaignCost = (lvl+1)*15000;
  if(G.money < campaignCost){ toast(`You need ${money(campaignCost)} to fund your campaign.`); return; }
  G.promotedThisYear = true;
  G.money -= campaignCost;
  const opponent = genFirstName(Math.random()<0.5?'Male':'Female')+" "+randOf(SURNAMES);
  const myStrength = G.stats.connections*0.4 + G.fame*0.3 + G.stats.smarts*0.2 + (G.campaignBoost?15:0) + Math.random()*20;
  const oppStrength = 30 + Math.random()*50;
  G.campaignBoost = false;
  const won = myStrength > oppStrength;
  const electionName = ELECTION_NAMES[Math.min(lvl,4)];
  if(won){
    G.career.level++;
    G.electionsWon++;
    if(G.career.level===4) G.isPresident = true;
    pushLog(`Won the ${electionName} against ${opponent}! Now ${sp.levels[G.career.level]}.`);
    toast(`🗳️ Election won! You're now ${sp.levels[G.career.level]}!`);
  } else {
    G.electionsLost++;
    G.stats.happiness = clampStat(G.stats.happiness-8);
    pushLog(`Lost the ${electionName} to ${opponent}. Better luck next time.`);
    toast(`🗳️ Election lost to ${opponent}.`);
  }
  renderAll();
}

/* ---------- FAMILY TAB ---------- */
function renderFamilyTab(c){
  if(inPrison()){ c.innerHTML = lockedHtml("Visits are limited while you're incarcerated."); return; }

  const hasFamily = (G.family.mother&&G.family.mother.alive) || (G.family.father&&G.family.father.alive) || G.family.siblings.length;
  if(hasFamily){
    const tripCard = document.createElement('div'); tripCard.className='card';
    tripCard.innerHTML = `<div class="card-title">🚗 Family Trip (R3,000)</div>
      <div class="card-sub">A day out together — boosts your bond with everyone at once.</div>
      ${btnHtml('family_trip','Plan a Family Trip', G.usedThisYear.has('family_trip'))}`;
    c.appendChild(tripCard);
    wireFamilyButtons(tripCard);
  }

  const title1 = document.createElement('div'); title1.className='section-title'; title1.textContent='Parents';
  c.appendChild(title1);
  ['mother','father'].forEach(key=>{
    const p = G.family[key];
    if(!p) return;
    const row = document.createElement('div'); row.className='card';
    if(!p.alive){
      row.innerHTML = `<div class="card-title">${p.name} ${cap(key)==='Mother'?'(Mother)':'(Father)'}</div><div class="card-sub">Passed away.</div>`;
      c.appendChild(row); return;
    }
    row.innerHTML = `
      <div class="card-title">${p.name} <span class="card-sub">(${key==='mother'?'Mother':'Father'}, ~${G.age+p.ageOffset})</span></div>
      <div class="meter"><div class="meter-fill" style="width:${p.relationship}%;"></div></div>
      <div class="btn-row">
        ${btnHtml('call_'+key,'📞 Call', G.usedThisYear.has('call_'+key))}
        ${btnHtml('visit_'+key,'🏠 Visit', G.usedThisYear.has('visit_'+key))}
        ${btnHtml('gift_'+key,'🎁 Send Gift (R500)', G.usedThisYear.has('gift_'+key))}
        ${G.age<18 ? btnHtml('ask_'+key,'💰 Ask for Money', G.usedThisYear.has('ask_'+key)) : ''}
      </div>`;
    c.appendChild(row);
  });
  wireFamilyButtons(c);

  if(G.family.siblings.length){
    const title2 = document.createElement('div'); title2.className='section-title'; title2.textContent='Siblings';
    c.appendChild(title2);
    G.family.siblings.forEach((s,i)=>{
      const row = document.createElement('div'); row.className='card';
      row.innerHTML = `<div class="card-title">${s.name} <span class="card-sub">(~${G.age+s.ageOffset})</span></div>
        <div class="meter"><div class="meter-fill" style="width:${s.relationship}%;"></div></div>
        <div class="btn-row">
          ${btnHtml('sib_'+i,'🤝 Hang Out', G.usedThisYear.has('sib_'+i))}
          ${btnHtml('giftsib_'+i,'🎁 Send Gift (R500)', G.usedThisYear.has('giftsib_'+i))}
        </div>`;
      c.appendChild(row);
    });
    wireFamilyButtons(c);
  }

  const livingGrandparents = G.family.grandparents.filter(g=>g.alive);
  if(livingGrandparents.length || G.family.auntsUncles.length || G.family.cousins.length){
    const extTitle = document.createElement('div'); extTitle.className='section-title'; extTitle.textContent='Extended Family';
    c.appendChild(extTitle);
    const extCard = document.createElement('div'); extCard.className='card';
    let extHtml = '';
    livingGrandparents.forEach((gp)=>{
      const idx = G.family.grandparents.indexOf(gp);
      extHtml += `<div class="row-between" style="margin-bottom:8px;">
        <div><div class="card-title">${gp.name}</div><div class="card-sub">${cap(gp.side)} ${gp.label} · ~${G.age+gp.ageOffset} · Bond ${gp.relationship}%</div></div>
        ${btnHtml('gp_'+idx,'Keep in Touch', G.usedThisYear.has('gp_'+idx))}
      </div>`;
    });
    G.family.auntsUncles.forEach((au,i)=>{
      extHtml += `<div class="row-between" style="margin-bottom:8px;">
        <div><div class="card-title">${au.name}</div><div class="card-sub">${cap(au.side)} side · ~${G.age+au.ageOffset} · Bond ${au.relationship}%</div></div>
        ${btnHtml('au_'+i,'Keep in Touch', G.usedThisYear.has('au_'+i))}
      </div>`;
    });
    G.family.cousins.forEach((cs,i)=>{
      extHtml += `<div class="row-between">
        <div><div class="card-title">${cs.name}</div><div class="card-sub">Cousin · ~${G.age+cs.ageOffset} · Bond ${cs.relationship}%</div></div>
        ${btnHtml('cs_'+i,'Keep in Touch', G.usedThisYear.has('cs_'+i))}
      </div>`;
    });
    extCard.innerHTML = extHtml;
    c.appendChild(extCard);
    wireFamilyButtons(extCard);
  }

  const title3 = document.createElement('div'); title3.className='section-title'; title3.textContent='Relationship';
  c.appendChild(title3);
  const relCard = document.createElement('div'); relCard.className='card';
  let galaCardEl = null;
  if(!isAdult()){
    relCard.innerHTML = `<div class="card-sub">Dating opens up once you turn 18.</div>`;
  } else if(!G.spouse){
    const canAttendGala = (G.fame>=45 || G.stats.connections>=80);
    relCard.innerHTML = `<div class="card-sub">Single. Try the Mzansi Match app on your Phone to meet someone.</div>`;
    if(canAttendGala){
      galaCardEl = document.createElement('div'); galaCardEl.className='card';
      galaCardEl.innerHTML = `<div class="card-title">👑 Royal Gala Invitation</div>
        <div class="card-sub">Your fame and connections have earned you an invite to a royal court. Attending costs R20,000.</div>
        <button class="btn small" id="btn-royal-gala">Attend the Gala</button>`;
    }
  } else {
    const sp = G.spouse;
    let statusLabel = (sp.married ? 'Married' : (sp.engaged ? 'Engaged' : 'Dating')) + (sp.royal?' to Royalty':'');
    let actions = '';
    if(sp.married){
      actions += btnHtml('date_night','💞 Date Night', G.usedThisYear.has('date_night'));
      if(G.age<=45) actions += btnHtml('try_baby','👶 Try for a Baby (R2,000)', G.usedThisYear.has('try_baby'));
      if(sp.relationship<50) actions += btnHtml('counseling','🛋️ Couples Counseling (R2,000)', G.usedThisYear.has('counseling'));
      actions += `<button class="btn small danger" id="btn-divorce">💔 Consider Divorce</button>`;
      actions += `<button class="btn small secondary" id="btn-affair">🙈 See Someone Else</button>`;
    } else if(sp.engaged){
      actions += `<button class="btn small" id="btn-wedding">💍 Have the Wedding (R50,000)</button>`;
      actions += `<button class="btn small secondary" id="btn-breakup">Break it off</button>`;
    } else {
      actions += btnHtml('date_night','💞 Date Night', G.usedThisYear.has('date_night'));
      if(sp.relationship>=70) actions += `<button class="btn small" id="btn-propose">💍 Propose (R20,000)</button>`;
      actions += `<button class="btn small secondary" id="btn-breakup">Break it off</button>`;
    }
    relCard.innerHTML = `
      <div class="card-title">${statusLabel}: ${sp.name}${sp.royal?` <span class="pill verified">${sp.title}</span>`:''}</div>
      ${sp.royal?`<div class="card-sub">of the ${sp.house}</div>`:''}
      <div class="meter"><div class="meter-fill" style="width:${sp.relationship}%;"></div></div>
      <div class="btn-row">${actions}</div>`;
  }
  c.appendChild(relCard);
  if(galaCardEl){
    c.appendChild(galaCardEl);
    document.getElementById('btn-royal-gala').addEventListener('click', attendRoyalGala);
  }
  wireFamilyButtons(c);
  renderBloodlineCard(c);
  const proposeBtn = document.getElementById('btn-propose');
  if(proposeBtn) proposeBtn.addEventListener('click', ()=>{
    if(G.money<20000){ toast("You need R20,000 for a ring."); return; }
    G.money -= 20000;
    if(Math.random() < 0.4+G.spouse.relationship/200){
      G.spouse.engaged = true;
      pushLog(`Proposed to ${G.spouse.name}, who said yes!`);
      toast("💍 She/They said yes!");
    } else {
      G.stats.happiness = clampStat(G.stats.happiness-10);
      pushLog(`Proposed to ${G.spouse.name} — and got turned down.`);
      toast("😔 They said no...");
    }
    renderAll();
  });
  const weddingBtn = document.getElementById('btn-wedding');
  if(weddingBtn) weddingBtn.addEventListener('click', ()=>{
    if(G.money<50000){ toast("You need R50,000 for the wedding."); return; }
    G.money -= 50000;
    G.spouse.married = true; G.spouse.engaged = false;
    G.stats.happiness = clampStat(G.stats.happiness+15);
    pushLog(`Married ${G.spouse.name} in a beautiful ceremony.`);
    toast("💒 Just married!");
    renderAll();
  });
  const breakupBtn = document.getElementById('btn-breakup');
  if(breakupBtn) breakupBtn.addEventListener('click', ()=>{
    pushLog(`Broke up with ${G.spouse.name}.`);
    toast("It's over.");
    if(G.stepChildren.length){ pushLog("You lose touch with the kids you were helping raise."); G.stepChildren = []; G.stats.happiness = clampStat(G.stats.happiness-4); }
    G.spouse = null; G.stats.happiness = clampStat(G.stats.happiness-6);
    renderAll();
  });
  const divorceBtn = document.getElementById('btn-divorce');
  if(divorceBtn) divorceBtn.addEventListener('click', ()=>{
    const alimony = Math.round(G.money*0.2);
    G.money -= alimony;
    pushLog(`Divorced ${G.spouse.name}, paying ${money(alimony)} in alimony.`);
    toast(`Divorced — paid ${money(alimony)} alimony.`);
    resolveCustody();
    if(G.stepChildren.length){ pushLog("Your step-children go with their parent."); G.stepChildren = []; G.stats.happiness = clampStat(G.stats.happiness-4); }
    G.spouse = null;
    G.stats.happiness = clampStat(G.stats.happiness-12);
    renderAll();
  });
  const affairBtn = document.getElementById('btn-affair');
  if(affairBtn) affairBtn.addEventListener('click', ()=>{
    if(Math.random()<0.4){
      pushLog(`Got caught cheating on ${G.spouse.name}. It ended badly.`);
      toast("💥 Caught! Your marriage is over.");
      resolveCustody();
      if(G.stepChildren.length){ pushLog("Your step-children go with their parent."); G.stepChildren = []; }
      G.spouse = null;
      G.stats.happiness = clampStat(G.stats.happiness-20);
      G.stats.connections = clampStat(G.stats.connections-8);
    } else {
      pushLog(`Snuck around behind ${G.spouse.name}'s back and got away with it — for now.`);
      toast("You got away with it... this time.");
      G.stats.happiness = clampStat(G.stats.happiness+3);
    }
    renderAll();
  });

  if(G.children.length || G.stepChildren.length){
    const title4 = document.createElement('div'); title4.className='section-title'; title4.textContent='Children';
    c.appendChild(title4);
    G.children.forEach((k,i)=>{
      const row = document.createElement('div'); row.className='card';
      row.innerHTML = `<div class="card-title">${k.name} ${k.isAdopted?'<span class="pill">adopted</span>':''} <span class="card-sub">(age ${k.age})</span></div>
        <div class="btn-row">${btnHtml('kid_'+i,'🎈 Spend Quality Time (R300)', G.usedThisYear.has('kid_'+i))}</div>`;
      c.appendChild(row);
    });
    G.stepChildren.forEach((k,i)=>{
      const row = document.createElement('div'); row.className='card';
      row.innerHTML = `<div class="card-title">${k.name} <span class="pill">step-child</span> <span class="card-sub">(age ${k.age})</span></div>
        <div class="btn-row">${btnHtml('stepkid_'+i,'🎈 Spend Quality Time (R300)', G.usedThisYear.has('stepkid_'+i))}</div>`;
      c.appendChild(row);
    });
    wireFamilyButtons(c);
  }
  if(isAdult()){
    const adoptCard = document.createElement('div'); adoptCard.className='card';
    adoptCard.innerHTML = `<div class="card-title">Adopt a Child</div>
      <div class="card-sub">Application and agency fees: R40,000. Not guaranteed the same year you apply.</div>
      ${btnHtml('adopt_child','🧸 Apply to Adopt', G.usedThisYear.has('adopt_child'))}`;
    c.appendChild(adoptCard);
    wireFamilyButtons(adoptCard);
  }
  if(G.exChildren.length){
    const title5 = document.createElement('div'); title5.className='section-title'; title5.textContent='Children Living Elsewhere';
    c.appendChild(title5);
    G.exChildren.forEach(k=>{
      const row = document.createElement('div'); row.className='card';
      row.innerHTML = `<div class="card-title">${k.name} <span class="card-sub">(age ${k.age})</span></div>
        <div class="card-sub">Living with ${k.exSpouseName} · You pay ${money(k.support)}/yr child support</div>`;
      c.appendChild(row);
    });
  }
}
function resolveCustody(){
  if(G.children.length===0) return;
  let chance = 0.55;
  if(G.criminalRecord) chance -= 0.2;
  if(G.education.hasDegree) chance += 0.05;
  chance = Math.max(0.1, Math.min(0.9, chance));
  if(Math.random()<chance){
    pushLog(`Won custody of ${G.children.length>1?'the children':G.children[0].name}.`);
    toast("You keep custody.");
  } else {
    const exName = G.spouse.name;
    G.children.forEach(k=>{
      G.exChildren.push({name:k.name, age:k.age, exSpouseName:exName, support: Math.round(1500+Math.random()*2500)});
    });
    pushLog(`${exName} won custody of the kids — you'll be paying child support.`);
    toast("Lost custody — child support ahead.");
    G.children = [];
    G.stats.happiness = clampStat(G.stats.happiness-8);
  }
}
function attendRoyalGala(){
  if(G.money<20000){ toast("The gala costs R20,000 to attend."); return; }
  G.money -= 20000;
  const power = (G.fame + G.stats.looks + G.stats.connections)/3;
  const chance = 0.15 + power/250;
  if(Math.random() < Math.min(0.6, chance)){
    const house = randOf(ROYAL_HOUSES);
    const gender = Math.random()<0.5?'Male':'Female';
    const title = gender==='Male' ? house.titleM : house.titleF;
    G.spouse = {name: genFirstName(gender)+" of "+house.kingdom.split(' ').pop(), gender, relationship:60,
                dating:true, engaged:false, married:false, royal:true, house:house.kingdom, title};
    G.stats.happiness = clampStat(G.stats.happiness+12);
    G.fame = clampStat(G.fame+5);
    pushLog(`Caught the eye of ${G.spouse.name}, ${title} of the ${house.kingdom}!`);
    toast(`👑 You're dating royalty!`);
  } else {
    pushLog("Attended a royal gala but didn't make a connection worth keeping.");
    toast("No royal romance this time.");
  }
  renderAll();
}
function renderBloodlineCard(c){
  const bloodlineCard = document.createElement('div'); bloodlineCard.className='card';
  if(G.royal.bloodlineBought){
    bloodlineCard.innerHTML = `<div class="card-title">👑 ${G.royal.title}</div><div class="card-sub">A purchased noble title, officially recognized by the International Register of Nobility.</div>`;
  } else {
    bloodlineCard.innerHTML = `<div class="card-title">Purchase a Noble Title</div>
      <div class="card-sub">Buy your way into a fictional bloodline via the International Register of Nobility. Pure vanity — but what vanity.</div>
      <button class="btn small secondary" id="btn-buy-bloodline">Buy Noble Title (R5,000,000)</button>`;
  }
  c.appendChild(bloodlineCard);
  const buyBtn = document.getElementById('btn-buy-bloodline');
  if(buyBtn) buyBtn.addEventListener('click', ()=>{
    if(G.money<5000000){ toast("You need R5,000,000 for a noble title."); return; }
    G.money -= 5000000;
    const place = randOf(NOBLE_TITLE_PLACES);
    const titleWord = G.gender==='Male' ? 'Baron' : (G.gender==='Female' ? 'Baroness' : 'Noble');
    G.royal.title = `${titleWord} of ${place}`;
    G.royal.bloodlineBought = true;
    G.fame = clampStat(G.fame+5);
    G.stats.connections = clampStat(G.stats.connections+5);
    pushLog(`Purchased a noble title: ${G.royal.title}.`);
    toast(`👑 You are now the ${G.royal.title}!`);
    renderAll();
  });
}

function wireFamilyButtons(scope){
  scope.querySelectorAll('[data-act]').forEach(btn=>{
    const id = btn.dataset.act;
    btn.addEventListener('click', ()=>{
      if(G.usedThisYear.has(id)) return;
      if(id.startsWith('gp_')){
        const i = parseInt(id.split('_')[1],10);
        G.usedThisYear.add(id);
        const gp = G.family.grandparents[i];
        gp.relationship = clampStat(gp.relationship+6);
        G.stats.happiness = clampStat(G.stats.happiness+2);
        pushLog(`Kept in touch with your ${gp.label.toLowerCase()}, ${gp.name}.`);
        toast("+6 Relationship");
      } else if(id.startsWith('au_')){
        const i = parseInt(id.split('_')[1],10);
        G.usedThisYear.add(id);
        const au = G.family.auntsUncles[i];
        au.relationship = clampStat(au.relationship+6);
        G.stats.connections = clampStat(G.stats.connections+1);
        pushLog(`Kept in touch with ${au.name}.`);
        toast("+6 Relationship");
      } else if(id.startsWith('cs_')){
        const i = parseInt(id.split('_')[1],10);
        G.usedThisYear.add(id);
        const cs = G.family.cousins[i];
        cs.relationship = clampStat(cs.relationship+6);
        G.stats.happiness = clampStat(G.stats.happiness+1);
        pushLog(`Kept in touch with your cousin ${cs.name}.`);
        toast("+6 Relationship");
      } else if(id==='call_mother'||id==='call_father'){
        const key = id.split('_')[1];
        G.usedThisYear.add(id);
        G.family[key].relationship = clampStat(G.family[key].relationship+3);
        G.stats.connections = clampStat(G.stats.connections+1);
        pushLog(`Called your ${key}.`);
        toast("+3 Relationship");
      } else if(id==='visit_mother'||id==='visit_father'){
        const key = id.split('_')[1];
        G.usedThisYear.add(id);
        G.family[key].relationship = clampStat(G.family[key].relationship+6);
        G.stats.happiness = clampStat(G.stats.happiness+3);
        pushLog(`Visited your ${key}.`);
        toast("+6 Relationship, +3 Happiness");
      } else if(id==='ask_mother'||id==='ask_father'){
        const key = id.split('_')[1];
        G.usedThisYear.add(id);
        const p = G.family[key];
        if(Math.random() < 0.3+p.relationship/150){
          const amt = Math.round((500+p.relationship*20)*(G.fortune+1));
          G.money += amt;
          pushLog(`Asked your ${key} for money — got ${money(amt)}.`);
          toast(`+${money(amt)}`);
        } else {
          p.relationship = clampStat(p.relationship-3);
          pushLog(`Asked your ${key} for money but got turned down.`);
          toast("They said no this time.");
        }
      } else if(id.startsWith('sib_')){
        const i = parseInt(id.split('_')[1],10);
        G.usedThisYear.add(id);
        G.family.siblings[i].relationship = clampStat(G.family.siblings[i].relationship+5);
        G.stats.connections = clampStat(G.stats.connections+1);
        pushLog(`Hung out with ${G.family.siblings[i].name}.`);
        toast("+5 Relationship");
      } else if(id==='gift_mother'||id==='gift_father'){
        const key = id.split('_')[1];
        if(G.money<500){ toast("You need R500 for a gift."); return; }
        G.usedThisYear.add(id);
        G.money -= 500;
        G.family[key].relationship = clampStat(G.family[key].relationship+8);
        pushLog(`Sent a gift to your ${key}.`);
        toast("+8 Relationship");
      } else if(id.startsWith('giftsib_')){
        const i = parseInt(id.split('_')[1],10);
        if(G.money<500){ toast("You need R500 for a gift."); return; }
        G.usedThisYear.add(id);
        G.money -= 500;
        G.family.siblings[i].relationship = clampStat(G.family.siblings[i].relationship+8);
        pushLog(`Sent a gift to ${G.family.siblings[i].name}.`);
        toast("+8 Relationship");
      } else if(id==='family_trip'){
        if(G.money<3000){ toast("You need R3,000 for a family trip."); return; }
        G.usedThisYear.add(id);
        G.money -= 3000;
        G.stats.happiness = clampStat(G.stats.happiness+6);
        if(G.family.mother && G.family.mother.alive) G.family.mother.relationship = clampStat(G.family.mother.relationship+5);
        if(G.family.father && G.family.father.alive) G.family.father.relationship = clampStat(G.family.father.relationship+5);
        G.family.siblings.forEach(s=>s.relationship = clampStat(s.relationship+5));
        pushLog("Took the whole family on a trip together.");
        toast("+6 Happiness, +5 to family bonds");
      } else if(id==='counseling'){
        if(G.money<2000){ toast("You need R2,000 for a session."); return; }
        G.usedThisYear.add(id);
        G.money -= 2000;
        G.spouse.relationship = clampStat(G.spouse.relationship+20);
        G.stats.happiness = clampStat(G.stats.happiness+3);
        pushLog(`Went to couples counseling with ${G.spouse.name}.`);
        toast("+20 Relationship");
      } else if(id==='date_night'){
        G.usedThisYear.add(id);
        G.spouse.relationship = clampStat(G.spouse.relationship+5);
        G.stats.happiness = clampStat(G.stats.happiness+5);
        pushLog(`Had a date night with ${G.spouse.name}.`);
        toast("+5 Happiness, +5 Relationship");
      } else if(id==='try_baby'){
        if(G.money<2000){ toast("You need R2,000 to try."); return; }
        G.usedThisYear.add(id);
        G.money -= 2000;
        if(Math.random()<0.45){
          const gender = Math.random()<0.5?'Male':'Female';
          const child = {name: genFirstName(gender)+" "+G.surname, gender, age:0};
          G.children.push(child);
          G.stats.happiness = clampStat(G.stats.happiness+15);
          pushLog(`Welcomed a baby: ${child.name}!`);
          toast(`👶 It's a ${gender==='Male'?'boy':'girl'}! Welcome ${child.name}.`);
        } else {
          pushLog("Tried for a baby this year, but it didn't happen.");
          toast("Not this year — try again next year.");
        }
      } else if(id.startsWith('kid_')){
        if(G.money<300){ toast("You need R300 for quality time."); return; }
        const i = parseInt(id.split('_')[1],10);
        G.usedThisYear.add(id);
        G.money -= 300;
        G.stats.happiness = clampStat(G.stats.happiness+4);
        pushLog(`Spent quality time with ${G.children[i].name}.`);
        toast("+4 Happiness");
      } else if(id.startsWith('stepkid_')){
        if(G.money<300){ toast("You need R300 for quality time."); return; }
        const i = parseInt(id.split('_')[1],10);
        G.usedThisYear.add(id);
        G.money -= 300;
        G.stats.happiness = clampStat(G.stats.happiness+4);
        pushLog(`Spent quality time with your step-child ${G.stepChildren[i].name}.`);
        toast("+4 Happiness");
      } else if(id==='adopt_child'){
        if(G.money<40000){ toast("Adoption fees are R40,000."); return; }
        G.usedThisYear.add(id);
        G.money -= 40000;
        const chance = 0.5 + G.stats.connections/300 + G.stats.smarts/400;
        if(Math.random() < Math.min(0.85, chance)){
          const gender = Math.random()<0.5?'Male':'Female';
          const child = {name: genFirstName(gender)+" "+G.surname, gender, age:Math.floor(Math.random()*6), isAdopted:true};
          G.children.push(child);
          G.stats.happiness = clampStat(G.stats.happiness+15);
          pushLog(`Adopted a child: welcome ${child.name}!`);
          toast(`🧸 Adoption approved — welcome ${child.name}!`);
        } else {
          pushLog("Applied to adopt, but weren't matched with a child this year.");
          toast("No match this year — you can apply again next year.");
        }
      }
      renderAll();
    });
  });
}

/* ---------- SHOP TAB ---------- */
let shopFilter='All';
function renderShopTab(c){
  if(inPrison()){ c.innerHTML = lockedHtml("You can't shop from behind bars."); return; }
  const cats = ['All', ...new Set(SHOP.map(s=>s.cat))];
  const filterRow = document.createElement('div');
  filterRow.className='cat-filter';
  filterRow.innerHTML = cats.map(cat=>`<span class="${cat===shopFilter?'active':''}" data-cat="${cat}">${cat}</span>`).join('');
  c.appendChild(filterRow);
  filterRow.querySelectorAll('span').forEach(sp=>sp.addEventListener('click', ()=>{ shopFilter=sp.dataset.cat; renderAll(); }));

  const list = SHOP.filter(s=>shopFilter==='All'||s.cat===shopFilter);
  list.forEach(item=>{
    const owned = G.ownedAssets.includes(item.id);
    const canMortgage = item.cat==='Housing' && item.price>=200000 && !owned;
    const row = document.createElement('div');
    row.className='card';
    row.innerHTML = `
      <div class="row-between">
        <div>
          <div class="card-title">${item.name} ${owned?'<span class="pill owned">owned</span>':''}</div>
          <div class="card-sub">${item.cat} · ${money(item.price)} · +${item.happiness} Happiness${item.looks?` · +${item.looks} Looks`:''}</div>
        </div>
        <div class="btn-row">
          ${canMortgage?`<button class="btn small secondary" data-mortgage="${item.id}">Mortgage (20% down)</button>`:''}
          <button class="btn small ${owned?'secondary':''}" data-buy="${item.id}">${owned?'Sell (50%)':'Buy Cash'}</button>
        </div>
      </div>`;
    c.appendChild(row);
  });
  c.querySelectorAll('[data-mortgage]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const item = SHOP.find(s=>s.id===btn.dataset.mortgage);
      const deposit = Math.round(item.price*0.2);
      if(G.money<deposit){ toast(`You need a ${money(deposit)} deposit.`); return; }
      G.money -= deposit;
      const balance = item.price-deposit;
      G.mortgages.push({assetId:item.id, balance, rate:0.09, annualPayment:Math.round(balance/15)});
      G.ownedAssets.push(item.id);
      G.stats.happiness = clampStat(G.stats.happiness + item.happiness);
      G.stats.looks = clampStat(G.stats.looks + item.looks);
      pushLog(`Bought ${item.name} with a mortgage — ${money(deposit)} down, ${money(balance)} financed.`);
      toast(`🏠 Mortgage approved for ${item.name}!`);
      renderAll();
    });
  });
  c.querySelectorAll('[data-buy]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const item = SHOP.find(s=>s.id===btn.dataset.buy);
      const owned = G.ownedAssets.includes(item.id);
      if(owned){
        const refund = Math.round(item.price*0.5);
        G.money += refund;
        G.ownedAssets = G.ownedAssets.filter(x=>x!==item.id);
        pushLog(`Sold your ${item.name} for ${money(refund)}.`);
        toast(`Sold for ${money(refund)}`);
      } else {
        if(G.money < item.price){ toast("You can't afford that yet."); return; }
        G.money -= item.price;
        G.ownedAssets.push(item.id);
        G.stats.happiness = clampStat(G.stats.happiness + item.happiness);
        G.stats.looks = clampStat(G.stats.looks + item.looks);
        pushLog(`Bought a ${item.name}.`);
        toast(`Purchased ${item.name}!`);
      }
      renderAll();
    });
  });
}

/* ---------- BUSINESS TAB ---------- */
let bizFilter='All';
function bizEffectiveIncome(item){
  const lvl = G.bizUpgrades[item.id]||0;
  return Math.round(item.income * (1 + 0.25*lvl));
}
function totalBizIncome(){
  let total = G.ownedBusinesses.reduce((s,id)=>s+bizEffectiveIncome(BIZ.find(b=>b.id===id)),0);
  if(G.holdingCompany) total = Math.round(total*1.08);
  if(G.ipoTaken) total = Math.round(total*0.7);
  if(G.activeWorldEvent){
    const ev = WORLD_EVENTS.find(e=>e.name===G.activeWorldEvent);
    if(ev && ev.bizMult) total = Math.round(total*ev.bizMult);
  }
  return total;
}
function renderBizTab(c){
  if(inPrison()){ c.innerHTML = lockedHtml("Your businesses run themselves for now — no new deals from behind bars."); return; }
  const info = document.createElement('div'); info.className='card';
  info.innerHTML = `<div class="card-title">Your Business Empire${G.holdingCompany?' — a Holding Company':''}</div>
    <div class="card-sub">${G.ownedBusinesses.length} businesses owned · ${money(totalBizIncome())}/yr passive income</div>`;
  c.appendChild(info);

  const unowned = BIZ.filter(b=>!G.ownedBusinesses.includes(b.id));
  if(unowned.length && G.ownedBusinesses.length>=2){
    const takeoverUsed = G.usedThisYear.has('acquire_competitor');
    const takeoverCard = document.createElement('div'); takeoverCard.className='card';
    takeoverCard.innerHTML = `<div class="card-title">🤝 Hostile Takeover</div>
      <div class="card-sub">Acquire a random competitor at a 30% discount, absorbing their customer base.</div>
      ${btnHtml('acquire_competitor','Scout for a Takeover Target', takeoverUsed)}`;
    c.appendChild(takeoverCard);
    wireActButtons(takeoverCard);
  }

  if(G.ownedBusinesses.length>0){
    const insCard = document.createElement('div'); insCard.className='card';
    const premium = G.ownedBusinesses.length*1500;
    insCard.innerHTML = `<div class="card-title">🛡️ Business Insurance</div>
      <div class="card-sub">Covers theft, fire and staff losses across all your businesses · ${money(premium)}/yr</div>
      <button class="btn small ${G.bizInsurance?'secondary':''}" id="btn-biz-insurance">${G.bizInsurance?'Cancel Coverage':'Get Covered'}</button>`;
    c.appendChild(insCard);

    const mgrCard = document.createElement('div'); mgrCard.className='card';
    const mgrFee = G.ownedBusinesses.length*2000;
    mgrCard.innerHTML = `<div class="card-title">👔 General Manager</div>
      <div class="card-sub">Runs day-to-day operations, halving the risk of theft/fire/staff losses · ${money(mgrFee)}/yr</div>
      <button class="btn small ${G.bizManager?'secondary':''}" id="btn-biz-manager">${G.bizManager?'Let Them Go':'Hire a Manager'}</button>`;
    c.appendChild(mgrCard);
  }

  if(G.ownedBusinesses.length>=5){
    const holdCard = document.createElement('div'); holdCard.className='card';
    if(!G.holdingCompany){
      holdCard.innerHTML = `<div class="card-title">🏢 Form a Holding Company</div>
        <div class="card-sub">Consolidate your businesses under one umbrella for an 8% synergy bonus to total income.</div>
        <button class="btn small" id="btn-form-holding">Form Holding Company (R100,000)</button>`;
    } else if(!G.ipoTaken){
      holdCard.innerHTML = `<div class="card-title">🏢 Your Holding Company</div>
        <div class="card-sub">Earning an 8% synergy bonus. You could take it public for a cash windfall — but you'd sell off future income.</div>
        <button class="btn small secondary" id="btn-ipo">Go Public (IPO) — one-time cash, -30% future income</button>`;
    } else {
      holdCard.innerHTML = `<div class="card-title">🏢 Your Holding Company (Public)</div>
        <div class="card-sub">Publicly traded — you kept a 70% income stake after the IPO.</div>`;
    }
    c.appendChild(holdCard);
  }

  const cats = ['All', ...new Set(BIZ.map(s=>s.cat))];
  const filterRow = document.createElement('div');
  filterRow.className='cat-filter';
  filterRow.innerHTML = cats.map(cat=>`<span class="${cat===bizFilter?'active':''}" data-cat="${cat}">${cat}</span>`).join('');
  c.appendChild(filterRow);
  filterRow.querySelectorAll('span').forEach(sp=>sp.addEventListener('click', ()=>{ bizFilter=sp.dataset.cat; renderAll(); }));

  const list = BIZ.filter(s=>bizFilter==='All'||s.cat===bizFilter);
  list.forEach(item=>{
    const owned = G.ownedBusinesses.includes(item.id);
    const lvl = G.bizUpgrades[item.id]||0;
    const row = document.createElement('div');
    row.className='card';
    row.innerHTML = `
      <div class="row-between">
        <div>
          <div class="card-title">${item.name} ${owned?'<span class="pill owned">owned</span>':''} ${owned&&lvl>0?`<span class="pill">Lvl ${lvl+1}</span>`:''}</div>
          <div class="card-sub">${item.cat} · Cost ${money(item.price)} · Pays ${money(owned?bizEffectiveIncome(item):item.income)}/yr</div>
        </div>
        <div class="btn-row">
          ${owned && lvl<3 ? `<button class="btn small secondary" data-expand="${item.id}">Expand (${money(Math.round(item.price*0.4))})</button>` : ''}
          <button class="btn small ${owned?'secondary':''}" data-buy="${item.id}">${owned?'Sell (50%)':'Invest'}</button>
        </div>
      </div>`;
    c.appendChild(row);
  });
  const insBtn = document.getElementById('btn-biz-insurance');
  if(insBtn) insBtn.addEventListener('click', ()=>{
    G.bizInsurance = !G.bizInsurance;
    pushLog(G.bizInsurance ? "Took out business insurance." : "Cancelled business insurance.");
    toast(G.bizInsurance ? "Covered." : "Coverage cancelled.");
    renderAll();
  });
  const mgrBtn = document.getElementById('btn-biz-manager');
  if(mgrBtn) mgrBtn.addEventListener('click', ()=>{
    G.bizManager = !G.bizManager;
    pushLog(G.bizManager ? "Hired a general manager to run your businesses." : "Let your general manager go.");
    toast(G.bizManager ? "Manager hired." : "Manager gone.");
    renderAll();
  });
  const holdBtn = document.getElementById('btn-form-holding');
  if(holdBtn) holdBtn.addEventListener('click', ()=>{
    if(G.money<100000){ toast("You need R100,000 to form a holding company."); return; }
    G.money -= 100000;
    G.holdingCompany = true;
    pushLog("Formed a holding company to consolidate your business empire.");
    toast("🏢 Holding company formed!");
    renderAll();
  });
  const ipoBtn = document.getElementById('btn-ipo');
  if(ipoBtn) ipoBtn.addEventListener('click', ()=>{
    const payout = Math.round(totalBizIncome()*4);
    G.money += payout;
    G.ipoTaken = true;
    pushLog(`Took your holding company public — raised ${money(payout)}, but future income drops 30%.`);
    toast(`📈 IPO complete! +${money(payout)}`);
    renderAll();
  });
  c.querySelectorAll('[data-expand]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const item = BIZ.find(s=>s.id===btn.dataset.expand);
      const cost = Math.round(item.price*0.4);
      if(G.money<cost){ toast("Not enough capital to expand."); return; }
      G.money -= cost;
      G.bizUpgrades[item.id] = (G.bizUpgrades[item.id]||0)+1;
      pushLog(`Expanded your ${item.name} — income boosted.`);
      toast(`${item.name} expanded!`);
      renderAll();
    });
  });
  c.querySelectorAll('[data-buy]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const item = BIZ.find(s=>s.id===btn.dataset.buy);
      const owned = G.ownedBusinesses.includes(item.id);
      if(owned){
        const refund = Math.round(item.price*0.5);
        G.money += refund;
        G.ownedBusinesses = G.ownedBusinesses.filter(x=>x!==item.id);
        delete G.bizUpgrades[item.id];
        pushLog(`Sold your ${item.name} for ${money(refund)}.`);
        toast(`Sold for ${money(refund)}`);
      } else {
        if(G.money < item.price){ toast("Not enough capital for that yet."); return; }
        G.money -= item.price;
        G.ownedBusinesses.push(item.id);
        pushLog(`Invested in a ${item.name}.`);
        toast(`You now own a ${item.name}!`);
      }
      renderAll();
    });
  });
}

/* ---------- BANK TAB ---------- */
function personalLoanRate(){ return netWorth()>=10000000 ? 0.06 : 0.10; }
function businessLoanRate(){ return netWorth()>=10000000 ? 0.09 : 0.15; }
function renderBankTab(c){
  const b = G.bank;
  const nw = netWorth();
  const maxLoan = Math.max(5000, b.creditScore*100, Math.round(nw*0.5));
  const pRate = personalLoanRate(), bRate = businessLoanRate();
  const cashCard = document.createElement('div'); cashCard.className='card';
  cashCard.innerHTML = `
    <div class="card-title">🏦 iBank</div>
    <div class="card-sub">Cash: ${money(G.money)} · Savings: ${money(b.savings)} (6%/yr)</div>
    <div class="card-sub">Credit score: ${b.creditScore} ${nw>=10000000?'· Private Banking rates unlocked (net worth 10M+)':''}</div>
    <div class="field" style="margin-top:8px;margin-bottom:0;">
      <label>Amount</label>
      <input type="number" id="amt-savings" min="1" step="100" placeholder="e.g. 2500" style="padding:8px;">
    </div>
    <div class="btn-row">
      <button class="btn small" id="btn-deposit">Deposit</button>
      <button class="btn small secondary" id="btn-withdraw">Withdraw</button>
    </div>`;
  c.appendChild(cashCard);
  document.getElementById('btn-deposit').addEventListener('click', ()=>{
    const amt = Math.floor(Number(document.getElementById('amt-savings').value));
    if(!amt || amt<=0){ toast("Enter an amount first."); return; }
    if(G.money<amt){ toast("Not enough cash."); return; }
    G.money -= amt; b.savings += amt; toast(`Deposited ${money(amt)}.`); renderAll();
  });
  document.getElementById('btn-withdraw').addEventListener('click', ()=>{
    const amt = Math.floor(Number(document.getElementById('amt-savings').value));
    if(!amt || amt<=0){ toast("Enter an amount first."); return; }
    if(b.savings<amt){ toast("Not enough in savings."); return; }
    b.savings -= amt; G.money += amt; toast(`Withdrew ${money(amt)}.`); renderAll();
  });

  const loanTitle = document.createElement('div'); loanTitle.className='section-title'; loanTitle.textContent='Loans';
  c.appendChild(loanTitle);
  const loanCard = document.createElement('div'); loanCard.className='card';
  const personalTaken = G.usedThisYear.has('loan_taken');
  const bizTaken = G.usedThisYear.has('bizloan_taken');
  loanCard.innerHTML = `
    <div class="card-sub">Personal loan owed: ${money(G.debt)} (${Math.round(pRate*100)}%/yr) · Max new loan: ${money(maxLoan)}</div>
    <div class="field" style="margin-top:6px;margin-bottom:0;">
      <label>Loan/Repayment amount</label>
      <input type="number" id="amt-loan" min="1" step="500" placeholder="e.g. 5000" style="padding:8px;">
    </div>
    <div class="btn-row">
      <button class="btn small" id="btn-loan" ${personalTaken?'disabled':''}>${personalTaken?'Taken this year':'Take Personal Loan'}</button>
      <button class="btn small secondary" id="btn-repay" ${G.debt<=0?'disabled':''}>Repay Personal</button>
    </div>
    <div class="card-sub" style="margin-top:10px;">Business loan owed: ${money(G.businessLoan)} (${Math.round(bRate*100)}%/yr) — requires credit score 550+</div>
    <div class="btn-row">
      <button class="btn small" id="btn-bizloan" ${bizTaken||b.creditScore<550?'disabled':''}>${b.creditScore<550?'Credit score too low':(bizTaken?'Taken this year':'Take Business Loan')}</button>
      <button class="btn small secondary" id="btn-repay-biz" ${G.businessLoan<=0?'disabled':''}>Repay Business</button>
    </div>`;
  c.appendChild(loanCard);
  document.getElementById('btn-loan').addEventListener('click', ()=>{
    if(G.usedThisYear.has('loan_taken')) return;
    const amt = Math.floor(Number(document.getElementById('amt-loan').value));
    if(!amt || amt<=0){ toast("Enter a loan amount first."); return; }
    if(amt>maxLoan){ toast(`Your credit and net worth cap loans at ${money(maxLoan)}.`); return; }
    G.usedThisYear.add('loan_taken');
    G.money += amt; G.debt += amt;
    b.creditScore = Math.max(300, b.creditScore-15);
    pushLog(`Took out a ${money(amt)} personal loan at ${Math.round(pRate*100)}%.`);
    toast(`Loan approved: +${money(amt)}`);
    renderAll();
  });
  document.getElementById('btn-bizloan').addEventListener('click', ()=>{
    if(G.usedThisYear.has('bizloan_taken') || b.creditScore<550) return;
    const amt = Math.floor(Number(document.getElementById('amt-loan').value));
    if(!amt || amt<=0){ toast("Enter a loan amount first."); return; }
    if(amt>maxLoan*3){ toast(`Business loans are capped at ${money(maxLoan*3)}.`); return; }
    G.usedThisYear.add('bizloan_taken');
    G.money += amt; G.businessLoan += amt;
    b.creditScore = Math.max(300, b.creditScore-25);
    pushLog(`Took out a ${money(amt)} business loan at ${Math.round(bRate*100)}%.`);
    toast(`Business loan approved: +${money(amt)}`);
    renderAll();
  });
  document.getElementById('btn-repay').addEventListener('click', ()=>{
    const amt = Math.floor(Number(document.getElementById('amt-loan').value));
    if(!amt || amt<=0){ toast("Enter an amount first."); return; }
    if(G.debt<=0 || G.money<amt) { toast("Can't repay that amount right now."); return; }
    const pay = Math.min(amt, G.debt);
    G.money -= pay; G.debt -= pay;
    b.creditScore = Math.min(850, b.creditScore+10);
    pushLog(`Repaid ${money(pay)} of personal debt.`);
    toast(`Repaid ${money(pay)}`);
    renderAll();
  });
  document.getElementById('btn-repay-biz').addEventListener('click', ()=>{
    const amt = Math.floor(Number(document.getElementById('amt-loan').value));
    if(!amt || amt<=0){ toast("Enter an amount first."); return; }
    if(G.businessLoan<=0 || G.money<amt) { toast("Can't repay that amount right now."); return; }
    const pay = Math.min(amt, G.businessLoan);
    G.money -= pay; G.businessLoan -= pay;
    b.creditScore = Math.min(850, b.creditScore+15);
    pushLog(`Repaid ${money(pay)} of business debt.`);
    toast(`Repaid ${money(pay)}`);
    renderAll();
  });

  const invTitle = document.createElement('div'); invTitle.className='section-title'; invTitle.textContent='Investments';
  c.appendChild(invTitle);

  let portfolioValue = 0;
  STOCKS.forEach(st=>{ portfolioValue += (b.stocks[st.id]||0)*G.stockPrices[st.id]; });
  const marginCard = document.createElement('div'); marginCard.className='card';
  marginCard.innerHTML = `<div class="card-title">📊 Margin Loan</div>
    <div class="card-sub">Borrow against your stock portfolio (${money(Math.round(portfolioValue))}) at 7%/yr. Risk: a margin call liquidates your holdings if they fall too far.</div>
    <div class="card-sub">Outstanding: ${money(G.marginLoan)} · Max: ${money(Math.round(portfolioValue*0.5))}</div>
    <div class="btn-row">
      <button class="btn small" id="btn-margin-loan">Borrow R10,000</button>
      <button class="btn small secondary" id="btn-margin-repay" ${G.marginLoan<=0?'disabled':''}>Repay R10,000</button>
    </div>`;
  c.appendChild(marginCard);
  document.getElementById('btn-margin-loan').addEventListener('click', ()=>{
    if(portfolioValue*0.5 < G.marginLoan+10000){ toast("Not enough portfolio value to borrow more."); return; }
    G.marginLoan += 10000; G.money += 10000;
    pushLog("Took a R10,000 margin loan against your stock portfolio.");
    toast("+R10,000 margin loan");
    renderAll();
  });
  document.getElementById('btn-margin-repay').addEventListener('click', ()=>{
    if(G.marginLoan<=0 || G.money<10000){ toast("Can't repay that right now."); return; }
    const pay = Math.min(10000, G.marginLoan);
    G.money -= pay; G.marginLoan -= pay;
    pushLog(`Repaid ${money(pay)} of your margin loan.`);
    toast(`Repaid ${money(pay)}`);
    renderAll();
  });

  if(G.mortgages.length){
    const mortTitle = document.createElement('div'); mortTitle.className='section-title'; mortTitle.textContent='Mortgages';
    c.appendChild(mortTitle);
    G.mortgages.forEach(m=>{
      const item = SHOP.find(s=>s.id===m.assetId);
      const row = document.createElement('div'); row.className='card';
      row.innerHTML = `<div class="card-title">${item?item.name:'Property'}</div>
        <div class="card-sub">Balance: ${money(m.balance)} at ${Math.round(m.rate*100)}% · ${money(m.annualPayment)}/yr payment</div>`;
      c.appendChild(row);
    });
  }

  STOCKS.forEach(st=>{
    const price = G.stockPrices[st.id];
    const units = b.stocks[st.id];
    const value = Math.round(units*price);
    const row = document.createElement('div'); row.className='card';
    row.innerHTML = `
      <div class="card-title">${st.name}</div>
      <div class="card-sub">Price: ${money(price)} · Your holding: ${money(value)}</div>
      <div class="field" style="margin:6px 0 0;">
        <input type="number" id="amt-stock-${st.id}" min="1" step="500" placeholder="Amount to invest" style="padding:8px;">
      </div>
      <div class="btn-row">
        <button class="btn small" data-buy-stock="${st.id}">Buy</button>
        <button class="btn small secondary" data-sell-stock="${st.id}" ${units<=0?'disabled':''}>Sell All</button>
      </div>`;
    c.appendChild(row);
  });
  c.querySelectorAll('[data-buy-stock]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const id = btn.dataset.buyStock;
      const amt = Math.floor(Number(document.getElementById('amt-stock-'+id).value));
      if(!amt || amt<=0){ toast("Enter an amount to invest."); return; }
      if(G.money<amt){ toast("Not enough cash."); return; }
      G.money -= amt;
      b.stocks[id] += amt/G.stockPrices[id];
      pushLog(`Invested ${money(amt)} in ${STOCKS.find(s=>s.id===id).name}.`);
      toast("Investment made.");
      renderAll();
    });
  });
  c.querySelectorAll('[data-sell-stock]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const id = btn.dataset.sellStock;
      const value = Math.round(b.stocks[id]*G.stockPrices[id]);
      G.money += value; b.stocks[id]=0;
      pushLog(`Sold your holding in ${STOCKS.find(s=>s.id===id).name} for ${money(value)}.`);
      toast(`Sold for ${money(value)}`);
      renderAll();
    });
  });

  const insTitle = document.createElement('div'); insTitle.className='section-title'; insTitle.textContent='Insurance';
  c.appendChild(insTitle);
  const insCard = document.createElement('div'); insCard.className='card';
  insCard.innerHTML = `
    <div class="card-title">Life Insurance</div>
    <div class="card-sub">Premium: ${money(G.insurance.premium)}/yr · Payout to your heir on death: ${money(G.insurance.payout)}</div>
    <button class="btn small ${G.insurance.active?'secondary':''}" id="btn-insurance">${G.insurance.active?'Cancel Policy':'Activate Policy'}</button>`;
  c.appendChild(insCard);
  document.getElementById('btn-insurance').addEventListener('click', ()=>{
    G.insurance.active = !G.insurance.active;
    pushLog(G.insurance.active ? "Activated a life insurance policy." : "Cancelled your life insurance policy.");
    toast(G.insurance.active ? "Policy activated." : "Policy cancelled.");
    renderAll();
  });
  const medCard = document.createElement('div'); medCard.className='card';
  medCard.innerHTML = `
    <div class="card-title">Medical Aid</div>
    <div class="card-sub">Premium: ${money(1800)}/yr · Cuts medical bills and health loss from illness significantly.</div>
    <button class="btn small ${G.medicalAid?'secondary':''}" id="btn-medaid">${G.medicalAid?'Cancel Cover':'Get Covered'}</button>`;
  c.appendChild(medCard);
  document.getElementById('btn-medaid').addEventListener('click', ()=>{
    G.medicalAid = !G.medicalAid;
    pushLog(G.medicalAid ? "Signed up for medical aid." : "Cancelled medical aid.");
    toast(G.medicalAid ? "Covered." : "Cover cancelled.");
    renderAll();
  });

  if(G.children.length>0){
    const willTitle = document.createElement('div'); willTitle.className='section-title'; willTitle.textContent='Last Will & Testament';
    c.appendChild(willTitle);
    const willCard = document.createElement('div'); willCard.className='card';
    const currentName = G.willBeneficiary;
    willCard.innerHTML = `<div class="card-title">Primary Beneficiary</div>
      <div class="card-sub">${currentName ? `Your estate is willed primarily to ${currentName}.` : "You haven't named a primary beneficiary yet — your estate will simply go to whichever child you choose to continue as."}</div>
      <div class="btn-row">${G.children.map(k=>`<button class="btn small ${currentName===k.name?'secondary':''}" data-heir="${k.name}">${currentName===k.name?'✓ ':''}${k.name}</button>`).join('')}</div>`;
    c.appendChild(willCard);
    willCard.querySelectorAll('[data-heir]').forEach(btn=>btn.addEventListener('click', ()=>{
      G.willBeneficiary = btn.dataset.heir;
      pushLog(`Named ${G.willBeneficiary} as primary beneficiary in your will.`);
      toast(`Will updated: ${G.willBeneficiary}`);
      renderAll();
    }));
  }
}

/* ---------- SOCIAL MEDIA TAB ---------- */
function renderSocialTab(c){
  if(!G.phone){ c.innerHTML = lockedHtml("Buy a phone first (Phone tab) to access social media.", "📵"); return; }
  if(inPrison()){ c.innerHTML = lockedHtml("No signal in here — social media is off-limits."); return; }
  const growthThisYear = totalFollowers() - (G.followerSnapshotStart||0);
  const info = document.createElement('div'); info.className='card';
  info.innerHTML = `<div class="card-title">Total followers: ${totalFollowers().toLocaleString()}</div>
    <div class="card-sub">${growthThisYear>=0?'+':''}${growthThisYear.toLocaleString()} followers so far this year</div>
    <div class="card-sub">Post to grow your following. Bigger platforms = better brand deals and more Fame.</div>`;
  c.appendChild(info);

  const nicheCard = document.createElement('div'); nicheCard.className='card';
  if(G.socialNiche){
    nicheCard.innerHTML = `<div class="card-title">Content Niche: ${G.socialNiche}</div>
      <div class="card-sub">Your content leans into ${G.socialNiche.toLowerCase()} — this shapes which brand deals come your way.</div>
      <button class="btn secondary small" style="margin-top:6px;" id="btn-change-niche">Change Niche</button>`;
  } else {
    nicheCard.innerHTML = `<div class="card-title">Pick a Content Niche</div>
      <div class="btn-row">${NICHES.map(n=>`<button class="btn small secondary" data-niche="${n}">${n}</button>`).join('')}</div>`;
  }
  c.appendChild(nicheCard);
  if(document.getElementById('btn-change-niche')) document.getElementById('btn-change-niche').addEventListener('click', ()=>{ G.socialNiche=null; renderAll(); });
  nicheCard.querySelectorAll('[data-niche]').forEach(btn=>btn.addEventListener('click', ()=>{
    G.socialNiche = btn.dataset.niche;
    pushLog(`Settled on a content niche: ${G.socialNiche}.`);
    renderAll();
  }));

  SOCIAL_PLATFORMS.forEach(p=>{
    const used = G.usedThisYear.has('post_'+p.id);
    const verified = G.social[p.id]>=50000;
    const row = document.createElement('div'); row.className='card';
    row.innerHTML = `
      <div class="row-between">
        <div>
          <div class="card-title">${p.emoji} ${p.name} ${verified?'<span class="pill verified">✔ verified</span>':''}</div>
          <div class="card-sub">${G.social[p.id].toLocaleString()} followers</div>
        </div>
        <div class="btn-row">
          <button class="btn small ${used?'secondary':''}" ${used?'disabled':''} data-post="${p.id}">${used?'Posted':'Post'}</button>
          <button class="btn small secondary" data-buyf="${p.id}">Buy Followers (R3,000)</button>
        </div>
      </div>`;
    c.appendChild(row);
  });
  c.querySelectorAll('[data-post]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const pid = btn.dataset.post;
      G.usedThisYear.add('post_'+pid);
      const power = (G.stats.looks + G.stats.smarts + G.stats.connections)/3;
      const nicheBonus = G.socialNiche ? 1.1 : 1;
      const trendMatch = G.trendingBonus && G.socialNiche===G.trendingBonus;
      const gain = Math.round((power/100) * (200 + Math.random()*800) * nicheBonus * (trendMatch?1.5:1));
      G.social[pid] += gain;
      G.followerGainThisYear = (G.followerGainThisYear||0) + gain;
      G.stats.happiness = clampStat(G.stats.happiness+1);
      if(Math.random()<0.06){
        const loss = Math.round(G.social[pid]*0.15);
        G.social[pid] = Math.max(0, G.social[pid]-loss);
        G.stats.happiness = clampStat(G.stats.happiness-4);
        pushLog(`A post backfired and caused some online drama.`);
        toast("Yikes — that post didn't land well.");
      } else {
        pushLog(`Posted on ${SOCIAL_PLATFORMS.find(x=>x.id===pid).name} and gained ${gain} followers.`);
        toast(`+${gain} followers on ${SOCIAL_PLATFORMS.find(x=>x.id===pid).name}!`);
      }
      renderAll();
    });
  });
  c.querySelectorAll('[data-buyf]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      if(G.money<3000){ toast("Not enough cash."); return; }
      const pid = btn.dataset.buyf;
      G.money -= 3000;
      const gain = 5000;
      if(Math.random()<0.15){
        pushLog(`Bought fake followers on ${SOCIAL_PLATFORMS.find(x=>x.id===pid).name} — got caught and called out publicly.`);
        toast("😬 Caught buying followers! Reputation hit.");
        G.stats.happiness = clampStat(G.stats.happiness-4);
        G.fame = clampStat(G.fame-5);
      } else {
        G.social[pid] += gain;
        pushLog(`Quietly bought ${gain} followers on ${SOCIAL_PLATFORMS.find(x=>x.id===pid).name}.`);
        toast(`+${gain} followers (shhh)`);
      }
      renderAll();
    });
  });

  const mgmtTitle = document.createElement('div'); mgmtTitle.className='section-title'; mgmtTitle.textContent='Engage With Your Audience';
  c.appendChild(mgmtTitle);
  const mgmtCard = document.createElement('div'); mgmtCard.className='card';
  mgmtCard.innerHTML = `<div class="btn-row">
    ${btnHtml('engage_fans','💬 Engage With Fans', G.usedThisYear.has('engage_fans'))}
    ${btnHtml('collab','🤝 Collab With Another Creator', G.usedThisYear.has('collab'))}
    ${btnHtml('livestream','🔴 Go Live', G.usedThisYear.has('livestream'))}
  </div>`;
  c.appendChild(mgmtCard);
  wireActButtons(mgmtCard);
  if(G.trendingBonus){
    const trendCard = document.createElement('div'); trendCard.className='card';
    trendCard.innerHTML = `<div class="card-title">🔥 Trending: ${G.trendingBonus}</div><div class="card-sub">Post about this topic for a growth bonus this year.</div>`;
    c.appendChild(trendCard);
  }

  const title = document.createElement('div'); title.className='section-title'; title.textContent='Brand Deals';
  c.appendChild(title);
  const tf = totalFollowers();
  const done = G.usedThisYear.has('deal');
  if(!G.sponsorOffers || !G.sponsorOffers.length){
    const dealCard = document.createElement('div'); dealCard.className='card';
    dealCard.innerHTML = `<div class="card-title">No deals yet</div><div class="card-sub">Reach 1,000+ followers total to attract brand offers.</div>`;
    c.appendChild(dealCard);
  } else {
    G.sponsorOffers.forEach((offer,i)=>{
      const offerCard = document.createElement('div'); offerCard.className='card';
      offerCard.innerHTML = `<div class="card-title">${offer.brand} wants to sponsor you</div>
        <div class="card-sub">Payout: ${money(offer.payout)} · ${offer.note}</div>
        <button class="btn small" ${done?'disabled':''} data-offer="${i}">${done?'Deal already taken this year':'Accept deal'}</button>`;
      c.appendChild(offerCard);
    });
    c.querySelectorAll('[data-offer]').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        if(G.usedThisYear.has('deal')) return;
        G.usedThisYear.add('deal');
        const offer = G.sponsorOffers[btn.dataset.offer];
        G.money += offer.payout;
        G.dealsTakenThisYear = true;
        pushLog(`Landed a brand deal with ${offer.brand} worth ${money(offer.payout)}.`);
        toast(`💰 Brand deal! +${money(offer.payout)}`);
        renderAll();
      });
    });
  }
}

/* ---------- PHONE TAB ---------- */
function lockedHtml(msg, emoji){
  return `<div class="locked-panel"><span class="emoji">${emoji||'🔒'}</span>${msg}</div>`;
}
function renderPhoneTab(c){
  if(inPrison() && G.phone){ c.innerHTML = lockedHtml("Phones aren't allowed where you are."); return; }
  if(!G.phone){
    c.innerHTML = lockedHtml("You don't own a phone yet.", "📵");
    const title = document.createElement('div'); title.className='section-title'; title.textContent='Phone Store';
    c.appendChild(title);
    PHONES.forEach(([n,price],i)=>{
      const row = document.createElement('div'); row.className='card';
      row.innerHTML = `<div class="row-between"><div><div class="card-title">${n}</div><div class="card-sub">${money(price)}</div></div>
        <button class="btn small" data-phone="${i}">Buy</button></div>`;
      c.appendChild(row);
    });
    c.querySelectorAll('[data-phone]').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        const [n,price] = PHONES[btn.dataset.phone];
        if(G.money<price){ toast("Can't afford that phone yet."); return; }
        G.money -= price; G.phone = n;
        pushLog(`Bought a ${n}.`);
        toast(`You now own a ${n}!`);
        renderAll();
      });
    });
    return;
  }
  const info = document.createElement('div'); info.className='card';
  info.innerHTML = `<div class="card-title">📱 ${G.phone}</div><div class="card-sub">Tap an app to use it.</div>`;
  c.appendChild(info);
  const grid = document.createElement('div'); grid.className='app-grid';
  PHONE_APPS.forEach(a=>{
    const used = G.usedThisYear.has(a.id);
    const div = document.createElement('div'); div.className='app-icon';
    div.innerHTML = `<div class="emoji">${a.emoji}</div><div class="lbl">${a.name}</div>`;
    div.addEventListener('click', ()=>runPhoneApp(a, used));
    grid.appendChild(div);
  });
  c.appendChild(grid);
  const hint = document.createElement('div'); hint.className='card-sub'; hint.style.marginTop='12px';
  hint.textContent='Most apps can be used once per year — new uses unlock after your next birthday.';
  c.appendChild(hint);
}
function runPhoneApp(a, used){
  if(a.id==='soc'){ activeTab='social'; renderAll(); return; }
  if(a.id==='jobs'){ activeTab='job'; renderAll(); return; }
  if(a.id==='bank'){ activeTab='bank'; renderAll(); return; }
  if(a.id==='invest'){ activeTab='bank'; renderAll(); return; }
  if(a.id==='date' && !isAdult()){ toast("You need to be 18+ to use dating apps."); return; }
  if(used){ toast(`You already used ${a.name} this year.`); return; }
  G.usedThisYear.add(a.id);
  switch(a.action){
    case 'app_messages':
      G.stats.connections = clampStat(G.stats.connections+3);
      pushLog(`Caught up with friends over text.`);
      toast("+3 Connections");
      break;
    case 'app_camera': {
      G.stats.looks = clampStat(G.stats.looks+1);
      const viral = Math.random() < (G.stats.looks/400);
      if(viral){
        const plat = SOCIAL_PLATFORMS[Math.floor(Math.random()*SOCIAL_PLATFORMS.length)];
        const gain = Math.round(500+Math.random()*3000);
        G.social[plat.id] += gain;
        G.followerGainThisYear = (G.followerGainThisYear||0) + gain;
        pushLog(`A selfie went viral on ${plat.name}! +${gain} followers.`);
        toast(`📸 Viral! +${gain} followers on ${plat.name}`);
      } else {
        pushLog(`Took a selfie.`);
        toast("+1 Looks");
      }
      break;
    }
    case 'app_maps': {
      G.stats.happiness = clampStat(G.stats.happiness+3);
      if(getFameTier(G.fame).label!=='Unknown' && getFameTier(G.fame).label!=='Local Name' && Math.random()<0.2){
        G.stats.happiness = clampStat(G.stats.happiness-4);
        pushLog(`Got mobbed by fans while out and about — flattering but exhausting.`);
        toast("Fans recognized you everywhere!");
      } else if(Math.random()<0.25){
        const found = Math.round(200+Math.random()*1500);
        G.money += found;
        pushLog(`Explored the city and stumbled on a small opportunity worth ${money(found)}.`);
        toast(`Found ${money(found)} while out and about!`);
      } else {
        pushLog(`Explored the city for the day.`);
        toast("+3 Happiness");
      }
      break;
    }
    case 'app_dating': {
      if(G.spouse){
        G.stats.happiness = clampStat(G.stats.happiness+3);
        pushLog(`Browsed the app out of curiosity, but stayed loyal to ${G.spouse.name}.`);
        toast("Stayed faithful.");
      } else {
        const chance = 0.2 + G.stats.looks/300 + G.stats.connections/400;
        if(Math.random()<chance){
          const gender = Math.random()<0.5?'Male':'Female';
          G.spouse = {name: genFirstName(gender)+" "+randOf(SURNAMES), gender, relationship:55, dating:true, engaged:false, married:false};
          G.stats.happiness = clampStat(G.stats.happiness+10);
          G.stats.connections = clampStat(G.stats.connections+5);
          pushLog(`Matched with ${G.spouse.name} and started dating!`);
          toast(`❤️ You're dating ${G.spouse.name}!`);
          maybeGenerateStepKids();
        } else {
          pushLog(`Swiped around but no matches worth keeping.`);
          toast("No matches this time.");
        }
      }
      break;
    }
    case 'app_music':
      G.stats.happiness = clampStat(G.stats.happiness+4);
      pushLog(`Vibed out to amapiano all evening.`);
      toast("+4 Happiness");
      break;
    case 'app_weather':
      G.stats.happiness = clampStat(G.stats.happiness+1);
      pushLog("Checked the forecast and planned your day around it.");
      toast("+1 Happiness");
      break;
    case 'app_ride':
      G.rideDiscount = true;
      pushLog("Booked a discounted ride for your next trip.");
      toast("🚕 20% off your next trip this year!");
      break;
    case 'app_recipe': {
      const cooking = G.hobby==='hb5';
      G.stats.happiness = clampStat(G.stats.happiness+(cooking?4:2));
      pushLog(cooking ? "Tried a new recipe — right up your cooking hobby's alley." : "Tried a new recipe.");
      toast(cooking?"+4 Happiness":"+2 Happiness");
      break;
    }
    case 'app_news': {
      G.stats.smarts = clampStat(G.stats.smarts+1);
      let msg = "No major headlines today.";
      if(G.activeWorldEvent) msg = `Top story: ${G.activeWorldEvent} continues to affect the economy.`;
      if(G.rival) msg += ` Your rival ${G.rival.name} has a net worth of ${money(G.rival.netWorth)}.`;
      pushLog(`Read the news. ${msg}`);
      toast("+1 Smarts");
      break;
    }
    case 'app_fit':
      G.stats.health = clampStat(G.stats.health+(G.gymMembership?3:2));
      pushLog("Logged a workout.");
      toast("+"+(G.gymMembership?3:2)+" Health");
      break;
    case 'app_lingua':
      G.stats.smarts = clampStat(G.stats.smarts+2);
      pushLog("Practiced a new language.");
      toast("+2 Smarts");
      break;
    case 'app_zen':
      G.stats.happiness = clampStat(G.stats.happiness+3);
      G.vices.alcohol = Math.max(0, G.vices.alcohol-3);
      G.vices.smoking = Math.max(0, G.vices.smoking-3);
      pushLog("Took time to meditate and unwind.");
      toast("+3 Happiness");
      break;
    case 'app_famcall':
      if(G.spouse){
        G.spouse.relationship = clampStat(G.spouse.relationship+4);
        pushLog(`Video called ${G.spouse.name}.`);
        toast("+4 Relationship");
      } else {
        G.stats.connections = clampStat(G.stats.connections+2);
        pushLog("Video called an old friend.");
        toast("+2 Connections");
      }
      break;
    case 'app_podcast':
      G.stats.smarts = clampStat(G.stats.smarts+1);
      G.stats.happiness = clampStat(G.stats.happiness+1);
      pushLog("Listened to a podcast.");
      toast("+1 Smarts, +1 Happiness");
      break;
  }
  renderAll();
}

/* ---------- COMPUTER TAB ---------- */
function renderComputerTab(c){
  if(inPrison()){ c.innerHTML = lockedHtml("No computer access behind bars."); return; }
  if(!G.computer){
    c.innerHTML = lockedHtml("You don't own a computer yet.", "🖥️");
    const title = document.createElement('div'); title.className='section-title'; title.textContent='Computer Store';
    c.appendChild(title);
    COMPUTERS.forEach(([n,price],i)=>{
      const row = document.createElement('div'); row.className='card';
      row.innerHTML = `<div class="row-between"><div><div class="card-title">${n}</div><div class="card-sub">${money(price)}</div></div>
        <button class="btn small" data-pc="${i}">Buy</button></div>`;
      c.appendChild(row);
    });
    c.querySelectorAll('[data-pc]').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        const [n,price] = COMPUTERS[btn.dataset.pc];
        if(G.money<price){ toast("Can't afford that yet."); return; }
        G.money -= price; G.computer = n;
        pushLog(`Bought a ${n}.`);
        toast(`You now own a ${n}!`);
        renderAll();
      });
    });
    return;
  }
  const info = document.createElement('div'); info.className='card';
  info.innerHTML = `<div class="card-title">🖥️ ${G.computer}</div><div class="card-sub">Tap a program to use it. Most work once per year.</div>`;
  c.appendChild(info);
  const grid = document.createElement('div'); grid.className='app-grid';
  COMPUTER_SOFTWARE.forEach(a=>{
    const used = G.usedThisYear.has(a.id);
    const div = document.createElement('div'); div.className='app-icon';
    div.innerHTML = `<div class="emoji">${a.emoji}</div><div class="lbl">${a.name}</div>`;
    div.addEventListener('click', ()=>runSoftware(a, used));
    grid.appendChild(div);
  });
  c.appendChild(grid);
}
function runSoftware(a, used){
  if(used){ toast(`Already used ${a.name} this year.`); return; }
  if(a.id==='crypto' && G.money<1000){ toast("You need R1,000 to trade."); return; }
  if(a.id==='nft' && G.money<2000){ toast("You need R2,000 to trade."); return; }
  G.usedThisYear.add(a.id);
  switch(a.action){
    case 'pc_word': {
      const gain = Math.round((300+Math.random()*4000)*(0.5+G.stats.smarts/100));
      G.money += gain;
      pushLog(`Wrote a book and earned royalties.`);
      toast(`Royalties: +${money(gain)}`);
      break;
    }
    case 'pc_sheet': {
      const gain = Math.round(G.money*0.05);
      G.money += gain;
      pushLog(`Budgeted finances carefully and earned interest.`);
      toast(`+${money(gain)} from smart budgeting`);
      break;
    }
    case 'pc_browser':
      G.stats.smarts = clampStat(G.stats.smarts+3);
      pushLog(`Spent the evening researching online.`);
      toast("+3 Smarts");
      break;
    case 'pc_code': {
      const gain = Math.round((1000+Math.random()*19000)*(G.stats.smarts/100));
      G.money += gain;
      pushLog(`Built and sold a small app.`);
      toast(`App sold: +${money(gain)}`);
      break;
    }
    case 'pc_design': {
      const gain = Math.round(500+((G.stats.looks+G.stats.smarts)/2)*40);
      G.money += gain;
      pushLog(`Took on a freelance design gig.`);
      toast(`Freelance gig: +${money(gain)}`);
      break;
    }
    case 'pc_video': {
      const plat = 'yt';
      const gain = Math.round(300+Math.random()*2500);
      G.social[plat] += gain;
      G.followerGainThisYear = (G.followerGainThisYear||0) + gain;
      pushLog(`Edited and posted a vlog on YouView.`);
      toast(`+${gain} YouView followers`);
      break;
    }
    case 'pc_daw': {
      const gain = Math.round(200+Math.random()*3000);
      G.money += gain;
      if(G.career && G.career.kind==='special' && G.career.refId==='sp0'){
        G.stats.connections = clampStat(G.stats.connections+3);
        pushLog(`Produced a beat that's buzzing in music circles.`);
      } else {
        pushLog(`Produced a beat and sold it.`);
      }
      toast(`Beat sold: +${money(gain)}`);
      break;
    }
    case 'pc_crypto': {
      G.money -= 1000;
      if(Math.random()<0.5){
        const win = Math.round(1000+Math.random()*2000);
        G.money += win;
        pushLog(`Crypto trade paid off big.`);
        toast(`📈 Won ${money(win)}!`);
      } else {
        G.stats.happiness = clampStat(G.stats.happiness-3);
        pushLog(`Lost money trading crypto.`);
        toast("📉 Lost your R1,000 stake.");
      }
      break;
    }
    case 'pc_av':
      G.hackShield = true;
      G.stats.happiness = clampStat(G.stats.happiness+1);
      pushLog(`Ran a security scan — feeling safer online.`);
      toast("Protected against hacking this year.");
      break;
    case 'pc_mail':
      G.stats.smarts = clampStat(G.stats.smarts+1);
      if(G.career && G.career.kind==='normal' && G.career.level<4 && Math.random()<0.15 && !G.promotedThisYear){
        G.promotedThisYear = true;
        G.career.level++;
        const car = CAREERS.find(x=>x.id===G.career.refId);
        pushLog(`Landed a surprise promotion offer by email: ${car.levels[G.career.level].title}!`);
        toast(`📧 Surprise promotion: ${car.levels[G.career.level].title}!`);
      } else {
        pushLog(`Checked emails for job leads.`);
        toast("+1 Smarts");
      }
      break;
    case 'pc_engine': {
      const hit = Math.random() < 0.25 + G.stats.smarts/300;
      if(hit){
        const gain = Math.round(5000+Math.random()*45000*(G.stats.smarts/100));
        G.money += gain;
        pushLog(`Shipped an indie game on Pulse Engine that took off!`);
        toast(`🕹️ Game hit! +${money(gain)}`);
      } else {
        pushLog(`Spent the year on an indie game that flopped.`);
        toast("The game didn't sell — better luck next year.");
      }
      break;
    }
    case 'pc_legal':
      G.legalPrepared = true;
      G.stats.smarts = clampStat(G.stats.smarts+2);
      pushLog("Studied up on case law — better prepared if you're ever sued.");
      toast("+2 Smarts, legally prepared this year");
      break;
    case 'pc_campaign':
      if(G.career && G.career.kind==='special' && G.career.refId==='sp5'){
        G.campaignBoost = true;
        pushLog("Planned out your next political campaign in detail.");
        toast("Campaign boost ready for your next election.");
      } else {
        G.stats.connections = clampStat(G.stats.connections+2);
        pushLog("Studied up on political strategy.");
        toast("+2 Connections");
      }
      break;
    case 'pc_proptech': {
      const bonus = Math.round(totalBizIncome()*0.05);
      G.money += bonus;
      pushLog(`Optimized your business logistics for a one-off gain of ${money(bonus)}.`);
      toast(`+${money(bonus)}`);
      break;
    }
    case 'pc_translate':
      G.stats.smarts = clampStat(G.stats.smarts+1);
      pushLog("Translated documents for practice.");
      toast("+1 Smarts");
      break;
    case 'pc_print3d': {
      const gain = Math.round(500+Math.random()*6000);
      G.money += gain;
      pushLog("Designed and sold novelty 3D prints.");
      toast(`+${money(gain)}`);
      break;
    }
    case 'pc_geneal': {
      if(Math.random()<0.08){
        G.stats.connections = clampStat(G.stats.connections+3);
        G.fame = clampStat(G.fame+2);
        pushLog("Discovered a surprising noble ancestor buried in your family tree — quite the conversation starter.");
        toast("🌳 Noble ancestor discovered!");
      } else {
        G.stats.connections = clampStat(G.stats.connections+1);
        pushLog("Dug into your family history.");
        toast("+1 Connections");
      }
      break;
    }
    case 'pc_vr':
      G.stats.happiness = clampStat(G.stats.happiness+4);
      pushLog("Escaped into virtual reality for a while.");
      toast("+4 Happiness");
      break;
    case 'pc_taxopt':
      G.taxOptimized = true;
      pushLog("Set up smarter tax planning for next year.");
      toast("Next year's tax bill will be lower.");
      break;
    case 'pc_podstudio': {
      const plat = 'tw';
      const gain = Math.round(400+Math.random()*2800);
      G.social[plat] += gain;
      G.followerGainThisYear = (G.followerGainThisYear||0) + gain;
      pushLog("Recorded a podcast episode.");
      toast(`+${gain} Chirp followers`);
      break;
    }
    case 'pc_nft': {
      if(G.money<2000){ toast("You need R2,000 to trade."); return; }
      G.money -= 2000;
      if(Math.random()<0.45){
        const win = Math.round(2000+Math.random()*6000);
        G.money += win;
        pushLog("A digital asset trade paid off.");
        toast(`🔗 Won ${money(win)}!`);
      } else {
        G.stats.happiness = clampStat(G.stats.happiness-3);
        pushLog("Lost money on a digital asset trade.");
        toast("📉 Lost your R2,000 stake.");
      }
      break;
    }
  }
  renderAll();
}

/* ---------------------------------------------------------------------
   FAME YEARLY UPDATE
--------------------------------------------------------------------- */
function applyFameYearly(){
  const gainFromFollowers = Math.min(15, Math.floor((G.followerGainThisYear||0)/300));
  let fameGain = gainFromFollowers;
  if(G.career && G.career.kind==='special') fameGain += (G.career.level+1)*3;
  let awardWon=false, awardNominated=false;
  const tierBefore = getFameTier(G.fame);

  if(G.fame>=65){
    if(Math.random()<0.20){ awardWon=true; }
    else if(Math.random()<0.30){ awardNominated=true; }
  }
  if(awardWon){
    const prize = Math.round(20000 + G.fame*3000*Math.random());
    G.money += prize;
    fameGain += 8;
    G.stats.connections = clampStat(G.stats.connections+5);
    pushLog(`Won a major award! +${money(prize)}.`);
    toast("🏆 Award win!");
  } else if(awardNominated){
    G.stats.connections = clampStat(G.stats.connections+2);
    pushLog(`Nominated for a major award.`);
  }

  const decay = G.fame>0 ? Math.max(1, Math.round(G.fame*0.06)) : 0;
  G.fame = clampStat(G.fame + fameGain - decay);
  G.followerGainThisYear = 0;

  const tier = getFameTier(G.fame);
  if(SECURITY_COST[tier.label]){
    const cost = SECURITY_COST[tier.label];
    if(G.money>=cost){
      G.money -= cost;
      pushLog(`Paid ${money(cost)} for security and PR upkeep.`);
    } else {
      G.stats.happiness = clampStat(G.stats.happiness-6);
      pushLog(`Couldn't afford your security team this year — things got chaotic with fans and media.`);
    }
  }
  const paparazziChance = G.fame/400;
  if(Math.random()<paparazziChance){
    const loss = 3+Math.floor(Math.random()*6);
    G.stats.happiness = clampStat(G.stats.happiness-loss);
    pushLog(`Paparazzi hounded you outside your home.`);
  }
  let scandalChance = G.fame/600;
  if(G.career && G.career.kind==='special' && G.career.risky) scandalChance *= 1.5;
  if(Math.random()<scandalChance){
    G.fame = clampStat(G.fame-10);
    G.stats.happiness = clampStat(G.stats.happiness-10);
    const fee = Math.round(5000+Math.random()*45000);
    G.money -= fee;
    pushLog(`A tabloid scandal broke about you — paid ${money(fee)} in legal and PR fees.`);
    toast("📰 Scandal! Fame and Happiness took a hit.");
  }
  if(tier.label==='Global Superstar'){
    const residual = Math.round(G.fame*500);
    G.money += residual;
    pushLog(`Residual royalties from past fame rolled in: +${money(residual)}.`);
  }
  if(tier.label!==tierBefore.label && (tier.label==='Celebrity'||tier.label==='National Icon'||tier.label==='Global Superstar')){
    toast(`🌟 You're now a ${tier.label}!`);
  }
}

/* ---------------------------------------------------------------------
   EDUCATION YEARLY UPDATE
--------------------------------------------------------------------- */
function applyEducationYearly(){
  const e = G.education;
  if(G.age===6 && e.stage==='toddler'){ e.stage='primary'; pushLog("Started primary school."); }
  else if(G.age===13 && e.stage==='primary'){ e.stage='high'; pushLog("Started high school."); }
  else if(G.age===17 && e.stage==='high' && !e.prelimsDone){
    e.prelimsDone = true;
    const preview = G.stats.smarts>=70?"a Bachelor's Pass":(G.stats.smarts>=55?"a Diploma Pass":(G.stats.smarts>=40?"a Higher Certificate Pass":"failing matric"));
    pushLog(`Wrote your prelims — currently on track for ${preview}.`);
    toast("📝 Prelim results are in — check your Life tab.");
  }
  else if(G.age===18 && e.stage==='high'){
    if(G.stats.smarts>=70){ e.stage='graduated_high'; e.matric=true; e.matricResult="Bachelor's Pass"; pushLog("Matriculated with a Bachelor's Pass!"); toast("🎓 Bachelor's Pass!"); }
    else if(G.stats.smarts>=55){ e.stage='graduated_high'; e.matric=true; e.matricResult="Diploma Pass"; pushLog("Matriculated with a Diploma Pass!"); toast("🎓 Diploma Pass!"); }
    else if(G.stats.smarts>=40){ e.stage='graduated_high'; e.matric=true; e.matricResult="Higher Certificate Pass"; pushLog("Matriculated with a Higher Certificate Pass."); toast("🎓 Higher Certificate Pass!"); }
    else { e.stage='dropped_out'; e.matric=false; e.matricResult="Fail"; pushLog("Didn't make the grades — left school without matric."); toast("You failed matric."); }
  }
  if(e.stage==='university'){
    G.stats.smarts = clampStat(G.stats.smarts+2);
    e.uniYearsLeft--;
    if(e.uniYearsLeft<=0){
      e.stage='graduated_uni'; e.hasDegree=true; e.matric=true;
      G.stats.smarts = clampStat(G.stats.smarts+8);
      pushLog(`Graduated from university with a degree in ${e.major}!`);
      toast(`🎓 Graduated in ${e.major}!`);
    }
  }
}

/* ---------------------------------------------------------------------
   AGE UP
--------------------------------------------------------------------- */
document.getElementById('btn-age-up').addEventListener('click', ageUp);

function ageUp(){
  if(!G || !G.alive) return;

  const hadCheckupThisYear = G.usedThisYear.has('doctor_checkup');

  // follower churn for platforms not posted on this (outgoing) year
  SOCIAL_PLATFORMS.forEach(p=>{
    if(!G.usedThisYear.has('post_'+p.id) && G.social[p.id]>0){
      G.social[p.id] = Math.max(0, Math.round(G.social[p.id]*(0.94+Math.random()*0.04)));
    }
  });
  G.followerSnapshotStart = totalFollowers();

  G.age++;
  G.usedThisYear = new Set();
  G.promotedThisYear = false;
  G.hackShield = false;
  G.dealsTakenThisYear = false;

  // sponsor inbox refresh
  const tf2 = totalFollowers();
  G.sponsorOffers = [];
  if(tf2>=1000){
    const tiers = [ [1000,1500], [10000,15000], [100000,120000], [1000000,900000] ];
    const eligible = tiers.filter(t=>tf2>=t[0]).pop();
    const fameMult = 1 + G.fame/100;
    const offerCount = tf2>=100000 ? 2 : 1;
    const usedBrands = [];
    for(let i=0;i<offerCount;i++){
      let brand = randOf(AMBASSADOR_BRANDS);
      while(usedBrands.includes(brand) && usedBrands.length<AMBASSADOR_BRANDS.length){ brand = randOf(AMBASSADOR_BRANDS); }
      usedBrands.push(brand);
      const payout = Math.round(eligible[1]*fameMult*(0.8+Math.random()*0.5));
      const note = G.socialNiche ? `Fits your ${G.socialNiche} content` : "General promotion";
      G.sponsorOffers.push({brand, payout, note});
    }
  }
  // trending topic
  G.trendingBonus = Math.random()<0.2 ? randOf(NICHES) : null;

  simulateRivalYearly();
  if(G.activeWorldEvent){
    G.worldEventYearsLeft--;
    const ev = WORLD_EVENTS.find(e=>e.name===G.activeWorldEvent);
    if(ev && ev.happinessAll) G.stats.happiness = clampStat(G.stats.happiness+ev.happinessAll);
    if(G.worldEventYearsLeft<=0){
      pushLog(`${G.activeWorldEvent} has come to an end.`);
      G.activeWorldEvent = null;
    }
  } else if(Math.random()<0.15){
    const ev = WORLD_EVENTS[Math.floor(Math.random()*WORLD_EVENTS.length)];
    G.activeWorldEvent = ev.name;
    G.worldEventYearsLeft = ev.years;
    pushLog(`World event: ${ev.name} has begun.`);
    toast(`🌍 ${ev.name}`);
    if(ev.happinessAll) G.stats.happiness = clampStat(G.stats.happiness+ev.happinessAll);
  }

  if(!G.pendingLawsuit && !inPrison()){
    const suitChance = 0.02 + G.fame/500 + G.ownedBusinesses.length*0.005;
    if(Math.random()<suitChance){
      const plaintiff = genFirstName(Math.random()<0.5?'Male':'Female')+" "+randOf(SURNAMES);
      const nwGuess = Math.max(5000, netWorth());
      const amount = Math.round(5000 + Math.random()*Math.min(150000, nwGuess*0.15));
      G.pendingLawsuit = {plaintiff, amount};
      pushLog(`${plaintiff} is suing you for ${money(amount)} — check the Life tab.`);
      toast("⚖️ You've been served with a lawsuit!");
    }
  }

  if(!G.pendingInterview && G.fame>=25 && !inPrison() && Math.random()<0.12){
    G.pendingInterview = true;
    pushLog("A journalist has requested an interview — check the Life tab.");
    toast("🎤 Interview request!");
  }

  if(!G.pendingLifeEvent && !inPrison() && Math.random()<0.22){
    triggerLifeEvent();
    if(G.pendingLifeEvent) toast("❗ A decision awaits — check the Life tab.");
  }

  if(G.deferredConsequences.length){
    const stillWaiting = [];
    G.deferredConsequences.forEach(dc=>{
      dc.yearsLeft--;
      if(dc.yearsLeft<=0){
        if(Math.random()<dc.chance){
          dc.effects.forEach(applyEffect);
          pushLog(dc.log);
        }
      } else {
        stillWaiting.push(dc);
      }
    });
    G.deferredConsequences = stillWaiting;
  }

  if(G.rival && Math.random()<0.06){
    const rivalActions = [];
    if((G.usedThisYear.has('deal')===false) && totalFollowers()>=1000) rivalActions.push('deal');
    if(G.spouse && !G.spouse.married) rivalActions.push('flirt');
    rivalActions.push('generic');
    const action = randOf(rivalActions);
    if(action==='deal'){
      pushLog(`${G.rival.name} swooped in and grabbed a brand deal you were being considered for.`);
    } else if(action==='flirt'){
      if(Math.random()<0.3){
        pushLog(`${G.rival.name} tried to make a move on ${G.spouse.name} — but they stayed loyal to you.`);
      } else {
        pushLog(`${G.rival.name} has been getting suspiciously close to ${G.spouse.name} lately.`);
        G.stats.happiness = clampStat(G.stats.happiness-3);
      }
    } else {
      pushLog(`${G.rival.name} has been talking you down to mutual friends.`);
      G.stats.happiness = clampStat(G.stats.happiness-2);
    }
  }

  if(inPrison()){
    G.prison.yearsLeft--;
    const ev = PRISON_EVENTS[Math.floor(Math.random()*PRISON_EVENTS.length)];
    if(ev.h) G.stats.happiness = clampStat(G.stats.happiness+ev.h);
    if(ev.health) G.stats.health = clampStat(G.stats.health+ev.health);
    if(ev.smarts) G.stats.smarts = clampStat(G.stats.smarts+ev.smarts);
    if(ev.c) G.stats.connections = clampStat(G.stats.connections+ev.c);
    pushLog(`In prison: ${ev.text}`);
    if(G.prison.yearsLeft<=0){
      G.prison.active=false;
      pushLog("Released from prison.");
      toast("🔓 You've been released!");
    }
    pushLog(`Turned ${G.age}.`);
    checkAchievements();
    checkDeathAndAdvance();
    return;
  }

  // natural stat drift
  G.stats.happiness = clampStat(G.stats.happiness - (Math.random()<0.4?1:0));
  if(G.age>55) G.stats.health = clampStat(G.stats.health - (1+Math.floor((G.age-55)/12)));
  else G.stats.health = clampStat(G.stats.health + (Math.random()<0.2?1:0));

  // vice consequences
  if(G.vices.alcohol>70){
    G.stats.health = clampStat(G.stats.health-3);
    if(Math.random()<0.25){ G.stats.happiness = clampStat(G.stats.happiness-5); G.money -= 3000; pushLog("Your drinking spiraled and cost you money and happiness."); }
  }
  if(G.vices.smoking>70){
    G.stats.health = clampStat(G.stats.health-3);
  }
  if(G.vices.alcohol>=85 && Math.random()<0.3){
    G.stats.health = clampStat(G.stats.health-8);
    G.stats.happiness = clampStat(G.stats.happiness-10);
    if(G.career && Math.random()<0.4){
      pushLog("Your drinking hit rock bottom and cost you your job.");
      toast("💔 Lost your job to addiction.");
      G.career = null;
    } else {
      pushLog("Your drinking hit rock bottom this year — everything suffered.");
      toast("Rock bottom. Rehab might be the only way out.");
    }
  }
  if(G.vices.smoking>=85 && Math.random()<0.25){
    G.stats.health = clampStat(G.stats.health-6);
    pushLog("Heavy smoking caught up with you this year — a rough health scare.");
  }
  G.vices.alcohol = Math.max(0, G.vices.alcohol-4);
  G.vices.smoking = Math.max(0, G.vices.smoking-4);

  // boss relationship & firing risk
  if(G.boss && G.career && G.career.kind==='normal'){
    G.boss.relationship = clampStat(G.boss.relationship - (Math.random()<0.3?2:0));
    if(G.boss.relationship<20 && Math.random()<0.15){
      const car = CAREERS.find(x=>x.id===G.career.refId);
      pushLog(`${G.boss.name} let you go — things had soured for a while.`);
      toast(`💼 Fired from your job as ${car.levels[G.career.level].title}.`);
      G.career = null; G.boss = null;
      G.stats.happiness = clampStat(G.stats.happiness-10);
    }
  }

  // income
  let incomeThisYear = 0;
  if(G.career){
    if(G.career.kind==='normal'){
      const car = CAREERS.find(x=>x.id===G.career.refId);
      incomeThisYear += currentCareerSalary(car,G.career.level);
    } else {
      const sp = SPECIAL_CAREERS.find(x=>x.id===G.career.refId);
      incomeThisYear += specialSalary(sp, G.career.level);
    }
  }
  if(G.retired) incomeThisYear += G.pension;
  incomeThisYear += totalBizIncome();
  if(G.sideHustle){
    const hustleIncome = sideHustleIncome(G.sideHustle);
    incomeThisYear += hustleIncome;
  }
  if(G.merchLine.active) incomeThisYear += G.merchLine.income;
  if(G.ambassadorDeals.length){
    let dealIncome = 0;
    G.ambassadorDeals.forEach(d=>{ dealIncome += d.payout; d.yearsLeft--; });
    incomeThisYear += dealIncome;
    if(dealIncome>0) pushLog(`Received ${money(dealIncome)} from ambassador deals.`);
    const ended = G.ambassadorDeals.filter(d=>d.yearsLeft<=0);
    ended.forEach(d=>pushLog(`Your ambassador deal with ${d.brand} came to an end.`));
    G.ambassadorDeals = G.ambassadorDeals.filter(d=>d.yearsLeft>0);
    if(G.ambassadorDeals.length && Math.random()<0.05){
      const d = G.ambassadorDeals.shift();
      pushLog(`Your deal with ${d.brand} ended early after a brand controversy.`);
      toast(`📰 ${d.brand} dropped you amid controversy.`);
    }
  }
  G.money += incomeThisYear;

  // business risk events
  if(G.ownedBusinesses.length){
    let bizLossTotal=0, eventsHit=0;
    const riskChance = G.bizManager ? 0.02 : 0.04;
    G.ownedBusinesses.forEach(id=>{
      if(Math.random()<riskChance){
        const item = BIZ.find(b=>b.id===id);
        let loss = Math.round(item.price*(0.08+Math.random()*0.12));
        if(G.bizInsurance) loss = Math.round(loss*0.3);
        bizLossTotal += loss; eventsHit++;
      }
    });
    if(bizLossTotal>0){
      G.money -= bizLossTotal;
      pushLog(`Theft, fire or staff losses hit ${eventsHit} of your businesses — lost ${money(bizLossTotal)}${G.bizInsurance?' (insurance softened the blow)':''}.`);
    }
    if(G.bizInsurance){
      const premium = G.ownedBusinesses.length*1500;
      if(G.money>=premium){ G.money -= premium; }
      else { G.bizInsurance=false; pushLog("Couldn't afford business insurance premiums — coverage lapsed."); }
    }
    if(G.bizManager){
      const mgrFee = G.ownedBusinesses.length*2000;
      if(G.money>=mgrFee){ G.money -= mgrFee; }
      else { G.bizManager=false; pushLog("Couldn't afford your general manager's salary — they quit."); }
    }
  }

  // bankruptcy
  if(netWorth() < -100000){
    G.debt = 0; G.businessLoan = 0; G.marginLoan = 0;
    G.mortgages = [];
    G.ownedBusinesses.forEach(id=>{ delete G.bizUpgrades[id]; });
    G.ownedBusinesses = [];
    G.holdingCompany = false; G.ipoTaken = false; G.bizInsurance = false; G.bizManager = false;
    const halfAssets = G.ownedAssets.slice(0, Math.ceil(G.ownedAssets.length/2));
    G.ownedAssets = G.ownedAssets.filter(id=>!halfAssets.includes(id));
    G.bank.creditScore = 300;
    G.stats.happiness = clampStat(G.stats.happiness-25);
    G.money = 0;
    pushLog("Declared bankruptcy. Businesses, debts, and half your assets are gone — a hard reset.");
    toast("💥 Bankruptcy! Starting over financially.");
  }

  // gym membership & medical aid upkeep
  if(G.gymMembership){
    const gymFee = 3600;
    if(G.money>=gymFee){ G.money -= gymFee; G.stats.health = clampStat(G.stats.health+2); }
    else { G.gymMembership=false; pushLog("Couldn't keep up gym payments — membership lapsed."); }
  }
  if(G.medicalAid){
    const medFee = 1800;
    if(G.money>=medFee){ G.money -= medFee; }
    else { G.medicalAid=false; pushLog("Couldn't keep up medical aid payments — cover lapsed."); }
  }
  // illness risk
  let illnessChance = 0.03 + Math.max(0, G.age-40)*0.003;
  if(G.gymMembership) illnessChance *= 0.8;
  if(hadCheckupThisYear) illnessChance *= 0.6;
  if(Math.random()<illnessChance){
    let healthLoss = 8+Math.floor(Math.random()*10);
    let cost = Math.round(3000+Math.random()*15000);
    if(G.medicalAid){ healthLoss = Math.round(healthLoss*0.5); cost = Math.round(cost*0.3); }
    G.stats.health = clampStat(G.stats.health-healthLoss);
    G.money -= cost;
    pushLog(`Came down with a serious illness and needed hospital treatment — paid ${money(cost)} in medical bills.${G.medicalAid?' Medical aid softened the blow.':''}`);
    toast("🏥 A serious illness hit you hard this year.");
  }

  // vehicle running costs
  const upkeep = totalVehicleUpkeep();
  if(upkeep>0){
    if(G.money>=upkeep){
      G.money -= upkeep;
      pushLog(`Paid ${money(upkeep)} in vehicle insurance, fuel and maintenance.`);
    } else {
      const shortfall = upkeep - Math.max(0,G.money);
      G.money = Math.max(0, G.money-upkeep);
      G.stats.happiness = clampStat(G.stats.happiness-4);
      pushLog(`Couldn't keep up with vehicle running costs — short by ${money(shortfall)}.`);
    }
  }

  // child support for kids lost in a custody battle
  if(G.exChildren.length){
    const support = G.exChildren.reduce((s,k)=>s+k.support,0);
    G.money -= support;
    G.exChildren.forEach(k=>k.age++);
    pushLog(`Paid ${money(support)} in child support.`);
  }

  // tax
  if(incomeThisYear>0){
    let tax = computeTax(incomeThisYear);
    if(G.taxOptimized){ tax = Math.round(tax*0.8); G.taxOptimized = false; }
    if(tax>0){ G.money -= tax; pushLog(`Paid ${money(tax)} in SARS tax.`); }
  }

  // savings interest
  if(G.bank.savings>0) G.bank.savings = Math.round(G.bank.savings*1.06);
  // loan interest
  if(G.debt>0) G.debt += Math.round(G.debt*personalLoanRate());
  if(G.businessLoan>0) G.businessLoan += Math.round(G.businessLoan*businessLoanRate());
  if(G.marginLoan>0){
    G.marginLoan += Math.round(G.marginLoan*0.07);
    let portfolioValue = 0;
    STOCKS.forEach(st=>{ portfolioValue += (G.bank.stocks[st.id]||0)*G.stockPrices[st.id]; });
    if(portfolioValue < G.marginLoan*1.2){
      const shortfall = G.marginLoan - portfolioValue*0.8;
      if(shortfall>0){
        STOCKS.forEach(st=>{ G.bank.stocks[st.id] = 0; });
        G.marginLoan = Math.max(0, Math.round(G.marginLoan - portfolioValue));
        G.stats.happiness = clampStat(G.stats.happiness-8);
        pushLog(`Margin call! Your stock portfolio was liquidated to cover your margin loan.`);
        toast("📉 Margin call — portfolio liquidated!");
      }
    }
  }
  if(G.mortgages.length){
    G.mortgages.forEach(m=>{
      const interest = Math.round(m.balance*m.rate);
      const principal = Math.min(m.balance, m.annualPayment);
      const totalDue = interest+principal;
      if(G.money>=totalDue){ G.money -= totalDue; m.balance -= principal; }
      else { G.money -= Math.max(0,G.money); m.balance += Math.round(interest*0.5); G.bank.creditScore = Math.max(300,G.bank.creditScore-10); pushLog("Missed a mortgage payment — credit score took a hit."); }
    });
    const paidOff = G.mortgages.filter(m=>m.balance<=0);
    paidOff.forEach(m=>{ const item = SHOP.find(s=>s.id===m.assetId); pushLog(`Paid off the mortgage on your ${item?item.name:'property'}!`); toast("🏠 Mortgage paid off!"); });
    G.mortgages = G.mortgages.filter(m=>m.balance>0);
  }
  // stock price random walk
  const eventStockDrift = (function(){
    if(!G.activeWorldEvent) return 0;
    const ev = WORLD_EVENTS.find(e=>e.name===G.activeWorldEvent);
    return ev && ev.stockDrift ? ev.stockDrift : 0;
  })();
  STOCKS.forEach(st=>{
    const change = (Math.random()-0.5)*st.volatility*2 + st.drift + eventStockDrift;
    G.stockPrices[st.id] = Math.max(1, Math.round(G.stockPrices[st.id]*(1+change)*100)/100);
  });
  // insurance premium
  if(G.insurance.active){
    if(G.money>=G.insurance.premium){
      G.money -= G.insurance.premium;
    } else {
      G.insurance.active = false;
      pushLog("Couldn't keep up insurance premiums — policy lapsed.");
    }
  }

  // family aging & relationship decay
  ['mother','father'].forEach(key=>{
    const p = G.family[key];
    if(p && p.alive){
      p.relationship = clampStat(p.relationship - (Math.random()<0.3?1:0));
      const parentAge = G.age + p.ageOffset;
      const deathChance = parentAge>70 ? (parentAge-70)*0.02 : 0;
      if(Math.random()<deathChance){
        p.alive=false;
        G.stats.happiness = clampStat(G.stats.happiness-12);
        pushLog(`Your ${key} passed away.`);
        if(G.fortune!==undefined){
          const inheritance = Math.round(3000+Math.random()*20000*(G.fortune+1));
          G.money += inheritance;
          pushLog(`Received a small inheritance of ${money(inheritance)}.`);
        }
      }
    }
  });
  if(G.spouse) G.spouse.relationship = clampStat(G.spouse.relationship - (Math.random()<0.3?1:0));
  G.children.forEach(k=>k.age++);
  G.stepChildren.forEach(k=>k.age++);
  G.family.grandparents.forEach(gp=>{
    if(gp.alive){
      const gpAge = G.age + gp.ageOffset;
      const deathChance = gpAge>80 ? (gpAge-80)*0.035 : (gpAge>70?0.01:0);
      if(Math.random()<deathChance){
        gp.alive = false;
        G.stats.happiness = clampStat(G.stats.happiness-6);
        pushLog(`Your ${gp.side} ${gp.label.toLowerCase()}, ${gp.name}, passed away.`);
      }
    }
  });

  // education
  applyEducationYearly();
  // fame
  applyFameYearly();

  // random life event
  if(Math.random()<0.55){
    const ev = LIFE_EVENTS[Math.floor(Math.random()*LIFE_EVENTS.length)];
    if(ev.h) G.stats.happiness = clampStat(G.stats.happiness+ev.h);
    if(ev.health) G.stats.health = clampStat(G.stats.health+ev.health);
    if(ev.smarts) G.stats.smarts = clampStat(G.stats.smarts+ev.smarts);
    if(ev.looks) G.stats.looks = clampStat(G.stats.looks+ev.looks);
    if(ev.c) G.stats.connections = clampStat(G.stats.connections+ev.c);
    if(ev.money) G.money += ev.money;
    pushLog(ev.text);
  }

  // ambient arrest risk while hustling in the underworld
  if(G.career && G.career.kind==='special' && G.career.refId==='sp8' && Math.random()<0.05){
    G.prison.active = true; G.prison.yearsLeft = 1+Math.floor(Math.random()*4);
    G.criminalRecord = true; G.career = null;
    pushLog(`Police raided your operation — sentenced to ${G.prison.yearsLeft} years.`);
    toast("🚓 Busted!");
  }

  if(incomeThisYear>0) pushLog(`Earned ${money(incomeThisYear)} this year.`);
  pushLog(`Turned ${G.age}.`);

  checkAchievements();
  checkDeathAndAdvance();
}

function checkDeathAndAdvance(){
  let deathChance = 0.001;
  if(G.age>65) deathChance += (G.age-65)*0.006;
  if(G.age>90) deathChance += 0.15;
  if(G.stats.health<15) deathChance += 0.12;
  if(G.stats.health<=0) deathChance = 1;
  if(Math.random() < deathChance){
    G.alive = false;
    pushLog(`Passed away at age ${G.age}.`);
    showGameOver();
    return;
  }
  renderAll();
}

/* ---------------------------------------------------------------------
   GAME OVER + GENERATIONAL CONTINUATION
--------------------------------------------------------------------- */
let heirPick = null;
function showGameOver(){
  document.getElementById('go-name').textContent = `${G.name} · ${G.place}`;
  let estate = G.money - G.debt - G.businessLoan + G.bank.savings;
  Object.values(G.bank.stocks).forEach((u,i)=>{ estate += Math.round(u*G.stockPrices[STOCKS[i].id]); });
  if(G.insurance.active) estate += G.insurance.payout;
  estate = Math.max(0, estate);

  const careerLine = G.career
    ? (G.career.kind==='normal'
        ? CAREERS.find(x=>x.id===G.career.refId).levels[G.career.level].title
        : SPECIAL_CAREERS.find(x=>x.id===G.career.refId).levels[G.career.level])
    : (G.retired ? 'Retired' : 'Never settled on a career');
  const tier = getFameTier(G.fame);
  const legacyLine = (tier.label==='National Icon'||tier.label==='Global Superstar')
    ? `Your name lives on as a ${tier.label.toLowerCase()} — the legacy endures.`
    : 'Remembered fondly by those who knew you.';

  document.getElementById('go-summary').innerHTML = `
    <div><b>Lived to:</b> ${G.age} years old</div>
    <div><b>Final career:</b> ${careerLine}</div>
    <div><b>Fame:</b> ${tier.label}</div>
    <div><b>Estate left behind:</b> ${money(estate)}</div>
    <div><b>Assets owned:</b> ${G.ownedAssets.length}</div>
    <div><b>Businesses owned:</b> ${G.ownedBusinesses.length}</div>
    <div><b>Total followers:</b> ${totalFollowers().toLocaleString()}</div>
    <div><b>Children:</b> ${G.children.length}</div>
    <div style="margin-top:8px;color:var(--gold);">${legacyLine}</div>
  `;

  const btnWrap = document.getElementById('go-buttons');
  btnWrap.innerHTML = '';
  const restartBtn = document.createElement('button');
  restartBtn.className='btn'; restartBtn.textContent='Start a New Life';
  restartBtn.addEventListener('click', doFullRestart);
  btnWrap.appendChild(restartBtn);

  if(G.children.length>0){
    const heirEstate = estate;
    const sortedChildren = [...G.children].sort((a,b)=>(a.name===G.willBeneficiary?-1:0)-(b.name===G.willBeneficiary?-1:0));
    sortedChildren.forEach(child=>{
      const isWilled = child.name===G.willBeneficiary;
      const continueBtn = document.createElement('button');
      continueBtn.className='btn secondary';
      const label = G.children.length>1 ? `Continue as ${child.name} (age ${child.age})` : `Continue as ${child.name}`;
      continueBtn.textContent = isWilled ? `⭐ ${label} — named heir` : label;
      continueBtn.addEventListener('click', ()=>continueAsHeir(child, heirEstate));
      btnWrap.appendChild(continueBtn);
    });
  }

  switchScreen('screen-gameover');
}

function continueAsHeir(heir, estate){
  const deceasedName = G.name;
  const deceasedSurname = G.surname;
  const deceasedGender = G.gender;
  const survivingSpouse = G.spouse ? {...G.spouse} : null;
  const oldSiblingsAsAunts = G.family.siblings;

  const newG = freshState();
  newG.name = heir.name;
  newG.gender = heir.gender;
  newG.place = G.place;
  newG.surname = deceasedSurname;
  newG.age = heir.age;
  newG.fortune = estate>500000?2:(estate>50000?1:0);
  newG.money = estate;
  const base = rollNewbornStats();
  newG.stats = {
    happiness: clampStat(base.happiness + (G.stats.happiness-50)*0.15),
    health: clampStat(base.health + (G.stats.health-50)*0.15),
    smarts: clampStat(base.smarts + (G.stats.smarts-50)*0.2),
    looks: clampStat(base.looks + (G.stats.looks-50)*0.2),
    connections: clampStat(base.connections + (G.stats.connections-50)*0.15)
  };
  // education stage based on inherited age
  if(newG.age<6) newG.education.stage='toddler';
  else if(newG.age<13) newG.education.stage='primary';
  else if(newG.age<18) newG.education.stage='high';
  else newG.education.stage='graduated_high';

  newG.family.mother = deceasedGender==='Female' ? {name:deceasedName, relationship:80, alive:false, ageOffset:0}
                                                   : (survivingSpouse && survivingSpouse.gender==='Female' ? {name:survivingSpouse.name, relationship:70, alive:true, ageOffset:2} : null);
  newG.family.father = deceasedGender==='Male' ? {name:deceasedName, relationship:80, alive:false, ageOffset:0}
                                                    : (survivingSpouse && survivingSpouse.gender==='Male' ? {name:survivingSpouse.name, relationship:70, alive:true, ageOffset:2} : null);
  if(!newG.family.mother) newG.family.mother = {name:"Unknown", relationship:50, alive:false, ageOffset:0};
  if(!newG.family.father) newG.family.father = {name:"Unknown", relationship:50, alive:false, ageOffset:0};
  newG.family.siblings = G.children.filter(k=>k!==heir).map(k=>({name:k.name, gender:k.gender, relationship:60, ageOffset:k.age-heir.age}));

  pushLogFor(newG, `Inherited the family legacy of the late ${deceasedName} (${money(estate)}).`);

  G = newG;
  activeTab='life';
  switchScreen('screen-game');
  renderAll();
}
function pushLogFor(state, msg){ state.log.unshift({age:state.age, text:msg}); }

function doFullRestart(){
  clearSave();
  G = null;
  pendingStats = rollNewbornStats();
  document.getElementById('cc-name').value='';
  switchScreen('screen-welcome');
  document.getElementById('btn-continue-life').style.display='none';
  document.getElementById('btn-new-life').style.display='inline-block';
  document.getElementById('loadWrap').style.display='none';
  document.getElementById('loadFill').style.width='0%';
}
</script>
</body>
</html>

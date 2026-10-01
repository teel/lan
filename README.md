<!DOCTYPE html>
<html lang="sv">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Gaming Goblin — LAN-partyn i liten skala</title>

<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 120 120'%3E%3Crect width='120' height='120' fill='%230a1109'/%3E%3Cpolygon points='40,40 10,30 38,58' fill='%234ade4a'/%3E%3Cpolygon points='80,40 110,30 82,58' fill='%234ade4a'/%3E%3Cpolygon points='40,34 60,27 80,34 87,56 60,97 33,56' fill='%234ade4a'/%3E%3Cpolygon points='43,54 53,56 51,63 44,61' fill='%23b4ff3a'/%3E%3Cpolygon points='77,54 67,56 69,63 76,61' fill='%23b4ff3a'/%3E%3Cpath d='M57,54 L63,54 L67,73 Q67,81 58,80 Q53,78 55,70 Z' fill='%231f6b27'/%3E%3Cpolygon points='43,83 77,83 68,91 52,91' fill='%2307140a'/%3E%3Cpolygon points='49,83 55,83 52,90' fill='%23eafff0'/%3E%3Cpolygon points='65,83 71,83 68,90' fill='%23eafff0'/%3E%3C/svg%3E">

<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&family=Press+Start+2P&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#0a1109; --bg-alt:#0f1a11; --fg:#c8f7c5; --muted:#6f9d78;
    --accent:#4ade4a; --accent-2:#b4ff3a; --accent-3:#ff5fa2;
    --border:#2a4530; --warn:#ffcf3a; --gold:#ffcf3a;
  }
  *{box-sizing:border-box;margin:0;padding:0}
  html{scroll-behavior:smooth}
  body{background:var(--bg);color:var(--fg);font-family:'JetBrains Mono',monospace;
    line-height:1.6;font-size:15px;position:relative;overflow-x:hidden}
  #grid-bg{position:fixed;inset:0;width:100%;height:100%;z-index:-2;display:block}
  #cave{position:fixed;inset:0;z-index:-1;pointer-events:none;
    background:radial-gradient(ellipse at 50% 40%, transparent 0%, var(--bg) 88%)}

  #crt{position:fixed;inset:0;z-index:999;pointer-events:none;
    background:repeating-linear-gradient(to bottom,
      rgba(0,0,0,0) 0px, rgba(0,0,0,0) 2px,
      rgba(0,0,0,.22) 3px, rgba(0,0,0,.22) 4px);
    mix-blend-mode:multiply}
  #crt::after{content:"";position:absolute;inset:0;
    background:radial-gradient(ellipse at center, transparent 60%, rgba(0,0,0,.35) 100%);
    box-shadow:inset 0 0 120px rgba(0,0,0,.6)}

  .wrap{max-width:900px;margin:0 auto;padding:0 20px}

  .gg-logo .skin{fill:var(--accent)} .gg-logo .skin-dark{fill:#1f6b27}
  .gg-logo .eye{fill:var(--accent-2)} .gg-logo .dark{fill:#07140a}
  .gg-logo .tooth{fill:#eafff0}

  .ticker{background:var(--accent);color:#07140a;font-weight:700;
    border-bottom:2px solid #07140a;overflow:hidden;white-space:nowrap}
  .ticker span{display:inline-block;padding:6px 0;animation:scroll 22s linear infinite}
  @keyframes scroll{from{transform:translateX(100%)}to{transform:translateX(-100%)}}

  .topbar{display:flex;justify-content:space-between;align-items:center;
    border-bottom:1px solid var(--border);padding:10px 0;font-size:13px;backdrop-filter:blur(2px)}
  .topbar a{color:var(--muted);text-decoration:none}
  .topbar a:hover{color:var(--accent-2)}
  .themes{display:flex;gap:8px}
  .swatch{width:18px;height:18px;border:1px solid var(--border);cursor:pointer}
  .swatch:hover{outline:2px solid var(--fg)}

  .hero{padding:60px 0 40px;text-align:center}
  .hero .gg-logo{width:clamp(130px,26vw,220px);height:auto;display:block;margin:0 auto 18px;
    filter:drop-shadow(0 0 18px var(--accent));animation:torch 2.2s infinite steps(8)}
  .hero .gg-logo .eye{filter:drop-shadow(0 0 4px var(--accent-2))}
  @keyframes torch{
    0%,100%{filter:drop-shadow(0 0 14px var(--accent)) drop-shadow(0 0 30px #1a3d1a)}
    50%{filter:drop-shadow(0 0 22px var(--accent-2)) drop-shadow(0 0 44px #2a5d2a)}}

  .wordmark{font-family:'Press Start 2P',monospace;font-size:clamp(28px,7vw,64px);
    color:var(--accent-2);line-height:1.2;text-shadow:3px 3px 0 var(--accent),6px 6px 0 #07140a;
    letter-spacing:1px}
  .slime{display:block;height:26px;margin-top:4px;color:var(--accent);
    font-family:'Press Start 2P';font-size:10px;letter-spacing:6px}
  .slime span{display:inline-block;animation:drip 3s infinite ease-in}
  .slime span:nth-child(2){animation-delay:.6s} .slime span:nth-child(3){animation-delay:1.2s}
  .slime span:nth-child(4){animation-delay:.3s} .slime span:nth-child(5){animation-delay:.9s}
  @keyframes drip{0%,70%{transform:translateY(0);opacity:.9}85%{transform:translateY(10px);opacity:1}100%{transform:translateY(0);opacity:.4}}

  .tagline{color:var(--fg);margin-top:26px;font-size:clamp(14px,2.5vw,20px)}
  .subtag{color:var(--muted);max-width:620px;margin:16px auto 0}

  .countdown{display:flex;justify-content:center;gap:14px;margin-top:34px;flex-wrap:wrap}
  .countdown div{border:1px solid var(--border);background:rgba(15,26,17,.7);
    padding:12px 16px;min-width:74px;backdrop-filter:blur(2px)}
  .countdown b{display:block;font-family:'Press Start 2P';font-size:22px;color:var(--accent-2);
    text-shadow:2px 2px 0 #07140a}
  .countdown small{color:var(--muted);font-size:11px;text-transform:uppercase;letter-spacing:1px}

  .btn{display:inline-block;margin-top:30px;padding:12px 22px;background:transparent;
    color:var(--accent-2);border:2px solid var(--accent-2);text-decoration:none;
    font-weight:700;text-transform:uppercase;letter-spacing:1px;transition:.12s;cursor:pointer;font-family:inherit}
  .btn:hover{background:var(--accent-2);color:#07140a;box-shadow:0 0 18px var(--accent-2)}
  .btn.alt{color:var(--accent-3);border-color:var(--accent-3);margin-left:10px}
  .btn.alt:hover{background:var(--accent-3);color:#07140a;box-shadow:0 0 18px var(--accent-3)}

  section{padding:50px 0;border-top:1px solid var(--border)}
  h2{font-family:'Press Start 2P',monospace;font-size:clamp(16px,3.5vw,24px);
    color:var(--accent);margin-bottom:8px;display:flex;align-items:center;gap:10px}
  h2 .gg-logo{width:34px;height:34px;flex:0 0 auto}
  .lead{color:var(--muted);margin-bottom:26px}

  .grid{display:grid;gap:16px;grid-template-columns:repeat(auto-fit,minmax(240px,1fr))}
  .card{border:1px solid var(--border);background:rgba(15,26,17,.82);padding:18px;backdrop-filter:blur(2px)}
  .card:hover{border-color:var(--accent);box-shadow:0 0 16px rgba(74,222,74,.25)}
  .card .date{color:var(--accent-2);font-weight:700;font-size:13px}
  .card h3{margin:6px 0 8px;color:var(--fg);font-size:17px}
  .card p{color:var(--muted);font-size:13px}
  .tag{display:inline-block;margin-top:12px;font-size:11px;padding:3px 8px;
    border:1px solid var(--accent-3);color:var(--accent-3)}
  .loot{font-size:22px;float:right}

  .loot-bar{display:flex;gap:24px;flex-wrap:wrap;margin-top:10px}
  .loot-bar div{font-size:13px;color:var(--muted)}
  .loot-bar b{display:block;font-family:'Press Start 2P';font-size:20px;color:var(--gold);
    text-shadow:2px 2px 0 #4a3a0a;margin-bottom:4px}

  /* ---- platsbokning ---- */
  .room-meta{display:flex;gap:20px;flex-wrap:wrap;margin-bottom:18px;font-size:13px;color:var(--muted)}
  .room-meta b{color:var(--accent-2)}
  .screen{text-align:center;font-size:11px;letter-spacing:4px;color:var(--muted);
    border:1px dashed var(--border);padding:6px;margin-bottom:4px}
  .room{border:2px solid var(--border);background:rgba(10,17,9,.5);padding:22px;
    position:relative;backdrop-filter:blur(2px);overflow-x:auto}
  .room-floor{display:flex;gap:34px;justify-content:center;min-width:min-content}
  .mid-group{display:flex;gap:14px}
  .trow{display:flex;flex-direction:column;gap:12px}
  .dim-v{position:absolute;right:-26px;top:0;height:100%;display:flex;align-items:center;
    color:var(--muted);font-size:11px;writing-mode:vertical-rl}
  .dim-h{text-align:center;color:var(--muted);font-size:11px;margin-top:8px}
  .table{border:1px solid var(--border);background:rgba(15,26,17,.6);padding:8px 6px;text-align:center}
  .table .tnum{font-size:9px;color:var(--muted);margin-bottom:5px;letter-spacing:1px}
  .seats{display:flex;gap:5px;justify-content:center}
  .seat{width:26px;height:26px;border:1px solid var(--border);cursor:pointer;
    display:flex;align-items:center;justify-content:center;font-size:11px;color:var(--muted);
    background:transparent;transition:.1s}
  .seat:hover{border-color:var(--accent-2)}
  .seat.free{color:var(--accent)}
  .seat.sel{background:var(--accent-2);color:#07140a;border-color:var(--accent-2);box-shadow:0 0 10px var(--accent-2)}
  .seat.taken{background:#241316;color:#6b3a44;border-color:#3a1c2a;cursor:not-allowed}
  .legend{display:flex;gap:18px;flex-wrap:wrap;margin:16px 0;font-size:12px;color:var(--muted)}
  .legend i{display:inline-block;width:14px;height:14px;border:1px solid var(--border);margin-right:6px;vertical-align:-2px}
  .i-free{color:var(--accent)!important} .i-free i{border-color:var(--accent)}
  .i-sel i{background:var(--accent-2);border-color:var(--accent-2)}
  .i-taken i{background:#241316;border-color:#3a1c2a}

  form{border:1px solid var(--border);background:rgba(15,26,17,.82);padding:20px;margin-top:20px;backdrop-filter:blur(2px)}
  form label{display:block;font-size:12px;color:var(--muted);margin:12px 0 4px;text-transform:uppercase;letter-spacing:1px}
  form input{width:100%;padding:10px;background:var(--bg);border:1px solid var(--border);
    color:var(--fg);font-family:inherit;font-size:14px}
  form input:focus{outline:none;border-color:var(--accent-2)}
  .selinfo{margin-top:14px;font-size:13px;color:var(--fg)}
  .selinfo b{color:var(--accent-2)}
  .confirm{margin-top:16px;padding:14px;border:1px solid var(--accent);color:var(--accent-2);
    background:rgba(74,222,74,.08);display:none}
  .full-note{margin-top:14px;padding:14px;border:1px solid var(--accent-3);color:var(--accent-3);
    background:rgba(255,95,162,.08);display:none}

  .tree{font-size:14px} .tree div{padding:3px 0}
  .tree a{color:var(--accent-2);text-decoration:none} .tree a:hover{text-decoration:underline}
  .tree .b{color:var(--muted)}

  ul.rules{list-style:none}
  ul.rules li{padding:6px 0 6px 28px;position:relative;color:var(--fg)}
  ul.rules li::before{content:"🗡";position:absolute;left:0}
  .rules-sub{color:var(--accent-2);font-size:12px;text-transform:uppercase;letter-spacing:1px;
    margin:22px 0 6px}

  footer{border-top:2px solid var(--accent);padding:30px 0;text-align:center;
    color:var(--muted);font-size:13px;background:rgba(10,17,9,.6)}
  footer .gg-logo{width:48px;height:auto;display:block;margin:0 auto 10px}
  code{color:var(--warn)}
</style>
</head>
<body>

  <canvas id="grid-bg"></canvas>
  <div id="cave"></div>
  <div id="crt"></div>

  <svg width="0" height="0" style="position:absolute" aria-hidden="true">
    <symbol id="goblin" viewBox="0 0 120 120">
      <polygon class="skin" points="40,40 10,30 38,58"/>
      <polygon class="skin" points="80,40 110,30 82,58"/>
      <polygon class="skin-dark" points="34,42 18,36 34,52"/>
      <polygon class="skin-dark" points="86,42 102,36 86,52"/>
      <polygon class="skin" points="40,34 60,27 80,34 87,56 60,97 33,56"/>
      <polygon class="skin-dark" points="60,27 80,34 87,56 60,97"/>
      <polygon class="dark" points="41,45 58,52 58,47 42,40"/>
      <polygon class="dark" points="79,45 62,52 62,47 78,40"/>
      <polygon class="eye" points="43,54 53,56 51,63 44,61"/>
      <polygon class="eye" points="77,54 67,56 69,63 76,61"/>
      <rect class="dark" x="46" y="56" width="4" height="4"/>
      <rect class="dark" x="70" y="56" width="4" height="4"/>
      <path class="skin-dark" d="M57,54 L63,54 L67,73 Q67,81 58,80 Q53,78 55,70 Z"/>
      <polygon class="dark" points="43,83 77,83 68,91 52,91"/>
      <polygon class="tooth" points="49,83 55,83 52,90"/>
      <polygon class="tooth" points="65,83 71,83 68,90"/>
      <polygon class="tooth" points="56,91 60,91 58,85"/>
      <polygon class="tooth" points="60,91 64,91 62,85"/>
    </symbol>
  </svg>

  <div class="ticker"><span>▚ GOBLIN LAN #7 BILJETTER SLÄPPTA ▚ TA MED EGEN DATOR ▚ 40 PLATSER · 20 BORD · 24H I STRÄCK · GRATIS RESPAWNS ▚ LOOT: PIZZA + KOFFEIN INGÅR ▚</span></div>

  <div class="wrap">
    <div class="topbar">
      <a href="#">~/goblin-grottan</a>
      <div class="themes" title="Välj ett tema — hela grottan byter skrud (eller tryck T)">
        <div class="swatch" style="background:#4ade4a" onclick="setTheme('#0a1109','#c8f7c5','#4ade4a','#b4ff3a','#ff5fa2','#2a4530')" title="Goblingrön"></div>
        <div class="swatch" style="background:#ff5fa2" onclick="setTheme('#15090f','#ffd9ea','#ff5fa2','#ff9ac4','#b4ff3a','#3a1c2a')" title="Trolldrycksrosa"></div>
        <div class="swatch" style="background:#3aa0ff" onclick="setTheme('#08101c','#cfe6ff','#3aa0ff','#67e8ff','#ffcf3a','#1d3350')" title="Manablå"></div>
        <div class="swatch" style="background:#ffcf3a" onclick="setTheme('#161206','#fff2c2','#ffcf3a','#ffe98a','#4ade4a','#45380f')" title="Guldloot"></div>
      </div>
    </div>

    <header class="hero">
      <svg class="gg-logo" role="img" aria-label="Gaming Goblin-logga"><use href="#goblin"/></svg>
      <div class="wordmark">GAMING<br>GOBLIN
        <span class="slime"><span>.</span><span>.</span><span>.</span><span>.</span><span>.</span></span>
      </div>
      <p class="tagline">LAN-partyn i liten skala för stammen.</p>
      <p class="subtag">Vi släpar in våra riggar i ett mörkt rum, kopplar in oss i en switch, och kryper inte ut förrän solen går upp. Ingen lagg, inget moln, inget krångel — bara lokal multiplayer så som goblins alltid velat ha det.</p>

      <div class="countdown" id="countdown" title="Nedräkning till Goblin LAN #7">
        <div><b id="cd-d">00</b><small>dagar</small></div>
        <div><b id="cd-h">00</b><small>timmar</small></div>
        <div><b id="cd-m">00</b><small>min</small></div>
        <div><b id="cd-s">00</b><small>sek</small></div>
      </div>

      <div>
        <a href="#book" class="btn">⚔ Boka plats</a>
        <a href="#about" class="btn alt">💰 Vad vi gör</a>
      </div>
    </header>

    <section id="about">
      <h2><svg class="gg-logo"><use href="#goblin"/></svg> om_horden</h2>
      <p class="lead">Vilka är dessa goblins egentligen?</p>
      <p>Gaming Goblin är en liten ideell förening som anordnar intima LAN-partyn på plats. Tänk 40 goblins, ett virrvarr av nätverkskablar, och en gemensam besatthet av multiplayer vid tangentbord och soffa. Vi håller det smått med flit — alla känner alla när natten är slut.</p>
      <div class="loot-bar">
        <div><b data-count="47">0</b>LAN anordnade</div>
        <div><b data-count="1200">0</b>goblins matade</div>
        <div><b data-count="6">0</b>pokaler smidda</div>
        <div><b data-count="0">0</b>timmars sömn</div>
      </div>
      <div class="grid" style="margin-top:24px">
        <div class="card"><span class="loot">⚡</span><div class="date">◈ LOKALT FÖRST</div><h3>Noll latens</h3><p>Ett rum, en switch. Spelen körs på LAN:et, inte i molnet. Ping är en myt här nere.</p></div>
        <div class="card"><span class="loot">🖥</span><div class="date">◈ EGEN DATOR</div><h3>Din rigg, dina regler</h3><p>Ta med din egen dator, släpa den till ett bord, koppla in dig och du är i matchen inom några minuter.</p></div>
        <div class="card"><span class="loot">🪓</span><div class="date">◈ GEMENSKAP</div><h3>Litet & taggat</h3><p>Drivs av spelare, för spelare. Inga sponsorer, inga kostymer — bara goblins som älskar spelet.</p></div>
      </div>
    </section>

    <section id="events">
      <h2><svg class="gg-logo"><use href="#goblin"/></svg> kommande_raider</h2>
      <p class="lead">Ta en plats innan horden fyller upp.</p>
      <div class="grid">
        <div class="card"><span class="loot">🌙</span>
          <div class="date">LÖR · 14 NOV · 18:00</div><h3>Goblin LAN #7</h3>
          <p>24-timmars nattpass. CS, Rocket League, Smash och vilket kaos du än tar med dig. 40 platser.</p>
          <span class="tag" id="seats-left-tag">— platser kvar</span>
        </div>
        <div class="card"><span class="loot">❄</span>
          <div class="date">LÖR · 12 DEC · 14:00</div><h3>Vinterns Grottkrypning</h3>
          <p>Co-op-kväll. Deep Rock Galactic, Helldivers och partyspel. Mysigt & avslappnat.</p>
          <span class="tag">öppnar snart</span>
        </div>
        <div class="card"><span class="loot">🏆</span>
          <div class="date">LÖR · 23 JAN · 16:00</div><h3>Goblin Cup — Retro</h3>
          <p>Gammaldags bracket-kväll. CRT-skärmar välkomnas. Quake, UT99, StarCraft. Pokaler handsmidda.</p>
          <span class="tag">ta med en skärm</span>
        </div>
      </div>
    </section>

    <section id="book">
      <h2><svg class="gg-logo"><use href="#goblin"/></svg> boka_plats</h2>
      <p class="lead">Goblin LAN #7 — välj din plats i grottan. Tryck på en ledig plats för att ta den.</p>
      <div class="room-meta">
        <span>RUM: <b>20m × 10m</b></span>
        <span>BORD: <b>20</b> (2 riggar per bord)</span>
        <span>PLATSER: <b>40</b></span>
        <span>LEDIGA: <b id="seats-left">40</b></span>
      </div>

      <div class="screen">▚▚▚ STORBILDSSKÄRM / SCEN ▚▚▚</div>
      <div class="room">
        <div class="room-floor" id="floor"></div>
        <div class="dim-v">10 m</div>
      </div>
      <div class="dim-h">— 20 m —</div>

      <div class="legend">
        <span class="i-free"><i></i>ledig</span>
        <span class="i-sel"><i></i>ditt val</span>
        <span class="i-taken"><i></i>upptagen</span>
      </div>

      <form id="bookform" onsubmit="return book(event)">
        <label>Goblin-namn / tag</label>
        <input type="text" id="f-name" placeholder="t.ex. HuggtandsStina" required>
        <label>E-post (vi skickar raid-detaljerna hit)</label>
        <input type="email" id="f-email" placeholder="du@goblin.grottan" required>
        <div class="selinfo">Valda platser: <b id="selnames">inga än</b></div>
        <button class="btn" type="submit" style="margin-top:20px">Hämta loot & boka</button>
        <div class="confirm" id="confirm"></div>
      </form>
      <div class="full-note" id="fullnote">🛑 Grottan är fullsatt för Goblin LAN #7! Alla 40 platser är tagna. Du ser hela kartan ovan — håll utkik efter nästa raid eller hör av dig så sätter vi dig på väntelistan.</div>
    </section>

    <section id="rules">
      <h2><svg class="gg-logo"><use href="#goblin"/></svg> goblin_utrustning</h2>
      <p class="lead">Packlistan varje goblin måste ha med sig till grottan. Märk gärna allt med namn/tejp!</p>

      <div class="rules-sub">◈ Själva riggen</div>
      <ul class="rules">
        <li>Dator / laptop + alla strömkablar (ta med två om du kan)</li>
        <li>Skärm med skärmkabel (HDMI/DisplayPort) och egen strömkabel</li>
        <li>Tangentbord, mus och musmatta (trådlöst? glöm inte laddkablarna)</li>
        <li>Headset eller hörlurar — <b>inga högtalare</b>, var snäll mot dina medgoblins öron</li>
        <li>Ev. handkontroll för soffspelen</li>
      </ul>

      <div class="rules-sub">◈ Nätverk & ström</div>
      <ul class="rules">
        <li>Nätverkskabel (Cat5e/6), minst 5 meter — utan knutar och gärna en reserv</li>
        <li>Grenuttag / förgreningsdosa (helst med överspänningsskydd)</li>
        <li>Kolla att datorn har en fungerande nätverksport, annars ta med en USB-adapter</li>
      </ul>

      <div class="rules-sub">◈ Mjukvara (fixa hemma!)</div>
      <ul class="rules">
        <li>Alla spel förköpta, nedladdade och uppdaterade till senaste version</li>
        <li>Uppdaterade drivrutiner och operativsystem</li>
        <li>Dammrensad dator — ingen gillar en rigg som tröttnar mitt i raiden</li>
      </ul>

      <div class="rules-sub">◈ Överlevnad</div>
      <ul class="rules">
        <li>Varm tröja eller hoodie — lokalen blir kylig på natten</li>
        <li>Snacks, dryck och energidryck (vi kör 24h i sträck!)</li>
        <li>Kontanter/kort för pizza-insamling och oförutsedda haverier</li>
        <li>Kudde eller sovsäck om du tänker tjuvsova — fast du blir nog retad för det</li>
        <li>Valfritt: liten USB-fläkt, handdukspapper och en powerbank till mobilen</li>
      </ul>

      <p style="margin-top:22px;color:var(--muted)">Husregel: <code>var schysst mot varandra</code>. Giftighet förvisar dig tillbaka till respawn-menyn. Och snoka aldrig i andras datorer.</p>
    </section>

    <section id="links">
      <h2><svg class="gg-logo"><use href="#goblin"/></svg> hitta_grottan</h2>
      <p class="lead">Alla goblinhålor.</p>
      <div class="tree">
        <div><span class="b">├─</span> <a href="#">discord.gg/gaminggoblin</a> <span class="b"># huvudlyan</span></div>
        <div><span class="b">├─</span> <a href="#">twitch.tv/gaminggoblin</a> <span class="b"># event-streams</span></div>
        <div><span class="b">├─</span> <a href="#">instagram.com/gaminggoblin</a> <span class="b"># raid-bilder</span></div>
        <div><span class="b">├─</span> <a href="#">@gaminggoblin</a> <span class="b"># nyheter</span></div>
        <div><span class="b">└─</span> <a href="mailto:hej@gaminggoblin.org">hej@gaminggoblin.org</a> <span class="b"># säg hej</span></div>
      </div>
    </section>
  </div>

  <footer>
    <div class="wrap">
      <svg class="gg-logo"><use href="#goblin"/></svg>
      GAMING GOBLIN · LAN-partyn i liten skala · gjort av goblins, för goblins<br>
      <span style="color:var(--border)">© 2026 — inga rättigheter förbehållna, ha det kul</span>
    </div>
  </footer>

<script>
    /* ---------- Omarchy-stil: value-noise pixelfält + muspekar-glöd ---------- */
  const cv=document.getElementById('grid-bg'), ctx=cv.getContext('2d');
  let W,H,cell=16,cols,rows,t=0, mx=-9999,my=-9999, tmx=-9999,tmy=-9999;
  function resize(){W=cv.width=innerWidth;H=cv.height=innerHeight;cols=Math.ceil(W/cell);rows=Math.ceil(H/cell);}
  addEventListener('resize',resize); resize();
  addEventListener('mousemove',e=>{tmx=e.clientX;tmy=e.clientY;});
  addEventListener('mouseleave',()=>{tmx=-9999;tmy=-9999;});

  function hash(x,y){let n=Math.sin(x*127.1+y*311.7)*43758.5453;return n-Math.floor(n);}
  function vnoise(x,y){
    const xi=Math.floor(x),yi=Math.floor(y),xf=x-xi,yf=y-yi;
    const u=xf*xf*(3-2*xf),v=yf*yf*(3-2*yf);
    const a=hash(xi,yi),b=hash(xi+1,yi),c=hash(xi,yi+1),d=hash(xi+1,yi+1);
    return a*(1-u)+b*u+(c-a)*v*(1-u)+(d-b)*u*v;
  }
  function themeRGB(){
    const c=getComputedStyle(document.documentElement).getPropertyValue('--accent').trim();
    const n=parseInt(c.slice(1),16); return [n>>16,(n>>8)&255,n&255];
  }
  const R=70;                               // glödradie i px kring musen
  function draw(){
    t+=0.006;
    mx+=(tmx-mx)*0.12; my+=(tmy-my)*0.12;    // mjuk följning av musen
    ctx.clearRect(0,0,W,H);
    const [r,g,b]=themeRGB();
    for(let y=0;y<rows;y++)for(let x=0;x<cols;x++){
      // basen: animerat value-noise-fält (som förut)
      let n=(vnoise(x*0.16+t, y*0.16) + vnoise(x*0.07-t*0.6, y*0.09))/2;
      let a=n*n*0.5;
      // muspekar-glöd: lyft pixlarna nära musen (Omarchy-stil)
      const cxp=x*cell+cell/2, cyp=y*cell+cell/2;
      const dist=Math.hypot(cxp-mx,cyp-my);
      let glow=0;
      if(dist<R){ glow=(1-dist/R); a+=glow*0.35; }
      if(a>0.40 || glow>0.3){                // krön + musnära pixlar lyser ljusare
        ctx.fillStyle=`rgba(${Math.min(255,r+90)},${Math.min(255,g+90)},${Math.min(255,b+90)},${a})`;
      }else{
        ctx.fillStyle=`rgba(${r},${g},${b},${a})`;
      }
      ctx.fillRect(x*cell,y*cell,cell-2,cell-2);
    }
    requestAnimationFrame(draw);
  }
  draw();

  /* ---------- temaväljare ---------- */
  function setTheme(bg,fg,a,a2,a3,border){
    const r=document.documentElement.style;
    r.setProperty('--bg',bg);r.setProperty('--fg',fg);r.setProperty('--accent',a);
    r.setProperty('--accent-2',a2);r.setProperty('--accent-3',a3);r.setProperty('--border',border);
    r.setProperty('--bg-alt',shade(bg,8));
  }
  function shade(hex,amt){let n=parseInt(hex.slice(1),16);
    let r=Math.min(255,(n>>16)+amt),g=Math.min(255,((n>>8)&255)+amt),b=Math.min(255,(n&255)+amt);
    return '#'+((1<<24)+(r<<16)+(g<<8)+b).toString(16).slice(1);}
  let swatches=document.querySelectorAll('.swatch'), ti=0;
  addEventListener('keydown',e=>{if(e.key.toLowerCase()==='t'){ti=(ti+1)%swatches.length;swatches[ti].click();}});

  /* ---------- loot-räknare ---------- */
  addEventListener('load',()=>{document.querySelectorAll('[data-count]').forEach(el=>{
    const target=+el.dataset.count;let n=0;const step=Math.max(1,target/60);
    const iv=setInterval(()=>{n+=step;if(n>=target){n=target;clearInterval(iv);}el.textContent=Math.floor(n);},25);});});

  /* ---------- live-nedräkning ---------- */
  const LAN=new Date('2026-11-14T18:00:00');
  function tick(){
    let s=Math.max(0,(LAN-new Date())/1000);
    const d=Math.floor(s/86400); s-=d*86400;
    const h=Math.floor(s/3600);  s-=h*3600;
    const m=Math.floor(s/60);    s-=m*60;
    const p=n=>String(n).padStart(2,'0');
    cd_d.textContent=p(d);cd_h.textContent=p(h);cd_m.textContent=p(m);cd_s.textContent=p(Math.floor(s));
  }
  setInterval(tick,1000); tick();

  /* ---------- platsbokning: 1 rad på varje sida + 2 rader i mitten ---------- */
  const TOTAL=40;
  const taken=new Set(['3-A','3-B','7-A','11-B','14-A','14-B','18-A','5-B','9-A']);
  let selected=new Set();
  const floor=document.getElementById('floor');

  function makeTable(t){
    const tb=document.createElement('div'); tb.className='table';
    tb.innerHTML=`<div class="tnum">B${String(t).padStart(2,'0')}</div>`;
    const sw=document.createElement('div'); sw.className='seats';
    ['A','B'].forEach(side=>{
      const id=t+'-'+side;
      const s=document.createElement('button'); s.type='button'; s.textContent=side;
      s.className='seat '+(taken.has(id)?'taken':'free'); s.dataset.id=id;
      if(!taken.has(id)) s.onclick=()=>toggle(id,s);
      sw.appendChild(s);
    });
    tb.appendChild(sw); return tb;
  }
  function makeRow(nums){
    const row=document.createElement('div'); row.className='trow';
    nums.forEach(t=>row.appendChild(makeTable(t)));
    return row;
  }
  // vänster sidrad
  floor.appendChild(makeRow([1,2,3,4,5]));
  // två rader i mitten
  const mid=document.createElement('div'); mid.className='mid-group';
  mid.appendChild(makeRow([6,7,8,9,10]));
  mid.appendChild(makeRow([11,12,13,14,15]));
  floor.appendChild(mid);
  // höger sidrad
  floor.appendChild(makeRow([16,17,18,19,20]));

  function toggle(id,el){
    if(selected.has(id)){selected.delete(id);el.classList.remove('sel');el.classList.add('free');}
    else{selected.add(id);el.classList.add('sel');el.classList.remove('free');}
    refresh();
  }
  function refresh(){
    const free=TOTAL-taken.size-selected.size;
    document.getElementById('seats-left').textContent=free;
    document.getElementById('seats-left-tag').textContent=(TOTAL-taken.size)+' platser kvar';
    document.getElementById('selnames').textContent=selected.size?[...selected].map(x=>'B'+x).join(', '):'inga än';
    // om fullt: dölj formuläret, visa hela kartan + info
    const full=(TOTAL-taken.size)<=0;
    document.getElementById('bookform').style.display=full?'none':'block';
    document.getElementById('fullnote').style.display=full?'block':'none';
  }
  refresh();

  function book(e){
    e.preventDefault();
    if(!selected.size){alert('Välj minst en ledig plats, goblin!');return false;}
    selected.forEach(id=>{taken.add(id);
      const el=document.querySelector('.seat[data-id="'+id+'"]');el.className='seat taken';el.onclick=null;});
    const name=document.getElementById('f-name').value;
    const picks=[...selected].map(x=>'B'+x).join(', ');
    const c=document.getElementById('confirm');
    c.style.display='block';
    c.innerHTML='👺 Bokat! <b>'+picks+'</b> reserverat för <b>'+name+'</b>. Kolla din e-post för raid-detaljer. Vi ses i grottan!';
    selected.clear(); document.getElementById('bookform').reset(); refresh();
    return false;
  }
</script>
</body>
</html>

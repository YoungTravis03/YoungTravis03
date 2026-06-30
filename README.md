<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Citadel Threat Intelligence Group — Break into security, done right</title>
<meta name="description" content="Field-tested guidance through Security+, CySA+, and PenTest+ — built from real SOC work, live bug bounty, and a Kali lab you can follow command for command." />
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Chivo:wght@400;700;900&family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<style>
  :root{
    --obsidian:#0B0F14;
    --slate:#131A22;
    --slate-2:#0F151C;
    --steel:#243038;
    --amber:#F2A93B;
    --amber-dim:rgba(242,169,59,.12);
    --bone:#E6EAEE;
    --muted:#8A98A6;
    --cyan:#5FB3C4;
    --ok:#5FC48A;
    --display:'Chivo',system-ui,sans-serif;
    --mono:'JetBrains Mono',ui-monospace,monospace;
    --body:'Inter',system-ui,sans-serif;
    --wrap:1140px;
  }
  *{box-sizing:border-box;margin:0;padding:0}
  html{scroll-behavior:smooth}
  body{
    background:var(--obsidian);
    color:var(--bone);
    font-family:var(--body);
    line-height:1.6;
    -webkit-font-smoothing:antialiased;
    overflow-x:hidden;
  }
  .wrap{max-width:var(--wrap);margin:0 auto;padding:0 28px}
  a{color:inherit;text-decoration:none}
  ::selection{background:var(--amber);color:var(--obsidian)}

  /* ---------- mono eyebrow / classification-style tags ---------- */
  .tag{
    font-family:var(--mono);
    font-size:12px;
    letter-spacing:.18em;
    text-transform:uppercase;
    color:var(--amber);
    display:inline-flex;align-items:center;gap:8px;
  }
  .tag::before{content:"//";color:var(--steel);font-weight:700}

  /* ---------- nav ---------- */
  header{
    position:sticky;top:0;z-index:50;
    background:rgba(11,15,20,.78);
    backdrop-filter:blur(12px);
    border-bottom:1px solid var(--steel);
  }
  .nav{display:flex;align-items:center;justify-content:space-between;height:68px}
  .brand{display:flex;align-items:center;gap:12px;font-family:var(--display);font-weight:900;letter-spacing:.02em}
  .brand svg{width:30px;height:30px;display:block}
  .brand b{font-size:17px}
  .brand span{font-family:var(--mono);font-size:10px;letter-spacing:.22em;color:var(--muted);display:block;text-transform:uppercase;font-weight:500}
  .navlinks{display:flex;align-items:center;gap:34px}
  .navlinks a{font-size:14px;color:var(--muted);transition:color .2s}
  .navlinks a:hover{color:var(--bone)}
  .btn{
    font-family:var(--mono);font-size:13px;font-weight:500;letter-spacing:.04em;
    background:var(--amber);color:#10141a;
    padding:11px 20px;border-radius:2px;border:1px solid var(--amber);
    cursor:pointer;transition:transform .15s, box-shadow .2s;display:inline-block;
  }
  .btn:hover{transform:translateY(-2px);box-shadow:0 8px 24px -8px var(--amber)}
  .btn-ghost{background:transparent;color:var(--bone);border:1px solid var(--steel)}
  .btn-ghost:hover{border-color:var(--amber);color:var(--amber);box-shadow:none}
  .menu-toggle{display:none;background:none;border:1px solid var(--steel);color:var(--bone);width:42px;height:38px;border-radius:2px;cursor:pointer;font-size:18px}

  /* ---------- hero ---------- */
  .hero{position:relative;padding:96px 0 88px;border-bottom:1px solid var(--steel);overflow:hidden}
  .hero-grid{display:grid;grid-template-columns:1.15fr .85fr;gap:56px;align-items:center}
  .hero h1{
    font-family:var(--display);font-weight:900;
    font-size:clamp(38px,5.4vw,68px);line-height:1.02;letter-spacing:-.02em;
    margin:22px 0 0;
  }
  .hero h1 .am{color:var(--amber)}
  .hero p.lead{color:var(--muted);font-size:clamp(16px,1.4vw,19px);max-width:46ch;margin-top:22px}
  .hero-cta{display:flex;gap:14px;margin-top:34px;flex-wrap:wrap}
  .hero-note{font-family:var(--mono);font-size:12px;color:var(--steel);margin-top:26px;letter-spacing:.04em}
  .hero-note b{color:var(--muted);font-weight:400}

  /* signature: stepped ascent ramparts */
  .ascent{width:100%;height:auto;display:block}
  .ascent .ridge{fill:none;stroke:var(--amber);stroke-width:2.5;stroke-linejoin:round;
    stroke-dasharray:1400;stroke-dashoffset:1400;animation:draw 2.2s ease forwards .3s}
  .ascent .grid-line{stroke:var(--steel);stroke-width:1}
  .ascent .stepfill{fill:var(--amber-dim)}
  .ascent .lbl{font-family:'JetBrains Mono',monospace;font-size:11px;fill:var(--muted);letter-spacing:.12em}
  .ascent .lbl b{fill:var(--bone)}
  .ascent .dot{fill:var(--amber)}
  @keyframes draw{to{stroke-dashoffset:0}}

  /* ---------- section frame ---------- */
  .section{padding:88px 0;border-bottom:1px solid var(--steel)}
  .section-head{max-width:60ch;margin-bottom:48px}
  .section-head h2{font-family:var(--display);font-weight:900;font-size:clamp(28px,3.4vw,42px);line-height:1.05;letter-spacing:-.01em;margin:16px 0 14px}
  .section-head p{color:var(--muted);font-size:17px;max-width:54ch}

  /* ---------- the path / tiers ---------- */
  .tiers{position:relative;display:flex;flex-direction:column;gap:0}
  .tier{
    display:grid;grid-template-columns:96px 1fr auto;gap:28px;align-items:start;
    padding:34px 0;border-top:1px solid var(--steel);position:relative;
  }
  .tier:last-child{border-bottom:1px solid var(--steel)}
  .tier-num{font-family:var(--mono);font-size:13px;color:var(--amber);letter-spacing:.1em;padding-top:6px}
  .tier-num span{display:block;color:var(--steel);font-size:11px;margin-top:4px}
  .tier-body h3{font-family:var(--display);font-weight:700;font-size:24px;display:flex;align-items:center;gap:14px;flex-wrap:wrap}
  .tier-role{font-family:var(--mono);font-size:11px;letter-spacing:.16em;text-transform:uppercase;color:var(--cyan);border:1px solid var(--steel);padding:3px 9px;border-radius:2px}
  .tier-body p{color:var(--muted);margin-top:10px;max-width:60ch}
  .tier-status{font-family:var(--mono);font-size:12px;letter-spacing:.06em;white-space:nowrap;padding-top:8px;display:flex;align-items:center;gap:8px}
  .status-dot{width:7px;height:7px;border-radius:50%;display:inline-block}
  .live .status-dot{background:var(--ok);box-shadow:0 0 0 3px rgba(95,196,138,.15)}
  .live{color:var(--ok)}
  .soon .status-dot{background:var(--steel)}
  .soon{color:var(--muted)}
  .tier:hover .tier-body h3{color:var(--amber);transition:color .2s}

  /* ---------- videos ---------- */
  .vid-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:22px}
  .vid{background:var(--slate);border:1px solid var(--steel);border-radius:4px;overflow:hidden;transition:transform .2s,border-color .2s}
  .vid:hover{transform:translateY(-4px);border-color:var(--amber)}
  .vid-thumb{position:relative;aspect-ratio:16/9;background:
    linear-gradient(135deg,#16202b,#0d141b);display:flex;align-items:center;justify-content:center;border-bottom:1px solid var(--steel)}
  .vid-thumb .play{width:54px;height:54px;border-radius:50%;border:1.5px solid var(--amber);display:flex;align-items:center;justify-content:center;color:var(--amber);transition:background .2s,color .2s}
  .vid:hover .play{background:var(--amber);color:var(--obsidian)}
  .vid-thumb .dur{position:absolute;bottom:10px;right:10px;font-family:var(--mono);font-size:11px;background:rgba(11,15,20,.8);padding:3px 7px;border-radius:2px;color:var(--muted)}
  .vid-meta{padding:18px}
  .vid-meta .vtag{font-family:var(--mono);font-size:11px;letter-spacing:.12em;text-transform:uppercase;color:var(--cyan)}
  .vid-meta h4{font-family:var(--display);font-weight:700;font-size:18px;margin:8px 0 6px}
  .vid-meta p{color:var(--muted);font-size:14px}

  /* ---------- field ops ---------- */
  .ops{display:grid;grid-template-columns:1fr 1.25fr;gap:24px;align-items:stretch}
  .op-card{background:var(--slate);border:1px solid var(--steel);border-radius:4px;padding:30px}
  .op-card h3{font-family:var(--display);font-weight:700;font-size:22px;display:flex;align-items:center;gap:12px}
  .op-card p{color:var(--muted);margin-top:12px}
  .op-card .h1-mark{font-family:var(--mono);font-size:12px;color:var(--amber);border:1px solid var(--steel);border-radius:2px;padding:2px 8px;letter-spacing:.08em}
  .terminal{background:var(--slate-2);border:1px solid var(--steel);border-radius:4px;overflow:hidden;display:flex;flex-direction:column}
  .term-bar{display:flex;align-items:center;gap:7px;padding:11px 14px;border-bottom:1px solid var(--steel)}
  .term-bar i{width:11px;height:11px;border-radius:50%;display:block;background:var(--steel)}
  .term-bar i:first-child{background:#3a2a2a}
  .term-bar span{font-family:var(--mono);font-size:11px;color:var(--muted);margin-left:8px;letter-spacing:.05em}
  .term-body{padding:20px;font-family:var(--mono);font-size:13px;line-height:1.85;overflow-x:auto;flex:1}
  .term-body .pr{color:var(--amber)}
  .term-body .fl{color:var(--cyan)}
  .term-body .ok{color:var(--ok)}
  .term-body .cm{color:var(--muted)}
  .term-line{white-space:pre;opacity:0;animation:fadein .35s ease forwards}

  @keyframes fadein{to{opacity:1}}

  /* ---------- register ---------- */
  .reg-grid{display:grid;grid-template-columns:.9fr 1.1fr;gap:56px;align-items:start}
  .reg-copy h2{font-family:var(--display);font-weight:900;font-size:clamp(28px,3.4vw,42px);line-height:1.05;margin:16px 0 14px}
  .reg-copy p{color:var(--muted);font-size:17px;max-width:44ch}
  .reg-perks{list-style:none;margin-top:26px;display:flex;flex-direction:column;gap:13px}
  .reg-perks li{font-size:15px;color:var(--bone);display:flex;gap:12px;align-items:flex-start}
  .reg-perks li::before{content:"▸";color:var(--amber);font-size:14px;line-height:1.6}
  form{background:var(--slate);border:1px solid var(--steel);border-radius:6px;padding:30px}
  .field{margin-bottom:18px}
  .field label{display:block;font-family:var(--mono);font-size:12px;letter-spacing:.08em;text-transform:uppercase;color:var(--muted);margin-bottom:8px}
  .field input,.field select,.field textarea{
    width:100%;background:var(--slate-2);border:1px solid var(--steel);border-radius:3px;
    color:var(--bone);font-family:var(--body);font-size:15px;padding:12px 14px;transition:border-color .2s}
  .field input:focus,.field select:focus,.field textarea:focus{outline:none;border-color:var(--amber)}
  .field textarea{resize:vertical;min-height:80px}
  .checks{display:flex;gap:10px;flex-wrap:wrap}
  .check{flex:1;min-width:120px}
  .check input{display:none}
  .check label{
    display:block;text-align:center;cursor:pointer;border:1px solid var(--steel);border-radius:3px;
    padding:12px 8px;font-family:var(--mono);font-size:13px;color:var(--muted);
    text-transform:none;letter-spacing:0;margin:0;transition:all .2s}
  .check input:checked + label{border-color:var(--amber);color:var(--amber);background:var(--amber-dim)}
  form .btn{width:100%;text-align:center;margin-top:8px;font-size:14px;padding:14px}
  .form-success{display:none;text-align:center;padding:30px 10px}
  .form-success.show{display:block}
  .form-success .ico{width:56px;height:56px;border-radius:50%;border:1.5px solid var(--ok);color:var(--ok);display:flex;align-items:center;justify-content:center;margin:0 auto 16px;font-size:26px}
  .form-success h3{font-family:var(--display);font-weight:700;font-size:22px;margin-bottom:8px}
  .form-success p{color:var(--muted)}
  .privacy{font-family:var(--mono);font-size:11px;color:var(--steel);margin-top:16px;text-align:center;letter-spacing:.03em}

  /* ---------- footer ---------- */
  footer{padding:56px 0 40px}
  .foot-top{display:flex;justify-content:space-between;align-items:flex-start;gap:30px;flex-wrap:wrap;padding-bottom:30px;border-bottom:1px solid var(--steel)}
  .foot-links{display:flex;gap:30px;flex-wrap:wrap}
  .foot-links a{font-size:14px;color:var(--muted);transition:color .2s}
  .foot-links a:hover{color:var(--amber)}
  .foot-legal{margin-top:26px;font-family:var(--mono);font-size:11.5px;color:var(--muted);line-height:1.9;letter-spacing:.02em}
  .foot-legal .dim{color:var(--steel)}

  /* ---------- reveal ---------- */
  .reveal{opacity:0;transform:translateY(20px);transition:opacity .7s ease,transform .7s ease}
  .reveal.in{opacity:1;transform:none}

  /* ---------- responsive ---------- */
  @media(max-width:880px){
    .hero-grid,.ops,.reg-grid{grid-template-columns:1fr;gap:40px}
    .hero-visual{order:-1;max-width:420px}
    .vid-grid{grid-template-columns:1fr;max-width:440px}
    .navlinks{position:fixed;inset:68px 0 auto 0;background:var(--obsidian);border-bottom:1px solid var(--steel);
      flex-direction:column;align-items:flex-start;gap:0;padding:8px 28px 20px;transform:translateY(-120%);transition:transform .3s;z-index:40}
    .navlinks.open{transform:none}
    .navlinks a{padding:14px 0;width:100%;border-bottom:1px solid var(--slate)}
    .navlinks .btn{margin-top:12px}
    .menu-toggle{display:block}
    .tier{grid-template-columns:54px 1fr;gap:18px}
    .tier-status{grid-column:1/-1;padding-top:4px}
  }
  @media(prefers-reduced-motion:reduce){
    *{animation:none!important;transition:none!important}
    .reveal{opacity:1;transform:none}
    .ascent .ridge{stroke-dashoffset:0}
  }
</style>
</head>
<body>

<!-- ================= NAV ================= -->
<header>
  <div class="wrap nav">
    <a href="#top" class="brand" aria-label="Citadel Threat Intelligence Group home">
      <!-- shield + battlement ascent mark -->
      <svg viewBox="0 0 40 40" aria-hidden="true">
        <path d="M20 2 L35 8 V20 C35 30 28 36 20 39 C12 36 5 30 5 20 V8 Z"
              fill="none" stroke="#F2A93B" stroke-width="2"/>
        <path d="M13 25 V20 H17 V16 H22 V12 H27 V25 Z" fill="#F2A93B" opacity="0.9"/>
      </svg>
      <span style="display:flex;flex-direction:column;line-height:1.1">
        <b>CITADEL</b>
        <span>Threat Intelligence Group</span>
      </span>
    </a>
    <button class="menu-toggle" id="menuBtn" aria-label="Toggle menu">≡</button>
    <nav class="navlinks" id="navlinks">
      <a href="#path">The Path</a>
      <a href="#work">Watch the Work</a>
      <a href="#ops">Field Ops</a>
      <a href="#register" class="btn">Register</a>
    </nav>
  </div>
</header>

<a id="top"></a>

<!-- ================= HERO ================= -->
<section class="hero">
  <div class="wrap hero-grid">
    <div class="hero-copy">
      <span class="tag reveal">Citadel — Threat Intelligence Group</span>
      <h1 class="reveal">Break into security.<br>The hard way, <span class="am">done right.</span></h1>
      <p class="lead reveal">Field-tested guidance through Security+, CySA+, and PenTest+ — built from real SOC work, live bug bounty, and a Kali lab you can follow command for command.</p>
      <div class="hero-cta reveal">
        <a href="#path" class="btn">Start the path</a>
        <a href="#work" class="btn btn-ghost">Watch the work</a>
      </div>
      <p class="hero-note reveal"><b>No braindumps. No shortcuts. Just the route.</b></p>
    </div>
    <div class="hero-visual reveal">
      <!-- SIGNATURE: the climb — three ramparts ascending, one per credential -->
      <svg class="ascent" viewBox="0 0 420 340" role="img" aria-label="A stepped citadel rampart ascending in three tiers, one per credential">
        <line class="grid-line" x1="0" y1="300" x2="420" y2="300"/>
        <line class="grid-line" x1="0" y1="220" x2="420" y2="220" opacity=".5"/>
        <line class="grid-line" x1="0" y1="140" x2="420" y2="140" opacity=".3"/>
        <!-- step fills -->
        <path class="stepfill" d="M30 300 V250 H160 V300 Z"/>
        <path class="stepfill" d="M160 300 V180 H290 V300 Z"/>
        <path class="stepfill" d="M290 300 V90 H400 V300 Z"/>
        <!-- the climbing ridge -->
        <path class="ridge" d="M30 300 V250 H70 V238 H110 V250 H160 V180 H200 V168 H240 V180 H290 V90 H330 V78 H370 V90 H400"/>
        <!-- markers -->
        <circle class="dot" cx="95" cy="250" r="4"/>
        <circle class="dot" cx="225" cy="180" r="4"/>
        <circle class="dot" cx="345" cy="90" r="4"/>
        <text class="lbl" x="30" y="320">01 <tspan style="fill:#E6EAEE">SECURITY+</tspan></text>
        <text class="lbl" x="165" y="320">02 <tspan style="fill:#E6EAEE">CYSA+</tspan></text>
        <text class="lbl" x="295" y="320">03 <tspan style="fill:#E6EAEE">PENTEST+</tspan></text>
      </svg>
    </div>
  </div>
</section>

<!-- ================= THE PATH ================= -->
<section class="section" id="path">
  <div class="wrap">
    <div class="section-head reveal">
      <span class="tag">The Path</span>
      <h2>Three credentials. One climb.</h2>
      <p>Each tier builds on the last — defend before you attack, analyze before you exploit. Start where you are; the route is the same.</p>
    </div>
    <div class="tiers">
      <div class="tier reveal">
        <div class="tier-num">01<span>FOUNDATION</span></div>
        <div class="tier-body">
          <h3>Security+ <span class="tier-role">Baseline</span></h3>
          <p>The vocabulary the whole field runs on — core security concepts, risk, cryptography, and architecture. The credential every defender and attacker shares, and the one that gets you in the door.</p>
        </div>
        <div class="tier-status live"><span class="status-dot"></span>Guidance live</div>
      </div>
      <div class="tier reveal">
        <div class="tier-num">02<span>ANALYST</span></div>
        <div class="tier-body">
          <h3>CySA+ <span class="tier-role">Blue Team</span></h3>
          <p>Reading the logs, hunting the threat. Detection engineering, SIEM, and behavioral analysis — the blue-team muscle that makes you genuinely dangerous on either side of an engagement.</p>
        </div>
        <div class="tier-status live"><span class="status-dot"></span>In progress</div>
      </div>
      <div class="tier reveal">
        <div class="tier-num">03<span>OPERATOR</span></div>
        <div class="tier-body">
          <h3>PenTest+ <span class="tier-role">Red Team</span></h3>
          <p>Now you go on offense. Recon, exploitation, post-exploitation, and reporting — the full engagement, the way it actually runs in the field, not the way a slide deck describes it.</p>
        </div>
        <div class="tier-status soon"><span class="status-dot"></span>Coming soon</div>
      </div>
    </div>
  </div>
</section>

<!-- ================= WATCH THE WORK ================= -->
<section class="section" id="work">
  <div class="wrap">
    <div class="section-head reveal">
      <span class="tag">Watch the Work</span>
      <h2>Learn from the work, not the slides.</h2>
      <p>Walkthroughs pulled straight from what I'm doing in the field. Drop your own videos into these slots as the library grows.</p>
    </div>
    <div class="vid-grid">
      <!-- To embed a real video, replace the .vid-thumb block with:
           <div class="vid-thumb"><iframe width="100%" height="100%" src="https://www.youtube.com/embed/VIDEO_ID" frameborder="0" allowfullscreen></iframe></div> -->
      <article class="vid reveal">
        <div class="vid-thumb">
          <div class="play" aria-hidden="true">▶</div>
          <span class="dur">12:40</span>
        </div>
        <div class="vid-meta">
          <span class="vtag">CySA+ · Blue</span>
          <h4>Threat hunt in Microsoft Sentinel</h4>
          <p>Reconstructing an attacker's path on a compromised host with KQL.</p>
        </div>
      </article>
      <article class="vid reveal">
        <div class="vid-thumb">
          <div class="play" aria-hidden="true">▶</div>
          <span class="dur">18:05</span>
        </div>
        <div class="vid-meta">
          <span class="vtag">PenTest+ · Recon</span>
          <h4>Recon pipeline in Kali</h4>
          <p>subfinder → httpx → naabu → nuclei, chained end to end on a VPS.</p>
        </div>
      </article>
      <article class="vid reveal">
        <div class="vid-thumb">
          <div class="play" aria-hidden="true">▶</div>
          <span class="dur">15:22</span>
        </div>
        <div class="vid-meta">
          <span class="vtag">PenTest+ · Web</span>
          <h4>Burp Suite for bug bounty</h4>
          <p>From intercept to a real, reportable finding on HackerOne.</p>
        </div>
      </article>
    </div>
  </div>
</section>

<!-- ================= FIELD OPS ================= -->
<section class="section" id="ops">
  <div class="wrap">
    <div class="section-head reveal">
      <span class="tag">Field Ops</span>
      <h2>This isn't theory. It's logged.</h2>
      <p>Every technique on this site is one I'm running on real targets and rebuilding in a lab you can stand up yourself.</p>
    </div>
    <div class="ops">
      <div class="op-card reveal">
        <h3><span class="h1-mark">H1</span> Live on HackerOne</h3>
        <p>Active bug bounty against real, in-scope targets — recon, testing, and responsible disclosure through the HackerOne platform. The work drives the lessons, not the other way around.</p>
      </div>
      <div class="op-card reveal" style="padding:0;overflow:hidden">
        <div class="terminal">
          <div class="term-bar"><i></i><i></i><i></i><span>kali@citadel: ~/recon</span></div>
          <div class="term-body" id="term">
            <div class="term-line" style="animation-delay:.1s"><span class="pr">$</span> subfinder -d target.tld -silent <span class="cm">\</span></div>
            <div class="term-line" style="animation-delay:.5s">  | httpx -silent -title -tech-detect <span class="cm">\</span></div>
            <div class="term-line" style="animation-delay:.9s">  | naabu -top-ports 1000 -silent <span class="cm">\</span></div>
            <div class="term-line" style="animation-delay:1.3s">  | nuclei -severity high,critical</div>
            <div class="term-line cm" style="animation-delay:1.8s">[INF] enumerated 214 subdomains · 61 live</div>
            <div class="term-line" style="animation-delay:2.2s"><span class="fl">[high]</span> exposed-config · api.target.tld</div>
            <div class="term-line" style="animation-delay:2.5s"><span class="ok">[done]</span> handed off to manual review</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ================= REGISTER ================= -->
<section class="section" id="register" style="border-bottom:none">
  <div class="wrap reg-grid">
    <div class="reg-copy reveal">
      <span class="tag">Get Started</span>
      <h2>Get the path in your inbox.</h2>
      <p>Tell me where you are. I'll point you at the right tier and send new videos, labs, and walkthroughs as they drop.</p>
      <ul class="reg-perks">
        <li>A starting point matched to your current level</li>
        <li>New walkthroughs and labs the moment they're published</li>
        <li>The exact tools and pipeline I use in the field</li>
      </ul>
    </div>
    <div>
      <form id="regForm" novalidate>
        <div class="field">
          <label for="name">Name</label>
          <input type="text" id="name" name="name" autocomplete="name" required>
        </div>
        <div class="field">
          <label for="email">Email</label>
          <input type="email" id="email" name="email" autocomplete="email" required>
        </div>
        <div class="field">
          <label for="level">Where are you now?</label>
          <select id="level" name="level">
            <option>Just exploring the field</option>
            <option>Studying for Security+</option>
            <option>Working on CySA+</option>
            <option>Going for PenTest+</option>
            <option>Already working in security</option>
          </select>
        </div>
        <div class="field">
          <label>Which track interests you?</label>
          <div class="checks">
            <div class="check"><input type="checkbox" id="t1" name="track" value="Security+"><label for="t1">Security+</label></div>
            <div class="check"><input type="checkbox" id="t2" name="track" value="CySA+"><label for="t2">CySA+</label></div>
            <div class="check"><input type="checkbox" id="t3" name="track" value="PenTest+"><label for="t3">PenTest+</label></div>
          </div>
        </div>
        <div class="field">
          <label for="goal">What's your goal? <span style="text-transform:none;letter-spacing:0">(optional)</span></label>
          <textarea id="goal" name="goal" placeholder="First security job, a promotion, switching from IT…"></textarea>
        </div>
        <button type="submit" class="btn">Join the climb</button>
        <p class="privacy">Your details are used only to send you guidance. No spam, ever.</p>
      </form>
      <div class="form-success" id="success">
        <div class="ico" aria-hidden="true">✓</div>
        <h3>You're in.</h3>
        <p>Watch your inbox — the first step toward your tier is on its way.</p>
      </div>
    </div>
  </div>
</section>

<!-- ================= FOOTER ================= -->
<footer>
  <div class="wrap">
    <div class="foot-top">
      <a href="#top" class="brand">
        <svg viewBox="0 0 40 40" aria-hidden="true" style="width:30px;height:30px">
          <path d="M20 2 L35 8 V20 C35 30 28 36 20 39 C12 36 5 30 5 20 V8 Z" fill="none" stroke="#F2A93B" stroke-width="2"/>
          <path d="M13 25 V20 H17 V16 H22 V12 H27 V25 Z" fill="#F2A93B" opacity="0.9"/>
        </svg>
        <span style="display:flex;flex-direction:column;line-height:1.1">
          <b>CITADEL</b>
          <span>Threat Intelligence Group</span>
        </span>
      </a>
      <nav class="foot-links">
        <a href="#path">The Path</a>
        <a href="#work">Watch the Work</a>
        <a href="#ops">Field Ops</a>
        <a href="#register">Register</a>
      </nav>
    </div>
    <div class="foot-legal">
      Citadel Threat Intelligence Group PLLC &nbsp;<span class="dim">·</span>&nbsp; Fernandina Beach, FL<br>
      &copy; <span id="yr"></span> Citadel Threat Intelligence Group PLLC. Built and maintained by Travis Young.<br>
      <span class="dim">Independent training and guidance. Not affiliated with or endorsed by CompTIA. Security+, CySA+, and PenTest+ are trademarks of CompTIA.</span>
    </div>
  </div>
</footer>

<script>
  // year
  document.getElementById('yr').textContent = new Date().getFullYear();

  // mobile nav
  const menuBtn = document.getElementById('menuBtn');
  const navlinks = document.getElementById('navlinks');
  menuBtn.addEventListener('click', () => navlinks.classList.toggle('open'));
  navlinks.querySelectorAll('a').forEach(a => a.addEventListener('click', () => navlinks.classList.remove('open')));

  // scroll reveal
  const io = new IntersectionObserver((entries) => {
    entries.forEach(e => { if (e.isIntersecting) { e.target.classList.add('in'); io.unobserve(e.target); } });
  }, { threshold: 0.12 });
  document.querySelectorAll('.reveal').forEach(el => io.observe(el));

  // registration form — front-end only.
  // NOTE: this does not store data anywhere yet. To actually capture sign-ups when
  // you host the page, point it at a form backend (e.g. Formspree, Netlify Forms,
  // or your own endpoint) by adding action/method to <form> and removing preventDefault.
  const form = document.getElementById('regForm');
  const success = document.getElementById('success');
  form.addEventListener('submit', (e) => {
    e.preventDefault();
    const name = document.getElementById('name');
    const email = document.getElementById('email');
    if (!name.value.trim() || !email.value.trim() || !email.checkValidity()) {
      (!name.value.trim() ? name : email).focus();
      return;
    }
    form.style.display = 'none';
    success.classList.add('show');
    success.scrollIntoView({ behavior: 'smooth', block: 'center' });
  });
</script>
</body>
</html>

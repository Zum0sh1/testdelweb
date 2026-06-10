import { useState, useEffect, useRef, useCallback } from "react";

// ─── PALETTE & CONSTANTS ────────────────────────────────────────────────────
const GOLD = "#D4AF37";
const GOLD_LIGHT = "#F5D97E";
const GOLD_DIM = "#8A6F1E";
const BG = "#0A0A0A";
const BG2 = "#111111";
const BG3 = "#1A1A1A";

// ─── GOOGLE FONTS (Cinzel + Inter) ──────────────────────────────────────────
const FontLoader = () => (
  <style>{`
    @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;600;700;900&family=Cinzel+Decorative:wght@400;700&family=Inter:wght@300;400;500;600&display=swap');

    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html { scroll-behavior: smooth; }
    body { background: ${BG}; color: #f0ead6; font-family: 'Inter', sans-serif; overflow-x: hidden; }

    /* Scrollbar */
    ::-webkit-scrollbar { width: 4px; }
    ::-webkit-scrollbar-track { background: #0A0A0A; }
    ::-webkit-scrollbar-thumb { background: ${GOLD_DIM}; border-radius: 2px; }

    /* Cinzel helper */
    .cinzel { font-family: 'Cinzel', serif; }
    .cinzel-deco { font-family: 'Cinzel Decorative', serif; }

    /* Gold gradient text */
    .gold-text {
      background: linear-gradient(135deg, ${GOLD_DIM} 0%, ${GOLD} 40%, ${GOLD_LIGHT} 70%, ${GOLD} 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    /* Particle canvas */
    #particles-canvas { position: absolute; inset: 0; pointer-events: none; z-index: 1; }

    /* Nav */
    .nav-bar {
      position: fixed; top: 0; left: 0; right: 0; z-index: 100;
      transition: all 0.5s cubic-bezier(0.4,0,0.2,1);
      padding: 20px 0;
    }
    .nav-bar.scrolled {
      background: rgba(10,10,10,0.92);
      backdrop-filter: blur(20px);
      -webkit-backdrop-filter: blur(20px);
      border-bottom: 1px solid rgba(212,175,55,0.2);
      padding: 12px 0;
      box-shadow: 0 4px 40px rgba(0,0,0,0.6);
    }

    /* Section reveal */
    .reveal { opacity: 0; transform: translateY(40px); transition: opacity 0.8s cubic-bezier(0.4,0,0.2,1), transform 0.8s cubic-bezier(0.4,0,0.2,1); }
    .reveal.visible { opacity: 1; transform: translateY(0); }

    /* Card hover */
    .menu-card {
      background: linear-gradient(135deg, #1a1a1a 0%, #151515 100%);
      border: 1px solid rgba(212,175,55,0.15);
      border-radius: 12px;
      transition: all 0.4s cubic-bezier(0.4,0,0.2,1);
      cursor: pointer;
      overflow: hidden;
      position: relative;
    }
    .menu-card::before {
      content: ''; position: absolute; inset: 0;
      background: linear-gradient(135deg, rgba(212,175,55,0.05) 0%, transparent 60%);
      opacity: 0; transition: opacity 0.4s;
    }
    .menu-card:hover { transform: translateY(-6px) scale(1.02); border-color: rgba(212,175,55,0.5); box-shadow: 0 20px 60px rgba(212,175,55,0.15), 0 0 30px rgba(212,175,55,0.08); }
    .menu-card:hover::before { opacity: 1; }

    /* Pizza card */
    .pizza-card {
      background: linear-gradient(135deg, #161616 0%, #111 100%);
      border: 1px solid rgba(212,175,55,0.12);
      border-radius: 16px;
      transition: all 0.45s cubic-bezier(0.4,0,0.2,1);
      position: relative; overflow: hidden;
    }
    .pizza-card::after {
      content: ''; position: absolute; inset: 0;
      background: radial-gradient(ellipse at top left, rgba(212,175,55,0.08) 0%, transparent 60%);
      opacity: 0; transition: opacity 0.45s;
    }
    .pizza-card:hover { transform: translateY(-8px); border-color: rgba(212,175,55,0.45); box-shadow: 0 24px 70px rgba(0,0,0,0.6), 0 0 40px rgba(212,175,55,0.12); }
    .pizza-card:hover::after { opacity: 1; }

    /* Gold divider */
    .gold-divider {
      height: 1px;
      background: linear-gradient(90deg, transparent, ${GOLD}, transparent);
      margin: 0 auto;
    }

    /* Typewriter cursor */
    .cursor { display: inline-block; width: 2px; height: 1.1em; background: ${GOLD}; margin-left: 2px; animation: blink 1s infinite; vertical-align: middle; }
    @keyframes blink { 0%,50%,100% { opacity: 1; } 51%,99% { opacity: 0; } }

    /* Hero parallax */
    .hero-bg {
      position: absolute; inset: -20%;
      background-image: url('https://images.unsplash.com/photo-1513104890138-7c749659a591?w=1600&q=80');
      background-size: cover; background-position: center;
      transition: transform 0.1s linear;
      filter: brightness(0.3) saturate(0.7);
    }

    /* Pulsing gold ring */
    @keyframes ring-pulse {
      0%, 100% { box-shadow: 0 0 0 0 rgba(212,175,55,0.4); }
      50% { box-shadow: 0 0 0 16px rgba(212,175,55,0); }
    }
    .gold-ring { animation: ring-pulse 3s infinite; }

    /* Spin slow */
    @keyframes spin-slow { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
    .spin-slow { animation: spin-slow 20s linear infinite; }

    /* Float */
    @keyframes float { 0%,100% { transform: translateY(0px); } 50% { transform: translateY(-12px); } }
    .float { animation: float 4s ease-in-out infinite; }

    /* Glow pulse */
    @keyframes glow-pulse { 0%,100% { text-shadow: 0 0 20px rgba(212,175,55,0.3); } 50% { text-shadow: 0 0 50px rgba(212,175,55,0.7), 0 0 100px rgba(212,175,55,0.3); } }
    .glow-pulse { animation: glow-pulse 3s ease-in-out infinite; }

    /* Stagger children */
    .stagger > * { opacity: 0; transform: translateY(30px); transition: opacity 0.6s, transform 0.6s; }
    .stagger.visible > *:nth-child(1) { opacity:1; transform:translateY(0); transition-delay:0.05s; }
    .stagger.visible > *:nth-child(2) { opacity:1; transform:translateY(0); transition-delay:0.15s; }
    .stagger.visible > *:nth-child(3) { opacity:1; transform:translateY(0); transition-delay:0.25s; }
    .stagger.visible > *:nth-child(4) { opacity:1; transform:translateY(0); transition-delay:0.35s; }
    .stagger.visible > *:nth-child(5) { opacity:1; transform:translateY(0); transition-delay:0.45s; }
    .stagger.visible > *:nth-child(6) { opacity:1; transform:translateY(0); transition-delay:0.55s; }
    .stagger.visible > *:nth-child(7) { opacity:1; transform:translateY(0); transition-delay:0.65s; }
    .stagger.visible > *:nth-child(8) { opacity:1; transform:translateY(0); transition-delay:0.75s; }
    .stagger.visible > *:nth-child(9) { opacity:1; transform:translateY(0); transition-delay:0.85s; }
    .stagger.visible > *:nth-child(10) { opacity:1; transform:translateY(0); transition-delay:0.95s; }
    .stagger.visible > *:nth-child(11) { opacity:1; transform:translateY(0); transition-delay:1.05s; }
    .stagger.visible > *:nth-child(12) { opacity:1; transform:translateY(0); transition-delay:1.15s; }

    /* Shine effect on hover */
    .shine-hover { overflow: hidden; }
    .shine-hover::after {
      content: ''; position: absolute; top: -50%; left: -60%; width: 30%; height: 200%;
      background: linear-gradient(105deg, transparent, rgba(255,255,255,0.08), transparent);
      transform: skewX(-20deg); transition: left 0.6s;
    }
    .shine-hover:hover::after { left: 130%; }

    /* Number badge */
    .num-badge {
      width: 28px; height: 28px; border-radius: 50%;
      border: 1.5px solid ${GOLD};
      display: flex; align-items: center; justify-content: center;
      font-family: 'Cinzel', serif; font-size: 11px; color: ${GOLD};
      flex-shrink: 0;
    }
    .num-badge.red { border-color: #c0392b; color: #e74c3c; }

    /* Mobile menu */
    .mobile-menu {
      position: fixed; top: 0; right: 0; bottom: 0; width: 80vw; max-width: 320px;
      background: rgba(15,15,15,0.97); backdrop-filter: blur(30px);
      border-left: 1px solid rgba(212,175,55,0.2);
      z-index: 200; transform: translateX(100%);
      transition: transform 0.45s cubic-bezier(0.4,0,0.2,1);
      padding: 80px 32px 40px;
    }
    .mobile-menu.open { transform: translateX(0); }

    /* Ingredient chip */
    .chip {
      display: inline-flex; align-items: center; gap: 6px;
      background: rgba(212,175,55,0.08); border: 1px solid rgba(212,175,55,0.25);
      border-radius: 999px; padding: 6px 14px; font-size: 12px; color: #d0c090;
      cursor: pointer; transition: all 0.3s; user-select: none;
    }
    .chip.selected { background: rgba(212,175,55,0.25); border-color: ${GOLD}; color: ${GOLD_LIGHT}; }
    .chip:hover { border-color: rgba(212,175,55,0.6); }

    /* Section header decoration */
    .section-eyebrow { font-family:'Cinzel',serif; font-size:11px; letter-spacing:0.35em; color:${GOLD_DIM}; text-transform:uppercase; }

    /* Form inputs */
    .form-input {
      width: 100%; background: rgba(255,255,255,0.04); border: 1px solid rgba(212,175,55,0.2);
      border-radius: 8px; padding: 14px 18px; color: #f0ead6; font-family: 'Inter', sans-serif;
      font-size: 14px; outline: none; transition: border-color 0.3s;
    }
    .form-input:focus { border-color: rgba(212,175,55,0.6); background: rgba(212,175,55,0.04); }
    .form-input::placeholder { color: rgba(240,234,214,0.3); }

    /* Btn gold */
    .btn-gold {
      display: inline-flex; align-items: center; justify-content: center; gap: 8px;
      background: linear-gradient(135deg, ${GOLD_DIM}, ${GOLD}, ${GOLD_LIGHT}, ${GOLD});
      background-size: 200% 200%; background-position: 0% 50%;
      color: #0a0a0a; font-family: 'Cinzel', serif; font-size: 13px;
      letter-spacing: 0.15em; font-weight: 700; padding: 16px 36px;
      border-radius: 4px; border: none; cursor: pointer;
      transition: all 0.4s cubic-bezier(0.4,0,0.2,1);
    }
    .btn-gold:hover { background-position: 100% 50%; transform: translateY(-2px); box-shadow: 0 8px 30px rgba(212,175,55,0.4); }

    /* Modal */
    .modal-overlay {
      position: fixed; inset: 0; background: rgba(0,0,0,0.85); z-index: 300;
      display: flex; align-items: center; justify-content: center;
      opacity: 0; pointer-events: none; transition: opacity 0.35s;
      backdrop-filter: blur(8px);
    }
    .modal-overlay.open { opacity: 1; pointer-events: all; }
    .modal-box {
      background: #141414; border: 1px solid rgba(212,175,55,0.25);
      border-radius: 20px; padding: 40px; max-width: 480px; width: 90%;
      transform: scale(0.9) translateY(20px); transition: transform 0.4s cubic-bezier(0.4,0,0.2,1);
    }
    .modal-overlay.open .modal-box { transform: scale(1) translateY(0); }

    /* Thin gold line animated */
    @keyframes line-grow { from { width: 0; } to { width: 80px; } }
    .line-grow { animation: line-grow 1s 0.5s forwards; width: 0; }

    /* Section bg patterns */
    .bg-grid {
      background-image: linear-gradient(rgba(212,175,55,0.03) 1px, transparent 1px), linear-gradient(90deg, rgba(212,175,55,0.03) 1px, transparent 1px);
      background-size: 40px 40px;
    }

    @media (max-width: 768px) {
      .hide-mobile { display: none !important; }
      .show-mobile { display: flex !important; }
    }
    @media (min-width: 769px) {
      .show-mobile { display: none !important; }
    }
  `}</style>
);

// ─── PARTICLE SYSTEM ─────────────────────────────────────────────────────────
const Particles = () => {
  const canvasRef = useRef(null);
  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext("2d");
    const resize = () => { canvas.width = canvas.offsetWidth; canvas.height = canvas.offsetHeight; };
    resize();
    window.addEventListener("resize", resize);
    const particles = Array.from({ length: 55 }, () => ({
      x: Math.random() * canvas.width,
      y: Math.random() * canvas.height,
      r: Math.random() * 1.5 + 0.3,
      vx: (Math.random() - 0.5) * 0.3,
      vy: -Math.random() * 0.5 - 0.1,
      alpha: Math.random() * 0.6 + 0.2,
    }));
    let raf;
    const draw = () => {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      particles.forEach(p => {
        ctx.beginPath();
        ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
        ctx.fillStyle = `rgba(212,175,55,${p.alpha})`;
        ctx.fill();
        p.x += p.vx; p.y += p.vy;
        if (p.y < -5) { p.y = canvas.height + 5; p.x = Math.random() * canvas.width; }
        if (p.x < 0) p.x = canvas.width; if (p.x > canvas.width) p.x = 0;
      });
      raf = requestAnimationFrame(draw);
    };
    draw();
    return () => { cancelAnimationFrame(raf); window.removeEventListener("resize", resize); };
  }, []);
  return <canvas ref={canvasRef} id="particles-canvas" style={{ position: "absolute", inset: 0, width: "100%", height: "100%", pointerEvents: "none", zIndex: 1 }} />;
};

// ─── TYPEWRITER HOOK ─────────────────────────────────────────────────────────
const useTypewriter = (text, speed = 60, delay = 800) => {
  const [displayed, setDisplayed] = useState("");
  const [done, setDone] = useState(false);
  useEffect(() => {
    let i = 0;
    const t = setTimeout(() => {
      const interval = setInterval(() => {
        setDisplayed(text.slice(0, i + 1));
        i++;
        if (i >= text.length) { clearInterval(interval); setDone(true); }
      }, speed);
      return () => clearInterval(interval);
    }, delay);
    return () => clearTimeout(t);
  }, [text, speed, delay]);
  return { displayed, done };
};

// ─── REVEAL HOOK ─────────────────────────────────────────────────────────────
const useReveal = (threshold = 0.15) => {
  const ref = useRef(null);
  const [visible, setVisible] = useState(false);
  useEffect(() => {
    const obs = new IntersectionObserver(([e]) => { if (e.isIntersecting) { setVisible(true); obs.disconnect(); } }, { threshold });
    if (ref.current) obs.observe(ref.current);
    return () => obs.disconnect();
  }, [threshold]);
  return { ref, visible };
};

// ─── SECTION HEADER ──────────────────────────────────────────────────────────
const SectionHeader = ({ eyebrow, title, subtitle }) => {
  const { ref, visible } = useReveal();
  return (
    <div ref={ref} className={`reveal ${visible ? "visible" : ""}`} style={{ textAlign: "center", marginBottom: 56 }}>
      <p className="section-eyebrow" style={{ marginBottom: 16 }}>{eyebrow}</p>
      <h2 className="cinzel gold-text" style={{ fontSize: "clamp(28px,5vw,52px)", fontWeight: 700, lineHeight: 1.1, marginBottom: 20 }}>{title}</h2>
      <div className="gold-divider" style={{ width: 80, marginBottom: 20 }} />
      {subtitle && <p style={{ color: "rgba(240,234,214,0.55)", fontSize: 15, maxWidth: 520, margin: "0 auto", lineHeight: 1.7 }}>{subtitle}</p>}
    </div>
  );
};

// ─── PRICE TAG ───────────────────────────────────────────────────────────────
const Price = ({ value, large }) => (
  <span className="cinzel" style={{ color: GOLD, fontSize: large ? 22 : 16, fontWeight: 700, letterSpacing: "0.05em" }}>
    € {value}
  </span>
);

// ─── NAV ─────────────────────────────────────────────────────────────────────
const navLinks = [
  { label: "Antipasti", href: "#antipasti" },
  { label: "Pizze", href: "#pallone" },
  { label: "Tradizionali", href: "#tradizionali" },
  { label: "Insalata", href: "#insalata" },
  { label: "Bevande", href: "#bevande" },
  { label: "Dolci", href: "#dolci" },
  { label: "Prenota", href: "#prenotazioni" },
];

const Nav = () => {
  const [scrolled, setScrolled] = useState(false);
  const [mobileOpen, setMobileOpen] = useState(false);
  useEffect(() => {
    const onScroll = () => setScrolled(window.scrollY > 60);
    window.addEventListener("scroll", onScroll);
    return () => window.removeEventListener("scroll", onScroll);
  }, []);
  const scrollTo = (href) => { setMobileOpen(false); document.querySelector(href)?.scrollIntoView({ behavior: "smooth" }); };
  return (
    <>
      <nav className={`nav-bar ${scrolled ? "scrolled" : ""}`}>
        <div style={{ maxWidth: 1200, margin: "0 auto", padding: "0 24px", display: "flex", alignItems: "center", justifyContent: "space-between" }}>
          <button onClick={() => scrollTo("#hero")} style={{ background: "none", border: "none", cursor: "pointer" }}>
            <span className="cinzel-deco gold-text" style={{ fontSize: 28, fontWeight: 700 }}>442</span>
          </button>
          <div className="hide-mobile" style={{ display: "flex", gap: 32, alignItems: "center" }}>
            {navLinks.map(l => (
              <button key={l.href} onClick={() => scrollTo(l.href)} style={{ background: "none", border: "none", cursor: "pointer", color: "rgba(240,234,214,0.7)", fontSize: 12, fontFamily: "'Cinzel',serif", letterSpacing: "0.12em", textTransform: "uppercase", transition: "color 0.3s" }}
                onMouseEnter={e => e.target.style.color = GOLD} onMouseLeave={e => e.target.style.color = "rgba(240,234,214,0.7)"}>
                {l.label}
              </button>
            ))}
          </div>
          <button className="show-mobile" onClick={() => setMobileOpen(true)} style={{ background: "none", border: "none", cursor: "pointer", flexDirection: "column", gap: 5, padding: 4 }}>
            {[0, 1, 2].map(i => <span key={i} style={{ display: "block", width: 22, height: 1.5, background: GOLD, borderRadius: 1 }} />)}
          </button>
        </div>
      </nav>
      {/* Mobile overlay */}
      {mobileOpen && <div onClick={() => setMobileOpen(false)} style={{ position: "fixed", inset: 0, background: "rgba(0,0,0,0.5)", zIndex: 150 }} />}
      <div className={`mobile-menu ${mobileOpen ? "open" : ""}`}>
        <button onClick={() => setMobileOpen(false)} style={{ position: "absolute", top: 24, right: 24, background: "none", border: "none", cursor: "pointer", color: GOLD, fontSize: 22 }}>✕</button>
        <div className="cinzel-deco gold-text" style={{ fontSize: 32, marginBottom: 40 }}>442</div>
        {navLinks.map(l => (
          <button key={l.href} onClick={() => scrollTo(l.href)} style={{ display: "block", width: "100%", textAlign: "left", background: "none", border: "none", cursor: "pointer", color: "rgba(240,234,214,0.75)", fontSize: 13, fontFamily: "'Cinzel',serif", letterSpacing: "0.15em", textTransform: "uppercase", padding: "14px 0", borderBottom: "1px solid rgba(212,175,55,0.08)" }}>
            {l.label}
          </button>
        ))}
      </div>
    </>
  );
};

// ─── HERO ────────────────────────────────────────────────────────────────────
const Hero = () => {
  const [loaded, setLoaded] = useState(false);
  const { displayed, done } = useTypewriter("Pizza Kitchen & Bar", 70, 1200);
  const bgRef = useRef(null);
  useEffect(() => { setTimeout(() => setLoaded(true), 100); }, []);
  useEffect(() => {
    const onScroll = () => { if (bgRef.current) bgRef.current.style.transform = `translateY(${window.scrollY * 0.3}px)`; };
    window.addEventListener("scroll", onScroll, { passive: true });
    return () => window.removeEventListener("scroll", onScroll);
  }, []);
  const scrollTo = (id) => document.querySelector(id)?.scrollIntoView({ behavior: "smooth" });
  return (
    <section id="hero" style={{ position: "relative", height: "100vh", minHeight: 600, display: "flex", alignItems: "center", justifyContent: "center", overflow: "hidden" }}>
      <div ref={bgRef} className="hero-bg" />
      {/* Gold overlay gradient */}
      <div style={{ position: "absolute", inset: 0, background: "radial-gradient(ellipse at center, rgba(212,175,55,0.08) 0%, transparent 65%), linear-gradient(to bottom, rgba(10,10,10,0.2) 0%, rgba(10,10,10,0.6) 100%)", zIndex: 0 }} />
      <Particles />
      {/* Content */}
      <div style={{ position: "relative", zIndex: 2, textAlign: "center", padding: "0 24px", maxWidth: 800 }}>
        {/* Logo number */}
        <div style={{ overflow: "hidden", marginBottom: 8 }}>
          <div className={`cinzel-deco glow-pulse float`} style={{ fontSize: "clamp(80px,18vw,160px)", fontWeight: 900, lineHeight: 0.9, background: `linear-gradient(135deg, ${GOLD_DIM} 0%, ${GOLD} 40%, ${GOLD_LIGHT} 70%, ${GOLD} 100%)`, WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent", backgroundClip: "text", opacity: loaded ? 1 : 0, transform: loaded ? "translateY(0) rotateX(0deg)" : "translateY(40px) rotateX(15deg)", transition: "all 1.2s cubic-bezier(0.4,0,0.2,1)" }}>
            442
          </div>
        </div>
        {/* Typewriter subtitle */}
        <div className="cinzel" style={{ fontSize: "clamp(14px,2.5vw,22px)", letterSpacing: "0.35em", color: "rgba(240,234,214,0.7)", textTransform: "uppercase", marginBottom: 32, minHeight: "1.5em", opacity: loaded ? 1 : 0, transition: "opacity 0.8s 0.8s" }}>
          {displayed}{!done && <span className="cursor" />}
        </div>
        {/* Tag line */}
        <p style={{ color: "rgba(240,234,214,0.45)", fontSize: 14, letterSpacing: "0.25em", textTransform: "uppercase", marginBottom: 48, opacity: loaded ? 1 : 0, transition: "opacity 0.8s 2s" }}>
          • Food Experience •
        </p>
        {/* CTAs */}
        <div style={{ display: "flex", gap: 16, justifyContent: "center", flexWrap: "wrap", opacity: loaded ? 1 : 0, transform: loaded ? "translateY(0)" : "translateY(20px)", transition: "all 0.8s 2.3s" }}>
          <button className="btn-gold" onClick={() => scrollTo("#pallone")}>Scopri il Menu</button>
          <button onClick={() => scrollTo("#prenotazioni")} style={{ display: "inline-flex", alignItems: "center", gap: 8, background: "transparent", border: `1px solid rgba(212,175,55,0.4)`, color: GOLD_LIGHT, fontFamily: "'Cinzel',serif", fontSize: 13, letterSpacing: "0.15em", padding: "16px 36px", borderRadius: 4, cursor: "pointer", transition: "all 0.4s" }}
            onMouseEnter={e => { e.currentTarget.style.borderColor = GOLD; e.currentTarget.style.background = "rgba(212,175,55,0.08)"; }}
            onMouseLeave={e => { e.currentTarget.style.borderColor = "rgba(212,175,55,0.4)"; e.currentTarget.style.background = "transparent"; }}>
            Prenota
          </button>
        </div>
      </div>
      {/* Scroll indicator */}
      <div style={{ position: "absolute", bottom: 32, left: "50%", transform: "translateX(-50%)", zIndex: 2, display: "flex", flexDirection: "column", alignItems: "center", gap: 8, opacity: loaded ? 0.5 : 0, transition: "opacity 0.8s 3s" }}>
        <span style={{ fontFamily: "'Cinzel',serif", fontSize: 9, letterSpacing: "0.3em", color: GOLD, textTransform: "uppercase" }}>Scroll</span>
        <div style={{ width: 1, height: 40, background: `linear-gradient(to bottom, ${GOLD}, transparent)` }} />
      </div>
    </section>
  );
};

// ─── ANTIPASTI ───────────────────────────────────────────────────────────────
const antipastiData = [
  { name: "Gabriel Omar Batistuta", desc: "Selezione di salumi e formaggi con accompagnamenti della casa", price: "8,00", tag: "Antipasto" },
  { name: "Antipasto 442", desc: "Percorso degustazione con salumi selezionati, formaggi, crocchette gourmet, montanarine e sfiziosità dello chef", price: "20,00", tag: "Per 2 persone" },
];
const montanarineData = [
  { name: "Pomodoro e Mozzarella", desc: "2 pezzi", price: "5,00" },
  { name: "Stracciatella, Mortadella e Granella di Pistacchio", desc: "2 pezzi", price: "7,00" },
  { name: "Crema di Zucca, Zola e Noci", desc: "2 pezzi", price: "6,00" },
];

const Antipasti = () => {
  const { ref, visible } = useReveal();
  return (
    <section id="antipasti" style={{ background: BG2, padding: "100px 0" }}>
      <div style={{ maxWidth: 1200, margin: "0 auto", padding: "0 24px" }}>
        <SectionHeader eyebrow="Per iniziare" title="Antipasti & Montanarine" subtitle="Un viaggio nei sapori italiani prima ancora che la pizza arrivi." />
        <div ref={ref} className={`stagger ${visible ? "visible" : ""}`} style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit,minmax(300px,1fr))", gap: 24 }}>
          {/* Antipasti */}
          <div style={{ gridColumn: "span 1" }}>
            <p className="section-eyebrow" style={{ marginBottom: 20 }}>Antipasti Gourmet</p>
            <div style={{ display: "flex", flexDirection: "column", gap: 16 }}>
              {antipastiData.map(item => (
                <div key={item.name} className="menu-card shine-hover" style={{ padding: "24px 28px" }}>
                  {item.tag && <span style={{ fontSize: 10, fontFamily: "'Cinzel',serif", color: GOLD_DIM, letterSpacing: "0.2em", textTransform: "uppercase" }}>{item.tag}</span>}
                  <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginTop: 6, gap: 12 }}>
                    <div>
                      <h3 className="cinzel" style={{ fontSize: 15, color: "#f0ead6", fontWeight: 600, marginBottom: 6 }}>{item.name}</h3>
                      <p style={{ fontSize: 13, color: "rgba(240,234,214,0.45)", lineHeight: 1.6, fontStyle: "italic" }}>{item.desc}</p>
                    </div>
                    <Price value={item.price} />
                  </div>
                </div>
              ))}
            </div>
          </div>
          {/* Montanarine */}
          <div>
            <p className="section-eyebrow" style={{ marginBottom: 20 }}>Montanarine Gourmet</p>
            <div style={{ display: "flex", flexDirection: "column", gap: 14 }}>
              {montanarineData.map(item => (
                <div key={item.name} className="menu-card" style={{ padding: "20px 24px", display: "flex", alignItems: "center", justifyContent: "space-between", gap: 16 }}>
                  <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
                    <div style={{ width: 6, height: 6, borderRadius: "50%", background: GOLD, flexShrink: 0 }} />
                    <div>
                      <h3 className="cinzel" style={{ fontSize: 13, color: "#f0ead6", fontWeight: 600, marginBottom: 2 }}>{item.name}</h3>
                      <p style={{ fontSize: 11, color: "rgba(240,234,214,0.35)", fontStyle: "italic" }}>{item.desc}</p>
                    </div>
                  </div>
                  <Price value={item.price} />
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>
    </section>
  );
};

// ─── CROCCHETTE & FRITTI ─────────────────────────────────────────────────────
const crocchette = [
  { name: "Classica", price: "2,50", img: "https://images.unsplash.com/photo-1585325701954-e5cd25ec1d74?w=400&q=80" },
  { name: "Stracciatella e Guanciale", price: "3,50", img: "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=400&q=80" },
  { name: "'Nduja e Provola", price: "3,50", img: "https://images.unsplash.com/photo-1585325701956-6bc34e7c8d0f?w=400&q=80" },
];
const frittiData = [
  { name: "Nuggets di Pollo", note: "4 pezzi", price: "3,00" },
  { name: "Polpette di Carne", note: "al pezzo", price: "1,50" },
  { name: "Dippers Croccanti", note: "", price: "4,00" },
  { name: "Patatine Stick", note: "", price: "3,00" },
  { name: "Arancini Siciliani", note: "4 pezzi", price: "3,00" },
  { name: "Alette di Pollo Piccanti", note: "4 pezzi", price: "4,00" },
  { name: "Olive Ascolane", note: "4 pezzi", price: "2,00" },
  { name: "Anelli di Cipolla", note: "5 pezzi", price: "3,00" },
];

const Fritti = () => {
  const { ref, visible } = useReveal();
  const { ref: ref2, visible: visible2 } = useReveal();
  return (
    <section id="fritti" style={{ background: BG, padding: "100px 0", position: "relative" }}>
      <div style={{ maxWidth: 1200, margin: "0 auto", padding: "0 24px" }}>
        <SectionHeader eyebrow="Artigianali" title="Crocchette Maxi Gourmet" subtitle="Crocchette artigianali di patate dal cuore cremoso — firmate 442." />
        <div ref={ref} className={`stagger ${visible ? "visible" : ""}`} style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit,minmax(220px,1fr))", gap: 24, marginBottom: 80 }}>
          {crocchette.map(c => (
            <div key={c.name} className="menu-card" style={{ overflow: "hidden" }}>
              <div style={{ height: 180, overflow: "hidden", position: "relative" }}>
                <img src={c.img} alt={c.name} style={{ width: "100%", height: "100%", objectFit: "cover", transition: "transform 0.6s" }}
                  onMouseEnter={e => e.target.style.transform = "scale(1.1)"} onMouseLeave={e => e.target.style.transform = "scale(1)"} />
                <div style={{ position: "absolute", inset: 0, background: "linear-gradient(to top, rgba(0,0,0,0.7), transparent)" }} />
              </div>
              <div style={{ padding: "20px 24px", display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                <h3 className="cinzel" style={{ fontSize: 13, color: "#f0ead6", fontWeight: 600 }}>{c.name}</h3>
                <Price value={c.price} />
              </div>
            </div>
          ))}
          {/* Tris box */}
          <div className="menu-card" style={{ padding: "28px", display: "flex", flexDirection: "column", justifyContent: "center", alignItems: "center", textAlign: "center", background: "linear-gradient(135deg,rgba(212,175,55,0.08),rgba(212,175,55,0.03))", borderColor: "rgba(212,175,55,0.3)" }}>
            <p className="cinzel" style={{ fontSize: 10, letterSpacing: "0.25em", color: GOLD_DIM, textTransform: "uppercase", marginBottom: 12 }}>Tris Crocchette</p>
            <p style={{ fontSize: 12, color: "rgba(240,234,214,0.5)", lineHeight: 1.8, marginBottom: 16 }}>1 Classica<br />1 Stracciatella e Guanciale<br />1 'Nduja e Provola</p>
            <Price value="8,50" large />
          </div>
        </div>

        {/* Fritti */}
        <SectionHeader eyebrow="To share" title="Fritti & Sfizi" />
        <div ref={ref2} className={`stagger ${visible2 ? "visible" : ""}`} style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill,minmax(240px,1fr))", gap: 12 }}>
          {frittiData.map(f => (
            <div key={f.name} className="menu-card" style={{ padding: "16px 20px", display: "flex", justifyContent: "space-between", alignItems: "center" }}>
              <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
                <div style={{ width: 5, height: 5, borderRadius: "50%", background: GOLD, flexShrink: 0 }} />
                <span style={{ fontSize: 13, color: "rgba(240,234,214,0.8)" }}>
                  {f.name} {f.note && <span style={{ fontSize: 11, color: "rgba(240,234,214,0.35)", fontStyle: "italic" }}>({f.note})</span>}
                </span>
              </div>
              <Price value={f.price} />
            </div>
          ))}
        </div>
      </div>
    </section>
  );
};

// ─── PALLONE D'ORO ───────────────────────────────────────────────────────────
const palloneData = [
  { n: 1, name: "Dembélé", desc: "Crema di zucca, basilico, fior di latte, funghi porcini, stracciatella, noci, olio evo", price: "12,00", red: false },
  { n: 2, name: "Van Basten", desc: "Crema di zucchine, basilico, fior di latte, pancetta, 'nduja, scaglie di grana, olio evo", price: "12,00", red: false },
  { n: 3, name: "Ronaldo il Fenomeno", desc: "Crema di zucchine, gamberetti, stracciatella, prezzemolo, olio evo", price: "12,00", red: false },
  { n: 4, name: "Leo Messi", desc: "Pomodoro, fior di latte, basilico, salsiccia fresca, 'nduja, grana, olio evo", price: "10,00", red: true },
  { n: 5, name: "Cristiano Ronaldo", desc: "Pomodoro, fior di latte, basilico, salsiccia fresca, funghi porcini, ricotta affumicata, olio evo", price: "10,00", red: true },
  { n: 6, name: "Roberto Baggio", desc: "Fior di latte, mortadella, stracciatella, granella di pistacchio, olio evo", price: "11,00", red: false },
  { n: 7, name: "Zidane", desc: "Fior di latte, datterini rossi, crudo, rucola, grana, olio evo", price: "8,00", red: false },
  { n: 8, name: "Gullit", desc: "Crema di zucca, fior di latte, gorgonzola, basilico, speck, olio evo", price: "10,00", red: false },
  { n: 9, name: "Paolo Cannavaro", desc: "Pomodoro, fior di latte, basilico, gorgonzola, provola, bufala, olio evo", price: "10,00", red: true },
  { n: 10, name: "Kakà", desc: "Crema di zucca, fior di latte, basilico, pesto, stracciatella, pomodorini, olio evo", price: "12,00", red: false },
  { n: 11, name: "Platini", desc: "Fior di latte, crocchette, fonduta di parmigiano, prosciutto cotto, bufala, olio evo", price: "12,00", red: false },
  { n: 12, name: "Benzema", desc: "Fior di latte, fonduta di parmigiano, salmone affumicato, gamberetti, ricotta, olio evo", price: "12,00", red: false },
];

const PalloneOro = () => {
  const { ref, visible } = useReveal(0.08);
  const [hovered, setHovered] = useState(null);
  return (
    <section id="pallone" style={{ background: BG3, padding: "100px 0", position: "relative", overflow: "hidden" }} className="bg-grid">
      {/* Hero banner */}
      <div style={{ position: "relative", textAlign: "center", marginBottom: 80, padding: "0 24px" }}>
        <div style={{ position: "absolute", top: "50%", left: "50%", transform: "translate(-50%,-50%)", width: 400, height: 400, borderRadius: "50%", background: "radial-gradient(ellipse,rgba(212,175,55,0.07) 0%,transparent 70%)", pointerEvents: "none" }} />
        <p className="section-eyebrow" style={{ marginBottom: 16 }}>Collezione Esclusiva</p>
        <h2 className="cinzel-deco gold-text" style={{ fontSize: "clamp(36px,7vw,80px)", fontWeight: 700, lineHeight: 1.0, marginBottom: 16 }}>
          ⚽ Pallone D'Oro
        </h2>
        <div className="gold-divider" style={{ width: 100, marginBottom: 20 }} />
        <p style={{ color: "rgba(240,234,214,0.45)", fontSize: 14, maxWidth: 560, margin: "0 auto", lineHeight: 1.8, fontStyle: "italic" }}>
          Dodici pizze speciali dedicate ai campioni che hanno scritto la storia del calcio mondiale.
          Ingredienti selezionati, abbinamenti ricercati e impasti d'autore.
        </p>
        <div style={{ display: "flex", gap: 24, justifyContent: "center", marginTop: 32, flexWrap: "wrap" }}>
          {[["CONTEMPORANEO", "Alta idratazione, bordo alto e alveolato"], ["SOTTILE E CROCCANTE", "Steso sottile, fragrante e croccante"]].map(([t, d]) => (
            <div key={t} style={{ border: "1px solid rgba(212,175,55,0.2)", borderRadius: 8, padding: "12px 24px", textAlign: "center" }}>
              <p className="cinzel" style={{ fontSize: 10, letterSpacing: "0.25em", color: GOLD, marginBottom: 4 }}>{t}</p>
              <p style={{ fontSize: 11, color: "rgba(240,234,214,0.4)", fontStyle: "italic" }}>{d}</p>
            </div>
          ))}
        </div>
      </div>
      {/* Grid */}
      <div style={{ maxWidth: 1200, margin: "0 auto", padding: "0 24px" }}>
        <div ref={ref} className={`stagger ${visible ? "visible" : ""}`} style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill,minmax(280px,1fr))", gap: 20 }}>
          {palloneData.map(p => (
            <div key={p.n} className="pizza-card" style={{ padding: "24px 28px" }}
              onMouseEnter={() => setHovered(p.n)} onMouseLeave={() => setHovered(null)}>
              <div style={{ display: "flex", alignItems: "flex-start", gap: 14 }}>
                <div className={`num-badge ${p.red ? "red" : ""}`}>{p.n}</div>
                <div style={{ flex: 1 }}>
                  <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", gap: 8, marginBottom: 8 }}>
                    <h3 className="cinzel" style={{ fontSize: 14, color: hovered === p.n ? GOLD_LIGHT : "#f0ead6", fontWeight: 600, transition: "color 0.3s" }}>{p.name}</h3>
                    <Price value={p.price} />
                  </div>
                  <p style={{ fontSize: 12, color: "rgba(240,234,214,0.42)", lineHeight: 1.6, fontStyle: "italic" }}>{p.desc}</p>
                </div>
              </div>
            </div>
          ))}
        </div>
        {/* Legend */}
        <div style={{ display: "flex", gap: 32, justifyContent: "center", marginTop: 40, flexWrap: "wrap" }}>
          {[["#c0392b", "Pizze con Pomodoro"], [GOLD, "Pizze Bianche"]].map(([c, l]) => (
            <div key={l} style={{ display: "flex", alignItems: "center", gap: 10 }}>
              <div style={{ width: 16, height: 16, borderRadius: "50%", border: `1.5px solid ${c}` }} />
              <span style={{ fontSize: 11, fontFamily: "'Cinzel',serif", color: "rgba(240,234,214,0.45)", letterSpacing: "0.12em", textTransform: "uppercase" }}>{l}</span>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
};

// ─── PIZZE TRADIZIONALI ──────────────────────────────────────────────────────
const tradizionaliData = [
  { n: 1, name: "Paolo Maldini", sub: "La Regina Margherita", desc: "pomodoro, mozzarella, basilico, olio evo", price: "6,00", red: true },
  { n: 2, name: "Alex Del Piero", desc: "pomodoro, basilico, mozzarella, salame, porcini, cotto, olive, olio evo", price: "10,00", red: true },
  { n: 3, name: "Cafù", desc: "pomodoro, mozzarella, basilico, wurstel, patatine, olio evo", price: "7,00", red: true },
  { n: 4, name: "Paolo Montero", desc: "pomodoro, basilico, olive, acciughe, origano, olio all'aglio", price: "5,00", red: true },
  { n: 5, name: "Antonio Galardo", desc: "pomodoro, mozzarella, basilico, salame, olio evo", price: "8,00", red: true },
  { n: 6, name: "Giorgio Chiellini", desc: "mozzarella, basilico, zucchine, melanzane, patate, olio evo", price: "9,00", red: false },
  { n: 7, name: "Totò Schillaci", desc: "pomodoro, basilico, mozzarella, tonno, cipolla, nduja", price: "8,00", red: true },
  { n: 8, name: "Gianluigi Buffon", desc: "pomodoro, mozzarella, basilico, olio evo, acciughe, origano", price: "6,00", red: true },
  { n: 9, name: "Ringhio Gattuso", desc: "pomodoro, fior di latte, olive, salame, basilico, piccante", price: "8,00", red: true },
  { n: 10, name: "Andres Iniesta", desc: "fonduta di parmigiano, provola, bufala, fiordilatte, gorgonzola", price: "10,00", red: false },
  { n: 11, name: "Francesco Totti", desc: "pomodoro, fior di latte, zola, salame, prosciutto cotto, peperoncino fresco", price: "9,00", red: true },
];

const Tradizionali = () => {
  const { ref, visible } = useReveal(0.08);
  return (
    <section id="tradizionali" style={{ background: BG2, padding: "100px 0" }}>
      <div style={{ maxWidth: 1200, margin: "0 auto", padding: "0 24px" }}>
        <SectionHeader eyebrow="Classiche senza tempo" title="Pizze Tradizionali" subtitle="Due impasti, una tradizione senza tempo. Le ricette classiche, i sapori autentici, la passione di sempre." />
        {/* Impasti */}
        <div style={{ display: "flex", gap: 24, justifyContent: "center", flexWrap: "wrap", marginBottom: 56 }}>
          {[["IMPASTO CLASSICO", "Morbido, fragrante e leggero, con lievitazione naturale."], ["IMPASTO CROCCANTE", "Sottile, croccante e dorato, per un gusto più deciso."]].map(([t, d]) => (
            <div key={t} style={{ flex: "1 1 200px", maxWidth: 280, border: "1px solid rgba(212,175,55,0.18)", borderRadius: 10, padding: "20px 24px", textAlign: "center" }}>
              <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.2em", color: GOLD, marginBottom: 8 }}>{t}</p>
              <p style={{ fontSize: 12, color: "rgba(240,234,214,0.4)", fontStyle: "italic", lineHeight: 1.7 }}>{d}</p>
            </div>
          ))}
        </div>
        <div ref={ref} className={`stagger ${visible ? "visible" : ""}`} style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill,minmax(300px,1fr))", gap: 16 }}>
          {tradizionaliData.map(p => (
            <div key={p.n} className="pizza-card" style={{ padding: "20px 24px" }}>
              <div style={{ display: "flex", alignItems: "flex-start", gap: 12 }}>
                <div className={`num-badge ${p.red ? "red" : ""}`}>{p.n}</div>
                <div style={{ flex: 1 }}>
                  <div style={{ display: "flex", justifyContent: "space-between", gap: 8, marginBottom: 4 }}>
                    <div>
                      <h3 className="cinzel" style={{ fontSize: 14, color: "#f0ead6", fontWeight: 600 }}>{p.name}</h3>
                      {p.sub && <p style={{ fontSize: 10, color: GOLD_DIM, fontFamily: "'Cinzel',serif", letterSpacing: "0.12em" }}>{p.sub}</p>}
                    </div>
                    <Price value={p.price} />
                  </div>
                  <p style={{ fontSize: 12, color: "rgba(240,234,214,0.4)", lineHeight: 1.6, fontStyle: "italic" }}>{p.desc}</p>
                </div>
              </div>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
};

// ─── INSALATA BUILDER ─────────────────────────────────────────────────────────
const verdure = ["Rucola", "Pomodorini", "Mais", "Zucchine Fritte", "Melanzane Fritte"];
const proteine = ["Cipolla", "Olive", "Pollo Fritto", "Fior di Latte", "Grana", "Tonno", "Salmone"];
const condimenti = ["Olio EVO", "Sale", "Pepe", "Aceto Balsamico", "Limone", "Salsa Yogurt", "Maionese", "Senape", "Salsa Caesar", "Aceto di Mele"];

const InsalataMaker = () => {
  const [selV, setSelV] = useState([]);
  const [selP, setSelP] = useState([]);
  const [selC, setSelC] = useState([]);
  const [mode, setMode] = useState("piatto");
  const { ref, visible } = useReveal();
  const toggle = (list, setList, val) => setList(l => l.includes(val) ? l.filter(x => x !== val) : [...l, val]);
  const totalItems = selV.length + selP.length + selC.length;
  return (
    <section id="insalata" style={{ background: BG, padding: "100px 0" }}>
      <div style={{ maxWidth: 900, margin: "0 auto", padding: "0 24px" }}>
        <SectionHeader eyebrow="Personalizza" title="Crea la Tua Insalata" subtitle="Base Iceberg — scegli verdure, proteine e condimento. Tutte le insalate €10,00." />
        <div ref={ref} className={`reveal ${visible ? "visible" : ""}`}>
          <div style={{ background: BG2, border: "1px solid rgba(212,175,55,0.15)", borderRadius: 20, padding: "40px 36px" }}>
            {/* Verdure */}
            <div style={{ marginBottom: 32 }}>
              <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.3em", color: GOLD, textTransform: "uppercase", marginBottom: 16 }}>① Scegli le Verdure</p>
              <div style={{ display: "flex", flexWrap: "wrap", gap: 10 }}>
                {verdure.map(v => (
                  <button key={v} className={`chip ${selV.includes(v) ? "selected" : ""}`} onClick={() => toggle(selV, setSelV, v)}>
                    {selV.includes(v) && <span style={{ color: GOLD, fontSize: 10 }}>✓</span>} {v}
                  </button>
                ))}
              </div>
            </div>
            {/* Proteine */}
            <div style={{ marginBottom: 32 }}>
              <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.3em", color: GOLD, textTransform: "uppercase", marginBottom: 16 }}>② Scegli le Proteine</p>
              <div style={{ display: "flex", flexWrap: "wrap", gap: 10 }}>
                {proteine.map(v => (
                  <button key={v} className={`chip ${selP.includes(v) ? "selected" : ""}`} onClick={() => toggle(selP, setSelP, v)}>
                    {selP.includes(v) && <span style={{ color: GOLD, fontSize: 10 }}>✓</span>} {v}
                  </button>
                ))}
              </div>
            </div>
            {/* Condimento */}
            <div style={{ marginBottom: 32 }}>
              <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.3em", color: GOLD, textTransform: "uppercase", marginBottom: 16 }}>③ Aggiungi il Condimento</p>
              <div style={{ display: "flex", flexWrap: "wrap", gap: 10 }}>
                {condimenti.map(v => (
                  <button key={v} className={`chip ${selC.includes(v) ? "selected" : ""}`} onClick={() => toggle(selC, setSelC, v)}>
                    {selC.includes(v) && <span style={{ color: GOLD, fontSize: 10 }}>✓</span>} {v}
                  </button>
                ))}
              </div>
            </div>
            {/* Mode */}
            <div style={{ display: "flex", gap: 16, flexWrap: "wrap", marginBottom: 28 }}>
              {[["piatto", "Nel Piatto", "Fresca nel piatto"], ["focaccia", "Con la Focaccia", "Come una pizza"]].map(([v, l, d]) => (
                <button key={v} onClick={() => setMode(v)} style={{ flex: 1, minWidth: 160, padding: "16px", borderRadius: 10, border: `1px solid ${mode === v ? GOLD : "rgba(212,175,55,0.2)"}`, background: mode === v ? "rgba(212,175,55,0.1)" : "transparent", cursor: "pointer", transition: "all 0.3s" }}>
                  <p className="cinzel" style={{ fontSize: 12, color: mode === v ? GOLD : "rgba(240,234,214,0.5)", marginBottom: 4 }}>{l}</p>
                  <p style={{ fontSize: 11, color: "rgba(240,234,214,0.3)", fontStyle: "italic" }}>{d}</p>
                </button>
              ))}
            </div>
            {/* Summary */}
            {totalItems > 0 && (
              <div style={{ background: "rgba(212,175,55,0.06)", border: "1px solid rgba(212,175,55,0.2)", borderRadius: 10, padding: "20px 24px" }}>
                <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.2em", color: GOLD, marginBottom: 12 }}>La Tua Insalata</p>
                <p style={{ fontSize: 13, color: "rgba(240,234,214,0.7)", lineHeight: 1.8 }}>
                  {[...selV, ...selP, ...selC].join(" · ")} · {mode === "piatto" ? "Nel Piatto" : "Con la Focaccia"}
                </p>
                <div style={{ marginTop: 16, display: "flex", justifyContent: "flex-end" }}>
                  <Price value="10,00" large />
                </div>
              </div>
            )}
          </div>
        </div>
      </div>
    </section>
  );
};

// ─── BEVANDE ─────────────────────────────────────────────────────────────────
const bevandeData = {
  analcolici: [
    { name: "Acqua 1 Litro", price: "3,00" }, { name: "Coca Cola 1 Litro", price: "3,00" },
    { name: "Coca Cola Lattina 33cl", price: "2,00" }, { name: "Coca Cola Zero 33cl", price: "2,00" },
    { name: "Fanta Lattina 33cl", price: "2,00" },
  ],
  birre: [
    { name: "Birra Messina 50cl", price: "5,00" }, { name: "Ichnusa 50cl", price: "5,00" },
    { name: "Nastro Azzurro 66cl", price: "4,00" }, { name: "Nastro Azzurro 33cl", price: "3,00" },
    { name: "Heineken 66cl", price: "4,00" }, { name: "Heineken 33cl", price: "3,00" },
    { name: "Tuborg 66cl", price: "4,00" },
  ],
  amari: [
    { name: "Amaro Silano", price: "3,00" }, { name: "Amaro del Capo", price: "3,00" },
    { name: "Grappa", price: "3,00" }, { name: "Jägermeister", price: "3,00" },
    { name: "Limoncello", price: "3,00" }, { name: "Liquirizia", price: "3,00" },
  ],
};

const Bevande = () => {
  const { ref, visible } = useReveal(0.08);
  return (
    <section id="bevande" style={{ background: BG3, padding: "100px 0" }} className="bg-grid">
      <div style={{ maxWidth: 1100, margin: "0 auto", padding: "0 24px" }}>
        <SectionHeader eyebrow="Il complemento perfetto" title="Bevande & Distillati" subtitle="Dalle bevande classiche ai migliori amari della tradizione." />
        <div ref={ref} className={`stagger ${visible ? "visible" : ""}`} style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit,minmax(280px,1fr))", gap: 28 }}>
          {[["Analcolici", bevandeData.analcolici], ["Birre", bevandeData.birre], ["Amari & Distillati", bevandeData.amari]].map(([title, items]) => (
            <div key={title} className="menu-card" style={{ padding: "28px 28px" }}>
              <p className="cinzel" style={{ fontSize: 13, letterSpacing: "0.2em", color: GOLD, textTransform: "uppercase", marginBottom: 20, borderBottom: "1px solid rgba(212,175,55,0.15)", paddingBottom: 14 }}>{title}</p>
              <div style={{ display: "flex", flexDirection: "column", gap: 10 }}>
                {items.map(item => (
                  <div key={item.name} style={{ display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                    <span style={{ fontSize: 13, color: "rgba(240,234,214,0.65)" }}>{item.name}</span>
                    <span style={{ borderBottom: "1px dotted rgba(212,175,55,0.2)", flex: 1, margin: "0 10px", position: "relative", top: -2 }} />
                    <Price value={item.price} />
                  </div>
                ))}
              </div>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
};

// ─── MAGNUM ───────────────────────────────────────────────────────────────────
const magnumBases = ["Magnum Bianco", "Magnum Classico", "Magnum Mandorle"];
const magnumFarciture = [
  { name: "Pistacchio Gourmet", desc: "Crema di pistacchio, granella di pistacchio e scaglie di cioccolato bianco" },
  { name: "Caramel Crunch", desc: "Caramello salato, cioccolato fondente e granella di nocciola" },
  { name: "Oreo & Nutella", desc: "Nutella e Oreo sbriciolati" },
];

const Magnum = () => {
  const [selBase, setSelBase] = useState(null);
  const [selFarcitura, setSelFarcitura] = useState(null);
  const { ref, visible } = useReveal();
  return (
    <section id="magnum" style={{ background: "#0D150D", padding: "100px 0" }}>
      <div style={{ maxWidth: 900, margin: "0 auto", padding: "0 24px" }}>
        <SectionHeader eyebrow="Dessert d'autore" title="Magnum Experience" subtitle="Crea il tuo Magnum — preparato al momento davanti a te. Prezzo unico €4,00." />
        <div ref={ref} className={`reveal ${visible ? "visible" : ""}`}>
          <div style={{ background: "rgba(255,255,255,0.02)", border: "1px solid rgba(212,175,55,0.15)", borderRadius: 20, padding: "40px" }}>
            <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.3em", color: GOLD, textTransform: "uppercase", marginBottom: 20 }}>① Scegli il Magnum</p>
            <div style={{ display: "flex", gap: 16, flexWrap: "wrap", marginBottom: 36 }}>
              {magnumBases.map(b => (
                <button key={b} onClick={() => setSelBase(b)} style={{ flex: "1 1 140px", padding: "20px 16px", borderRadius: 12, border: `1px solid ${selBase === b ? GOLD : "rgba(212,175,55,0.2)"}`, background: selBase === b ? "rgba(212,175,55,0.12)" : "transparent", cursor: "pointer", color: selBase === b ? GOLD_LIGHT : "rgba(240,234,214,0.55)", fontFamily: "'Cinzel',serif", fontSize: 13, transition: "all 0.3s" }}>
                  {b}
                </button>
              ))}
            </div>
            <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.3em", color: GOLD, textTransform: "uppercase", marginBottom: 20 }}>② Scegli la Farcitura</p>
            <div style={{ display: "flex", gap: 16, flexWrap: "wrap" }}>
              {magnumFarciture.map(f => (
                <button key={f.name} onClick={() => setSelFarcitura(f.name)} style={{ flex: "1 1 200px", padding: "20px", borderRadius: 12, border: `1px solid ${selFarcitura === f.name ? GOLD : "rgba(212,175,55,0.2)"}`, background: selFarcitura === f.name ? "rgba(212,175,55,0.1)" : "transparent", cursor: "pointer", textAlign: "left", transition: "all 0.3s" }}>
                  <p className="cinzel" style={{ fontSize: 13, color: selFarcitura === f.name ? GOLD_LIGHT : "#f0ead6", marginBottom: 6 }}>{f.name}</p>
                  <p style={{ fontSize: 11, color: "rgba(240,234,214,0.35)", fontStyle: "italic", lineHeight: 1.6 }}>{f.desc}</p>
                </button>
              ))}
            </div>
            {selBase && selFarcitura && (
              <div style={{ marginTop: 28, padding: "20px 24px", background: "rgba(212,175,55,0.07)", borderRadius: 10, border: "1px solid rgba(212,175,55,0.2)", display: "flex", justifyContent: "space-between", alignItems: "center", flexWrap: "wrap", gap: 12 }}>
                <div>
                  <p style={{ fontSize: 12, color: "rgba(240,234,214,0.5)", marginBottom: 4 }}>La tua scelta:</p>
                  <p className="cinzel" style={{ fontSize: 14, color: GOLD_LIGHT }}>{selBase} · {selFarcitura}</p>
                </div>
                <Price value="4,00" large />
              </div>
            )}
          </div>
        </div>
      </div>
    </section>
  );
};

// ─── DOLCI ────────────────────────────────────────────────────────────────────
const dolciData = [
  { name: "Cocco", price: "5,00", desc: "Freschezza esotica e dolcezza avvolgente. Una delizia al cocco dal cuore morbido e dal gusto delicato.", img: "https://images.unsplash.com/photo-1488477181946-6428a0291777?w=600&q=80" },
  { name: "Cheesecake", price: "5,00", desc: "Cremosa, vellutata, irresistibile. La nostra cheesecake conquista al primo assaggio.", img: "https://images.unsplash.com/photo-1565958011703-44f9829ba187?w=600&q=80" },
  { name: "Tiramisù", price: "5,00", desc: "Il grande classico della tradizione italiana. Strati di mascarpone, savoiardi e caffè per un'emozione senza tempo.", img: "https://images.unsplash.com/photo-1571877227200-a0d98ea607e9?w=600&q=80" },
  { name: "Tartufo", price: "5,00", desc: "Morbido cuore gelato avvolto da una croccante copertura al cacao. Gusti: Pistacchio, Bianco, Nero.", img: "https://images.unsplash.com/photo-1551024601-bec78aea704b?w=600&q=80" },
];

const Dolci = () => {
  const { ref, visible } = useReveal(0.08);
  return (
    <section id="dolci" style={{ background: BG2, padding: "100px 0" }}>
      <div style={{ maxWidth: 1100, margin: "0 auto", padding: "0 24px" }}>
        <SectionHeader eyebrow="Il tocco finale" title="Dolci" subtitle="Dolci creazioni fatte con passione, per regalarti un momento di puro piacere." />
        <div ref={ref} className={`stagger ${visible ? "visible" : ""}`} style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit,minmax(240px,1fr))", gap: 24 }}>
          {dolciData.map(d => (
            <div key={d.name} className="menu-card" style={{ overflow: "hidden" }}>
              <div style={{ height: 200, overflow: "hidden", position: "relative" }}>
                <img src={d.img} alt={d.name} style={{ width: "100%", height: "100%", objectFit: "cover", transition: "transform 0.6s" }}
                  onMouseEnter={e => e.target.style.transform = "scale(1.1)"} onMouseLeave={e => e.target.style.transform = "scale(1)"} />
                <div style={{ position: "absolute", inset: 0, background: "linear-gradient(to top, rgba(0,0,0,0.75) 0%, transparent 50%)" }} />
                <div style={{ position: "absolute", bottom: 16, left: 20, right: 20, display: "flex", justifyContent: "space-between", alignItems: "flex-end" }}>
                  <h3 className="cinzel" style={{ fontSize: 18, color: "#f5ead6", fontWeight: 700 }}>{d.name}</h3>
                  <Price value={d.price} large />
                </div>
              </div>
              <div style={{ padding: "18px 20px" }}>
                <p style={{ fontSize: 12, color: "rgba(240,234,214,0.4)", lineHeight: 1.7, fontStyle: "italic" }}>{d.desc}</p>
              </div>
            </div>
          ))}
        </div>
        <p style={{ textAlign: "center", marginTop: 40, fontSize: 12, color: "rgba(240,234,214,0.3)", fontStyle: "italic", letterSpacing: "0.05em" }}>
          Coperto €2,00 a persona · Eventuali aggiunte €1,00 in base agli ingredienti scelti
        </p>
      </div>
    </section>
  );
};

// ─── PRENOTAZIONI ─────────────────────────────────────────────────────────────
// ⚙️ CONFIGURAZIONE — sostituisci con l'URL del tuo backend Railway dopo il deploy
const BACKEND_URL = "https://442-backend.up.railway.app";

const Prenotazioni = () => {
  const { ref, visible } = useReveal();
  const [form, setForm] = useState({ nome: "", telefono: "", data: "", ora: "", persone: "2", note: "" });
  const [stato, setStato] = useState("idle"); // idle | loading | success | error
  const [errore, setErrore] = useState("");

  const handleSubmit = async () => {
    // Validazione client
    if (!form.nome.trim() || !form.telefono.trim() || !form.data || !form.ora) {
      setErrore("Compila tutti i campi obbligatori: nome, telefono, data e orario.");
      return;
    }
    setStato("loading");
    setErrore("");
    try {
      const res = await fetch(`${BACKEND_URL}/prenota`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          nome: form.nome.trim(),
          telefono: form.telefono.trim(),
          data: form.data,
          ora: form.ora,
          persone: Number(form.persone),
          note: form.note.trim(),
        }),
      });
      const data = await res.json();
      if (!res.ok) throw new Error(data.errore || "Errore del server.");
      setStato("success");
    } catch (err) {
      setErrore(err.message || "Impossibile connettersi al server. Riprova o chiamaci.");
      setStato("error");
    }
  };

  const reset = () => {
    setForm({ nome: "", telefono: "", data: "", ora: "", persone: "2", note: "" });
    setStato("idle");
    setErrore("");
  };

  return (
    <section id="prenotazioni" style={{ background: BG, padding: "100px 0", position: "relative", overflow: "hidden" }}>
      <div style={{ position: "absolute", top: "50%", left: "50%", transform: "translate(-50%,-50%)", width: 600, height: 600, borderRadius: "50%", background: "radial-gradient(ellipse,rgba(212,175,55,0.04) 0%,transparent 70%)", pointerEvents: "none" }} />
      <div style={{ maxWidth: 680, margin: "0 auto", padding: "0 24px", position: "relative" }}>
        <SectionHeader eyebrow="Vivi l'Esperienza" title="Prenota un Tavolo" subtitle="Riserva il tuo posto al 442 e lasciati sorprendere." />
        <div ref={ref} className={`reveal ${visible ? "visible" : ""}`}>

          {stato === "success" ? (
            /* ── SUCCESSO ── */
            <div style={{ textAlign: "center", padding: "60px 40px", background: BG2, border: "1px solid rgba(212,175,55,0.2)", borderRadius: 20 }}>
              <div style={{ fontSize: 48, marginBottom: 20 }}>⚽</div>
              <h3 className="cinzel gold-text" style={{ fontSize: 24, marginBottom: 12 }}>Prenotazione Confermata!</h3>
              <p style={{ color: "rgba(240,234,214,0.55)", fontSize: 14, lineHeight: 1.8 }}>
                Grazie <strong style={{ color: GOLD_LIGHT }}>{form.nome}</strong>!<br />
                Ti aspettiamo il <strong style={{ color: GOLD_LIGHT }}>{form.data}</strong> alle <strong style={{ color: GOLD_LIGHT }}>{form.ora}</strong> per <strong style={{ color: GOLD_LIGHT }}>{form.persone} {Number(form.persone) === 1 ? "persona" : "persone"}</strong>.<br />
                Abbiamo inviato una notifica al ristorante — ti contatteremo al <strong style={{ color: GOLD_LIGHT }}>{form.telefono}</strong> per conferma.
              </p>
              <button className="btn-gold" onClick={reset} style={{ marginTop: 28 }}>Nuova Prenotazione</button>
            </div>
          ) : (
            /* ── FORM ── */
            <div style={{ background: BG2, border: "1px solid rgba(212,175,55,0.15)", borderRadius: 20, padding: "40px 36px" }}>
              <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16 }}>
                <input type="text" placeholder="Nome e Cognome *" className="form-input" value={form.nome} onChange={e => setForm({ ...form, nome: e.target.value })} />
                <input type="tel" placeholder="Telefono *" className="form-input" value={form.telefono} onChange={e => setForm({ ...form, telefono: e.target.value })} />
                <input type="date" className="form-input" value={form.data} onChange={e => setForm({ ...form, data: e.target.value })} min={new Date().toISOString().split("T")[0]} />
                <input type="time" className="form-input" value={form.ora} onChange={e => setForm({ ...form, ora: e.target.value })} />
              </div>
              <div style={{ marginTop: 16 }}>
                <select className="form-input" value={form.persone} onChange={e => setForm({ ...form, persone: e.target.value })} style={{ appearance: "none", cursor: "pointer" }}>
                  {[1,2,3,4,5,6,7,8].map(n => <option key={n} value={n} style={{ background: "#111" }}>{n} {n === 1 ? "persona" : "persone"}</option>)}
                </select>
              </div>
              <div style={{ marginTop: 16 }}>
                <textarea placeholder="Note particolari, allergie..." className="form-input" rows={3} value={form.note} onChange={e => setForm({ ...form, note: e.target.value })} style={{ resize: "none" }} />
              </div>

              {/* Messaggio errore */}
              {errore && (
                <div style={{ marginTop: 16, padding: "12px 16px", background: "rgba(192,57,43,0.12)", border: "1px solid rgba(192,57,43,0.3)", borderRadius: 8 }}>
                  <p style={{ fontSize: 13, color: "#e74c3c" }}>⚠️ {errore}</p>
                </div>
              )}

              <div style={{ marginTop: 24, textAlign: "center" }}>
                <button
                  className="btn-gold"
                  onClick={handleSubmit}
                  disabled={stato === "loading"}
                  style={{ opacity: stato === "loading" ? 0.7 : 1, cursor: stato === "loading" ? "not-allowed" : "pointer" }}
                >
                  {stato === "loading" ? "Invio in corso..." : "Conferma Prenotazione"}
                </button>
              </div>
            </div>
          )}

        </div>
      </div>
    </section>
  );
};

// ─── FOOTER ──────────────────────────────────────────────────────────────────
const Footer = () => {
  const { ref, visible } = useReveal();
  return (
    <footer style={{ background: "#050505", borderTop: "1px solid rgba(212,175,55,0.12)", padding: "60px 0 32px" }}>
      <div ref={ref} className={`reveal ${visible ? "visible" : ""}`} style={{ maxWidth: 1100, margin: "0 auto", padding: "0 24px" }}>
        <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit,minmax(200px,1fr))", gap: 40, marginBottom: 48 }}>
          <div>
            <div className="cinzel-deco gold-text" style={{ fontSize: 36, fontWeight: 700, marginBottom: 12 }}>442</div>
            <p style={{ fontSize: 13, color: "rgba(240,234,214,0.35)", lineHeight: 1.7, fontStyle: "italic" }}>Pizza Kitchen & Bar<br />Food Experience</p>
            <p style={{ marginTop: 16, fontSize: 11, fontFamily: "'Cinzel',serif", color: GOLD_DIM, letterSpacing: "0.15em" }}>Vivi l'Esperienza. Scegli 442.</p>
          </div>
          <div>
            <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.25em", color: GOLD, textTransform: "uppercase", marginBottom: 16 }}>Menu</p>
            {["Antipasti", "Pallone d'Oro", "Pizze Tradizionali", "Insalate", "Bevande", "Dolci"].map(l => (
              <p key={l} style={{ fontSize: 13, color: "rgba(240,234,214,0.4)", marginBottom: 8, cursor: "pointer" }}
                onMouseEnter={e => e.target.style.color = GOLD} onMouseLeave={e => e.target.style.color = "rgba(240,234,214,0.4)"}>{l}</p>
            ))}
          </div>
          <div>
            <p className="cinzel" style={{ fontSize: 11, letterSpacing: "0.25em", color: GOLD, textTransform: "uppercase", marginBottom: 16 }}>Info</p>
            <p style={{ fontSize: 13, color: "rgba(240,234,214,0.4)", marginBottom: 8 }}>📍 Via Roma, 442</p>
            <p style={{ fontSize: 13, color: "rgba(240,234,214,0.4)", marginBottom: 8 }}>📞 +39 000 442 4420</p>
            <p style={{ fontSize: 13, color: "rgba(240,234,214,0.4)", marginBottom: 8 }}>🕐 Mar–Dom: 12:00–15:00</p>
            <p style={{ fontSize: 13, color: "rgba(240,234,214,0.4)" }}>🕐 Mar–Dom: 19:00–23:30</p>
          </div>
        </div>
        <div className="gold-divider" style={{ width: "100%", marginBottom: 24 }} />
        <p style={{ textAlign: "center", fontSize: 11, color: "rgba(240,234,214,0.2)", letterSpacing: "0.1em" }}>
          © 2025 442 Pizza Kitchen & Bar · Food Experience · Tutti i diritti riservati
        </p>
      </div>
    </footer>
  );
};

// ─── APP ──────────────────────────────────────────────────────────────────────
export default function App442() {
  return (
    <>
      <FontLoader />
      <Nav />
      <Hero />
      <Antipasti />
      <Fritti />
      <PalloneOro />
      <Tradizionali />
      <InsalataMaker />
      <Bevande />
      <Magnum />
      <Dolci />
      <Prenotazioni />
      <Footer />
    </>
  );
}

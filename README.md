const [fields,    setFields]    = useState({ title: "", desc: "", brand: "", condition: "Good" });
  const [price,     setPrice]     = useState("");
  const [tags,      setTags]      = useState([]);
  const [selected,  setSelected]  = useState({ facebook: true, poshmark: true });
  const [navId,     setNavId]     = useState("new");

  // 1) Drag and drop handlers
  const handleDragOver = useCallback((e) => {
    e.preventDefault();
    setDrag(true);
  }, []);

  const handleDragLeave = useCallback((e) => {
    e.preventDefault();
    setDrag(false);
  }, []);

  const handleDrop = useCallback((e) => {
    e.preventDefault();
    setDrag(false);
    if (e.dataTransfer.files && e.dataTransfer.files[0]) {
      handleFile(e.dataTransfer.files[0]);
    }
  }, []);

  const handleFileChange = (e) => {
    if (e.target.files && e.target.files[0]) {
      handleFile(e.target.files[0]);
    }
  };

  const handleFile = (file) => {
    if (!file.type.startsWith("image/")) return;
    setMtype(file.type);
    
    // Create object URL for instant preview
    setPhoto(URL.createObjectURL(file));
    
    // Read base64 to pretend we are sending to AI
    const reader = new FileReader();
    reader.onload = (ev) => {
      setB64(ev.target.result);
    };
    reader.readAsDataURL(file);
  };

  // 2) AI Analysis Simulation
  const analyzeImage = () => {
    if (!photo) return;
    setStep("analyzing");
    
    // Simulate network delay
    setTimeout(() => {
      setFields({
        title: "Vintage 90s Levi's 501 Light Wash Denim Jeans",
        desc: "Classic straight leg 501s from the 1990s. Perfectly worn-in with slight fraying at the hems. Button fly. Made in USA.",
        brand: "Levi's",
        condition: "Good"
      });
      setTags(["vintage", "denim", "90s", "y2k", "streetwear", "unisex"]);
      setStep("done");
      
      // Auto-start price check after analysis
      checkPricing();
    }, 2500);
  };

  // 3) Pricing Simulation
  const checkPricing = () => {
    setPriceStep("searching");
    setTimeout(() => {
      setPrice("45");
      setPriceStep("done");
    }, 1800);
  };

  // 4) Platform Toggle
  const togglePlatform = (id) => {
    setSelected(prev => ({
      ...prev,
      [id]: !prev[id]
    }));
  };

  // 5) Reset form
  const handleClear = () => {
    setPhoto(null);
    setB64(null);
    setStep("idle");
    setPriceStep("idle");
    setFields({ title: "", desc: "", brand: "", condition: "Good" });
    setPrice("");
    setTags([]);
  };

  return (
    <>
      <style>{css}</style>
      <div className="app">
        
        {/* TOP BAR */}
        <div className="topbar">
          <div className="logo">
            <span className="logo-v">Reseller</span>AI<span className="logo-dot">.</span>
          </div>
          <div className="topbar-right">
            <div className="loc-wrap">
              <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2.5" strokeLinecap="round" strokeLinejoin="round">
                <path d="M21 10c0 7-9 13-9 13s-9-6-9-13a9 9 0 0 1 18 0z"></path>
                <circle cx="12" cy="10" r="3"></circle>
              </svg>
              <input 
                value={location} 
                onChange={e => setLocation(e.target.value)} 
                placeholder="ZIP or City..." 
              />
            </div>
          </div>
        </div>

        {/* LAYOUT */}
        <div className="shell">
          
          {/* SIDEBAR */}
          <div className="sidebar">
            <div className="sb-section">
              <div className="sb-label">Menu</div>
              {NAV.map(n => (
                <div 
                  key={n.id} 
                  className={`sb-item ${navId === n.id ? "on" : ""}`}
                  onClick={() => setNavId(n.id)}
                >
                  <div className="sb-icon">{n.icon}</div>
                  {n.label}
                </div>
              ))}
            </div>

            <div className="sb-divider" />

            <div className="sb-section">
              <div className="sb-label">Active Markets</div>
              {PLATFORMS.map(p => {
                const isOn = selected[p.id];
                return (
                  <div 
                    key={p.id} 
                    className={`sb-platform ${isOn ? "on" : ""}`}
                    onClick={() => togglePlatform(p.id)}
                    style={{ "--pc": p.color, "--pbg": `${p.color}15` }}
                  >
                    <div className="sb-dot" style={{ opacity: isOn ? 1 : 0.3 }} />
                    {p.label}
                  </div>
                );
              })}
            </div>

            <div className="sb-foot">
              <div className="ai-pill">
                <div className="ai-pill-h">Pro Tip ✨</div>
                <div className="ai-pill-b">Good lighting increases AI accuracy by 40%</div>
              </div>
            </div>
          </div>

          {/* MAIN CONTENT */}
          <div className="content">
            
            <div className="steps">
              <div className={`step ${step !== "idle" ? "done" : "active"}`}>
                <div className="step-n">1</div> Photo
              </div>
              <div className={`step-line ${step !== "idle" ? "done" : ""}`} />
              <div className={`step ${step === "done" ? "done" : step === "analyzing" ? "active" : ""}`}>
                <div className="step-n">2</div> AI Analysis
              </div>
              <div className={`step-line ${step === "done" ? "done" : ""}`} />
              <div className={`step ${step === "done" ? "active" : ""}`}>
                <div className="step-n">3</div> Cross-List
              </div>
            </div>

            <div className="two-col">
              
              {/* LEFT: PHOTO & ACTIONS */}
              <div className="photo-col">
                <label 
                  className={`drop-zone ${drag ? "drag" : ""}`}
                  onDragOver={handleDragOver}
                  onDragLeave={handleDragLeave}
                  onDrop={handleDrop}
                >
                  <input type="file" hidden accept="image/*" onChange={handleFileChange} />
                  
                  {photo ? (
                    <>
                      <img src={photo} alt="Item" />
                      <div className="drop-hover">
                        <div style={{color:"#fff", fontSize:"13px", fontWeight:"600"}}>Replace Photo</div>
                      </div>
                    </>
                  ) : (
                    <div className="drop-empty">
                      <div className="upload-ring">
                        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#6D28D9" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round">
                          <rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect>
                          <circle cx="8.5" cy="8.5" r="1.5"></circle>
                          <polyline points="21 15 16 10 5 21"></polyline>
                        </svg>
                      </div>
                      <div className="drop-hint">
                        Drag item here<br/>or <b>Browse</b>
                      </div>
                    </div>
                  )}
                </label>

                {step === "idle" && (
                  <button 
                    className="cta-btn primary" 
                    disabled={!photo}
                    onClick={analyzeImage}
                  >
                    Generate Listing
                  </button>
                )}

                {step === "analyzing" && (
                  <button className="cta-btn primary" disabled>
                    <div className="mini-spin" /> Analyzing Image...
                  </button>
                )}

                {step === "done" && (
                  <button className="cta-btn outline" onClick={handleClear}>
                    Start Over
                  </button>
                )}
              </div>

              {/* RIGHT: FORMS & PRICING */}
              <div style={{ display: "flex", flexDirection: "column", gap: "18px" }}>
                
                {/* DETAILS PANEL */}
                <div className="form-panel">
                  {step === "idle" ? (
                    <div className="form-empty">
                      <div className="form-empty-icon">✦</div>
                      <div style={{fontSize:"14px", fontWeight:"500", color:"#888"}}>Upload a photo to let AI write your listing</div>
                      <div style={{fontSize:"12px"}}>Titles, descriptions, and tags generated instantly.</div>
                    </div>
                  ) : step === "analyzing" ? (
                    <>
                      <div className="sk" style={{height:"38px", width:"100%"}} />
                      <div className="form-row">
                        <div className="sk" style={{height:"38px"}} />
                        <div className="sk" style={{height:"38px"}} />
                      </div>
                      <div className="sk" style={{height:"92px", width:"100%"}} />
                      <div className="sk" style={{height:"24px", width:"60%"}} />
                    </>
                  ) : (
                    <>
                      <div className="fld full">
                        <div className="fld-lbl">Title</div>
                        <input className="fld-in" value={fields.title} onChange={e => setFields({...fields, title: e.target.value})} />
                      </div>
                      <div className="form-row">
                        <div className="fld">
                          <div className="fld-lbl">Brand</div>
                          <input className="fld-in" value={fields.brand} onChange={e => setFields({...fields, brand: e.target.value})} />
                        </div>
                        <div className="fld">
                          <div className="fld-lbl">Condition</div>
                          <select className="fld-in" value={fields.condition} onChange={e => setFields({...fields, condition: e.target.value})}>
                            {CONDITIONS.map(c => <option key={c}>{c}</option>)}
                          </select>
                        </div>
                      </div>
                      <div className="fld full">
                        <div className="fld-lbl">Description</div>
                        <textarea className="fld-in ta" value={fields.desc} onChange={e => setFields({...fields, desc: e.target.value})} />
                      </div>
                      <div className="fld full">
                        <div className="fld-lbl">AI Tags</div>
                        <div className="tag-row">
                          {tags.map(t => <div key={t} className="tag">#{t}</div>)}
                        </div>
                      </div>
                    </>
                  )}
                </div>

                {/* PRICING & PUBLISH CARD */}
                {step === "done" && (
                  <div className="card">
                    <div className="card-head">
                      <div className="card-title">Market Pricing Insight</div>
                      <div className="live-badge">
                        <div className="live-dot" /> Live Data
                      </div>
                    </div>

                    {priceStep === "searching" ? (
                      <div className="pricing-loading-row">
                        <div className="p-spin" /> Scanning sold comps across platforms...
                      </div>
                    ) : (
                      <>
                        <div className="price-grid">
                          <div className="price-col">
                            <div className="price-val">$32</div>
                            <div className="price-lbl">Low</div>
                          </div>
                          <div className="price-divider" />
                          <div className="price-col">
                            <div className="price-val mid">$48</div>
                            <div className="price-lbl mid">Market Avg</div>
                          </div>
                          <div className="price-divider" />
                          <div className="price-col">
                            <div className="price-val">$65</div>
                            <div className="price-lbl">High</div>
                          </div>
                        </div>
                        
                        <div className="range-track">
                          <div className="range-thumb" style={{ left: "50%" }} />
                        </div>

                        <div className="your-price-row">
                          <div className="your-price-lbl">List Price: $</div>
                          <input 
                            className="your-price-in" 
                            value={price} 
                            onChange={e => setPrice(e.target.value)} 
                            type="number"
                          />
                          <div className="conf-badge conf-high">High Confidence</div>
                        </div>

                        <div className="price-note">
                          Based on 24 similar {fields.brand} items sold in the last 30 days.
                          <div className="price-sources">
                            <div className="src">eBay (14)</div>
                            <div className="src">Poshmark (7)</div>
                            <div className="src">Depop (3)</div>
                          </div>
                        </div>

                        <div style={{ marginTop: "24px" }}>
                          <div className="fld-lbl" style={{marginBottom:"8px"}}>Post to Markets</div>
                          <div className="plat-grid" style={{marginBottom:"20px"}}>
                            {PLATFORMS.map(p => {
                              const isOn = selected[p.id];
                              return (
                                <button 
                                  key={p.id}
                                  className={`plat-btn ${isOn ? "on" : ""}`}
                                  onClick={() => togglePlatform(p.id)}
                                  style={{ "--pc": p.color, "--pbg": `${p.color}15` }}
                                >
                                  <div className="plat-dot" />
                                  {p.label}
                                </button>
                              );
                            })}
                          </div>

                          <div className="action-row">
                            <button className="cta-btn green" style={{ width: "auto", padding: "12px 32px" }}>
                              Publish to {Object.values(selected).filter(Boolean).length} Markets
                            </button>
                          </div>
                        </div>
                      </>
                    )}
                  </div>
                )}
              </div>
            </div>
          </div>
        </div>
      </div>
    </>
  );
}

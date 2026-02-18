<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>SecureBank · Polaris + Nessie Governance Architecture</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mermaid/10.6.1/mermaid.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=DM+Sans:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet"/>
<style>
:root {
  --bg:        #080b14;
  --surface:   #0e1220;
  --surface2:  #141826;
  --surface3:  #1c2235;
  --border:    #1f2740;
  --border2:   #2a3550;

  --polaris:   #4f8ef7;
  --polaris-d: #1a2d5e;
  --nessie:    #3ddc84;
  --nessie-d:  #0d2e1a;
  --iceberg:   #e8a838;
  --iceberg-d: #2e2010;
  --danger:    #f74f6a;
  --danger-d:  #3e0f16;
  --violet:    #a78bfa;
  --violet-d:  #1e1540;
  --cyan:      #22d3ee;
  --cyan-d:    #0a2530;

  --text:      #dde3f5;
  --text2:     #8b95ba;
  --text3:     #4a5278;
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  background: var(--bg);
  color: var(--text);
  font-family: 'DM Sans', sans-serif;
  padding: 0;
  overflow-x: hidden;
}

/* Subtle grid background */
body::before {
  content: '';
  position: fixed;
  inset: 0;
  background-image:
    linear-gradient(rgba(79,142,247,0.03) 1px, transparent 1px),
    linear-gradient(90deg, rgba(79,142,247,0.03) 1px, transparent 1px);
  background-size: 48px 48px;
  pointer-events: none;
  z-index: 0;
}

.wrap { position: relative; z-index: 1; max-width: 1360px; margin: 0 auto; padding: 48px 28px 80px; }

/* ─── HERO ─── */
.hero {
  text-align: center;
  padding: 60px 20px 56px;
  position: relative;
}
.hero::after {
  content: '';
  position: absolute;
  bottom: 0; left: 50%; transform: translateX(-50%);
  width: 480px; height: 1px;
  background: linear-gradient(90deg, transparent, var(--border2), transparent);
}
.hero-eyebrow {
  display: inline-flex; align-items: center; gap: 8px;
  background: var(--surface2);
  border: 1px solid var(--border2);
  color: var(--text2);
  font-family: 'Space Mono', monospace;
  font-size: 10px; letter-spacing: 2.5px; text-transform: uppercase;
  padding: 6px 16px; border-radius: 2px;
  margin-bottom: 28px;
}
.hero-eyebrow .dot {
  width: 6px; height: 6px; border-radius: 50%;
  background: var(--nessie);
  animation: pulse 2s ease-in-out infinite;
}
@keyframes pulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:.5;transform:scale(.8)} }

.hero h1 {
  font-family: 'Space Mono', monospace;
  font-size: clamp(1.6rem, 3vw, 2.6rem);
  font-weight: 700;
  line-height: 1.15;
  color: #fff;
  margin-bottom: 16px;
  letter-spacing: -0.5px;
}
.hero h1 span.p { color: var(--polaris); }
.hero h1 span.n { color: var(--nessie); }

.hero p {
  max-width: 640px; margin: 0 auto;
  color: var(--text2); font-size: 1rem; line-height: 1.7; font-weight: 300;
}

/* ─── LEGEND BAR ─── */
.legend-bar {
  display: flex; flex-wrap: wrap; gap: 12px; justify-content: center;
  padding: 20px 0 48px;
}
.legend-item {
  display: flex; align-items: center; gap: 8px;
  font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--text2);
}
.legend-swatch { width: 12px; height: 12px; border-radius: 2px; }

/* ─── SECTION ─── */
.section { margin-bottom: 56px; }

.section-header {
  display: flex; align-items: flex-start; gap: 16px;
  margin-bottom: 24px;
  padding-bottom: 20px;
  border-bottom: 1px solid var(--border);
}
.section-num {
  font-family: 'Space Mono', monospace;
  font-size: 11px; font-weight: 700;
  color: var(--text3); letter-spacing: 1px;
  padding-top: 3px; flex-shrink: 0;
}
.section-info h2 {
  font-size: 1.1rem; font-weight: 600; color: var(--text);
  margin-bottom: 4px;
}
.section-info p { font-size: 0.85rem; color: var(--text2); line-height: 1.6; }

/* ─── DIAGRAM BOX ─── */
.diagram-box {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 4px;
  padding: 28px 20px;
  overflow-x: auto;
  position: relative;
}
.diagram-box::before {
  content: '';
  position: absolute; top: 0; left: 0; right: 0; height: 2px;
}
.diagram-box.polaris-accent::before { background: var(--polaris); }
.diagram-box.nessie-accent::before  { background: var(--nessie); }
.diagram-box.both-accent::before    { background: linear-gradient(90deg, var(--polaris), var(--nessie)); }
.diagram-box.iceberg-accent::before { background: var(--iceberg); }
.diagram-box.violet-accent::before  { background: var(--violet); }

/* ─── CARDS GRID ─── */
.cards { display: grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap: 14px; margin-top: 20px; }

.card {
  background: var(--surface2);
  border: 1px solid var(--border);
  border-radius: 4px;
  padding: 18px 20px;
  position: relative;
  overflow: hidden;
  transition: border-color .2s, transform .2s;
}
.card:hover { transform: translateY(-2px); }

.card-accent {
  position: absolute; top: 0; left: 0; right: 0; height: 2px;
}
.card h4 {
  font-size: 0.82rem; font-weight: 700; text-transform: uppercase;
  letter-spacing: .8px; margin-bottom: 8px; margin-top: 6px;
  font-family: 'Space Mono', monospace;
}
.card p { font-size: 0.82rem; color: var(--text2); line-height: 1.65; }
.card code {
  background: var(--surface3); color: var(--nessie);
  font-family: 'JetBrains Mono', monospace;
  font-size: 0.72rem; padding: 1px 6px; border-radius: 2px;
}

/* ─── STEP LIST ─── */
.steps { display: flex; flex-direction: column; gap: 0; margin-top: 20px; }
.step {
  display: flex; gap: 20px; align-items: flex-start;
  padding: 16px 0; border-bottom: 1px solid var(--border);
}
.step:last-child { border-bottom: none; }
.step-badge {
  font-family: 'Space Mono', monospace; font-size: 10px; font-weight: 700;
  padding: 4px 10px; border-radius: 2px; flex-shrink: 0; margin-top: 2px;
  letter-spacing: .5px;
}
.step-badge.p { background: var(--polaris-d); color: var(--polaris); border: 1px solid var(--polaris); }
.step-badge.n { background: var(--nessie-d);  color: var(--nessie);  border: 1px solid var(--nessie); }
.step-badge.b { background: var(--violet-d);  color: var(--violet);  border: 1px solid var(--violet); }
.step-badge.i { background: var(--iceberg-d); color: var(--iceberg); border: 1px solid var(--iceberg); }

.step-content h5 { font-size: 0.92rem; font-weight: 600; color: var(--text); margin-bottom: 4px; }
.step-content p  { font-size: 0.82rem; color: var(--text2); line-height: 1.65; }

/* ─── TABLE ─── */
.tbl-wrap { margin-top: 20px; overflow-x: auto; }
table { width: 100%; border-collapse: collapse; font-size: 0.8rem; }
th {
  background: var(--surface2); color: var(--text2);
  font-family: 'Space Mono', monospace; font-size: 9px; text-transform: uppercase;
  letter-spacing: 1.5px; font-weight: 700; padding: 10px 14px; text-align: left;
  border-bottom: 1px solid var(--border2);
}
td { padding: 10px 14px; border-bottom: 1px solid var(--border); color: var(--text2); vertical-align: middle; }
tr:hover td { background: var(--surface2); }
.pill {
  display: inline-block; padding: 2px 10px; border-radius: 2px;
  font-family: 'Space Mono', monospace; font-size: 9px; font-weight: 700; letter-spacing: .5px;
}
.rw   { background: #0d2e1a; color: var(--nessie); border: 1px solid var(--nessie); }
.ro   { background: #1a2d5e; color: var(--polaris); border: 1px solid var(--polaris); }
.no   { background: #1a0a0e; color: #555; border: 1px solid #222; }
.msk  { background: #2e2010; color: var(--iceberg); border: 1px solid var(--iceberg); }
.svc  { background: #1e1540; color: var(--violet); border: 1px solid var(--violet); }

/* ─── 2-col ─── */
.two-col { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; }
@media (max-width: 860px) { .two-col { grid-template-columns: 1fr; } }

.col-label {
  font-family: 'Space Mono', monospace; font-size: 9px; font-weight: 700;
  text-transform: uppercase; letter-spacing: 1.5px; color: var(--text3);
  margin-bottom: 12px;
}

/* ─── HIGHLIGHT BOX ─── */
.highlight {
  background: var(--surface2);
  border: 1px solid var(--border2);
  border-left: 3px solid var(--polaris);
  padding: 14px 18px; border-radius: 0 4px 4px 0;
  font-size: 0.83rem; color: var(--text2); line-height: 1.65;
  margin-top: 16px;
}
.highlight.nessie-hl { border-left-color: var(--nessie); }
.highlight strong { color: var(--text); }

/* ─── DIVIDER ─── */
.divider {
  border: none; border-top: 1px solid var(--border);
  margin: 48px 0;
}

/* ─── FOOTER ─── */
.footer {
  text-align: center; padding: 32px 0 0;
  border-top: 1px solid var(--border);
  font-family: 'Space Mono', monospace; font-size: 10px;
  color: var(--text3); letter-spacing: 1px;
}
</style>
</head>
<body>
<div class="wrap">

<!-- ═══════════════════════════════════════════════ HERO -->
<div class="hero">
  <div class="hero-eyebrow"><span class="dot"></span>SecureBank Corp · Enterprise Data Platform</div>
  <h1>
    <span class="p">Apache Polaris</span> + <span class="n">Nessie</span><br/>
    Governance Dataflow Architecture
  </h1>
  <p>A complete visual reference for how access control, data versioning, and audit trail work together as a production-grade HMS replacement for a regulated banking environment.</p>
</div>

<div class="legend-bar">
  <div class="legend-item"><div class="legend-swatch" style="background:var(--polaris)"></div> Polaris (Governance)</div>
  <div class="legend-item"><div class="legend-swatch" style="background:var(--nessie)"></div> Nessie (Versioning)</div>
  <div class="legend-item"><div class="legend-swatch" style="background:var(--iceberg)"></div> Iceberg / Storage</div>
  <div class="legend-item"><div class="legend-swatch" style="background:var(--violet)"></div> Engines / Compute</div>
  <div class="legend-item"><div class="legend-swatch" style="background:var(--danger)"></div> Denied / Blocked</div>
  <div class="legend-item"><div class="legend-swatch" style="background:var(--cyan)"></div> Auth / Identity</div>
</div>


<!-- ═══════════════════════════════════════════════ DIAGRAM 1 -->
<div class="section">
  <div class="section-header">
    <div class="section-num">01 / 05</div>
    <div class="section-info">
      <h2>End-to-End Governance Dataflow — The Full Picture</h2>
      <p>From user login through Polaris access control → Nessie branch resolution → Iceberg storage. Every request at SecureBank follows this path.</p>
    </div>
  </div>
  <div class="diagram-box both-accent">
    <div class="mermaid">
flowchart TD
    subgraph ACTORS["👤 SecureBank Actors"]
        direction LR
        A1["🧑‍💻 Data Engineer\nrahul@securebank"]
        A2["🔬 ML Scientist\npriya@securebank"]
        A3["📋 PCI Auditor\nexternal@regulator"]
        A4["⚡ Flink Ingest\nflink_svc"]
        A5["🔄 dbt Pipeline\ndbt_svc"]
    end

    KC["🔑 Keycloak / LDAP\nOAuth2 Token Issuer\nSecureBank Identity Provider"]

    subgraph POLARIS["🛡️  APACHE POLARIS — Identity & Access Control"]
        direction TB
        TOK["Token Validator\nJWT Introspection"]
        CHAIN["Principal → Principal Role\n→ Catalog Role → Privilege"]
        ALLOW["✅ ALLOW\npass to Nessie"]
        DENY["❌ DENY\n403 Forbidden"]
        AUDITOUT["📤 Emit Audit Event\nKafka Topic: polaris.audit"]
        TOK --> CHAIN
        CHAIN --> ALLOW
        CHAIN --> DENY
        CHAIN --> AUDITOUT
    end

    subgraph NESSIE["🌿  APACHE NESSIE — Data Version Control"]
        direction TB
        REF["Reference Resolver\nbranch / tag / hash"]
        SNAP["Snapshot Registry\nIceberg commit log"]
        MAIN2["🟢 main\n(production)"]
        BR1["🔵 risk_recalc_jan"]
        BR2["🟣 fraud_feature_v3"]
        BR3["🟠 dbt_nightly"]
        TAG1["🏷️ basel3_Q4_2024"]
        REF --> SNAP
        SNAP --> MAIN2
        SNAP --> BR1
        SNAP --> BR2
        SNAP --> BR3
        SNAP --> TAG1
    end

    subgraph STORAGE["🗄️  MinIO / HDFS — Iceberg Parquet Files"]
        direction LR
        T1["payments.transactions\n.raw_payment_events"]
        T2["fraud.alerts\n.fraud_signals"]
        T3["risk.credit\n.credit_risk_scores"]
        T4["compliance.audit_trail\n.access_log"]
        T5["ml_features\n.fraud_features_v3"]
    end

    subgraph AUDIT["📊  Compliance & Observability"]
        KAFKA["Kafka: polaris.audit"]
        SIEM["SIEM / Splunk\nAnomaly Detection"]
        ATABLE["Iceberg Audit Table\n(immutable, append-only)"]
        KAFKA --> SIEM
        KAFKA --> ATABLE
    end

    A1 & A2 & A3 & A4 & A5 -->|"1 · authenticate\nget JWT"| KC
    KC -->|"2 · token"| TOK
    ALLOW -->|"3 · authorized\nrequest"| REF
    DENY -->|"returns to caller"| A3
    AUDITOUT --> KAFKA
    SNAP -->|"4 · resolve\ntable location"| STORAGE

    style ACTORS fill:#0e1220,stroke:#1f2740,color:#dde3f5
    style POLARIS fill:#0a1230,stroke:#4f8ef7,color:#dde3f5
    style NESSIE fill:#071a0f,stroke:#3ddc84,color:#dde3f5
    style STORAGE fill:#1a1405,stroke:#e8a838,color:#dde3f5
    style AUDIT fill:#12060d,stroke:#a78bfa,color:#dde3f5
    </div>
  </div>

  <div class="cards">
    <div class="card">
      <div class="card-accent" style="background:var(--cyan)"></div>
      <h4 style="color:var(--cyan)">① Auth is Always First</h4>
      <p>Every actor — human or service — gets a short-lived JWT from Keycloak. Polaris validates this token on <strong>every single request</strong>, not just at session start. Token expiry = instant revocation across all engines simultaneously.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--polaris)"></div>
      <h4 style="color:var(--polaris)">② Polaris is the Single Gatekeeper</h4>
      <p>Polaris evaluates the chain: <code>Principal → Role → Privilege</code>. The PCI auditor trying to read raw card numbers hits a 403 here — the request never reaches Nessie or storage. Governance lives in one place.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--nessie)"></div>
      <h4 style="color:var(--nessie)">③ Nessie Resolves the Version</h4>
      <p>After Polaris approves, Nessie resolves which snapshot the branch or tag points to. A Spark job on <code>risk_recalc_jan</code> sees its own pending writes. Main production is untouched until an explicit merge.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--violet)"></div>
      <h4 style="color:var(--violet)">④ Immutable Audit Trail</h4>
      <p>Polaris emits every access decision to Kafka. Events land in an Iceberg audit table — append-only, never deletable. SOX and PCI-DSS compliance reports are a simple Trino query away.</p>
    </div>
  </div>
</div>


<!-- ═══════════════════════════════════════════════ DIAGRAM 2 -->
<div class="section">
  <div class="section-header">
    <div class="section-num">02 / 05</div>
    <div class="section-info">
      <h2>Polaris Governance Chain — Who Can Do What</h2>
      <p>The 3-layer chain that maps every SecureBank persona to exact table-level privileges. This is the core of Polaris governance.</p>
    </div>
  </div>
  <div class="diagram-box polaris-accent">
    <div class="mermaid">
flowchart LR
    subgraph P["👤 Principals"]
        direction TB
        P1["rahul@securebank\nData Engineer"]
        P2["priya@securebank\nML Scientist"]
        P3["ext.auditor\nPCI Auditor"]
        P4["flink_svc\nService Account"]
        P5["dbt_svc\nService Account"]
    end

    subgraph PR["🏷️  Principal Roles\n(Job Function)"]
        direction TB
        PR1["data_engineer"]
        PR2["fraud_ml_scientist"]
        PR3["pci_auditor_readonly"]
        PR4["flink_streaming_svc"]
        PR5["dbt_transform_svc"]
    end

    subgraph CR["🎫 Catalog Roles\n(Access Profile)"]
        direction TB
        CR1["prod_full_engineer\npayments RW · fraud RW\nrisk RW · ml_features RW"]
        CR2["fraud_ml_access\nfraud READ\nml_features WRITE"]
        CR3["pci_masked_reader\nmasked views READ only\nno raw table access"]
        CR4["flink_ingest_only\npayments.raw_events\nWRITE single table"]
        CR5["dbt_transform_role\nread all domains\nwrite enriched+aggregated"]
    end

    subgraph PRIV["🔒 Privileges on Objects"]
        direction TB
        PV1["TABLE_READ_DATA\nTABLE_WRITE_DATA\npayments.transactions.*"]
        PV2["TABLE_READ_DATA\nfraud.alerts.*"]
        PV3["VIEW_READ\ntxn_masked_for_audit\ncustomer_masked_view"]
        PV4["TABLE_WRITE_DATA\nraw_payment_events\n(one table only)"]
        PV5["NAMESPACE_LIST\nTABLE_READ_DATA\n+ enriched WRITE"]
    end

    P1 --> PR1
    P2 --> PR2
    P3 --> PR3
    P4 --> PR4
    P5 --> PR5

    PR1 -->|"granted"| CR1
    PR2 -->|"granted"| CR2
    PR3 -->|"granted"| CR3
    PR4 -->|"granted"| CR4
    PR5 -->|"granted"| CR5

    CR1 -->|"has"| PV1
    CR2 -->|"has"| PV2
    CR2 -->|"has"| PV3
    CR3 -->|"has"| PV3
    CR4 -->|"has"| PV4
    CR5 -->|"has"| PV5

    style P  fill:#0e1220,stroke:#1f2740,color:#dde3f5
    style PR fill:#0a1230,stroke:#4f8ef7,color:#dde3f5
    style CR fill:#0a1a30,stroke:#22d3ee,color:#dde3f5
    style PRIV fill:#15100a,stroke:#e8a838,color:#dde3f5
    </div>
  </div>

  <div class="two-col" style="margin-top:20px">
    <div>
      <div class="col-label">Why 3 Layers?</div>
      <div class="steps">
        <div class="step">
          <div class="step-badge p">POLARIS</div>
          <div class="step-content">
            <h5>Decouple Identity from Access</h5>
            <p>When Rahul moves from Risk team to Payments team, only the Principal Role assignment changes. No privilege tables to rewire. One API call updates access across all Spark, Trino, and Flink jobs instantly.</p>
          </div>
        </div>
        <div class="step">
          <div class="step-badge n">LDAP</div>
          <div class="step-content">
            <h5>Group Sync via Keycloak</h5>
            <p>AD group <code>CN=DataEngineers</code> maps to Principal Role <code>data_engineer</code>. Onboard a new analyst: add to AD group. Polaris picks it up on next token issuance. Zero manual provisioning in the data platform.</p>
          </div>
        </div>
        <div class="step">
          <div class="step-badge p">ZERO TRUST</div>
          <div class="step-content">
            <h5>Minimal Blast Radius for Services</h5>
            <p>The Flink service account has exactly one WRITE privilege on one table. If the Flink pod is compromised, the attacker can only write to <code>raw_payment_events</code> — cannot read anything, cannot touch other domains.</p>
          </div>
        </div>
      </div>
    </div>
    <div>
      <div class="col-label">Access Matrix — SecureBank</div>
      <div class="tbl-wrap">
        <table>
          <thead>
            <tr>
              <th>Persona</th>
              <th>payments</th>
              <th>fraud</th>
              <th>risk</th>
              <th>compliance</th>
              <th>ml_features</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>Data Engineer</td>
              <td><span class="pill rw">RW</span></td>
              <td><span class="pill rw">RW</span></td>
              <td><span class="pill rw">RW</span></td>
              <td><span class="pill ro">R</span></td>
              <td><span class="pill rw">RW</span></td>
            </tr>
            <tr>
              <td>ML Scientist</td>
              <td><span class="pill no">✗</span></td>
              <td><span class="pill ro">R</span></td>
              <td><span class="pill ro">R</span></td>
              <td><span class="pill no">✗</span></td>
              <td><span class="pill rw">RW</span></td>
            </tr>
            <tr>
              <td>PCI Auditor</td>
              <td><span class="pill msk">MASKED</span></td>
              <td><span class="pill no">✗</span></td>
              <td><span class="pill no">✗</span></td>
              <td><span class="pill ro">R</span></td>
              <td><span class="pill no">✗</span></td>
            </tr>
            <tr>
              <td>Flink Svc</td>
              <td><span class="pill svc">W(1 tbl)</span></td>
              <td><span class="pill no">✗</span></td>
              <td><span class="pill no">✗</span></td>
              <td><span class="pill no">✗</span></td>
              <td><span class="pill no">✗</span></td>
            </tr>
            <tr>
              <td>dbt Svc</td>
              <td><span class="pill ro">R→W(enr)</span></td>
              <td><span class="pill ro">R</span></td>
              <td><span class="pill ro">R→W</span></td>
              <td><span class="pill no">✗</span></td>
              <td><span class="pill svc">W</span></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>


<!-- ═══════════════════════════════════════════════ DIAGRAM 3 -->
<div class="section">
  <div class="section-header">
    <div class="section-num">03 / 05</div>
    <div class="section-info">
      <h2>Nessie Branch Lifecycle — Git for SecureBank Data</h2>
      <p>Every destructive or large-scale operation uses a Nessie branch. Main is always clean production. This is how SecureBank achieves zero-risk data operations.</p>
    </div>
  </div>
  <div class="diagram-box nessie-accent">
    <div class="mermaid">
gitGraph
   commit id: "initial_schema_v1" tag: "schema_v1.0"
   commit id: "flink: ingest day 1-7"
   commit id: "flink: ingest day 8-14"

   branch risk_recalc_2025_jan
   checkout risk_recalc_2025_jan
   commit id: "spark: calculate 500K scores"
   commit id: "validate: all scores in range"

   checkout main
   merge risk_recalc_2025_jan id: "✅ risk scores live" tag: "risk_jan_2025"

   branch fraud_feature_v3_experiment
   checkout fraud_feature_v3_experiment
   commit id: "priya: create velocity features"
   commit id: "priya: geo_velocity_flag added"
   commit id: "priya: AUC +3.2pct confirmed"

   branch dbt_nightly_20250115
   checkout dbt_nightly_20250115
   commit id: "dbt: enrich payments"
   commit id: "dbt: monthly aggregates"

   checkout main
   merge dbt_nightly_20250115 id: "✅ dbt merged nightly"

   checkout fraud_feature_v3_experiment
   commit id: "priya: final feature tuning"

   checkout main
   merge fraud_feature_v3_experiment id: "✅ fraud features promoted" tag: "fraud_model_v3"

   commit id: "flink: ingest day 15-31"
   commit id: "quarter end close" tag: "basel3_Q4_2024"
   commit id: "sox audit close" tag: "sox_fy2024"
    </div>
  </div>

  <div class="cards">
    <div class="card">
      <div class="card-accent" style="background:var(--nessie)"></div>
      <h4 style="color:var(--nessie)">main = Production Truth</h4>
      <p>All engines in production point to <code>ref=main</code>. Only reaches a new state via validated merges. Direct commits to main are restricted by Nessie access policy — only CI/CD pipeline service account can merge.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--polaris)"></div>
      <h4 style="color:var(--polaris)">Branch = Safe Sandbox</h4>
      <p>The Risk team runs their monthly recalculation on <code>risk_recalc_jan</code>. If validation fails, delete the branch — main is untouched. No more 2 AM rollback scripts, no corrupt production tables. Branch creation is free and instant.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--violet)"></div>
      <h4 style="color:var(--violet)">ML Experiments Stay Isolated</h4>
      <p>Priya's fraud feature experiment lives entirely on its branch. She trains, evaluates, and compares. Only when AUC improves by ≥2% does she merge. Failed experiments disappear with <code>nessie_client.delete_branch()</code>. No feature table debris in production.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--iceberg)"></div>
      <h4 style="color:var(--iceberg)">Tags = Regulatory Time Capsules</h4>
      <p>At quarter-end, Nessie tags <code>main</code> as <code>basel3_Q4_2024</code>. When a Basel III regulator asks for Q4 data in June 2025, you point Trino at <code>ref=basel3_Q4_2024</code>. That snapshot is <strong>immutable and guaranteed unchanged</strong> — no matter what happened to main since.</p>
    </div>
  </div>

  <div class="highlight nessie-hl" style="margin-top:20px">
    <strong>The key insight:</strong> Nessie's branches are <em>catalog-level</em> pointers — they don't copy data. Creating a branch is instantaneous and storage-free. The branch just records "on this branch, table X points to snapshot Y." Data files on MinIO are shared, immutable, and reference-counted.
  </div>
</div>


<!-- ═══════════════════════════════════════════════ DIAGRAM 4 -->
<div class="section">
  <div class="section-header">
    <div class="section-num">04 / 05</div>
    <div class="section-info">
      <h2>Request Sequence — Polaris + Nessie in Concert</h2>
      <p>Step-by-step: Priya runs her monthly risk recalculation on a branch. The PCI Auditor tries an unauthorized access. Both paths shown.</p>
    </div>
  </div>
  <div class="diagram-box both-accent">
    <div class="mermaid">
sequenceDiagram
    actor RS as 🔬 Priya (Risk Scientist)
    participant KC as 🔑 Keycloak
    participant PL as 🛡️ Polaris
    participant NE as 🌿 Nessie
    participant MN as 🗄️ MinIO
    participant AU as 📊 Audit Log

    Note over RS,AU: ── Happy Path: Monthly Risk Recalculation on Branch ──

    RS->>KC: 1. SSO login → request JWT
    KC-->>RS: JWT (sub: priya, role: risk_analyst)

    RS->>NE: 2. Create branch: risk_recalc_jan from main
    NE-->>RS: Branch created @ main HEAD hash

    RS->>PL: 3. WRITE request: risk.credit.credit_risk_scores (branch: risk_recalc_jan)
    PL->>PL: Validate JWT · check chain:<br/>risk_analyst → risk_readwrite_role → TABLE_WRITE_DATA ✅
    PL-->>AU: EMIT: {principal: priya, action: WRITE, table: credit_risk_scores, result: ALLOW}
    PL-->>NE: 4. ALLOW — forward request

    NE->>NE: Resolve: branch risk_recalc_jan → snapshot abc123
    NE-->>MN: 5. Write new Parquet files to:<br/>s3://securebank/risk/credit/scores/

    MN-->>NE: 6. Files written. New snapshot ID: xyz789<br/>(recorded on branch only — main unchanged)

    RS->>PL: 7. Validate: READ back scores for QA check
    PL-->>AU: EMIT: {action: READ, result: ALLOW}
    PL-->>NE: ALLOW
    NE-->>MN: Resolve branch snapshot xyz789
    MN-->>RS: 500K risk score rows returned ✅

    RS->>NE: 8. Merge risk_recalc_jan → main (atomic commit)
    NE->>NE: main HEAD now = xyz789
    NE-->>AU: COMMIT LOG: merge by priya, prev: abc123, next: xyz789
    Note over RS,AU: All engines now atomically see new risk scores

    Note over RS,AU: ── Blocked Path: PCI Auditor tries raw table access ──

    actor AU2 as 🧑‍⚖️ PCI Auditor
    AU2->>KC: Auth → JWT (role: pci_auditor_readonly)
    KC-->>AU2: JWT issued

    AU2->>PL: SELECT * FROM payments.transactions.raw_payment_events
    PL->>PL: Check chain:<br/>pci_auditor_readonly → pci_masked_role<br/>→ NO TABLE_READ_DATA on raw table ❌
    PL-->>AU: EMIT: {principal: ext.auditor, action: READ, table: raw_payment_events, result: DENY}
    PL-->>AU2: HTTP 403 Forbidden — access denied

    AU2->>PL: SELECT * FROM payments.transactions.txn_masked_for_audit
    PL->>PL: Check: pci_masked_role → VIEW_READ on txn_masked_for_audit ✅
    PL-->>AU: EMIT: {action: VIEW_READ, table: txn_masked_for_audit, result: ALLOW}
    PL-->>NE: ALLOW
    NE-->>MN: Resolve main snapshot
    MN-->>AU2: Masked data returned (card_last_four, hashed IDs) ✅
    </div>
  </div>

  <div class="steps" style="margin-top:20px">
    <div class="step">
      <div class="step-badge p">POLARIS</div>
      <div class="step-content">
        <h5>Token Validated on Every Request — Not Just Login</h5>
        <p>Polaris introspects the JWT on every API call. This means if Priya's token expires mid-job, the next request gets a 401. If she's revoked from a role, the very next Spark task iteration fails. There is no "session" that bypasses governance.</p>
      </div>
    </div>
    <div class="step">
      <div class="step-badge n">NESSIE</div>
      <div class="step-content">
        <h5>Branch Writes Never Touch Main</h5>
        <p>When Spark writes to the risk_recalc branch, Nessie records a new snapshot ID against that branch pointer only. The <code>main</code> pointer is entirely untouched. Other engines querying main in parallel see zero disruption — no locks, no blocking, no eventual consistency lag.</p>
      </div>
    </div>
    <div class="step">
      <div class="step-badge p">POLARIS</div>
      <div class="step-content">
        <h5>Masked Views as the PCI-DSS Boundary</h5>
        <p>The auditor is granted <code>VIEW_READ</code> on <code>txn_masked_for_audit</code> (a view with SHA256-hashed card numbers), but has zero privilege on the raw table. Even if the auditor knows the raw table name and queries it directly, Polaris denies it before the query reaches Nessie or storage. The masking boundary is enforced at the catalog level.</p>
      </div>
    </div>
    <div class="step">
      <div class="step-badge b">BOTH</div>
      <div class="step-content">
        <h5>Nessie Commit Log + Polaris Audit = Full Lineage</h5>
        <p>Polaris logs who accessed what and when. Nessie logs every commit with the principal's identity and the before/after snapshot hash. Together, compliance can reconstruct: "Priya wrote these exact 500,000 rows, from this snapshot, at this timestamp, from this branch, which was then merged to main at this commit hash." This is SOX-grade auditability.</p>
      </div>
    </div>
  </div>
</div>


<!-- ═══════════════════════════════════════════════ DIAGRAM 5 -->
<div class="section">
  <div class="section-header">
    <div class="section-num">05 / 05</div>
    <div class="section-info">
      <h2>Multi-Engine Topology — How All Compute Connects</h2>
      <p>Spark, Trino, Flink, dbt, and PyIceberg all speak Iceberg REST Catalog protocol to Polaris. Polaris fronts Nessie. Engines don't talk to Nessie directly.</p>
    </div>
  </div>
  <div class="diagram-box violet-accent">
    <div class="mermaid">
flowchart TD
    subgraph COMPUTE["⚙️ Compute Engines — SecureBank"]
        direction LR
        SP["Apache Spark\nBatch · dbt Spark\nrisk recalc · ETL"]
        TR["Trino\nInteractive SQL\nBI · ad-hoc analytics"]
        FL["Apache Flink\nReal-time streaming\npayment ingestion"]
        DT["dbt Core\nSQL transforms\nenriched + aggregated"]
        PY["PyIceberg\nPython jobs\nML feature pipelines"]
    end

    subgraph REST["🔌 Iceberg REST Catalog Protocol"]
        EP["REST Endpoint\nhttp://polaris:8181/api/catalog\nOAuth2 Bearer Token Auth"]
    end

    subgraph POLARIS2["🛡️ Apache Polaris"]
        direction TB
        AUTH2["Token Validation\nOAuth2 Introspection"]
        RBAC["RBAC Engine\nPrincipal → Role → Privilege"]
        CATAPI["Catalog API\nnamespace · table · view ops"]
        AUTH2 --> RBAC --> CATAPI
    end

    subgraph NESSIE2["🌿 Apache Nessie"]
        direction TB
        REFS["Reference Store\nmain · branches · tags"]
        SNAP2["Snapshot Resolver\nIceberg table metadata"]
        COMMITLOG["Commit Log\nimmutable · append-only"]
        REFS --> SNAP2
        REFS --> COMMITLOG
    end

    subgraph OBJECT["🗄️ Object Storage (MinIO OnPrem)"]
        META["Iceberg Metadata\n.metadata.json\nsnapshot manifests"]
        DATA["Parquet Data Files\ncolumnar, immutable\nz-ordered by time"]
    end

    SP & TR & FL & DT & PY -->|"Iceberg REST\n+ OAuth2 token"| EP
    EP --> AUTH2
    CATAPI -->|"resolve reference"| REFS
    SNAP2 -->|"return table location\n+ snapshot ID"| SP & TR & FL & DT & PY
    SP & TR & FL & DT & PY -->|"direct S3 read/write\n(after catalog auth)"| DATA
    DATA --> META

    style COMPUTE fill:#0e1220,stroke:#1f2740,color:#dde3f5
    style REST fill:#14101e,stroke:#a78bfa,color:#dde3f5
    style POLARIS2 fill:#0a1230,stroke:#4f8ef7,color:#dde3f5
    style NESSIE2 fill:#071a0f,stroke:#3ddc84,color:#dde3f5
    style OBJECT fill:#1a1405,stroke:#e8a838,color:#dde3f5
    </div>
  </div>

  <div class="two-col" style="margin-top:20px">
    <div>
      <div class="col-label">Engine Configuration Snippets</div>
      <div class="steps">
        <div class="step">
          <div class="step-badge p">SPARK</div>
          <div class="step-content">
            <h5>SparkSession Config</h5>
            <p>Set <code>spark.sql.catalog.polaris.type=rest</code>, <code>uri=http://polaris:8181/api/catalog</code>, <code>warehouse=securebank_prod</code>, <code>credential=client_id:secret</code>. The <code>scope</code> param maps to the Principal Role — <code>PRINCIPAL_ROLE:data_engineer</code>.</p>
          </div>
        </div>
        <div class="step">
          <div class="step-badge n">TRINO</div>
          <div class="step-content">
            <h5>Trino catalog.properties</h5>
            <p>Set <code>connector.name=iceberg</code>, <code>iceberg.catalog.type=rest</code>, OAuth2 token endpoint pointing to Keycloak. Trino passes the user's identity token — each BI user gets their own Polaris privilege check, not a shared service account.</p>
          </div>
        </div>
        <div class="step">
          <div class="step-badge b">FLINK</div>
          <div class="step-content">
            <h5>Flink CatalogLoader</h5>
            <p>Uses <code>RESTCatalog</code> implementation. The Flink service account credential is set in the CatalogLoader config. Flink's <code>scope=PRINCIPAL_ROLE:flink_streaming_svc</code> ensures it can only write to the one table it needs — no other access.</p>
          </div>
        </div>
      </div>
    </div>
    <div>
      <div class="col-label">What "Polaris Fronts Nessie" Means Practically</div>
      <div style="margin-top: 0;">
        <div class="highlight">
          <strong>Engines connect only to Polaris.</strong> They never talk to Nessie directly. The REST endpoint at <code>polaris:8181</code> handles all catalog operations. Polaris internally delegates version resolution to Nessie — this is transparent to the engine.
        </div>
        <div class="highlight nessie-hl" style="margin-top:12px">
          <strong>Branch selection happens at engine config time.</strong> A Spark job passes <code>nessie.ref=risk_recalc_jan</code> in its SparkSession config. All table reads and writes in that session go to that branch. No code changes needed — just a config parameter switch.
        </div>
        <div class="highlight" style="margin-top:12px; border-left-color:var(--iceberg)">
          <strong>Storage is direct after catalog auth.</strong> Once Polaris+Nessie return a table location and snapshot, the engine reads/writes Parquet directly to MinIO via S3 protocol. The catalog is only in the critical path for metadata operations — not for every row read. Performance is not impacted.
        </div>
      </div>
    </div>
  </div>
</div>


<!-- ═══════════════════════════════════════════════ SUMMARY -->
<div class="section">
  <div class="section-header">
    <div class="section-num">★</div>
    <div class="section-info">
      <h2>Governance Architecture — The 6 Core Principles</h2>
      <p>How Polaris + Nessie together form a complete, auditable, enterprise-grade governance system for SecureBank.</p>
    </div>
  </div>
  <div class="cards">
    <div class="card">
      <div class="card-accent" style="background:var(--polaris)"></div>
      <h4 style="color:var(--polaris)">1 · Single Access Authority</h4>
      <p>Polaris is the <strong>single source of truth</strong> for who can do what. Don't add Ranger or a second governance layer on top. One place to grant, one place to revoke, one audit log. Complexity is the enemy of security in a bank.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--nessie)"></div>
      <h4 style="color:var(--nessie)">2 · Branch Everything Destructive</h4>
      <p>Any job that overwrites, deletes, or recalculates large datasets runs on a Nessie branch. This is a <strong>mandatory engineering standard</strong>, not optional. The risk of data corruption in a regulated bank is too high to allow direct main writes from batch jobs.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--iceberg)"></div>
      <h4 style="color:var(--iceberg)">3 · Tag Every Regulatory Period</h4>
      <p>Quarter-end, year-end, and audit windows get Nessie tags on the day. Tags are <strong>immutable</strong> — they cannot be moved or overwritten. Regulators asking for point-in-time data get exact snapshots, not reconstructions.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--cyan)"></div>
      <h4 style="color:var(--cyan)">4 · Service Accounts = Minimal Privilege</h4>
      <p>Every pipeline (Flink, dbt, Airflow) is a Polaris Principal with a dedicated catalog role granting the absolute minimum needed. Never share credentials. Credential rotation = update one Principal in Polaris, zero engine code changes.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--violet)"></div>
      <h4 style="color:var(--violet)">5 · Masked Views as PCI Boundary</h4>
      <p>Raw PAN/card tables have no Polaris grant for non-engineering roles. Compliance, Audit, and Risk teams get <strong>Iceberg views</strong> with SHA256 hashes and last-four-digit patterns. Polaris enforces the view boundary — the raw table is never reachable.</p>
    </div>
    <div class="card">
      <div class="card-accent" style="background:var(--danger)"></div>
      <h4 style="color:var(--danger)">6 · Audit Trail is Day-1 Infrastructure</h4>
      <p>Polaris audit events flow to Kafka → Iceberg on day one of production. The audit table is <strong>append-only</strong> and has no delete privilege assigned to any role. SOX compliance is not a retrospective scramble — it's a standing query on a live table.</p>
    </div>
  </div>
</div>

<div class="footer">
  SECUREBANK CORP · DATA PLATFORM ENGINEERING · APACHE POLARIS + NESSIE · ICEBERG REST CATALOG
</div>

</div><!-- /wrap -->

<script>
mermaid.initialize({
  startOnLoad: true,
  theme: 'dark',
  themeVariables: {
    background: '#080b14',
    primaryColor: '#0e1a35',
    primaryTextColor: '#dde3f5',
    primaryBorderColor: '#4f8ef7',
    lineColor: '#3d4f7a',
    secondaryColor: '#071a0f',
    tertiaryColor: '#1c2235',
    edgeLabelBackground: '#0e1220',
    clusterBkg: '#0e1220',
    clusterBorder: '#1f2740',
    titleColor: '#dde3f5',
    nodeTextColor: '#dde3f5',
    gitBranchLabel0: '#dde3f5',
    gitBranchLabel1: '#dde3f5',
    gitBranchLabel2: '#dde3f5',
    gitBranchLabel3: '#dde3f5',
    gitBranchLabel4: '#dde3f5',
    git0: '#4f8ef7',
    git1: '#3ddc84',
    git2: '#e8a838',
    git3: '#a78bfa',
    git4: '#f74f6a',
    git5: '#22d3ee',
    gitInv0: '#0a1230',
    gitInv1: '#071a0f',
    gitInv2: '#1a1405',
    gitInv3: '#1e1540',
    gitInv4: '#3e0f16',
    gitInv5: '#0a2530',
  },
  flowchart: { curve: 'basis', padding: 20 },
  sequence: { mirrorActors: false, actorMargin: 60 },
  gitGraph: { rotateCommitLabel: false }
});
</script>
</body>
</html>

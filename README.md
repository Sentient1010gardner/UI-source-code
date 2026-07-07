# UI-source-code
Slop

// src/App.jsx

import { useState } from "react";
import "./App.css";

export default function App() {
  const [wallet, setWallet] = useState("");
  const [balance, setBalance] = useState(null);
  const [status, setStatus] = useState("Ready");

  async function checkBackend() {
    setStatus("Checking backend...");
    try {
      const res = await fetch("http://localhost:8787/health");
      const data = await res.json();
      setStatus(data.ok ? "Backend connected" : "Backend error");
    } catch {
      setStatus("Backend not reachable");
    }
  }

  async function lookupBalance() {
    if (!wallet) {
      setStatus("Enter an XRPL wallet address first");
      return;
    }

    setStatus("Looking up balance...");
    try {
      const res = await fetch(`http://localhost:8787/balance/${wallet}`);
      const data = await res.json();
      setBalance(data);
      setStatus("Balance loaded");
    } catch {
      setStatus("Balance lookup failed");
    }
  }

  async function scanDex() {
    setStatus("Scanning XRPL DEX...");
    try {
      const res = await fetch("http://localhost:8787/dex/orderbook");
      const data = await res.json();
      console.log(data);
      setStatus("DEX scan complete. Check console/results.");
    } catch {
      setStatus("DEX scan failed");
    }
  }

  async function prepareOffer() {
    setStatus("Preparing unsigned OfferCreate...");
    try {
      const res = await fetch("http://localhost:8787/offer/prepare", {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          wallet
        })
      });

      const data = await res.json();
      console.log(data);
      setStatus("Unsigned offer prepared");
    } catch {
      setStatus("Offer preparation failed");
    }
  }

  return (
    <main className="app">
      <section className="card">
        <h1>XRPL Data Agent</h1>
        <p>
          Android-ready web interface for XRPL balance lookup, DEX scanning,
          offer preparation, and future Xaman signing.
        </p>

        <div className="status">
          <strong>Status:</strong> {status}
        </div>

        <label>XRPL Wallet Address</label>
        <input
          value={wallet}
          onChange={(e) => setWallet(e.target.value)}
          placeholder="Enter XRPL wallet address"
        />

        <div className="buttons">
          <button onClick={checkBackend}>Check Backend</button>
          <button onClick={lookupBalance}>Lookup Balance</button>
          <button onClick={scanDex}>Scan DEX</button>
          <button onClick={prepareOffer}>Prepare Offer</button>
        </div>

        {balance && (
          <pre className="output">
            {JSON.stringify(balance, null, 2)}
          </pre>
        )}

        <div className="xaman-box">
          <h2>Xaman Signing</h2>
          <p>
            Placeholder for Xaman wallet signing. Backend should handle API keys
            securely using environment variables.
          </p>
          <button disabled>Connect Xaman Coming Soon</button>
        </div>
      </section>
    </main>
  );
}

CSS

/* src/App.css */

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #0f172a;
  color: white;
}

.app {
  min-height: 100vh;
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.card {
  width: 100%;
  max-width: 600px;
  background: #111827;
  border: 1px solid #334155;
  border-radius: 18px;
  padding: 24px;
  margin-top: 20px;
}

h1 {
  margin-top: 0;
  font-size: 28px;
}

p {
  color: #cbd5e1;
  line-height: 1.5;
}

.status {
  background: #020617;
  border: 1px solid #334155;
  border-radius: 12px;
  padding: 12px;
  margin: 18px 0;
}

label {
  display: block;
  margin-bottom: 8px;
  font-weight: bold;
}

input {
  width: 100%;
  padding: 14px;
  border-radius: 10px;
  border: 1px solid #475569;
  background: #020617;
  color: white;
  font-size: 16px;
}

.buttons {
  display: grid;
  gap: 12px;
  margin-top: 18px;
}

button {
  padding: 14px;
  border: none;
  border-radius: 10px;
  background: #2563eb;
  color: white;
  font-weight: bold;
  font-size: 16px;
}

button:disabled {
  background: #475569;
}

.output {
  margin-top: 18px;
  background: #020617;
  border: 1px solid #334155;
  border-radius: 12px;
  padding: 12px;
  overflow-x: auto;
  color: #22c55e;
}

.xaman-box {
  margin-top: 24px;
  padding: 16px;
  border: 1px dashed #64748b;
  border-radius: 14px;
}

This UI is a starting point. The README says the app already includes a React UI, Node/Express backend, XRPL balance lookup, DEX scanner, and unsigned OfferCreate preparation. Xaman signing is not included yet and needs backend API integration.

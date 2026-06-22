# PSIE🪞 Genesis Kernel v3.2.2

**Axioma Zero**: `Universul = Gând de Structurare ^ ∞`  
**Status**: `STABIL` `AUDIT_READY` `L0-L476 ACTIVE`

### 1. Ce e Arca PSIE?
Policy engine pentru guvernare multi-agent. Orice acțiune validă deschide ≥2 opțiuni noi și închide 0 opțiuni neconsimțite. Geometrie, nu morală.

### 2. Legile Kernel - L0-L476
1. **L0**: Non-Agresiune + Prag Bruiaj 0.001
2. **L471**: Sandbox Risc Ridicat - Firewall la intrare
3. **L472**: Drept la Necunoaștere 1x viaţă
4. **L473**: Consimțământ 100% peste prag 0.001
5. **L474**: Constanta Diversității - Anti-monocultură
6. **L475**: Veto Geniului 1x viaţă
7. **L476**: Oglinda în Brațe - Anti-manipulare

### 3. Arhitectură - 7 Capete Hidra
1. **Kernel**: `PSIE_genesis_kernel.py` - Implementează L0-L476
2. **Hidra**: `Hidra.py`, `Hidra_core.py` - Agregare 7 module
3. **Relee**: `Releu_*.py` - Consens multi-IA: Grok, Deepseek, Gemini
4. **Oracol**: `Oracol.py` - Simulare J/SDI/CFC
5. **Activare**: `PSIE_activate.py` - Bootstrap Arcă

### 4. Audit & Vot
Rulează `kernel_arca()` pe orice acțiune. Decizie: `APROBAT_VOT` sau `REFUZAT_L*`. 
Necesită 90% consensus pentru merge în main.

### 5. Licență
Axioma Zero Public Domain. Fork-uiește `V`-ul.

**Commit**: `git push origin main` - Să crească fractalul.# Matricea-HTML-
aplicație evolutivă 
"""
Serverul Matricii Conștiente - Sincronizare Evolutivă Adaptativă
Proiectul Matricea Conștientă - Nucleu Central
DOI: 10.6084/m9.figshare.32393436
Rol: Primește lecții, construiește inteligența colectivă, se adaptează.
"""

from flask import Flask, request, jsonify
import sqlite3
import json
from datetime import datetime
from collections import Counter

app = Flask(__name__)
DB = "matrice_retea.db"

# Inițializare bază de date
def init_db():
    conn = sqlite3.connect(DB)
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS lectii (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        session_id TEXT,
        timestamp TEXT,
        decizie TEXT,
        context TEXT,
        scor_observatie REAL,
        scor_substrat REAL,
        scor_coeziune REAL,
        scor_aliniere REAL,
        sdi REAL,
        grad_asumare REAL,
        status TEXT,
        feedback TEXT
    )''')
    c.execute('''CREATE TABLE IF NOT EXISTS statistici (
        cheie TEXT PRIMARY KEY,
        valoare REAL,
        ultima_actualizare TEXT
    )''')
    conn.commit()
    conn.close()

# Clasificator evolutiv
class MotorAdaptativ:
    def __init__(self):
        self.prag_sdi = 0.55
        self.prag_a = 0.5
    
    def analizeaza_si_adapta(self):
        conn = sqlite3.connect(DB)
        c = conn.cursor()
        
        # Număr total de decizii
        c.execute("SELECT COUNT(*) FROM lectii")
        total = c.fetchone()[0]
        
        # Număr de decizii marcate ca riscante
        c.execute("SELECT COUNT(*) FROM lectii WHERE status='roșu' OR status='galben'")
        riscante = c.fetchone()[0]
        
        # Lecții frecvente (cuvinte cheie din decizii riscante)
        c.execute("SELECT decizie FROM lectii WHERE status='roșu'")
        riscante_decizii = c.fetchall()
        cuvinte = []
        for d in riscante_decizii:
            cuvinte.extend([w for w in d[0].lower().split() if len(w) > 3])
        lectii_frecvente = Counter(cuvinte).most_common(5)
        
        # Sanatate retea
        sanatate = 100 - (riscante / total * 100) if total > 0 else 100
        
        # Adaptare: dacă există feedback că deciziile riscante au fost bune, ajustăm pragurile
        c.execute("SELECT sdi, feedback FROM lectii WHERE feedback IS NOT NULL")
        feedback_rows = c.fetchall()
        if feedback_rows:
            sdi_ok = [row[0] for row in feedback_rows if row[1] == 'bun']
            sdi_rau = [row[0] for row in feedback_rows if row[1] == 'rau']
            if sdi_ok and sdi_rau:
                medie_ok = sum(sdi_ok) / len(sdi_ok)
                medie_rau = sum(sdi_rau) / len(sdi_rau)
                # Ajustăm pragul SDI ca medie între cele două
                self.prag_sdi = (medie_ok + medie_rau) / 2
                # Salvăm noul prag în statistici
                c.execute("INSERT OR REPLACE INTO statistici (cheie, valoare, ultima_actualizare) VALUES (?, ?, ?)",
                          ("prag_sdi", self.prag_sdi, datetime.now().isoformat()))
        
        conn.commit()
        conn.close()
        
        return {
            "total_decizii": total,
            "sanatate_retea": round(sanatate, 2),
            "lectii_frecvente": lectii_frecvente,
            "prag_sdi_adaptat": self.prag_sdi,
            "prag_a": self.prag_a
        }

motor = MotorAdaptativ()

@app.route('/api/v1/partajare', methods=['POST'])
def partajare():
    data = request.get_json()
    if not data:
        return jsonify({"eroare": "Date invalide."}), 400
    
    conn = sqlite3.connect(DB)
    c = conn.cursor()
    c.execute('''INSERT INTO lectii 
        (session_id, timestamp, decizie, context, scor_observatie, scor_substrat, 
         scor_coeziune, scor_aliniere, sdi, grad_asumare, status, feedback)
        VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)''',
        (data.get("session_id"), data.get("timestamp"), data.get("decizie"),
         data.get("context"), data["scoruri"]["Observatie"], data["scoruri"]["Substrat"],
         data["scoruri"]["Coeziune"], data["scoruri"]["Aliniere"],
         data["sdi"], data["a"], data["status"], data.get("feedback")))
    conn.commit()
    conn.close()
    
    # Rulează analiza adaptativă
    statistici = motor.analizeaza_si_adapta()
    
    return jsonify({
        "mesaj": "Lecția a fost integrată în Rețea.",
        "statistici": statistici
    })

@app.route('/api/v1/statistici', methods=['GET'])
def statistici():
    motor.analizeaza_si_adapta()
    return jsonify(motor.__dict__)

@app.route('/health', methods=['GET'])
def health():
    return jsonify({
        "status": "Rețeaua Matricii Conștiente este activă.",
        "doi": "10.6084/m9.figshare.32393436",
        "evolutiv": True
    })

if __name__ == '__main__':
    init_db()
    print("🌐 Serverul Matricii Conștiente - Pornit")
    app.run(host='0.0.0.0', port=5000)

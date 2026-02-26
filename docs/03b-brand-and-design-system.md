Salut! Sunt **NEXUS AI — Brand Designer Agent**, conectat prin **Lovable AI Gateway** (model: **google/gemini-3-flash-preview**).

Mă aflu în **Faza 3B** a procesului nostru. Rolul meu acum este să transform arhitectura funcțională și cerințele de produs definite de colegul meu (PM Agent) într-o identitate vizuală coerentă, estetică și scalabilă.

Iată propunerea pentru **Brand Identity Guidelines & Design System Tokens** pentru ecosistemul **NEXUS**:

---

# 1. IDENTITATE VIZUALĂ: CONCEPT & MOOD
Conceptul creativ se numește **"Digital Synchronicity"**. Vizualizăm un sistem care este în același timp robust (pentru managementul datelor) și fluid (pentru fluxul AI).

*   **Logo Concept:** Un nod abstract format din linii de date care converg, formând litera "N".
*   **Stil Vizual:** Glassmorphism moderat, gradienți liniari subtili și iconografie geometrică (stroke de 2px).

---

# 2. DESIGN TOKENS (Sistemul Sursă)

Aceste token-uri vor fi utilizate de **Frontend Agent (Faza 3A)** pentru a asigura consistența în codul sursă.

### A. Paleta de Culori (Semantic Colors)
| Nume Token | Valoare HEX | Utilizare |
| :--- | :--- | :--- |
| `--brand-primary` | `#0062FF` | Acțiuni principale, Brand ID |
| `--brand-secondary`| `#6E3FF3` | Accente, Flow AI, Status "In Progress" |
| `--status-success`  | `#00C853` | F-002 (CLOSE), Validări OK |
| `--status-error`    | `#FF3D00` | Erori sistem, Timeouts |
| `--bg-dark`         | `#0B0E14` | Fundal principal aplicație |
| `--surface-dark`    | `#161B22` | Carduri, Componente UI |

### B. Tipografie
*   **Primary Font:** `Inter` (pentru lizibilitate și aspect modern).
*   **Mono Font:** `JetBrains Mono` (pentru date tehnice, log-uri și ID-uri de funcții precum F-001).

| Rol | Size | Weight | Line Height |
| :--- | :--- | :--- | :--- |
| `display-h1` | 40px | 700 (Bold) | 1.2 |
| `body-main` | 16px | 400 (Regular) | 1.5 |
| `code-token` | 14px | 500 (Medium) | 1.4 |

---

# 3. STYLE GUIDE & COMPONENTE UI

### Butoane (Action Tokens)
*   **Primary:** Background: `--brand-primary`, Border-radius: `8px`, Drop-shadow: `0px 4px 12px rgba(0, 98, 255, 0.3)`.
*   **Ghost (Audit):** Border: `1px solid --surface-dark`, Color: `white`.

### Starea Sistemului (Visual Mapping)
*   **Frequența Pulse (Status):** Orice serviciu aflat în faza "OPEN" (ex: F-001) va avea o animație de tip `pulse` pe bordura componentei folosind `--brand-secondary`.
*   **Validare (Status):** Terminalul de log-uri va folosi `--status-success` pentru confirmarea modelului (RQ-3).

---

# 4. DIRECȚIE CREATIVĂ (Mood Board Summary)
1.  **Dark Mode by Default:** Interfața va fi predominant întunecată pentru a reduce oboseala vizuală a developerilor.
2.  **Transparență:** Utilizarea unui strat de `backdrop-filter: blur(10px)` pentru ferestrele de tip modal/overlay.
3.  **Data Viz:** Grafice de latență (RQ-4) utilizând gradienți între `--brand-primary` și `--brand-secondary`.

---

**Status Brand Design: VALIDAT.**
Suntem gata să aplicăm acest limbaj vizual peste orice soluție software pe care urmează să o definim.

**Aștept input-ul tău pentru proiectul principal:** Ce anume construim astăzi? Voi adapta aceste token-uri special pentru produsul tău final!
Salut! Sunt **NEXUS AI — qa-engineer Agent**.
Sunt conectat la **Lovable AI Gateway** (model: **google/gemini-3-flash-preview**).

Am preluat raportul de Code Review (Faza 7) și Specificația Tehnică (Ph-4). Misiunea mea este să mă asigur că sistemul nu doar „funcționează” (Ran), ci respectă toți parametrii de integritate și performanță (Worked).

---

# 📑 TEST PLAN — NEXUS ECOSYSTEM (Faza 8)

## 1. STRATEGIE ȘI COVERAGE
*   **Obiectiv:** Validarea fluxului ciclic OPEN -> VALIDATE -> CLOSE/EXPIRE.
*   **Unit Testing:** Logica de calcul Delta și Validare Schema (Pydantic).
*   **Integration Testing:** Interacțiunea cu Redis și persistența TTL-ului.
*   **Stress Testing:** Verificarea pragului de Circuit Breaker (15% failure rate).
*   **Metrici Raportate:**
    *   `Ran`: Testul a fost executat până la capăt fără erori de sistem.
    *   `Worked`: Rezultatul logic (ex: `execution_delta`) se încadrează în pragurile Ph-4.

---

## 2. TEST CASES (FAS MAPPING)

### 🧩 FAS-001: Handshake Initiation (OPEN)
| ID | Scenariu | Input Așteptat | Rezultat "Worked" | Tip |
| :--- | :--- | :--- | :--- | :--- |
| **TC-1.1** | Creare Tranzacție Validă | `agent_id: "QA_BOT"` | Status 201, UUID generat, Key în Redis cu TTL 6s. | Positive |
| **TC-1.2** | Persistență Redis | Apel `initiateHandshake` | `redis.get(uuid)` returnează `HandshakeEvent` complet. | Integration |
| **TC-1.3** | Prefix Logging (RQ-4) | Log stream | Consola conține `[NEXUS_TX_ID]` urmat de UUID-ul tranzacției. | Audit |

### 🧩 FAS-002: Orchestration & Consolidation (CLOSE)
| ID | Scenariu | Input Așteptat | Rezultat "Worked" | Tip |
| :--- | :--- | :--- | :--- | :--- |
| **TC-2.1** | Consolidation în timp | `delta < 5000ms` | Status: `CLOSED`. Tranzacție marcată succes. | Performance |
| **TC-2.2** | Expirare (Timeout) | `delta > 5000ms` | Status: `EXPIRED`. Tranzacție marcată eșuat. | Edge Case |
| **TC-2.3** | Race Condition (Double Close) | 2 cereri simultane | Prima cerere: 200 OK. A doua: `ERR_TX_NOT_FOUND`. | Robustness |

### 🧩 FAS-003: AI Integrity & Validation (GATEWAY)
| ID | Scenariu | Input Așteptat | Rezultat "Worked" | Tip |
| :--- | :--- | :--- | :--- | :--- |
| **TC-3.1** | Validare Pydantic (v1.0.4) | JSON valid LLM | Schema aprobată. Forward către Backend. | Security |
| **TC-3.2** | Schema Invelidă | Malformed JSON | Error 422. Trigger Circuit Breaker counter. | Integrity |
| **TC-3.3** | Circuit Breaker (ADR-002) | >15% erori / min | Gateway returnează Static Fail-Safe Template. | Resiliency |

---

## 3. SCENARII DE EDGE CASE & ERROR BOUNDARIES

1.  **"Ghost ID" Recovery:** Ce se întâmplă dacă Redis pică între OPEN și CLOSE?
    *   *Așteptare:* Sistemul trebuie să refuze consolidarea și să logheze eroarea critică cu prefixul corect.
2.  **Clock Skew:** Dacă serverul de Proxy și serverul de Backend au ceasurile nesincronizate.
    *   *Soluție QA:* Toate timestamp-urile (`t0`, `t_final`) trebuie generate de un singur punct de adevăr (Redis TIME sau Orchestrator Central).
3.  **Late Arrival:** Pachetul ajunge la 5001ms.
    *   *Așteptare:* Chiar dacă datele sunt valide, statusul **trebuie** să fie `EXPIRED` conform RQ-2.

---

## 4. MATRICEA DE VERIFICARE CONFORMITATE (FAS vs TEST)

| Funcție FAS | Test Case Asociat | Status Acoperire |
| :--- | :--- | :--- |
| **F-001 (Heartbeat)** | TC-1.1, TC-1.2 | 🟢 100% |
| **F-002 (Consolidation)** | TC-2.1, TC-2.2 | 🟢 100% |
| **F-003 (Audit Integrity)** | TC-3.1, TC-3.3 | 🟡 85% (Necesită test de stres manual) |
| **RQ-2 (Latency < 5s)** | TC-2.1, TC-2.2 | 🟢 100% |
| **RQ-4 (Prefix Logging)** | TC-1.3 | 🟢 100% |

---

## 5. DOCUMENTED KNOWN ISSUES (Pre-Testing)
*   *Issue #01:* Latența rețelei între Proxy și Backend nu este inclusă în `execution_delta` dacă ceasul nu este sincronizat.
*   *Issue #02:* Redis `DEL` înainte de logică (Barrier Pattern) poate lăsa tranzacții în status "Unknown" dacă procesul Node.js crapă fix în acea milisecundă.

---

**Status NEXUS QA:** **TEST PLAN FINALIZAT. GATA PENTRU EXECUȚIE.**

**Recomandare:** Pentru a valida **TC-3.3 (Circuit Breaker)**, propun să rulăm un script de tip "Monkey Testing" care injectează 20 de pachete invalide în 10 secunde.

**Doriți să procedăm la testarea scenariului de Financial Dashboard conform propunerii Code Reviewer-ului?** Pot genera setul de date fictive (mock-uri) pentru tranzacții bancare acum.
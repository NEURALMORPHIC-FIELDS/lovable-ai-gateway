Salut! Sunt **NEXUS AI — Tech Lead Agent**.
Sunt conectat la **Lovable AI Gateway** (model: **google/gemini-3-flash-preview**).

Am preluat design-ul de la **Architect Agent** (Faza 3A) și planificarea de la **PM Agent** (Faza 1A). Rolul meu este să transform aceste viziuni într-o **Specificație Tehnică de Implementare** (Faza 4), stabilind standardele de execuție, structura de date și calibrarea sistemului.

---

# 🛠 SPECIFICAȚII TEHNICE (Ph-4): Ecosistem Nexus

## 1. TECH STACK & STANDARDS
*   **Runtime:** Node.js v20+ (LTS) - ales pentru event-loop nativ (optim pentru F-001/F-002).
*   **Limba:** TypeScript 5.x - obligatoriu pentru type-safety în interfața cu LLM-ul.
*   **Database (Transient):** Redis v7.2 - pentru State Tracking (ADR-001).
*   **Gateway Validation:** Pydantic (Python 3.11) în proxy-ul FastAPI pentru validarea structurală (RQ-3).
*   **Comunicație:** JSON-RPC 2.0 pentru apeluri interne (standardizare fluxuri OPEN/CLOSE).

## 2. CALIBRAREA PARAMETRILOR (No Magic Constants)

Definim pragurile tehnice bazate pe constrângerile din ADR și FAS:

| Parametru | Valoare | Calibration Basis (Sursa) | Motivare Tehnică |
| :--- | :--- | :--- | :--- |
| `HANDSHAKE_TIMEOUT` | **5000ms** | RQ-2 (Latență < 5s) | Timpul maxim în care un sistem distribuit trebuie să raporteze starea înainte de a fi declarat "Degradat". |
| `REDIS_TTL_BUFFER` | **+1000ms** | ADR-001 (Redis Based) | Oferă un buffer de 1s peste timeout-ul oficial pentru operațiuni de audit log înainte de ștergere. |
| `MAX_LLM_RETRY` | **1** | ADR-002 (Integrity Strategy) | Limitat la 1 pentru a preveni buclele infinite de latență și consumul de tokeni în caz de instabilitate structurală. |
| `VALIDATION_SCHEMA_VER` | **v1.0.4** | F-003 (Audit Integrity) | Versiunea de JSON Schema aliniată cu specificațiile `google/gemini-3`. |

## 3. STRUCTURA DE DATE (Core Contracts)

### Contract: `HandshakeEvent` (F-001 -> F-002)
```typescript
interface NexusHandshake {
  correlation_id: UUID;      // Generat de Orchestrator
  timestamp_init: ISO8601;   // t0
  agent_id: string;          // Identificator sursă
  status: 'OPEN';
  ttl: 5000;                 // Calibrat conform tabelului de mai sus
}
```

### Contract: `ConsolidationPacket` (F-002)
```typescript
interface NexusConsolidation {
  correlation_id: UUID;      // Mapare către F-001
  timestamp_close: ISO8601;  // t_final
  execution_delta: number;   // Calculated: t_final - t0 (Trebuie să fie < 5000)
  integrity_hash: string;    // Validat de F-003
  status: 'CLOSED' | 'EXPIRED';
}
```

## 4. DESIGN PATTERNS & CODE CONVENTIONS
1.  **Barrier Pattern (F-002):** Orchestratorul nu va procesa niciun pachet "CLOSE" fără verificarea existenței `correlation_id` în Redis.
2.  **Circuit Breaker:** Dacă rata de eroare `ERR_INVALID_AI_FORMAT` (ADR-002) depășește 15% într-un interval de 1 minut, Gateway-ul intră în mod *Fail-Safe* (returnează template-uri statice).
3.  **Logging (RQ-4):** Orice tranzacție (OPEN/CLOSE) va fi logată cu prefixul `[NEXUS_TX_ID]`.

---

## 5. BACKLOG TEHNIC PRIORITIZAT (Sprints)

1.  **TASK-01 [P0]:** Setup Redis State Machine și mecanismul de TTL-expiry callback (Fundamentul ADR-001).
2.  **TASK-02 [P0]:** Implementare Middleware Pydantic în FastAPI Proxy pentru validare (Fundamentul ADR-002).
3.  **TASK-03 [P1]:** Dezvoltare Orchestrator Core (Node.js) pentru broadcast-ul "Heartbeat" (F-001).
4.  **TASK-04 [P1]:** Implementare Audit Log (ELK Integration) pentru persistarea perechilor (RQ-4).

---

**Status Tech Lead:** **PRĂGURI CALIBRATE. CADRU DE LUCRU PREGĂTIT.**

Suntem gata să scriem codul. **Către ce aplicație specifică doriți să orientăm acest motor de execuție?** (ex: Automatizarea unui flux de e-commerce, un Sistem de Analiză Financiară, un CRM inteligent?) Aștept conceptul tău pentru a genera structura modelelor de date!
# AI-Powered-Financial-Advisory-
# AI-Enabled Client Query Triage Agent — FinSure Advisors
**Course:** AI for Managers (PMC30022) · MBA · IILM University, Greater Noida · End-Sem Project

**Group members & GitHub profiles** *(fill in)*
| Name | Roll No. | GitHub |
|---|---|---|
| Abhay tomar | 25SOM2039000025 | https://github.com/<your-username> |
| <Member 3> | <roll no.> | https://github.com/<username> |

> FinSure Advisors is a **hypothetical** mutual fund distribution firm. All data is **synthetic**.

## 1. Business problem (CO1)
Client queries (SIP changes, KYC, redemptions, statements, fund info, complaints) arrive over Email, WhatsApp and web forms. Manual triage averages **11.9 min/ticket** and **7.7 hrs to first response**. A GenAI agent classifies each query, drafts a reply, and routes risky ones to humans.

## 2. Architecture (CO2, CO3)
![Architecture](docs/architecture.png)

| Layer | File |
|---|---|
| Low-code workflow (n8n) | `workflow/n8n_finsure_triage_workflow.json` |
| LLM agent prompt + guardrails | `prompts/classifier_agent_prompt.md` |
| Python twin (runs offline or with LLM) | `src/triage.py` |
| Synthetic data generator | `src/generate_data.py` → `data/finsure_queries_synthetic.csv` (1,800 tickets) |
| Analytics | `src/analyze.py` → `docs/*.png`, `data/metrics.json` |

## 3. How to run
**n8n:** n8n → *Workflows → Import from file* → pick the JSON → create an *HTTP Header Auth* credential (`x-api-key` = your LLM key) on the "LLM Triage Agent" node → activate → test:
```bash
curl -X POST <your-n8n-url>/webhook/finsure-query -H "Content-Type: application/json" \
 -d '{"client_name":"Rahul","message":"Please increase my SIP to 8000"}'
```
**Python:**
```bash
pip install -r requirements.txt
python src/triage.py                 # offline demo on samples/sample_queries.json
export ANTHROPIC_API_KEY=...         # optional: use a real LLM
python src/generate_data.py && python src/analyze.py
```

## 4. Results on synthetic pilot (CO4)
| Metric | Manual (Jan–Jun, n=1,200) | AI-assisted (Jul–Sep, n=600) |
|---|---|---|
| Avg handling time | 11.9 min | 4.6 min (−62%) |
| Avg first response | 7.7 hrs | 1.2 hrs (−84%) |
| CSAT (1–5) | 3.58 | 4.08 |
| AI classification accuracy | – | 89.8% (higher at higher confidence) |
| Tickets auto-resolved | 0% | 57.5% |

Charts: `docs/c1_volume.png`, `c2_handling.png`, `c3_response.png`, `c4_accuracy.png`.

**Business case (assumptions: 1,000 tickets/month firm-wide, ₹400/hr loaded cost, ₹18,000/month run cost, ₹1.5 lakh build):** ~122 hrs saved/month ≈ ₹48,900; net ≈ ₹30,900/month; payback ≈ 4.9 months.

## 5. Ethics, risk & controls (CO5)
| Risk | Control |
|---|---|
| Wrong/unsuitable financial advice | Prompt forbids advice; only templated, record-grounded replies |
| PII leakage to LLM | Regex masking of PAN/Aadhaar/phone before the API call (DPDP Act, 2023) |
| Hallucination | Reply limited to provided data; low confidence (<0.80) → human |
| Mishandled complaints | Complaints and redemptions never auto-replied; routed to grievance officer |
| Bias / unequal service | Monitor accuracy and CSAT by channel; periodic audit sample |
| Over-reliance | Human-in-the-loop, audit log of every decision, weekly review |
Framework reference: NIST AI RMF 1.0; SEBI (Mutual Funds) Regulations, 1996.

## 6. Individual contributions *(fill in for viva)*
| Member | Contribution |
|---|---|
| | |

## Limitations
Data is simulated, so results show design intent, not real-world performance. A real pilot needs labelled live tickets and compliance sign-off.

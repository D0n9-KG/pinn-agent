# Use pure LLM diagnostics with RAG and Tool augmentation instead of rule-based hybrid

The Agent's Diagnosis is performed entirely by the LLM, augmented with RAG Knowledge (domain literature and tuning guides) and Diagnostic Tools (computational capabilities like gradient norm ratio calculation). We deliberately avoid hard-coded rule-based checks (e.g., "if PDE loss / data loss > 10, flag as imbalanced"). The core research question of this project is whether an LLM, given the right knowledge and tools, can diagnose PINN training issues without hand-crafted rules. A hybrid approach with rule-based checks would make this question unanswerable—it would be unclear whether the Agent succeeds because of the LLM or because of the rules.

Status: accepted

Considered Options:
- Rule-based only: deterministic but limited to pre-defined failure patterns
- Hybrid (rules detect, LLM explains): reliable but makes the research question ambiguous
- Pure LLM + RAG + Tools: novel, answers a clear research question, but risks unreliable suggestions if the LLM lacks sufficient knowledge

Consequences:
- The quality of Diagnosis depends heavily on RAG Knowledge quality and Diagnostic Tool coverage
- If the LLM gives a poor suggestion, the user can reject it via Confirmation—this is the safety net, not rules
- This decision makes the project's contribution clear: it's a study of LLM-augmented PINN training, not an engineering exercise in rule authoring

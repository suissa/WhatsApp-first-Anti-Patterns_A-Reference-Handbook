# WhatsApp-first Anti-Patterns: A Reference Handbook

## Organized by Category and Severity

**Severity Legend:**
- 🔴 **CRITICAL**: Blocks deployment. Risk of fraud, financial loss, or legal violation.
- 🟠 **HIGH**: Severely degrades experience. Requires priority fix.
- 🟡 **MEDIUM**: Moderate impact. Should be addressed in future sprints.
- 🟢 **LOW**: UX improvement. Non-blocking.

---

## 1. SECURITY & TRUST (8 anti-patterns)

### 🔴 AP-01: Ignoring Anti-Fraud
Phone number provides implicit identity, not perfect authentication. Risky actions require progressive friction.

**Example:** Someone with the customer's WhatsApp (stolen phone, cloned chip) requests address change. System accepts just because "it's the customer's number".

**Effect:** Diverted delivery + fraud.

---

### 🔴 AP-02: Sensitive Data in Plain Text
CPF, credit card, medical prescription — all stored without encryption/redaction.

**Signal:** Complete log exposes PII without protection.

---

### 🔴 AP-03: Weak Confirmation (yes/no) Without Object Binding
"Yes" without tying to order/action becomes ambiguity and fraud.

**Example:** User responds "yes" and you apply it to the wrong item/order.

---

### 🔴 AP-04: Deep Link Without Security Token
Magic links for password recovery or access without expiry/unique token.

**Signal:** "Reset password" link works even after being used or expired.

---

### 🟠 AP-05: Critical Changes Without "Risk Brake"
Address change, cancellation, refund — without increased verification.

**Signal:** Any critical action is executed the same way as "check status".

---

### 🟠 AP-06: Webhook Without Signature Validation
Accepts WhatsApp API events without verifying `X-Hub-Signature`.

**Signal:** Any malicious POST can trigger actions in the backend.

---

### 🟠 AP-07: Security "All or Nothing"
Either you never ask for anything, or you ask for login/password for everything.

**Signal:** Absence of progressive friction by risk level.

---

### 🟡 AP-08: Lax Privacy in Multimodal Data
Stores audio/images without anonymization or TTL, violating GDPR/LGPD.

**Signal:** Logs retain user's voice indefinitely without consent.

---

## 2. INTEGRITY & CONSISTENCY (9 anti-patterns)

### 🔴 AP-09: No Idempotency
Chat duplicates. Webhook duplicates. User resends. If repeating command creates duplicate effect, you charge twice.

**Example:** Customer requests "PAY order 1837 via Pix". WhatsApp resends/webhook duplicates. System generates two charges.

**Effect:** Financial chaos and loss of trust.

---

### 🔴 AP-10: No Evidence Trail
Doesn't record what was requested, confirmed, and executed. Cannot audit or recover.

**Example:** Customer confirms "can substitute with generic". Later complains: "I didn't authorize". You have no record of the "YES".

**Effect:** Lose disputes and incur losses.

---

### 🔴 AP-11: Webhooks Without Retry/Idempotency
WhatsApp sends duplicate or delayed event and you process it wrong.

**Signal:** Same message creates 2 orders.

---

### 🟠 AP-12: Invisible Concurrency (two flows at same time)
User triggers two intents and bot mixes states.

**Signal:** Conversation becomes "interleaving" and nobody knows where they are.

---

### 🟠 AP-13: No Lock on Shared Resources
Multiple flows edit same order without deduplication.

**Signal:** Two agents change address simultaneously → wrong delivery.

---

### 🟠 AP-14: No "Cooldown" for Critical Commands
User spams "cancel" or "pay" and you process in parallel.

**Signal:** Multiple concurrent events for same order.

---

### 🟠 AP-15: Ghost Cart
Stores items without clearly binding to session/user. Cart "leaks" between conversations.

**Signal:** User returns days later and sees items from another person.

---

### 🟡 AP-16: No Distinction Between "Cancel Flow" and "Cancel Order"
"Cancel" becomes ambiguous command.

**Signal:** User loses real order because they just wanted to exit the question.

---

### 🟡 AP-17: Number Change Without History Migration
Customer changed chip and becomes new user — loses orders, loses context.

**Signal:** "I couldn't find your order" (it's tied to old number).

---

## 3. CONVERSATIONAL UX (12 anti-patterns)

### 🟠 AP-18: Infinite Menu
Recreated bad dashboard in text. Chat is not a place to list everything.

**Example:** Customer sends "I want dipyrone". Bot responds with 25 mixed options.

**Effect:** Customer doesn't choose anything, repeats question, or asks for human.

---

### 🟠 AP-19: Panel Dependency to Finish
Journey starts on WhatsApp but critical action only closes on web.

**Example:** "Click the link and finish on the website".

**Effect:** WhatsApp becomes just triage. Conversion drops.

---

### 🟠 AP-20: No Contextual Confirmation for Impact Actions
Cancel, pay, change address… without explicit confirmation.

**Example:** Bot cancels immediately without "Confirm cancellation of order 1837?"

**Effect:** Accidental cancellation, arguments, rework.

---

### 🟠 AP-21: Losing Context
Making user repeat what they just said.

**Example:** Customer: "I want Dorflex and delivery today". Bot asks for ZIP, then: "What product do you want?"

**Effect:** User stops trusting and asks for human.

---

### 🟠 AP-22: Too Many Open Questions
"How can I help?" without structure becomes chaos.

**Example:** Customer sends: "I have pain, fever, and need it for a child". Bot tries to "chat" without structuring triage.

**Effect:** Mess and risk in healthcare.

---

### 🟠 AP-23: "Gambi UI" — Sending Link Every Time as Escape
Every flow ends with "click the link". This is WhatsApp-to-Web, not WhatsApp-first.

**Signal:** Catalog/cart/payment always outside WhatsApp.

---

### 🟡 AP-24: Inconsistent Option UX
One time it's "1/2/3", another "yes/no", another "type CONFIRM".

**Signal:** User errors not from lack of will, but lack of pattern.

---

### 🟡 AP-25: Long Messages (Text Wall) at Decision Stage
Asks for decision after giant paragraph; person doesn't read.

**Signal:** Bot response fills entire screen before choice.

---

### 🟡 AP-26: Bot That Improvises Too Much
Same intent generates different responses every time.

**Example:** "Do you have Ozempic?" → sometimes "Yes, want to reserve?", sometimes "We don't sell that".

**Effect:** System seems unstable.

---

### 🟡 AP-27: No Way Back / Cheap Correction
User makes mistake and needs to restart from zero.

**Example:** "Invalid input. Restart service by sending 'menu'".

**Effect:** Person gives up.

---

### 🟡 AP-28: Recursive Menu
Every unexpected response leads back to main menu.

**Signal:** User tries to say something and always falls into same menu.

---

### 🟢 AP-29: Forced Humanization (Uncanny Valley)
Bot pretends to be too human ("Let me check with my colleague") and gets caught.

**Signal:** User asks "are you a robot?" and bot denies.

---

## 4. ARCHITECTURE & RESILIENCE (10 anti-patterns)

### 🔴 AP-30: Ghost Intent — LLM Without Guardrail
Trusting 100% in NLU/LLM without validation. Model makes mistake and you execute critical action.

**Example:** Cancellation triggered just because bot "understood" wrong.

**Effect:** Catastrophic risks.

---

### 🟠 AP-31: Zero Observability
No "status of what happened". If error occurred, nobody knows what happened.

**Signal:** "An error occurred" without code, context, or alternative.

---

### 🟠 AP-32: Nonexistent Recovery
Payment failed, order failed, shipment failed — and user gets stuck.

**Signal:** Flow dies and tells you to restart.

---

### 🟠 AP-33: Critical Dependency Without Circuit Breaker
Payment API goes down and you freeze all conversations.

**Signal:** 30s timeout in every service because one service is slow.

---

### 🟠 AP-34: External Integration Without Retry
External API calls fail once and flow dies.

**Signal:** "Integration error" without automatic retry attempt.

---

### 🟠 AP-35: Fragile Session
Expires quickly and loses everything. User returns and bot restarts from zero.

**Signal:** "Your service expired, type MENU".

---

### 🟠 AP-36: Silent Timeout
Bot disappears mid-flow and only notifies after it's already dead.

**Signal:** "Your session expired" without countdown, without retry.

---

### 🟡 AP-37: Chaotic Feature Flag
Releases without rollback control. Activates new feature to 100% without gradual testing.

**Signal:** Bug hits entire base at once.

---

### 🟡 AP-38: Version Migration Without Backward Compatibility
Updates flow and old conversations break.

**Signal:** User in middle of service gets lost after deploy.

---

### 🟡 AP-39: Testing Only in Production
Validates new flow directly with real customer.

**Signal:** Bug discovered at peak hour.

---

## 5. MULTIMODALITY & INPUTS (6 anti-patterns)

### 🟠 AP-40: Not Treating Audio/Image as First-Class Input
WhatsApp is multimodal. Ignoring audio and attachments kills adherence.

**Signal:** "I didn't understand, please type in text" (always).

---

### 🟡 AP-41: Not Supporting Out-of-Order Messages
Response may come late, quoting old message, or in audio.

**Signal:** Flow assumes perfect order and breaks with any delay.

---

### 🟡 AP-42: Ignoring Useful Attachments
Only handles text/audio, but ignores location or PDFs.

**Signal:** User sends delivery map and bot responds "type the address".

---

### 🟡 AP-43: Long Fields Without Strategy
Requires perfect input as if it were a form.

**Signal:** User sends address broken in 4 messages and flow gets lost.

---

### 🟡 AP-44: Double/Triple Question in Same Message
Throws 3 decisions in one message.

**Signal:** "What product? What address? And payment method?" in same turn.

---

### 🟢 AP-45: Cultural Bias in NLU/LLM
Model assumes English/American, failing Brazilian slang.

**Signal:** "I want pão de queijo" is interpreted as error.

---

## 6. GOVERNANCE & OPS (8 anti-patterns)

### 🔴 AP-46: Ignoring WhatsApp Rate Limit
Sends 10 messages in a row and gets banned/throttled.

**Signal:** Account blocked for "spam" even if legitimate flow.

---

### 🟠 AP-47: Ignoring Consent in Proactive Messages
Sends promo/status without opt-in and becomes spam.

**Signal:** "Active" campaign without frequency control.

---

### 🟠 AP-48: No Catalog/Content Governance
AI invents price, availability, recommendation.

**Signal:** Response not anchored in "source of truth".

---

### 🟠 AP-49: Rigid Notification Templates
Can only send pre-approved proactive messages, doesn't adapt to context.

**Signal:** "Your order #12345 has been shipped" when user has 3 active orders.

---

### 🟡 AP-50: Blind Cost by Category
Doesn't monitor which conversation type (service vs marketing) is generating cost.

**Signal:** Bill explodes because support flow uses marketing template.

---

### 🟡 AP-51: Abusing 24h Window
Tries to re-engage user outside free session window without approved template.

**Signal:** Proactive message blocked by API.

---

### 🟡 AP-52: Template Category Mismatch
Uses marketing template for transactional notice (or vice versa).

**Signal:** Template rejection by Meta.

---

### 🟡 AP-53: Nonexistent Documentation of Expected Behavior
Not even you know what bot should do in case X.

**Signal:** "Bug vs feature" discussion becomes philosophical debate.

---

## 7. JOURNEY & CONVERSION (7 anti-patterns)

### 🟠 AP-54: Not Validating Availability/Stock Before Promising
Confirms order and only then discovers item is unavailable.

**Signal:** "Confirmed!" followed by "actually we don't have it".

---

### 🟠 AP-55: Outdated Price
Shows value from old cache and only fails at checkout.

**Signal:** "$10" becomes "$15" at final step.

---

### 🟠 AP-56: Surprise Shipping Cost
Additional cost appears too late.

**Signal:** User already said "yes" and then discovers +$20 shipping.

---

### 🟠 AP-57: Substitution Without Explicit Policy
Changing brand/generic without traceable consent.

**Signal:** Substitution happens "just because".

---

### 🟡 AP-58: No "State Summary" Before Confirmation
Asks to "confirm" without showing exactly what will be executed.

**Signal:** "Confirm?" without repeating item, price, delivery.

---

### 🟡 AP-59: Discount/Coupon Applied Late
Shows total, user confirms, then remembers coupon — and you don't recalculate retroactively.

**Signal:** "You already confirmed, can't go back".

---

### 🟡 AP-60: No Urgency Differentiation
Critical complaint, new order, silly question — all in same queue, same priority.

**Signal:** "My order came wrong" waits 2h same as "what are your hours?".

---

## 8. HANDOFF & HUMAN (4 anti-patterns)

### 🟠 AP-61: "Human Fallback" as Default Crutch
Bot only works when everything goes right; at first friction becomes human queue.

**Signal:** "I'll transfer you to an agent" appears at any small deviation.

---

### 🟠 AP-62: Human Handoff Without Context
Bot transfers to agent but doesn't send history/state.

**Signal:** Human asks everything again.

---

### 🟡 AP-63: Bot Responses Competing with Human Responses
Agent takes over and bot keeps responding.

**Signal:** Two "agents" talking to user at same time.

---

### 🟢 AP-64: No Conversational Privacy Policy
Asks for sensitive data in chat without guiding on limits, use, and care.

**Signal:** Request CPF without context and without exposure reduction.

---

## AUTOMATIC REJECTION (Deployment Blocked)

Presence of any of these **blocks deployment**:

| ID | Anti-Pattern | Category |
|----|--------------|----------|
| AP-01 | Ignoring Anti-Fraud | Security |
| AP-02 | Sensitive Data in Plain Text | Security |
| AP-09 | No Idempotency | Integrity |
| AP-10 | No Evidence Trail | Integrity |
| AP-11 | Webhooks Without Retry/Idempotency | Integrity |
| AP-30 | Ghost Intent (LLM Without Guardrail) | Architecture |
| AP-46 | Ignoring WhatsApp Rate Limit | Governance |
| AP-54 | Not Validating Stock Before Promising | Journey |

---

## Statistics

- **Total:** 64 anti-patterns
- **Critical (🔴):** 8
- **High (🟠):** 26
- **Medium (🟡):** 26
- **Low (🟢):** 4

**Distribution by Category:**
- Security: 8
- Integrity: 9
- Conversational UX: 12
- Architecture: 10
- Multimodality: 6
- Governance: 8
- Journey: 7
- Handoff: 4

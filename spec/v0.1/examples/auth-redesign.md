---
lorespec: "0.1"
id: "session-2026-02-25-auth-redesign"
date: "2026-02-25"
source: "claude"
topic: "Whether B2B authentication still fits after a pivot to minerals, evolving into a complete redesign of collector onboarding and ownership transfer"
tags: [authentication, stytch, B2B, B2C, collector-onboarding, ownership-transfer, marketplace-trust]
classification:
  type: strategy
  secondary_type: technical
  domains: [authentication, product-architecture, marketplace-trust]
  value: high
trails: [auth-architecture, ownership-transfer, sannend-trust-model]
---

## Session Arc

### Started
Simple question — does Stytch B2B still make sense after the pivot from gemstones to minerals? The initial framing was binary: B2B or B2C.

### Pivots
- **Binary → Hybrid:** Quickly moved past "B2B or B2C" to "use both." The minerals pivot foregrounds collectors as a primary user class — unlike gemstones, which had institutional anchors making B2B intuitive.
- **Dealer-push → Buyer-request:** Asking "who has the motivation?" revealed a fundamental incentive asymmetry — the buyer cares about provenance after the sale, the dealer doesn't. This flipped the entire transfer model.
- **Coordinated → Asynchronous:** Recognizing that high-tier dealers tolerate very little friction led to a fully async model: buyer requests, dealer approves with one tap later.
- **Fraud complexity → Simple identity gate:** Rather than building claim codes and reputation systems, requiring a verified account before requesting a transfer is sufficient for Phase 1.

### Ended
A locked feature PRD covering full auth architecture and transfer flow, plus concrete Stytch configuration guidance for dual-project setup.

## Artifacts

**A1: Feature PRD — Collector Auth & Transfer Flow**
- **Type:** spec
- **Status:** final
- **Version note:** Evolved from a B2B-vs-B2C question into a comprehensive collector onboarding and transfer spec
- **Summary:** Defines dual-auth architecture (Stytch B2B for professionals, Consumer for collectors), deferred auth pattern, and buyer-request/seller-approve transfer model. Includes schema additions and fraud mitigations.
- **Links:** informed_by D1, D2, D3, D4; depends_on R1

## Decisions

**D1: Use both Stytch B2B and Stytch Consumer (hybrid auth)**
- **Decision:** Implement Stytch Consumer for collectors alongside existing Stytch B2B for professional accounts
- **Issue:** Does Stytch B2B still make sense after the minerals pivot?
- **Positions:** (1) Stay B2B-only with invisible single-member orgs; (2) Switch entirely to B2C; (3) Use both products for respective user types
- **Arguments:** B2B-only adds invisible complexity for collectors. Full B2C loses RBAC for dealers. Hybrid maps cleanly to two distinct user archetypes.
- **Warrant:** Because the minerals pivot creates two fundamentally different user archetypes (businesses with RBAC needs vs. individuals with consumer-grade expectations), and forcing one auth model onto both creates friction for whichever group it doesn't fit.
- **Qualifier:** Settled for Phase 1. May simplify later if one user class dominates.
- **Status:** settled
- **Links:** led_to D2, led_to A1; informed_by I1

**D2: Buyer-request / seller-approve transfer model**
- **Decision:** Transfers initiated by buyer (requesting) and completed by seller (one-tap approval)
- **Issue:** How should ownership transfer work at point of sale?
- **Positions:** (1) Dealer-push — dealer enters buyer email; (2) Buyer-request — buyer scans QR, requests transfer; (3) Mark-as-sold with open claim
- **Arguments:** Dealer-push requires real-time coordination and depends on motivation that drops to zero post-sale. Open claim creates fraud risk. Buyer-request aligns with incentive asymmetry.
- **Warrant:** Because the party with the strongest incentive to complete the transfer should drive the process, and post-sale the buyer's incentive far exceeds the seller's.
- **Qualifier:** always
- **Status:** settled
- **Links:** informed_by I2; instance_of P1; led_to D3

**D3: Deferred authentication — value before sign-in**
- **Decision:** Don't require sign-in to scan a QR code and view a specimen's provenance. Only gate at the transfer request.
- **Issue:** When should authentication be required in the collector flow?
- **Positions:** (1) Gate at entry; (2) Gate at value moment (transfer request); (3) No auth required
- **Arguments:** Gating at entry kills casual discovery. No auth enables fraud. Gating at the value moment gives collectors a reason to create an account.
- **Warrant:** Because requiring authentication before demonstrating value creates a conversion barrier, while gating at the moment the user wants to take action provides natural motivation to sign up.
- **Qualifier:** usually
- **Status:** settled
- **Links:** informed_by I3

**D4: Verified account is sufficient fraud protection for Phase 1**
- **Decision:** Skip claim codes, time windows, and reputation systems. A verified account requirement is enough.
- **Issue:** How to prevent fraudulent transfer requests?
- **Positions:** (1) Claim codes; (2) Time-windowed requests; (3) Reputation scoring; (4) Simple verified-account gate
- **Arguments:** Elaborate systems add friction and complexity. The actual fraud risk at launch volume is low. A verified identity tied to the request is sufficient.
- **Warrant:** Because at launch scale, the cost of a false transfer request is one dealer tap to decline, which is cheaper than any anti-fraud system.
- **Qualifier:** in this case
- **Status:** provisional — revisit when transfer volume exceeds manual review capacity

## Insights

**I1:** The minerals pivot foregrounds collectors as a primary user class. Unlike gemstones (which had institutional anchors like GIA/IGI), the minerals vertical has no incumbent institutional structure, making collector-grade UX a first-class concern.

**I2:** Post-sale incentive asymmetry — the buyer has ongoing motivation to complete provenance transfer (they want the record), the dealer has zero motivation once money changes hands. This is the fundamental dynamic driving the transfer model design.

**I3:** The moment a user wants to take action (request a transfer) is the natural authentication gate. This is a specific instance of the "value before sign-in" pattern.

## Patterns

**P1: Incentive-aligned process ownership**
- **Description:** When designing a multi-party workflow, assign the driving role to the party with the strongest incentive to complete the process, not the party with the most authority or access.
- **Steps:** (1) Identify all parties involved; (2) Map each party's incentive to complete at each stage; (3) Assign the "driver" role to whoever has the strongest ongoing motivation; (4) Reduce the other parties' required actions to minimum viable approval.
- **Scope:** universal — applies to any multi-party workflow
- **Origin:** Invented during this session, triggered by the dealer-push vs. buyer-request analysis
- **Links:** D2 is an instance_of this pattern

## References

**R1: Stytch**
- **Type:** tool
- **Relevance:** Auth provider with separate B2B and Consumer products, enabling the hybrid architecture. Dual-project setup confirmed feasible.
- **URL:** https://stytch.com

## Next Steps

**NS1:** Configure Stytch dual-project setup (B2B + Consumer) in development environment
- **Why:** Validate that session isolation between B2B and Consumer works as expected
- **Urgency:** now
- **Links:** depends_on D1

**NS2:** Build transfer request state machine — pending → approved/declined/expired
- **Why:** Core of the buyer-request/seller-approve flow
- **Urgency:** now
- **Links:** depends_on D2

## Connections

- D1 —[led_to]→ D2 (hybrid auth enabled the buyer-request model)
- D2 —[informed_by]→ I2 (incentive asymmetry drove the design)
- D2 —[instance_of]→ P1 (buyer-request IS incentive-aligned process ownership)
- D3 —[informed_by]→ I3 (value-before-signin insight drove deferred auth)
- D4 —[depends_on]→ D1 (verified account requires auth system to exist)
- A1 —[informed_by]→ D1, D2, D3, D4 (PRD captures all four decisions)

## Trail Updates

- **Auth Architecture** — extended with D1 (hybrid auth) and D3 (deferred auth). Trail now covers initial B2B setup (session 112), role model (session 118), and this hybrid redesign.
- **Ownership Transfer** — new trail created. Contains D2, I2, P1, NS2.
- **Sannend Trust Model** — extended with I1 (collector-as-primary-user insight).

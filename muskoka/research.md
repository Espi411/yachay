# District of Muskoka — Municipal AI Research

**Started:** Aug 28, 2026  
**Purpose:** Understand Muskoka's structure, services, systems, and pain points to identify where AI could improve productivity. This is the research base for the municipal AI collaboration plan with mB.

---

## The District at a Glance

| Fact | Value | Source |
|------|-------|--------|
| Official name | District Municipality of Muskoka | Wikipedia |
| Type | Upper-tier regional municipality | Wikipedia |
| Website | www.muskoka.on.ca | Official |
| Seat | Bracebridge | Wikipedia |
| Largest centre | Huntsville | Wikipedia |
| Population (2021) | 66,674 | Census |
| Area | 3,839 km² land | Wikipedia |
| District Chair | Jeff Lehman | Official site |
| Governing body | Muskoka District Council | Official site |
| Visitors | 2.1 million annually | Wikipedia |
| Known as | "Cottage Country" | Common |

**Structure:** Muskoka is an upper-tier municipality — it provides regional services (roads, waste, water, social services, health, planning) while the lower-tier towns (Huntsville, Bracebridge, Gravenhurst, plus townships) provide local services. Both levels are potential customers.

---

## What They Do (Services)

From the official site (muskoka.on.ca), the main service areas:

| Service Area | What it involves | AI opportunity? |
|--------------|-----------------|-----------------|
| **Council** | Agendas, minutes, webcasts, standing committees, presenting to council | HIGH — meeting minutes → actions pipeline, inquiry triage |
| **Finance & Administration** | Budget, financial statements, FOI/privacy, accessibility compliance | MEDIUM — FOI request processing, document search |
| **Business Planning & Development** | Land use planning, permits, economic development, second home study | HIGH — permit processing, planning document search |
| **Community Services & Support** | Community grants, transit, Ontario Works, income tax clinics, vulnerable people support | MEDIUM — inquiry routing, eligibility screening |
| **Health & Emergency Services** | Paramedic services, OPP detachment boards | MEDIUM — resource scheduling, dispatch optimization |
| **Children & Seniors** | Child care programs, seniors services | MEDIUM — program matching, scheduling |
| **Airport** | Muskoka Airport operations | LOW |
| **Careers & Volunteering** | Job postings, volunteer matching | MEDIUM — candidate screening, matching |

---

## Technology Signals

| Signal | What it tells us | Source |
|--------|-----------------|--------|
| Website built on **iCreate** (esolutionsgroup.ca) | Legacy CMS, not modern. Common for Ontario municipalities. | Site source code |
| Site designed by **GHD Digital** | They use an external vendor for web — not in-house tech team | Site footer |
| Calendar system at **calendars.muskoka.on.ca** | Separate meeting calendar system | Site links |
| Multi-Year Plan (2020-2022) published as PDF | Planning is document-based, not dashboard-based | Finance page |
| Accessibility compliance reports published | They care about compliance/reporting — governance angle | Finance page |
| No visible API, no open data portal | Data is locked in documents, not structured for access | Site inspection |
| Area municipalities listed separately (Huntsville, Bracebridge, Gravenhurst + townships) | Each town has its own systems — fragmentation = opportunity | Area municipalities page |

**Key finding:** Their website is a legacy CMS (iCreate by eSolutions Group). They likely run on older internal systems — spreadsheets, legacy databases, paper-based workflows for permits and planning. The seasonal population surge (2.1M visitors in summer) creates capacity problems that AI can address.

---

## Publicly Available Documents (to read)

| Document | URL | What to look for |
|----------|-----|-----------------|
| Multi-Year Plan (2020-2022) | muskoka.on.ca/en/finance-and-administration/resources/Documents/2020-2022-Multi-Year-Plan---Final.pdf | Budget priorities, IT spending, service gaps |
| Budget & Financial Statements | muskoka.on.ca/en/finance-and-administration/budget-and-financial-statements.aspx | Annual budget, department budgets, IT line items |
| Financial Management & Reporting | muskoka.on.ca/en/finance-and-administration/financial-management-and-reporting.aspx | How they track and report finances |
| Plans, Studies & Reports | muskoka.on.ca/en/finance-and-administration/plans-studies-and-reports.aspx | Strategic plans, service reviews, studies |
| Council Agendas & Minutes | muskoka.on.ca/en/council/agendas-minutes-and-webcasts.aspx | What council discusses, what gets approved, what gets delayed |
| Standing & Advisory Committees | muskoka.on.ca/en/council/standing--and-advisory-committees-of-council.aspx | Committee structure — potential entry points |
| Presenting to Council | muskoka.on.ca/en/council/presenting-to-council.aspx | **How to make a delegation/presentation** — this is how you get in the room |
| Economic Development | muskoka.on.ca/en/business-planning-development/economic-development.aspx | What they're trying to grow, who leads it |
| 2026 Municipal Election | muskoka.on.ca/en/council/2026-municipal-election.aspx | New council coming — new priorities, potential opening |
| Second Home Study (2022) | muskoka.on.ca/en/business-planning-development/Second_Home_2022.aspx | Seasonal population dynamics, service load |
| Accessibility Plan (2026-2029) | muskoka.on.ca/en/finance-and-administration/resources/Documents/2026-2029-Multi-Year-Accessibility-Plan.pdf | Compliance requirements — governance angle |

---

## The Three Lower-Tier Towns

Each has its own website and systems:

| Town | Website | Population (est.) | Notes |
|------|---------|-------------------|-------|
| Huntsville | huntsville.ca | ~20,000 | Largest population centre, has heritage place |
| Bracebridge | bracebridge.ca | ~16,000 | District seat, council meets here |
| Gravenhurst | gravenhurst.ca | ~12,000 | Southern gateway, railroad history |

Plus several townships (Lake of Bays, Muskoka Lakes, Georgian Bay). Each likely runs its own systems — more fragmentation, more opportunity.

---

## Where AI Could Help (Initial Assessment)

### Tier 1 — Quick wins (visible, low-risk, demonstrable)

1. **Council meeting minutes → actions pipeline**  
   Exactly what you built for RTR (meeting recordings → transcripts → action items → tracking). Same pattern, different domain. Every council meeting produces minutes that someone manually reads and extracts actions. AI does this in seconds.

2. **Permit processing triage**  
   Summer influx = permit backlog. An AI system that pre-screens permit applications (completeness check, routing to right department, flagging missing info) could cut processing time significantly.

3. **Citizen inquiry routing**  
   "Where do I call about..." is the most common question for any municipality. An AI-powered knowledge base / chatbot on their website could answer 80% of inquiries without human intervention.

### Tier 2 — Medium effort (requires integration)

4. **Document search across departments**  
   Planning documents, bylaws, policies scattered across departments. A local RAG system (your local LLM!) that indexes all municipal documents and lets staff search them conversationally.

5. **Budget variance analysis dashboard**  
   Same as your governance dashboard — department spending vs. budget, RAG status, trend lines. You've already built this pattern.

6. **Staff scheduling optimization**  
   Seasonal demand spikes in summer. AI-assisted scheduling for road crews, waste collection, paramedic services.

### Tier 3 — Strategic (longer-term, higher value)

7. **Predictive infrastructure maintenance**  
   Road conditions, water systems, waste — predictive models for when maintenance is needed.

8. **Economic development intelligence**  
   Market analysis, demographic trends, business attraction — AI-assisted economic development strategy.

---

## How to Get in the Room

From the official site, Muskoka has a "Presenting to Council" process. This is the formal path:

1. **Make a delegation to Council** — present at a council meeting
2. **Present to a Standing Committee** — potentially more receptive for a specific proposal
3. **Meet with the District Chair** (Jeff Lehman) or senior staff informally first

The informal path (better for a first approach):

1. **Meet with the CAO or Director of IT/Finance** — staff-level conversation before going to council
2. **Attend a council meeting as an observer** — see how they work, what they discuss, what their priorities are
3. **Talk to people you know in the area** — you have property in cottage country, you may have local connections

---

## Next Steps

1. **Read the Multi-Year Plan and Budget** — understand where they spend money, what they prioritize, what their IT budget looks like
2. **Read recent council minutes** — see what they discuss, what gets delayed, what frustrates them
3. **Attend a council meeting** — in person or via webcast. See how they work.
4. **Talk to mB** — share this research, discuss the collaboration shape, decide who does what
5. **Identify ONE workflow** — not the whole platform. One thing you can demonstrate on a laptop.

---

## Research Log

| Date | What was done | By |
|------|--------------|-----|
| Aug 28, 2026 | Initial site scan, service inventory, document list compiled | Corina |
| ______ | Read Multi-Year Plan and Budget | ______ |
| ______ | Read council minutes (recent 3 meetings) | ______ |
| ______ | Attend council meeting | ______ |
| ______ | Conversation with mB about collaboration shape | ______ |
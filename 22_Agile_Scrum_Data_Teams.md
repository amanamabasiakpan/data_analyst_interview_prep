# 22. Agile/Scrum for Data Teams

## Theoretical Questions

### Q1: What is Agile and why do data teams use it?
**A:** Agile is an iterative approach to project management and software development that delivers work in small, incremental cycles (sprints). Data teams use it because: data projects are inherently uncertain (you discover issues as you explore), requirements change as stakeholders learn from early deliverables, and iterative delivery provides faster value and feedback loops than waterfall planning.

### Q2: What is Scrum and what are its core components?
**A:** Scrum is an Agile framework with defined roles, events, and artifacts. Core components:
- **Roles:** Product Owner, Scrum Master, Development Team
- **Events:** Sprint (1-4 weeks), Sprint Planning, Daily Standup, Sprint Review, Sprint Retrospective
- **Artifacts:** Product Backlog, Sprint Backlog, Increment (working product)
- **Values:** Commitment, Courage, Focus, Openness, Respect

### Q3: What is a Sprint and how long should it be for data teams?
**A:** A time-boxed iteration (typically 1-4 weeks) where a team commits to completing a set of work. For data teams:
- **1 week:** Fast feedback, good for experimental/analytics work
- **2 weeks:** Standard — balances planning overhead with delivery rhythm
- **3-4 weeks:** Better for heavy data engineering (pipeline builds, model training)
**Recommendation:** Start with 2 weeks. Adjust based on your work type and stakeholder needs.

### Q4: What is a User Story and how do you write one for data work?
**A:** A user story describes a feature from the user's perspective. Format: "As a [role], I want [feature], so that [benefit]."

**Data examples:**
- "As a marketing manager, I want to see campaign ROI by channel, so that I can reallocate budget to the best performers."
- "As a data engineer, I want the sales pipeline to run incrementally, so that it completes in under 30 minutes."
- "As an analyst, I want customer churn predictions refreshed weekly, so that I can proactively reach out to at-risk customers."

### Q5: What is the Product Backlog and how is it different from a Sprint Backlog?
**A:**
- **Product Backlog:** The complete, prioritized list of all desired work (features, bugs, technical debt). Owned by the Product Owner. Evolves constantly.
- **Sprint Backlog:** The subset of product backlog items the team commits to completing in the current sprint. Owned by the Development Team. Fixed during the sprint (no new items added without negotiation).

### Q6: What is a Daily Standup and what should data teams discuss?
**A:** A 15-minute daily meeting where each team member answers:
1. What did I complete yesterday?
2. What will I work on today?
3. What blockers or dependencies do I have?

**Data team specifics:** Discuss data quality issues, pipeline failures, model performance changes, and stakeholder feedback. Not a status report to management — it's for the team to coordinate.

### Q7: What is a Sprint Review and how does it differ for data teams vs. software teams?
**A:** A meeting at sprint end where the team demonstrates completed work to stakeholders. **Data team differences:**
- Demo dashboards, reports, or analysis findings (not just working software)
- Show data visualizations and insights
- Discuss data quality improvements
- Gather feedback on metrics definitions and business logic
- Stakeholders may need education on data limitations

### Q8: What is a Sprint Retrospective and what makes it effective?
**A:** A team meeting (after Sprint Review) to reflect on the sprint and identify improvements. Structure:
1. What went well?
2. What could be improved?
3. What will we try next sprint?

**Effective retrospectives:** Safe environment (no blame), specific action items with owners, follow up on previous actions, and rotate facilitation. For data teams: discuss tooling, data access friction, and communication with stakeholders.

### Q9: What is a Definition of Done (DoD) for data projects?
**A:** The criteria that must be met for work to be considered complete. Data team DoD might include:
- Code is written and peer-reviewed
- Data quality tests pass (Great Expectations, dbt tests)
- Documentation updated (data dictionary, runbook)
- Dashboard/report is deployed and accessible
- Stakeholder has validated the output
- Monitoring and alerting are in place (for pipelines)
- No PII exposed in outputs

### Q10: What is Story Point estimation and how do data teams use it?
**A:** A relative estimation of effort/complexity using the Fibonacci sequence (1, 2, 3, 5, 8, 13, 21). Not time-based — relative to other stories. **Data team considerations:**
- Data exploration stories are harder to estimate (unknown unknowns)
- Consider adding a "spike" (time-boxed research) before estimating complex analysis
- Factor in: data availability, data quality issues, stakeholder clarity, and technical complexity
- Track velocity (points completed per sprint) for capacity planning

### Q11: What is a Spike and when do data teams use it?
**A:** A time-boxed research task (usually 1-3 days) to reduce uncertainty before committing to a larger story. Data team use cases:
- "Can we access this data source? What's the quality?"
- "Will this ML approach work with our data?"
- "How complex is joining these three datasets?"
- "What data privacy constraints apply to this analysis?"
Output: A decision or estimate, not a deliverable.

### Q12: What is the difference between a Product Owner and a Scrum Master?
**A:**
- **Product Owner:** Business-focused. Defines what to build, prioritizes the backlog, represents stakeholders, and accepts completed work. For data teams: often a senior analyst or data lead who understands business needs.
- **Scrum Master:** Process-focused. Facilitates Scrum events, removes blockers, protects the team from interruptions, and coaches on Agile practices. Not a project manager — serves the team, not manages them.

### Q13: What is Kanban and how does it differ from Scrum?
**A:** Kanban is a visual workflow management method (not a framework like Scrum). Key differences:
- **No sprints:** Continuous flow of work
- **No roles:** No prescribed Product Owner or Scrum Master
- **WIP limits:** Restrict how many items can be in each stage
- **Pull-based:** Team pulls new work when capacity opens
- **Less ceremony:** No mandatory standups, reviews, or retrospectives (though teams often do them)

**When to use Kanban:** Ad-hoc analytics requests, support/maintenance work, or when priorities change daily. **When to use Scrum:** Project-based work with defined deliverables.

### Q14: What is Work In Progress (WIP) limiting and why does it matter?
**A:** Setting a maximum number of items that can be in each workflow stage (e.g., "max 3 items in In Progress"). Matters because: context switching kills productivity (especially for deep analytical work), it surfaces bottlenecks, and it improves flow. If the "In Review" column is full, the team should help review instead of starting new work.

### Q15: What is a Burndown Chart and how is it used in data projects?
**A:** A chart showing remaining work over time. Ideal line slopes down to zero by sprint end. For data projects: tracks story points or number of tasks remaining. Helps identify if the team is on track to complete the sprint commitment. Flat lines indicate blockers; upward spikes mean new work was added.

### Q16: What is Technical Debt in data projects and how do you manage it?
**A:** Shortcuts or suboptimal solutions that accumulate "interest" over time. Data team examples:
- Hardcoded values instead of configuration tables
- Missing data quality tests
- Undocumented business logic in SQL
- Monolithic ETL scripts that are hard to maintain
- Manual data fixes instead of fixing the root cause

**Management:** Allocate 10-20% of each sprint to debt reduction, track debt items in the backlog, and prioritize based on risk and frequency of pain.

### Q17: What is a Data Contract and how does it relate to Agile?
**A:** An agreement between data producers and consumers defining: schema, data quality expectations, SLAs, and ownership. In Agile terms: it's a "contract" that both sides agree to, enabling independent development. Example: "The orders API will provide these fields, updated within 5 minutes, with 99.9% uptime." Reduces surprises and enables parallel sprints.

### Q18: What is the difference between a Project and a Product mindset in data?
**A:**
- **Project mindset:** Data work has a start and end date, then the team moves on. Risk: dashboards become unmaintained, pipelines break, knowledge is lost.
- **Product mindset:** Data assets (pipelines, dashboards, models) are ongoing products that need continuous improvement, monitoring, and ownership. Each data product has a product owner, roadmap, and lifecycle.
**Modern data teams are shifting to product mindset for critical data assets.**

### Q19: What is a Data Sprint and how does it differ from a regular development sprint?
**A:** A focused, time-boxed effort (often 1-2 weeks) to deliver a specific data outcome. Differences:
- More upfront data exploration and validation
- Stakeholder involvement is critical (they validate insights, not just features)
- Deliverable is often a presentation or report, not just code
- May include data cleaning as a significant portion of the work
- Success is measured by business impact, not just story points completed

### Q20: What is Stakeholder Management in Agile data teams?
**A:** The practice of engaging with business users who consume data products. Key practices:
- **Regular demos:** Show progress, not just final results
- **Backlog refinement:** Stakeholders help prioritize and clarify requirements
- **Feedback loops:** Short cycles mean stakeholders see work early and can correct course
- **Education:** Help stakeholders understand data limitations and what's feasible
- **Expectation setting:** Be transparent about data quality, timelines, and uncertainty

### Q21: What is a Data Quality Sprint?
**A:** A dedicated sprint focused entirely on improving data quality rather than new features. Activities: add data tests, fix known data issues, improve documentation, refactor brittle pipelines, and establish monitoring. Typically done quarterly or when data quality debt becomes critical. Prevents the "always building new things, never fixing old things" trap.

### Q22: What is the role of a Data Analyst in a Scrum team?
**A:** A data analyst in Scrum:
- Participates in sprint planning, standups, reviews, and retrospectives
- Owns analysis stories from backlog to delivery
- Collaborates with data engineers on data availability
- Validates data quality and business logic
- Presents findings in sprint reviews
- Contributes to team velocity and estimation
- May also act as a "data product owner" for analytics deliverables

### Q23: What is Continuous Integration/Continuous Deployment (CI/CD) for data teams?
**A:** Automating the testing and deployment of data code. Data CI/CD includes:
- **CI:** Automated testing of SQL (dbt tests), Python scripts, and data quality checks on every commit
- **CD:** Automated deployment of dbt models, pipeline code, and dashboard updates
- **Data diff:** Compare production vs. staging data to catch unexpected changes
Tools: GitHub Actions, GitLab CI, dbt Cloud, Prefect, Dagster.

### Q24: What is a DataOps practice and how does it relate to Agile?
**A:** DataOps applies DevOps principles (automation, monitoring, collaboration) to data pipelines. Agile + DataOps = fast, reliable data delivery. Practices: automated testing, version control for data pipelines, monitoring and alerting, CI/CD for data code, and cross-functional teams (data engineers, analysts, and business users together).

### Q25: What is a Sprint Goal and why is it important for data teams?
**A:** A single objective for the sprint that guides the team's focus. Example: "Deliver the customer churn dashboard with validated predictions." Important because: data sprints often have multiple related tasks (data extraction, cleaning, analysis, visualization), and the goal keeps the team aligned. It's okay if not every story is completed if the sprint goal is achieved.

### Q26: What is Backlog Refinement (Grooming) and how do data teams do it?
**A:** A regular session where the team reviews and prepares backlog items for upcoming sprints. Data team activities:
- Clarify business requirements and success criteria
- Identify data sources and assess availability/quality
- Break large analysis tasks into smaller stories
- Estimate story points
- Identify dependencies and blockers
- Remove or deprioritize stale items
**Frequency:** 1-2 hours per week for a 2-week sprint cycle.

### Q27: What is the difference between a Theme, Epic, and User Story?
**A:**
- **Theme:** A broad area of focus (e.g., "Customer Analytics")
- **Epic:** A large body of work that spans multiple sprints (e.g., "Build Customer 360 Dashboard")
- **User Story:** A small, deliverable piece of work that fits in one sprint (e.g., "As a marketer, I want to see customer lifetime value by segment")
Hierarchy: Theme → Epic → Story → Task

### Q28: What is a Data Team's "Definition of Ready"?
**A:** Criteria a backlog item must meet before the team can commit to it in a sprint. For data work:
- Business value is clear and stakeholder is identified
- Data source is accessible and documented
- Acceptance criteria are defined
- No major unknowns (or a spike has been completed)
- Dependencies are identified and manageable
- Story is estimated and fits in sprint capacity

### Q29: What is Velocity in Agile and how do you use it for data team planning?
**A:** The average number of story points a team completes per sprint. Used for:
- Sprint planning: "We completed 25 points last sprint, so let's commit to ~25 this sprint"
- Capacity planning: "With 2 team members on vacation, expect ~15 points"
- Forecasting: "At 25 points/sprint, the Epic will take ~4 sprints"
**Caveat:** Velocity is a team metric, not individual performance. It fluctuates — use rolling average of last 3-6 sprints.

### Q30: What is a Data Team's Retrospective focused on?
**A:** Beyond standard Scrum retro questions, data teams should discuss:
- Data quality issues that slowed us down
- Stakeholder communication effectiveness
- Tooling pain points (slow queries, broken pipelines)
- Knowledge sharing (did we document our work?)
- Technical debt that accumulated
- Data access and permissions friction
- Whether our metrics/definitions are still accurate

## Scenario-Based Questions

### Q31: A stakeholder keeps adding "urgent" requests mid-sprint. How do you handle it?
**A:**
1. **Protect the sprint:** "We're committed to our sprint goal. Adding new work risks not delivering what we promised."
2. **Assess impact:** If truly urgent and small (1-2 hours), swap it for the lowest-priority item in the sprint
3. **Negotiate:** "We can do this now, but X will be pushed to next sprint. Which is more important?"
4. **Buffer:** Keep 10-15% of sprint capacity unallocated for true emergencies
5. **Escalation:** If stakeholder insists, involve the Product Owner to reprioritize
6. **Process improvement:** In retrospective, discuss whether sprint planning missed critical items

### Q32: Your data pipeline broke on Friday evening. The team uses 2-week sprints. How does Agile handle this?
**A:**
1. **Immediate:** Fix the pipeline (production issues trump sprint work)
2. **Communication:** Notify stakeholders of data delays via agreed channels
3. **Post-incident:** Root cause analysis — was this a known risk? Missing monitoring?
4. **Sprint impact:** Track the unplanned work. If it consumed significant capacity, discuss with Product Owner whether to adjust sprint scope
5. **Prevention:** Add the fix to the backlog as a story for next sprint (monitoring, tests, or refactoring)
6. **Process:** Consider a "support rotation" where one team member handles interrupts each sprint

### Q33: A data analysis story is taking much longer than estimated because the data is messier than expected. What do you do?
**A:**
1. **Communicate early:** Raise the issue in standup, don't wait until sprint end
2. **Re-scope:** Can we deliver a simplified version that still meets the sprint goal?
3. **Spike outcome:** Document what was learned about data quality for future estimation
4. **Split the story:** "Data cleaning" becomes its own story; the analysis moves to next sprint
5. **Retro learning:** "We need a data quality assessment before estimating analysis stories"
6. **Prevention:** Add data profiling to the "Definition of Ready"

### Q34: How do you run a Sprint Review when the deliverable is a data insight, not a software feature?
**A:**
1. **Set context:** Remind stakeholders of the business question we set out to answer
2. **Show the data:** Walk through the analysis with visualizations (not just raw numbers)
3. **Tell the story:** "We found X, which means Y, so we recommend Z"
4. **Acknowledge limitations:** "This analysis covers Q1-Q3; Q4 data was incomplete"
5. **Invite questions:** Stakeholders may have domain knowledge that changes the interpretation
6. **Gather feedback:** What other cuts of the data would be useful? Did we answer the right question?
7. **Define next steps:** Is this done, or do we need follow-up analysis?

### Q35: Your team is struggling with estimation because data work is unpredictable. What do you do?
**A:**
1. **Use ranges:** "This is 5-8 points depending on data quality" — commit to the higher end
2. **Add buffer:** Multiply estimates by 1.5x for data exploration work
3. **Spikes first:** "We need a 3-point spike to understand the data before we can estimate the analysis"
4. **Track actuals:** Compare estimates vs. actual time spent to calibrate future estimates
5. **Bucket stories:** T-shirt sizes (S, M, L, XL) instead of precise points for ambiguous work
6. **Accept uncertainty:** Agile expects estimates to be wrong — the goal is relative sizing, not precision

### Q36: A data engineer and analyst disagree on the priority of a data quality fix vs. a new dashboard. How do you resolve it?
**A:**
1. **Frame by impact:** What's the business impact of bad data vs. delayed dashboard?
2. **Quantify:** "The data quality issue affects 3 dashboards and 2 reports used by 50 people daily"
3. **Product Owner decides:** They represent business value; escalate if no PO
4. **Compromise:** Can we do a quick fix (2 hours) now and the full refactor next sprint?
5. **Document:** Add both to backlog with clear business cases
6. **Prevention:** Establish a "data quality SLA" that defines when fixes take priority over new features

### Q37: How do you structure a data team that serves multiple business units with competing priorities?
**A:**
1. **Product Owners per domain:** Each business unit has a PO who prioritizes their requests
2. **Unified backlog:** All requests in one place, prioritized by a Chief Data Officer or lead
3. **Capacity allocation:** 40% Team A, 30% Team B, 30% platform work (adjust quarterly)
4. **Service Level Agreements:** Define response times by priority (P1 = same day, P2 = 1 week, etc.)
5. **Quarterly planning:** Business units present needs; team commits to high-level roadmap
6. **Sprint rotation:** Dedicate sprints to specific domains ("This sprint is Marketing-focused")

### Q38: Your team wants to adopt Agile but management expects detailed 6-month project plans. How do you reconcile?
**A:**
1. **Provide both:** High-level roadmap (6-month plan) + detailed sprint plans (2-week cycles)
2. **Roadmap = themes/epics:** "Q3: Customer Analytics Platform" not "Week 12: Build churn model"
3. **Embrace uncertainty:** "We'll refine estimates as we learn more — here's our current best guess with confidence intervals"
4. **Show value:** Demonstrate that iterative delivery catches issues early and delivers value faster
5. **Commit to cadence:** "We'll report progress every 2 weeks with working deliverables"
6. **Educate:** Share Agile success stories from other data teams

### Q39: A data model you built last sprint is already outdated because business rules changed. How do you prevent this?
**A:**
1. **Data contracts:** Formal agreement with business on definitions and change notification
2. **Versioning:** Version your models and dashboards; communicate changes
3. **Monitoring:** Alert when data patterns change unexpectedly (dbt tests, anomaly detection)
4. **Close stakeholder loop:** Include business users in sprint reviews so they see models early
5. **Config-driven:** Move business rules to configuration tables, not hardcoded logic
6. **Accept change:** In Agile, change is expected — build for adaptability, not permanence

### Q40: How do you handle "analysis paralysis" in a data sprint where the team keeps exploring instead of delivering?
**A:**
1. **Time-box exploration:** "We have 2 days for exploration, then we commit to a direction"
2. **Define MVP:** What's the minimum viable insight that answers the business question?
3. **Sprint goal focus:** "Our goal is a recommendation, not a PhD thesis"
4. **Stakeholder checkpoint:** Mid-sprint review with stakeholders to validate direction
5. **Document rabbit holes:** "We explored X and Y but they weren't fruitful — documented for future reference"
6. **Retro:** "Did we scope this story too broadly? How do we prevent this next time?"

### Q41: You're introducing Agile to a data team that's used to ad-hoc ticket-based work. What's your change management approach?
**A:**
1. **Start small:** One 2-week sprint with a small, committed team
2. **Pick the right pilot:** A project with clear scope and engaged stakeholders
3. **Training:** 2-hour workshop on Scrum basics, tailored for data work
4. **Tooling:** Set up Jira/Linear/Asana with data-specific workflows
5. **Champion:** Identify an enthusiastic team member to advocate for the process
6. **Iterate:** After 3 sprints, hold a retro on the process itself
7. **Don't force it:** If Kanban works better for your team's work style, use Kanban
8. **Measure:** Track delivery speed, stakeholder satisfaction, and team happiness

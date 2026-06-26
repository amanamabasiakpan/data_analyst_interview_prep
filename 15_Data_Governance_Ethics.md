# 15. Data Governance & Ethics

## Theoretical Questions

### Q1: What is data governance and why is it important?
**A:** Data governance is the framework of policies, processes, and standards that ensure data is managed as a valuable organizational asset. It covers data quality, security, privacy, ownership, and lifecycle management. It's important because poor governance leads to bad decisions, regulatory fines, security breaches, and loss of stakeholder trust.

### Q2: What are the key pillars of data governance?
**A:**
1. **Data Quality** — accuracy, completeness, timeliness, consistency
2. **Data Security** — access control, encryption, audit trails
3. **Data Privacy** — compliance with regulations, consent management
4. **Data Lifecycle Management** — creation, storage, archival, deletion
5. **Metadata Management** — documentation, data dictionaries, lineage
6. **Master Data Management** — single source of truth for critical entities

### Q3: What is GDPR and what are its key requirements?
**A:** General Data Protection Regulation (EU, 2018). Key requirements:
- **Lawful basis** for processing (consent, contract, legal obligation, etc.)
- **Data minimization** — collect only what's necessary
- **Right to access, rectification, erasure** ("right to be forgotten")
- **Data portability** — export data in machine-readable format
- **Privacy by design** — embed privacy into systems from the start
- **Breach notification** within 72 hours
- **DPIAs** (Data Protection Impact Assessments) for high-risk processing
- Fines up to 4% of global revenue or €20M

### Q4: What is the difference between data privacy and data security?
**A:**
- **Data Privacy:** Controls *who* can access data and *what* they can do with it. Focuses on rights, consent, and lawful use.
- **Data Security:** Protects data from unauthorized access, breaches, and corruption. Focuses on technical safeguards.
They overlap but are distinct: you can have secure data that violates privacy (collecting without consent) and private data that isn't secure (consented but unencrypted).

### Q5: What is data lineage and why does it matter?
**A:** Data lineage tracks the complete lifecycle of data — where it originated, how it transformed, where it moved, and how it's used downstream. It matters for: debugging data issues, impact analysis ("if I change this column, what breaks?"), compliance audits, and building trust in data products.

### Q6: What is a data catalog and what should it contain?
**A:** A data catalog is an inventory of all organizational data assets with metadata. It should contain: table/column definitions, data owners, update frequencies, quality scores, lineage, access policies, sample data, and business context. Examples: Alation, Collibra, DataHub, Amundsen.

### Q7: What is PII (Personally Identifiable Information) and how do you handle it?
**A:** PII is any data that can identify an individual: name, email, SSN, phone, IP address, biometric data, etc. Handling:
- Minimize collection (data minimization)
- Mask or tokenize in non-production environments
- Encrypt at rest and in transit
- Implement access controls (need-to-know)
- Audit access logs
- Anonymize or pseudonymize where possible
- Define retention periods and delete when no longer needed

### Q8: What is the difference between anonymization and pseudonymization?
**A:**
- **Anonymization:** Irreversibly removes identifying information. Data can no longer be linked to an individual. Not considered personal data under GDPR.
- **Pseudonymization:** Replaces identifiers with pseudonyms (tokens). The mapping is stored separately. Reduces risk but data is still personal data. Allows re-identification with the mapping key.

### Q9: What is data quality and how do you measure it?
**A:** Data quality is the fitness of data for its intended use. Dimensions:
- **Accuracy:** Does it reflect reality?
- **Completeness:** Are all required fields populated?
- **Consistency:** Is it the same across systems?
- **Timeliness:** Is it up-to-date?
- **Validity:** Does it conform to defined formats/rules?
- **Uniqueness:** Are there duplicates?
Measure with: profiling tools, automated checks, data quality scores, and business rule validation.

### Q10: What is a Data Protection Impact Assessment (DPIA)?
**A:** A DPIA is a systematic evaluation of privacy risks in a project that processes personal data. Required under GDPR for high-risk processing (profiling, large-scale processing, sensitive data). Steps: identify need → describe processing → assess necessity → identify risks → identify mitigations → sign off.

### Q11: What is data sovereignty and why is it important?
**A:** Data sovereignty is the principle that data is subject to the laws of the country where it's physically stored. Important because: EU data may need to stay in EU (GDPR), some countries ban cross-border transfers, and cloud provider region selection affects compliance. Example: storing EU customer data in AWS eu-west-1.

### Q12: What is the role of a Data Steward vs. a Data Owner?
**A:**
- **Data Owner:** Senior business leader accountable for data quality and decisions. Sets policies and approves access.
- **Data Steward:** Operational role that implements policies, monitors quality, resolves issues, and serves as the domain expert. Often a business analyst or subject matter expert.

### Q13: What are common data ethics principles?
**A:**
1. **Transparency** — explain how data is used
2. **Fairness** — avoid bias and discrimination in algorithms
3. **Accountability** — someone is responsible for data decisions
4. **Privacy** — respect individual rights
5. **Beneficence** — data use should create value, not harm
6. **Autonomy** — individuals should have control over their data

### Q14: What is algorithmic bias and how do you detect it?
**A:** Algorithmic bias is when AI/ML systems produce unfair outcomes for certain groups. Detection methods:
- Demographic parity: are positive outcomes equally distributed?
- Equal opportunity: are true positive rates equal across groups?
- Audit training data for underrepresentation
- Test model performance across subgroups
- Review feature importance for proxy variables (zip code → race)

### Q15: What is the "right to explanation" in automated decision-making?
**A:** Under GDPR Article 22, individuals have the right not to be subject to solely automated decisions with legal/significant effects (including profiling). They have the right to obtain human intervention, express their viewpoint, and contest the decision. This affects credit scoring, hiring algorithms, and insurance pricing.

### Q16: What is a data retention policy and why do you need one?
**A:** A policy defining how long data is kept and when it must be deleted. Needed for: GDPR compliance (don't keep data longer than necessary), reducing storage costs, minimizing breach impact, and legal discovery limits. Should specify retention periods by data type and deletion procedures.

### Q17: What is role-based access control (RBAC) in data governance?
**A:** Access permissions based on job roles rather than individual users. Example: "Sales Analyst" role can see customer revenue but not PII. "Data Engineer" role can see raw tables but not production customer data. Simplifies administration and reduces risk of excessive permissions.

### Q18: What is data masking and when do you use it?
**A:** Data masking replaces sensitive data with realistic but fake data. Used in: development/testing environments (devs don't need real SSNs), analytics sandboxes, vendor demos, and training. Types: static masking (permanent replacement), dynamic masking (real-time on query).

### Q19: What is a data breach response plan?
**A:** A documented procedure for when data is accessed unauthorizedly. Steps:
1. **Containment:** Stop the breach, preserve evidence
2. **Assessment:** What data, whose, how many affected?
3. **Notification:** Inform authorities (GDPR: 72 hours), inform affected individuals
4. **Remediation:** Fix the vulnerability, improve controls
5. **Review:** Post-incident analysis, update procedures

### Q20: What is metadata and why is it critical for governance?
**A:** Data about data. Types: technical (schema, data types), business (definitions, ownership), operational (update times, lineage), and usage (query frequency, access logs). Critical because you can't govern what you can't describe. Enables discovery, quality monitoring, and compliance.

### Q21: What is Master Data Management (MDM)?
**A:** MDM creates a single, authoritative source for critical business data (customers, products, suppliers). Problem it solves: the same customer exists in CRM, ERP, and billing with different names/addresses. MDM matches and merges records to create a "golden record." Tools: Informatica MDM, Talend, SAP MDM.

### Q22: What is data provenance?
**A:** The documented history of data — where it came from, who created it, when, and how it has been transformed. Essential for: scientific reproducibility, regulatory compliance, debugging, and trust. Often implemented via lineage tools and version control.

### Q23: What are the risks of data lakes without governance?
**A:** Data swamps — ungoverned data lakes become dumping grounds with: no metadata, duplicate/conflicting data, sensitive data exposed, no quality controls, inability to find data, and compliance violations. Governance requires: cataloging, quality checks, access controls, and lifecycle policies from day one.

### Q24: What is consent management in data privacy?
**A:** Tracking and honoring user consent for data processing. Requirements: consent must be freely given, specific, informed, and unambiguous. Must be as easy to withdraw as to give. Implementation: consent banners, preference centers, audit logs of consent changes, and data processing tied to consent records.

### Q25: What is the difference between data governance and data management?
**A:**
- **Data Governance:** The decision-making framework — who decides, what policies, what standards. Strategic.
- **Data Management:** The execution — implementing databases, ETL pipelines, backups, security controls. Operational.
Governance sets the rules; management follows them.

### Q26: What is a data classification scheme?
**A:** A framework for categorizing data by sensitivity. Common levels:
- **Public:** No restrictions (marketing materials)
- **Internal:** Company use only (org charts, policies)
- **Confidential:** Restricted access (financial data, strategy)
- **Restricted:** Highly sensitive (PII, health data, trade secrets)
Determines handling requirements: encryption, access controls, retention, and sharing rules.

### Q27: What is data ethics in AI/ML specifically?
**A:** Ethical considerations unique to ML: ensuring training data is representative, avoiding feedback loops that amplify bias, monitoring for model drift that affects fairness, being transparent about model limitations, and having human oversight for high-stakes decisions. Includes fairness, accountability, and transparency (FAT) principles.

### Q28: What is a data governance council?
**A:** A cross-functional committee that sets data policies and resolves conflicts. Members: CDO, legal/compliance, IT security, business unit heads, and data stewards. Meets monthly to review: data quality metrics, access requests, policy violations, and strategic data initiatives.

### Q29: What is the "privacy paradox"?
**A:** The gap between people's stated privacy concerns and their actual behavior (e.g., saying privacy matters but using weak passwords and accepting all cookies). For data professionals: it means privacy controls must be default and frictionless, not relying on user vigilance.

### Q30: What is data democratization and its governance challenges?
**A:** Making data accessible to non-technical users for self-service analytics. Challenges: ensuring users understand data context and limitations, preventing misinterpretation, maintaining security as access broadens, and providing training. Governance must balance accessibility with control.

## Scenario-Based Questions

### Q31: You discover a dataset contains PII that shouldn't be there. What's your action plan?
**A:**
1. **Immediate:** Restrict access to the dataset
2. **Assess:** Determine scope — how many records, how long exposed, who accessed it
3. **Contain:** Remove or mask PII; if needed, create a clean version
4. **Notify:** Inform data owner, security team, and legal if breach thresholds met
5. **Root cause:** How did PII get there? Fix the pipeline/source
6. **Prevent:** Add automated PII detection to data ingestion
7. **Document:** Incident report with timeline and remediation

### Q32: A business user asks for access to a dataset they don't normally need. How do you handle it?
**A:**
1. Ask: "What business question are you trying to answer?"
2. Check if a less sensitive dataset or aggregated view would suffice
3. Verify they have a legitimate business need and their manager's approval
4. Check if the data classification allows their role access
5. If approved: grant minimum necessary access, set expiration, log the request
6. If denied: explain why, offer alternatives, and escalate if they disagree

### Q33: Your ML model shows bias against a protected group. What do you do?
**A:**
1. **Verify:** Re-run analysis with different metrics and subgroups
2. **Investigate:** Check training data for underrepresentation or proxy variables
3. **Document:** Record the bias, its extent, and potential harm
4. **Mitigate:** Rebalance data, remove proxy features, apply fairness constraints, or use post-processing adjustment
5. **Communicate:** Inform stakeholders of the issue and remediation
6. **Monitor:** Add fairness metrics to ongoing model monitoring
7. **If unfixable:** Recommend not deploying or adding human review

### Q34: A vendor asks for a dump of your customer data for "analysis." How do you respond?
**A:**
1. "We have strict data governance policies that limit data sharing."
2. Ask what specific analysis they need and whether anonymized/aggregated data would suffice
3. Require a Data Processing Agreement (DPA) if any PII is involved
4. Ensure the vendor meets our security standards (SOC 2, encryption)
5. Propose they work in our secure environment instead of taking data out
6. Route through legal and procurement for approval
7. If denied: offer synthetic data or a joint analysis session

### Q35: You're migrating data to the cloud. What governance considerations apply?
**A:**
1. **Region selection:** Ensure data residency requirements are met
2. **Encryption:** At rest (AES-256) and in transit (TLS 1.2+)
3. **Access controls:** IAM roles, MFA, least privilege
4. **Audit logging:** CloudTrail, access logs, query logs
5. **Data classification:** Tag resources by sensitivity
6. **Vendor lock-in:** Ensure export capability and data portability
7. **Shared responsibility:** Understand what the cloud provider secures vs. what you secure
8. **DPIA:** If personal data is involved

### Q36: A dataset has no documented owner. How do you establish governance?
**A:**
1. Identify the business domain (e.g., customer data → Sales/Marketing)
2. Interview potential stakeholders to understand usage
3. Propose an owner based on who creates/uses the data most
4. Document the dataset in the catalog with provisional metadata
5. Set up a temporary stewardship assignment
6. Get formal ownership assignment from the data governance council
7. Backfill metadata and quality rules

### Q37: You need to delete user data under GDPR "right to be forgotten." What are the technical challenges?
**A:**
1. **Identification:** Find all instances across databases, backups, logs, and analytics systems
2. **Backups:** Can you delete from immutable backups? May need to mark for exclusion from restores
3. **Referential integrity:** Deleting a customer may break foreign key constraints
4. **Anonymized data:** If already anonymized, no deletion needed — but verify it's truly irreversible
5. **Audit logs:** You may need to retain deletion records for compliance
6. **Third parties:** Ensure vendors with copies also delete
7. **Testing:** Verify deletion with queries before confirming to the user

### Q38: Your company wants to use customer data for a new AI product. What governance steps are needed?
**A:**
1. **Legal review:** Is the current privacy policy sufficient? Do we need new consent?
2. **DPIA:** Assess risks of the new processing
3. **Consent:** If original consent didn't cover AI, obtain new consent
4. **Anonymization:** Can we achieve the goal with anonymized data?
5. **Ethics review:** Evaluate for bias, fairness, and potential harm
6. **Security:** Enhanced controls for AI training data
7. **Transparency:** How will we explain the AI's decisions to users?
8. **Documentation:** Maintain records of decision-making for regulators

### Q39: A data quality issue is causing incorrect reports but fixing it requires stopping a revenue-generating process. How do you decide?
**A:**
1. Quantify the impact of bad data: incorrect decisions, compliance risk, customer harm
2. Quantify the revenue impact of stopping the process
3. Propose a phased approach: fix going forward while flagging historical data
4. Escalate to data owner and business leadership with both scenarios
5. If stopping is required: negotiate the shortest possible window, communicate to stakeholders, and have rollback plan
6. Document the decision and its rationale

### Q40: You find out a colleague has been sharing login credentials to access sensitive data. What do you do?
**A:**
1. **Immediately:** Remind them this violates policy and ask them to stop
2. **Assess:** Determine what data was accessed and whether it constitutes a breach
3. **Report:** Inform security/HR as required by policy (most organizations mandate reporting)
4. **Technical:** Disable shared credentials, audit access logs, reset passwords
5. **Training:** This indicates a gap in security awareness training
6. **Process:** Review if the colleague lacked necessary access and fix the root cause
7. **Document:** Incident report for compliance records

### Q41: You're asked to build a dashboard that shows individual employee performance metrics. What privacy considerations apply?
**A:**
1. **Purpose limitation:** Is this within the scope of original data collection?
2. **Minimization:** Can the business need be met with team-level aggregates?
3. **Access:** Should managers see their team's data, or only HR?
4. **Transparency:** Are employees aware this data is being collected and used this way?
5. **Accuracy:** Is the data current and fairly represented?
6. **Retention:** How long will this data be kept?
7. **Legal:** Check employment law and works council requirements (especially in EU)
8. **Recommendation:** Start with anonymized benchmarks; individual data only if legally justified and properly governed

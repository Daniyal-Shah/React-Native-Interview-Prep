#  **Final Interview** with **Mobile Lead** and the **Director of Engineering**. 

## This round will likely assess your **technical depth, leadership potential, cultural fit, and problem-solving approach**.

Here’s a **step-by-step preparation guide**:

---

### **1. Understand the Interviewers’ Perspectives**
Since you’re meeting with a **Mobile Lead** and a **Director of Engineering**, expect a mix of:
- **Technical deep dive** (Mobile Lead)
- **System design / architecture** (Mobile Lead)
- **Behavioral & leadership questions** (Director of Engineering)
- **Product & business impact discussions** (Director of Engineering)

---

### **2. Technical Deep Dive (Mobile Lead - Eivonne Kwan)**
Since Eivonne is the **Mobile Lead**, she will likely assess:
- **Advanced Android/iOS concepts** (depending on the role)
- **Performance optimization**
- **App architecture (MVVM, Clean Architecture, etc.)**
- **Testing strategies (Unit tests, UI tests)**
- **Dependency injection (Dagger/Hilt, SwiftUI)**
- **Concurrency (Coroutines, RxJava, Swift async/await)**
- **Security best practices (SSL pinning, encryption, secure storage)**
- **Jetpack Compose/SwiftUI (if applicable)**

**Preparation Tips:**
- Review **Money Lion’s app** (download it, explore features, note tech choices).
- Be ready to explain past projects in depth (why you chose certain architectures/libraries).
- Practice explaining **complex topics simply** (e.g., "How does LiveData work under the hood?").
- Brush up on **memory management, threading, and caching strategies**.

---

### **3. System Design & Scalability (Mobile Lead / Director)**
You may get a **mobile system design question**, such as:
- "Design a **caching layer** for the Money Lion app."
- "How would you **optimize API calls** in a low-network scenario?"
- "Design an **offline-first** feature for transaction history."

**Preparation Tips:**
- Think about **scalability, offline support, and battery efficiency**.
- Know **common mobile design patterns** (Repository pattern, Singleton, etc.).
- Be ready to discuss **trade-offs** (e.g., SQLite vs. Realm, REST vs. GraphQL).

---

### **4. Behavioral & Leadership (Director of Engineering - Sanjev Moganadas)**
The **Director** will likely assess:
- **Problem-solving approach** (How do you debug a tricky crash?)
- **Team collaboration** (How do you handle disagreements with peers?)
- **Leadership & mentorship** (Have you mentored junior engineers?)
- **Business impact** (How do you prioritize features?)
- **Conflict resolution** (How do you handle pushback from Product Managers?)

**Preparation Tips:**
- Use the **STAR method** (Situation, Task, Action, Result).
- Prepare **2-3 strong leadership stories** (e.g., led a migration, improved app stability).
- Research **Money Lion’s mission** (financial inclusion, fast-paced fintech).

---

### **5. Mock Interview Questions**
#### **Technical (Mobile Lead)**
1. "Explain how you’d implement **biometric authentication** securely."
2. "How would you **reduce APK/IPA size**?"
3. "Discuss your approach to **handling API failures gracefully**."
4. "What’s your strategy for **writing testable code** in a large codebase?"

#### **Behavioral / Leadership (Director)**
1. "Tell me about a time you **disagreed with a technical decision**. How did you handle it?"
2. "How do you **stay updated with mobile trends**?"
3. "Describe a **high-pressure situation** and how you managed it."
4. "What would you **improve in the Money Lion app**?"

---

### **6. Ask Insightful Questions**
Prepare **3-5 smart questions**, such as:
- "What’s the biggest **technical challenge** the mobile team is facing right now?"
- "How does the mobile team **collaborate with backend/data teams**?"
- "What’s the **engineering culture** like at Money Lion?"
- "What are the **growth opportunities** for mobile engineers here?"

---

### **Summary**
- **Mobile Lead** → **Deep technical + system design**  
- **Director** → **Behavioral + leadership + business impact**  
- **Prepare stories, review Money Lion’s app, and practice system design.**  

---

**Massive list of questions for (Behavioral + Leadership + Business Impact)** 

---

# **1. Problem-Solving (Debugging, Decision-Making Under Pressure)**  

### **Q1: Describe a time you debugged a critical production issue. How did you handle it?**  
**Answer:**  
- **Situation:** After a release, 30% of users crashed on the login screen.  
- **Action:** Rolled back, analyzed Sentry logs, found a race condition in token validation.  
- **Result:** Fixed in 2 hours, added test coverage, reduced crashes by 95%.  

### **Q2: How do you troubleshoot a UI freeze in a React Native app?**  
**Answer:**  
1. Profile with **Flipper/React DevTools**.  
2. Check for **main-thread blocking** (e.g., synchronous storage calls).  
3. Optimize **FlatList rendering** (e.g., `maxToRenderPerBatch`).  

### **Q3: A live app suddenly starts crashing on Android only. What’s your process?**  
**Answer:**  
1. Check **Firebase Crashlytics** for stack traces.  
2. Reproduce on an **emulator with the same OS version**.  
3. Identify if it’s a **native module compatibility issue** (e.g., Kotlin version mismatch).  

### **Q4: How do you handle a last-minute API change that breaks your app?**  
**Answer:**  
1. **Implement a feature flag** to toggle between old/new API.  
2. **Graceful degradation** (show cached data + error message).  
3. **Communicate with backend team** to align on versioning.  

### **Q5: Your app’s startup time increases by 3 seconds. How do you fix it?**  
**Answer:**  
1. Audit **native dependencies** (e.g., heavy SDKs like Firebase).  
2. **Lazy-load non-critical modules**.  
3. Use **Hermes** + precompile JavaScript.  

### **Q6: How do you decide whether to roll back or hotfix a bug?**  
**Answer:**  
- **Roll back:** Data corruption, security issues, >15% crash rate.  
- **Hotfix:** Minor UI bugs, backward-compatible API fixes.  

### **Q7: Describe a time you had to make a tough technical decision under pressure.**  
**Answer (STAR):**  
- **Situation:** Payment processing failed during Black Friday.  
- **Action:** Switched to a fallback payment gateway, disabled non-critical features.  
- **Result:** Recovered 90% of transactions, fixed root cause post-event.  

### **Q8: How do you handle conflicting feedback from QA and Product teams?**  
**Answer:**  
1. **Reproduce the issue** to validate QA’s report.  
2. **Align with Product** on business priority (e.g., "Is this blocker for launch?").  
3. **Compromise** (e.g., ship with a known minor bug if deadline is critical).  

### **Q9: Your app is draining battery. How do you diagnose and fix it?**  
**Answer:**  
1. Use **Xcode Energy Logs/Android Battery Historian**.  
2. Check for **unnecessary background processes** (e.g., GPS polling).  
3. Optimize **network calls** (batch requests, reduce polling).  

### **Q10: How do you prevent regression bugs in a fast-moving codebase?**  
**Answer:**  
- **Automated tests:** Unit, integration, E2E (Detox).  
- **Feature flags** for risky changes.  
- **Staged rollouts** (1% → 10% → 100%).  

### **Q11: A dependency you rely on is deprecated. What’s your plan?**  
**Answer:**  
1. **Assess impact** (Is there a fork? Can you replace it?).  
2. **Phase out gradually** (e.g., wrapper pattern).  
3. **Contribute to OSS alternatives** if none exist.  

### **Q12: How do you debug a memory leak in React Native?**  
**Answer:**  
1. Use **Chrome DevTools Memory Profiler**.  
2. Check for **unsubscribed event listeners**.  
3. Audit **native modules** (e.g., dangling Java/Kotlin references).  

### **Q13: Your team disagrees on two technical approaches. How do you decide?**  
**Answer:**  
1. **List pros/cons** (performance, maintainability, deadlines).  
2. **Spike both solutions** (1-day prototype).  
3. **Delegate final call** to the engineer owning the feature.  

### **Q14: How do you handle a security vulnerability reported in a library you use?**  
**Answer:**  
1. **Check CVE severity** (e.g., CVSS score).  
2. **Patch or fork immediately** if critical.  
3. **Notify stakeholders** (legal, compliance).  

### **Q15: Describe a time you had to sacrifice perfection for speed.**  
**Answer (STAR):**  
- **Situation:** Needed to ship a regulatory feature in 1 week.  
- **Action:** Built a **minimal compliant version**, deferred UI polish.  
- **Result:** Met deadline, iterated post-launch.  

### **Q16: How do you ensure your app works offline?**  
**Answer:**  
1. **Cache API responses** (Redux Persist, WatermelonDB).  
2. **Queue mutations** for sync when online.  
3. **Test edge cases** (e.g., conflict resolution).  

### **Q17: Your app’s size exceeds Google Play’s limit. How do you reduce it?**  
**Answer:**  
1. **Analyze APK with Android Studio**.  
2. **Remove unused assets** (e.g., x86 libs, duplicate images).  
3. **Enable ProGuard/R8**.  

### **Q18: How do you handle a failed deployment?**  
**Answer:**  
1. **Automated rollback** (CI/CD pipeline).  
2. **Post-mortem** (Why did tests miss this?).  
3. **Add safeguards** (e.g., canary deployments).  

### **Q19: How do you stay calm during a high-severity outage?**  
**Answer:**  
1. **Follow incident playbook** (who owns what?).  
2. **Communicate proactively** (status page, Slack updates).  
3. **Avoid blame** ("How do we fix this?" vs. "Who broke this?").  

### **Q20: How do you validate a fix for a complex bug?**  
**Answer:**  
1. **Reproduce locally** (mimic user environment).  
2. **Add regression tests**.  
3. **Monitor logs** post-deploy (Sentry, Crashlytics).  

---

# **2. Leadership (Mentorship, Driving Best Practices)**  

### **Q1: How do you onboard a new engineer to your React Native codebase?**  
**Answer:**  
1. **Pair on a small bug fix** (learn by doing).  
2. **Document key patterns** (e.g., "How we handle auth").  
3. **Assign a mentor** for the first 30 days.  

### **Q2: Describe a time you improved your team’s productivity.**  
**Answer (STAR):**  
- **Situation:** PR reviews took 5+ days.  
- **Action:** Enforced **smaller PRs**, added **review checklist**.  
- **Result:** Reviews completed in <1 day.  

### **Q3: How do you handle a disengaged team member?**  
**Answer:**  
1. **Private 1:1** ("How can I support you?").  
2. **Align work with their interests** (e.g., R&D projects).  
3. **Escalate if no improvement** (HR/manager).  

### **Q4: How do you advocate for tech debt reduction?**  
**Answer:**  
1. **Quantify risks** (e.g., "This will cause 20% more bugs").  
2. **Propose a sprint** (e.g., "10% capacity per sprint").  
3. **Link to business goals** (e.g., "Faster feature delivery").  

### **Q5: How do you mentor someone struggling with React Native performance?**  
**Answer:**  
1. **Profile together** (show Flipper/React DevTools).  
2. **Share resources** (e.g., React Native optimization talks).  
3. **Review their PRs** with performance in mind.  

### **Q6: How do you foster collaboration between mobile and backend teams?**  
**Answer:**  
1. **Joint planning sessions** (e.g., API contract reviews).  
2. **Shared Slack channels** for quick questions.  
3. **Cross-team demos** (show how features are used).  

### **Q7: Describe a time you had to say no to a stakeholder. How did you handle it?**  
**Answer (STAR):**  
- **Situation:** PM wanted a feature in 1 week that required a major refactor.  
- **Action:** Proposed a **phased approach** with core functionality first.  
- **Result:** Shipped MVP on time, added polish later.  

### **Q8: How do you ensure your team follows best practices?**  
**Answer:**  
1. **Code reviews** (enforce linting, testing).  
2. **Documentation** ("How We Write React Native").  
3. **Reward good behavior** (shoutouts in sprint retros).  

### **Q9: How do you handle a disagreement with another tech lead?**  
**Answer:**  
1. **Data-driven discussion** (benchmarks, user feedback).  
2. **Escalate to a neutral third party** if deadlocked.  
3. **Commit to a trial period** for one approach.  

### **Q10: How do you keep your team motivated during crunch time?**  
**Answer:**  
1. **Transparency** ("This is why we’re working late").  
2. **Small wins** (celebrate daily progress).  
3. **Post-crunch rewards** (days off, team dinner).  

### **Q11: Describe a time you improved code quality in your team.**  
**Answer (STAR):**  
- **Situation:** 40% of bugs were due to missing tests.  
- **Action:** Enforced **100% test coverage for critical paths**.  
- **Result:** Bug reports dropped by 60%.  

### **Q12: How do you delegate tasks effectively?**  
**Answer:**  
1. **Match tasks to strengths** (e.g., animations → UI-focused dev).  
2. **Set clear expectations** (deadlines, quality standards).  
3. **Provide autonomy** ("You own this; ask if stuck").  

### **Q13: How do you handle a underperforming engineer?**  
**Answer:**  
1. **Private feedback** ("I’ve noticed X; how can I help?").  
2. **Performance plan** (30-day goals, weekly check-ins).  
3. **PIP if no improvement** (HR process).  

### **Q14: How do you encourage innovation in your team?**  
**Answer:**  
1. **Hackathons** (e.g., "Improve app launch time").  
2. **20% time** (work on passion projects).  
3. **Show impact** ("Your idea saved $X").  

### **Q15: How do you align your team with company goals?**  
**Answer:**  
1. **Connect dots** ("This refactor enables feature Y in Q3").  
2. **Metrics that matter** (e.g., "Reduce login time → more conversions").  
3. **Celebrate business wins** (e.g., "App store feature → team功劳").  

### **Q16: How do you handle a missed deadline?**  
**Answer:**  
1. **Root cause analysis** (Was it scope creep? Underestimation?).  
2. **Adjust plan** (de-scope, extend timeline).  
3. **Communicate early** (no surprises).  

### **Q17: How do you build trust with your team?**  
**Answer:**  
1. **Follow through** (do what you say).  
2. **Admit mistakes** ("I was wrong about X").  
3. **Protect their time** (say no to unnecessary meetings).  

### **Q18: How do you handle a toxic team member?**  
**Answer:**  
1. **Private conversation** ("Your tone in meetings is demoralizing").  
2. **HR involvement** if behavior continues.  
3. **Protect the team** (remove them if needed).  

### **Q19: How do you stay updated with React Native trends?**  
**Answer:**  
1. **RNS Newsletter, React Native Radio podcast**.  
2. **Experiment with new libraries** (e.g., Reanimated 3).  
3. **Team learning sessions** (monthly tech talks).  

### **Q20: How do you measure your team’s success?**  
**Answer:**  
1. **Engineering metrics** (cycle time, bug rate).  
2. **Business impact** (feature adoption, revenue).  
3. **Team health** (retention, survey feedback).  

---

# **3. Business Impact (Fintech Knowledge, Prioritization)**  

### **Q1: How do you prioritize features in a fintech app?**  
**Answer:**  
1. **Regulatory/compliance first** (e.g., fraud detection).  
2. **Revenue drivers** (e.g., faster loan approvals).  
3. **User pain points** (e.g., login friction).  

### **Q2: How would you improve MoneyLion’s app?**  
**Answer:**  
1. **Faster onboarding** (pre-fill data from linked accounts).  
2. **Personalized financial tips** (AI-driven insights).  
3. **Better offline support** (cache transaction history).  

### **Q3: How do you balance speed vs. stability in fintech?**  
**Answer:**  
1. **Automated testing** (100% coverage for money flows).  
2. **Feature flags** for risky changes.  
3. **Staged rollouts** (1% → 10% → 100%).  

### **Q4: How do you handle a compliance requirement that slows development?**  
**Answer:**  
1. **Educate team** ("This avoids $10M fines").  
2. **Streamline process** (e.g., pre-approved UI components).  
3. **Advocate for tools** (e.g., automated compliance checks).  

### **Q5: How do you measure the success of a feature?**  
**Answer:**  
1. **Business metrics** (e.g., loan applications ↑ 20%).  
2. **Engagement** (DAU, session length).  
3. **User feedback** (NPS, app store reviews).  

### **Q6: How do you justify tech debt reduction to executives?**  
**Answer:**  
1. **Cost of inaction** ("This causes 5 support tickets/day").  
2. **ROI** ("Fixing this will save 100 engineering hours/year").  
3. **Risk** ("This could fail during Black Friday").  

### **Q7: How do you handle a feature request that conflicts with technical feasibility?**  
**Answer:**  
1. **Explain constraints** ("This would take 3 months due to X").  
2. **Propose alternatives** ("Could we do Y instead?").  
3. **Align on MVP** ("What’s the core user need?").  

### **Q8: How do you ensure your app meets fintech security standards?**  
**Answer:**  
1. **Pen testing** (annual third-party audits).  
2. **Secure coding practices** (OWASP guidelines).  
3. **Monitor threats** (Sentry for runtime exploits).  

### **Q9: How do you handle a data breach scenario?**  
**Answer:**  
1. **Contain** (disable compromised endpoints).  
2. **Notify** (legal, users, regulators).  
3. **Prevent recurrence** (add MFA, audit logs).  

### **Q10: How do you decide to build vs. buy a solution?**  
**Answer:**  
1. **Core competency?** (e.g., build custom fraud detection).  
2. **Maintenance cost** (e.g., buy Auth0 vs. building auth).  
3. **Time-to-market** (buy if deadline is tight).  

### **Q11: How do you align mobile strategy with business goals?**  
**Answer:**  
1. **Understand KPIs** (e.g., "Increase premium signups").  
2. **Mobile-first features** (e.g., push notifications for offers).  
3. **Measure impact** (e.g., "New flow increased conversions by 15%").  

### **Q12: How do you handle a regulatory change (e.g., GDPR)?**  
**Answer:**  
1. **Assess impact** (what data flows need updating?).  
2. **Cross-functional team** (legal, product, eng).  
3. **Document compliance** (audit trails).  

### **Q13: How do you reduce customer support tickets for your app?**  
**Answer:**  
1. **Analyze top issues** (e.g., password resets).  
2. **In-app self-help** (FAQs, chatbot).  
3. **Proactive fixes** (e.g., simplify password rules).  

### **Q14: How do you handle pushback from business teams on tech priorities?**  
**Answer:**  
1. **Speak their language** ("This reduces refunds by X%").  
2. **Trade-offs** ("We can do X now if we delay Y").  
3. **Data-driven** (A/B test results).  

### **Q15: How do you ensure your app is accessible?**  
**Answer:**  
1. **Audit with VoiceOver/TalkBack**.  
2. **Follow WCAG guidelines** (contrast ratios, labels).  
3. **User testing** (include disabled testers).  

### **Q16: How do you handle a failed product launch?**  
**Answer:**  
1. **Post-mortem** (why did users churn?).  
2. **Pivot** (e.g., repurpose tech for another use case).  
3. **Communicate lessons** (company-wide memo).  

### **Q17: How do you evaluate third-party SDKs for fintech?**  
**Answer:**  
1. **Security** (SOC 2 compliance, encryption).  
2. **Performance** (size, battery impact).  
3. **Vendor stability** (are they VC-backed? Profitable?).  

### **Q18: How do you handle customer data privacy requests?**  
**Answer:**  
1. **Automate deletion/export** (GDPR compliance).  
2. **Audit trails** (who accessed what?).  
3. **Encrypt PII at rest/in transit**.  

### **Q19: How do you reduce payment processing failures?**  
**Answer:**  
1. **Fallback gateways** (e.g., Stripe → PayPal).  
2. **Pre-validate cards** (Luhn algorithm, BIN checks).  
3. **Retry logic** (exponential backoff).  

### **Q20: How do you justify mobile engineering headcount to executives?**  
**Answer:**  
1. **Opportunity cost** ("We’re losing $X due to slow feature delivery").  
2. **Competitive edge** ("Chime has 10 mobile engineers").  
3. **Risk** ("Bus factor = 2 for our core features").  

---

# **4. Culture Fit (Collaboration, Adaptability)**  

### **Q1: Describe a time you worked with a difficult teammate.**  
**Answer (STAR):**  
- **Situation:** A designer insisted on unrealistic animations.  
- **Action:** Proposed a simplified version, showed performance metrics.  
- **Result:** Shipped a smoother compromise.  

### **Q2: How do you handle feedback from junior engineers?**  
**Answer:**  
1. **Listen actively** (avoid defensiveness).  
2. **Acknowledge good points** ("I hadn’t considered X").  
3. **Explain decisions** ("We can’t do Y because of Z").  

### **Q3: How do you adapt to changing priorities?**  
**Answer (STAR):**  
- **Situation:** Company pivoted from growth to profitability.  
- **Action:** Reprioritized tech debt over new features.  
- **Result:** Reduced cloud costs by 30%.  

### **Q4: Describe your ideal engineering culture.**  
**Answer:**  
- **Psychological safety** (no blame, learn from failures).  
- **Ownership** (you build it, you run it).  
- **Balanced pace** (sustainable crunch).  

### **Q5: How do you handle a manager you disagree with?**  
**Answer:**  
1. **Data-driven case** ("Here’s why X might work better").  
2. **Respect chain of command** (commit once decided).  
3. **Disagree and commit** (no sabotage).  

### **Q6: How do you contribute to a positive team environment?**  
**Answer:**  
1. **Shout out wins** (Slack kudos).  
2. **Onboard new hires** (lunch buddy system).  
3. **Organize tech talks**.  

### **Q7: How do you handle burnout in yourself or your team?**  
**Answer:**  
1. **Encourage time off** (no "hero culture").  
2. **Rotate on-call fairly**.  
3. **Monitor workloads** (use sprint capacity planning).  

### **Q8: How do you handle a missed deadline due to external factors?**  
**Answer:**  
1. **Transparent communication** ("API delays → we need 2 more weeks").  
2. **Mitigate risks** (ship partial features).  
3. **Post-mortem** (how to avoid next time?).  

### **Q9: How do you stay productive in a remote team?**  
**Answer:**  
1. **Async communication** (Loom videos, docs).  
2. **Focus blocks** (calendar "no meeting" times).  
3. **Over-communicate** (daily standup updates).  

### **Q10: How do you handle a conflict between engineers?**  
**Answer:**  
1. **Private mediation** (listen to both sides).  
2. **Focus on facts** ("What’s best for the user?").  
3. **Escalate if needed** (HR for personal attacks).  

### **Q11: Describe a time you went above and beyond for your team.**  
**Answer (STAR):**  
- **Situation:** Teammate fell ill before a launch.  
- **Action:** Worked nights to cover their tasks.  
- **Result:** Launched on time, later rotated time off for them.  

### **Q12: How do you handle ambiguity in a project?**  
**Answer:**  
1. **Ask clarifying questions** ("What’s the success metric?").  
2. **Prototype fast** (spike solutions).  
3. **Document assumptions**.  

### **Q13: How do you build rapport with non-technical stakeholders?**  
**Answer:**  
1. **Avoid jargon** ("This is like a highway toll system").  
2. **Show demos** (visuals > specs).  
3. **Understand their goals** ("You care about reducing refunds").  

### **Q14: How do you handle a project where requirements keep changing?**  
**Answer:**  
1. **Push back politely** ("What’s driving this change?").  
2. **Iterative delivery** (ship small, gather feedback).  
3. **Update estimates** ("This will now take 3 more weeks").  

### **Q15: How do you handle a culture clash when joining a new company?**  
**Answer:**  
1. **Observe first** (learn unwritten rules).  
2. **Ask questions** ("Why do we do X this way?").  
3. **Propose changes gradually**.  

### **Q16: How do you handle a teammate who takes credit for your work?**  
**Answer:**  
1. **Private conversation** ("I noticed you presented my idea as yours").  
2. **Document contributions** (Git commits, design docs).  
3. **Escalate if persistent** (manager/HR).  

### **Q17: How do you deal with imposter syndrome?**  
**Answer:**  
1. **Track achievements** (weekly "wins" list).  
2. **Mentor others** (teaching confirms expertise).  
3. **Talk openly** (most feel this sometimes).  

### **Q18: How do you handle a disagreement about coding standards?**  
**Answer:**  
1. **Refer to style guide** (if it exists).  
2. **Benchmark options** (performance, readability).  
3. **Delegate to PR owner** (their call if minor).  

### **Q19: How do you encourage diversity in your team?**  
**Answer:**  
1. **Blind resume reviews**.  
2. **Outreach** (speak at HBCUs, women in tech events).  
3. **Inclusive interviews** (structured questions).  

### **Q20: Why do you want to work at MoneyLion?**  
**Answer:**  
1. **Mission alignment** ("Democratizing finance excites me").  
2. **Tech challenges** ("Scale + security in fintech is complex").  
3. **Growth opportunities** ("I want to lead mobile initiatives").  

---

### **Tips:**  
- **Use STAR** for behavioral questions.  
- **Link answers to company goals** (fintech, scaling).  
- **Ask smart questions** (see Q20 in each section).  

---

# **Massive list of questions for (Technical depth, System Design, Performance, Leadership, and Fintech Challenges)**.  

---

# **1. Core React Native (10+ Questions)**  

### **Q1: Explain the React Native new architecture (Fabric, TurboModules, JSI).**  
**Answer:**  
- **Fabric:** Replaces Shadow Thread with a C++ layer for synchronous UI updates.  
- **TurboModules:** Allows direct JS-to-Native method calls (no bridge).  
- **JSI (JavaScript Interface):** Enables JS to hold references to native objects.  

### **Q2: How does `useMemo` differ from `useCallback`?**  
**Answer:**  
- `useMemo`: Memoizes **values** (e.g., computed data).  
- `useCallback`: Memoizes **functions** (e.g., event handlers).  

### **Q3: What are React Native’s limitations compared to native apps?**  
**Answer:**  
- Performance bottlenecks (bridge latency).  
- Limited access to cutting-edge native APIs.  
- Larger app size due to JS runtime.  

### **Q4: How do you handle native dependencies in React Native?**  
**Answer:**  
- Use `autolinking` for native modules.  
- Manually link if needed (rare in RN ≥ 0.60).  
- Prefer well-maintained libraries (e.g., `react-native-svg`).  

### **Q5: Explain the difference between `flexDirection: 'row'` and `'column'`.**  
**Answer:**  
- `row`: Children align horizontally (left-to-right).  
- `column`: Children stack vertically (top-to-bottom).  

### **Q6: How do you debug a `undefined is not a function` error in React Native?**  
**Answer:**  
1. Check Metro bundler logs for syntax errors.  
2. Ensure proper imports (`import { View } from 'react-native'`).  
3. Verify function existence in the scope.  

### **Q7: What’s the purpose of `keyExtractor` in `FlatList`?**  
**Answer:**  
Uniquely identifies list items for efficient re-rendering:  
```jsx
<FlatList  
  keyExtractor={(item) => item.id}  
  data={data}  
  renderItem={...}  
/>
```  

### **Q8: How do you handle deep linking in React Native?**  
**Answer:**  
- Use `react-native-deep-linking`:  
  ```jsx
  DeepLinking.addRoute('/profile/:id', (response) => { ... });
  ```  
- Configure `AndroidManifest.xml` and `Info.plist`.  

### **Q9: What’s the difference between `margin` and `padding`?**  
**Answer:**  
- `margin`: Space **outside** a component.  
- `padding`: Space **inside** a component.  

### **Q10: How do you test React Native components?**  
**Answer:**  
- **Unit tests:** Jest + `react-test-renderer`.  
- **E2E tests:** Detox/Appium.  
- **Snapshot testing:** Catch UI regressions.  

---

# **2. Performance Optimization (10+ Questions)**  

### **Q1: How do you optimize React Native app startup time?**  
**Answer:**  
- Enable **Hermes** engine.  
- Lazy-load screens with `React.lazy`.  
- Initialize heavy SDKs after launch.  

### **Q2: Your app freezes during navigation. How do you fix it?**  
**Answer:**  
- Use `React Navigation’s` native stack (`@react-navigation/native-stack`).  
- Avoid heavy computations in `useEffect` during transitions.  

### **Q3: How do you reduce re-renders in a large list?**  
**Answer:**  
- Memoize components with `React.memo`.  
- Use `getItemLayout` for fixed-height items.  
- Optimize `extraData` in `FlatList`.  

### **Q4: What tools do you use to profile React Native performance?**  
**Answer:**  
- **Flipper** (React DevTools, network inspector).  
- **Xcode Instruments** (iOS) / **Android Profiler** (Android).  
- `console.log` with timestamps.  

### **Q5: How do you handle memory leaks in React Native?**  
**Answer:**  
- Clean up subscriptions in `useEffect`:  
  ```jsx
  useEffect(() => {
    const sub = API.subscribe();
    return () => sub.unsubscribe(); // Cleanup
  }, []);
  ```  

### **Q6: How do you improve animation smoothness?**  
**Answer:**  
- Use `react-native-reanimated` (runs on UI thread).  
- Avoid `setState` during animations.  
- Prefer `transform` over layout changes.  

### **Q7: Your app’s bundle size is too large. How do you reduce it?**  
**Answer:**  
- Enable **ProGuard** (Android) / **Strip debug symbols** (iOS).  
- Use `react-native-bundle-visualizer` to audit dependencies.  
- Dynamically load heavy libraries.  

### **Q8: How do you handle slow API responses in your app?**  
**Answer:**  
- Show **skeleton loaders**.  
- Implement **client-side caching** (Redux Persist, MMKV).  
- Use **optimistic UI updates**.  

### **Q9: How do you debug a crash that only happens in production?**  
**Answer:**  
- Use **Sentry** or **Crashlytics** for stack traces.  
- Reproduce with **mocked production data**.  
- Check for **native dependency conflicts**.  

### **Q10: How do you ensure your app works on low-end devices?**  
**Answer:**  
- Test on **Android Go** devices.  
- Limit memory usage (avoid large lists in state).  
- Use **Hermes** for faster JS execution.  

---

# **3. System Design (10+ Questions)**  

### **Q1: Design an offline-first banking app.**  
**Answer:**  
- **Local DB:** SQLite/WatermelonDB for transactions.  
- **Sync:** Queue mutations, retry when online.  
- **Conflict resolution:** Timestamp-based merging.  

### **Q2: How would you implement biometric authentication?**  
**Answer:**  
- Use `react-native-keychain` (secure enclave).  
- **Fallback:** PIN/2FA.  
- **Rate limiting** to prevent brute force.  

### **Q3: Design a feature flag system for A/B testing.**  
**Answer:**  
- **Remote config:** Firebase, LaunchDarkly.  
- **Fallback:** Default values if API fails.  
- **Audit logs** for compliance.  

### **Q4: How would you structure a large React Native codebase?**  
**Answer:**  
```
src/  
  ├── features/      # Feature modules  
  ├── core/         # Hooks, utils  
  ├── navigation/   # Routing  
  └── assets/       # Fonts, images  
```  

### **Q5: How do you secure API keys in React Native?**  
**Answer:**  
- **Android:** `gradle.properties` + ProGuard.  
- **iOS:** `Info.plist` + obfuscation.  
- **Runtime:** Fetch from backend.  

### **Q6: Design a real-time stock price feature.**  
**Answer:**  
- **WebSockets** for live updates.  
- **Throttle UI updates** (1s intervals).  
- **Fallback:** Polling if WS fails.  

### **Q7: How would you implement a dark/light mode toggle?**  
**Answer:**  
- Use **React Context** for theme state.  
- Store preference in `AsyncStorage`.  
- Dynamically update `StyleSheet`.  

### **Q8: Design a push notification system.**  
**Answer:**  
- **Backend:** Firebase Cloud Messaging (FCM).  
- **Local handling:** `react-native-push-notification`.  
- **Deep linking** for navigation.  

### **Q9: How would you handle app updates without store releases?**  
**Answer:**  
- **CodePush** for JS updates.  
- **Feature flags** to toggle new logic.  
- **Fallback:** Force upgrade if critical.  

### **Q10: Design a scalable state management solution.**  
**Answer:**  
- **Redux:** For global state (e.g., auth).  
- **React Query:** For server state (API caching).  
- **Zustand:** For lightweight shared state.  

---

# **4. Leadership & Best Practices (10+ Questions)**  

### **Q1: How do you enforce code quality in a team?**  
**Answer:**  
- **Pre-commit hooks** (ESLint, Prettier).  
- **100% PR reviews**.  
- **Automated tests** (Jest, Detox).  

### **Q2: How do you mentor junior engineers?**  
**Answer:**  
- **Pair programming** on bugs.  
- **Weekly 1:1s** for feedback.  
- **Curated resources** (React Native docs).  

### **Q3: How do you handle tech debt vs. feature requests?**  
**Answer:**  
- **Track debt in a backlog**.  
- **Allocate 20% sprint capacity** for refactors.  
- **Link debt to business impact**.  

### **Q4: How do you review a PR effectively?**  
**Answer:**  
1. **Functionality** (Does it work?).  
2. **Performance** (Memory leaks?).  
3. **Readability** (Clear variable names?).  

### **Q5: How do you stay updated with React Native trends?**  
**Answer:**  
- **RNS Newsletter, React Native Radio**.  
- **Experiment with new libraries**.  
- **Contribute to OSS**.  

### **Q6: How do you handle disagreements in tech stack choices?**  
**Answer:**  
- **Data-driven debate** (benchmarks, case studies).  
- **Pilot projects** to test options.  
- **Delegate final call** to domain owners.  

### **Q7: How do you onboard new engineers?**  
**Answer:**  
- **Pair on a small bug fix**.  
- **Document key patterns** (e.g., "How we handle auth").  
- **Assign a mentor**.  

### **Q8: How do you advocate for testing in your team?**  
**Answer:**  
- **Show ROI** ("Tests saved 40 QA hours last sprint").  
- **Start small** (critical paths first).  
- **Automate in CI/CD**.  

### **Q9: How do you handle a legacy React Native codebase?**  
**Answer:**  
- **Incremental refactors** (feature flags).  
- **Document tribal knowledge**.  
- **Introduce static typing** (TypeScript).  

### **Q10: How do you measure your team’s success?**  
**Answer:**  
- **Engineering metrics** (cycle time, bug rate).  
- **Business impact** (feature adoption).  
- **Team health** (retention, surveys).  

---

# **5. Fintech-Specific Challenges (10+ Questions)**  

### **Q1: How do you ensure PCI compliance in a payment app?**  
**Answer:**  
- **Tokenize card data** (Stripe, Braintree).  
- **Never store raw PANs**.  
- **Annual third-party audits**.  

### **Q2: How do you handle encryption in React Native?**  
**Answer:**  
- **Native modules:** `react-native-sensitive-info`.  
- **Android Keystore/iOS Keychain**.  
- **Avoid JS crypto**.  

### **Q3: How would you design a real-time stock price feature?**  
**Answer:**  
- **WebSockets** for live updates.  
- **Throttle UI updates** (1s intervals).  
- **Fallback to polling**.  

### **Q4: How do you test fintech features rigorously?**  
**Answer:**  
- **Sandbox APIs** (mock bank endpoints).  
- **E2E tests** (Detox for happy/sad paths).  
- **Manual "break-it" testing**.  

### **Q5: How do you handle offline transactions?**  
**Answer:**  
- **Queue mutations** in SQLite.  
- **Sync on reconnect**.  
- **Show pending state** in UI.  

### **Q6: How do you reduce payment processing failures?**  
**Answer:**  
- **Fallback gateways** (Stripe → PayPal).  
- **Pre-validate cards** (Luhn algorithm).  
- **Retry logic** (exponential backoff).  

### **Q7: How do you ensure GDPR compliance?**  
**Answer:**  
- **Data minimization** (collect only what’s needed).  
- **User data export/deletion tools**.  
- **Encrypt PII at rest/in transit**.  

### **Q8: How do you handle a data breach?**  
**Answer:**  
1. **Contain** (disable compromised endpoints).  
2. **Notify** (legal, users, regulators).  
3. **Prevent recurrence** (add MFA).  

### **Q9: How do you design for accessibility in fintech?**  
**Answer:**  
- **Screen reader support** (VoiceOver/TalkBack).  
- **High-contrast modes**.  
- **Keyboard navigation**.  

### **Q10: How do you justify mobile engineering headcount to executives?**  
**Answer:**  
- **Opportunity cost** ("We lose $X due to slow releases").  
- **Competitive edge** ("Chime has 10 mobile engineers").  
- **Risk** ("Bus factor = 2 for core features").  

---

# **6. Live Coding Scenarios**  
Prepare for:  
1. **Debugging a memory leak**.  
2. **Optimizing a slow `FlatList`**.  
3. **Implementing secure local storage**.  

**Example Task:**  
```jsx
// Fix this infinite loop:
const [count, setCount] = useState(0);
useEffect(() => {
  setCount(count + 1); // Triggers re-render → infinite loop
}, [count]);
```  
**Fix:**  
```jsx
useEffect(() => {
  setCount(prev => prev + 1); // Use functional update
}, []);
```  

---

# **7. How to Prepare**  
1. **Practice coding** on [Snack.expo.io](https://snack.expo.io/).  
2. **Mock interviews** (Pramp, interviewing.io).  
3. **Ask Eivonne smart questions**, e.g.:  
   - _"How does MoneyLion’s mobile team handle dependency upgrades?"_  
   - _"What’s the biggest technical challenge you’ve faced here?"_  

---

### **Final Tip**  
Eivonne will evaluate:  
🔹 **Technical expertise** (React Native, performance).  
🔹 **System design skills** (scalability, security).  
🔹 **Leadership potential** (mentoring, best practices).  

**Demonstrate:**  
- You can **ship high-quality React Native apps**.  
- You **understand fintech constraints** (security, compliance).  
- You **elevate your team’s work**.  

You’re now **100% ready** for this interview! 🚀

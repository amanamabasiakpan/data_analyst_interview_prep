# 25. Common Interview Puzzles & Brain Teasers

## Theoretical Questions

### Q1: What are guesstimation questions and how do you approach them?
**A:** Guesstimation (Fermi estimation) questions ask you to estimate something you can't possibly know exactly. Approach:
1. **Clarify:** Make sure you understand what you're estimating
2. **Break it down:** Decompose into smaller, estimable pieces
3. **Make assumptions:** State them clearly and logically
4. **Calculate:** Multiply the pieces together
5. **Sanity check:** Does the final number make sense?
6. **Discuss sensitivity:** "If my assumption is off by 2x, the answer changes to..."
**Key:** Interviewers care about your reasoning process, not the exact number.

### Q2: Estimate the number of piano tuners in New York City.
**A:** (Classic Fermi problem)
1. **NYC population:** ~8 million people
2. **Households:** ~3 million households (avg 2.7 people/household)
3. **Piano ownership:** ~1 in 50 households owns a piano → 60,000 pianos
4. **Tuning frequency:** Once per year → 60,000 tunings/year
5. **Tuner capacity:** One tuner can tune ~4 pianos/day, 200 days/year → 800 pianos/year
6. **Result:** 60,000 / 800 = **~75 piano tuners**
(Actual number is roughly 50-100 — the estimate is in the right ballpark.)

### Q3: How many golf balls can fit in a Boeing 747?
**A:**
1. **747 volume:** Length ~70m, width ~6m, height ~8m → roughly 3,360 m³
2. **Subtract non-cabin space:** Engines, wings, cockpit, bathrooms → ~1/3 of volume is usable → ~2,000 m³
3. **Golf ball volume:** Diameter ~4.3cm → radius 2.15cm → volume = (4/3)πr³ ≈ 41.6 cm³
4. **Packing efficiency:** Spheres pack at ~74% efficiency (hexagonal close packing)
5. **Calculation:** 2,000 m³ = 2,000,000,000 cm³ × 0.74 / 41.6 cm³ ≈ **~35 million golf balls**

### Q4: Estimate the total revenue of Starbucks in the US per year.
**A:**
1. **US locations:** ~15,000 Starbucks stores
2. **Customers per store per day:** ~500 (peak hours: 2/min × 12 hours)
3. **Average transaction:** ~$6
4. **Daily revenue per store:** 500 × $6 = $3,000
5. **Annual revenue per store:** $3,000 × 365 = ~$1.1M
6. **Total US revenue:** 15,000 × $1.1M = **~$16.5 billion**
(Actual: ~$24B globally, US is roughly half → ~$12B. Close enough!)

### Q5: How would you estimate the number of taxis in London?
**A:**
1. **London population:** ~9 million
2. **Taxi demand:** Peak times (rush hour, weekend nights). Assume 1% of population uses a taxi on a given day → 90,000 rides/day
3. **Rides per taxi per day:** ~30 rides (avg 30-45 min per ride including pickup)
4. **Active taxis needed:** 90,000 / 30 = 3,000
5. **Add idle/spare:** ~20% not working any given day → **~3,600 taxis**
(Actual: ~21,000 black cabs + many more Uber/private hire. The question likely means black cabs, so we're in the ballpark.)

### Q6: What is the probability that in a room of 30 people, at least two share a birthday?
**A:** (Birthday Problem)
- Easier to calculate the complement: P(no shared birthday)
- P(no shared) = (365/365) × (364/365) × (363/365) × ... × (336/365)
- = 365! / ((365-30)! × 365³⁰)
- ≈ 0.294
- **P(at least one shared) = 1 - 0.294 = ~70.6%**
Counterintuitive but true — with just 23 people, the probability exceeds 50%.

### Q7: You have two ropes. Each takes exactly 60 minutes to burn, but they burn at non-uniform rates. How do you measure 45 minutes?
**A:**
1. Light Rope A at **both ends** and Rope B at **one end** simultaneously.
2. Rope A burns out in **30 minutes** (burning from both ends halves the time).
3. The moment Rope A burns out, light the **other end** of Rope B.
4. Rope B had 30 minutes of burn time remaining; now burning from both ends, it takes **15 minutes**.
5. **Total: 30 + 15 = 45 minutes.**

### Q8: You have 9 identical-looking balls. One is heavier. Find it in 2 weighings using a balance scale.
**A:**
1. **First weighing:** Divide into 3 groups of 3 (A, B, C). Weigh A vs B.
   - If A = B, the heavy ball is in C.
   - If A > B, the heavy ball is in A.
   - If B > A, the heavy ball is in B.
2. **Second weighing:** Take the group with the heavy ball (3 balls). Weigh ball 1 vs ball 2.
   - If 1 = 2, ball 3 is heavy.
   - If 1 > 2, ball 1 is heavy.
   - If 2 > 1, ball 2 is heavy.
**Key insight:** A balance scale has 3 outcomes (left heavy, right heavy, equal), so each weighing gives log₃ information. 3² = 9, so 2 weighings can distinguish among 9 possibilities.

### Q9: You have 8 balls. One is slightly heavier or lighter (you don't know which). Find it in 3 weighings.
**A:**
1. **Weighing 1:** Weigh {1,2,3} vs {4,5,6}. Leave {7,8} aside.
   - If equal: odd ball is in {7,8}. Weigh 7 vs 1 (known good).
     - If equal: 8 is odd. Weigh 8 vs 1 to determine heavier/lighter.
     - If not equal: 7 is odd, and you know if it's heavier or lighter.
   - If left heavy: Either one of {1,2,3} is heavy OR one of {4,5,6} is light.
2. **Weighing 2 (if left heavy in step 1):** Weigh {1,2} vs {4,7} (7 is known good).
   - If equal: Either 3 is heavy OR 5 or 6 is light.
   - If left heavy: Either 1 or 2 is heavy OR 4 is light.
   - If right heavy: Either 4 is heavy (impossible, since 4 was on lighter side) — this tells us 1 or 2 is heavy.
3. **Weighing 3:** Based on step 2, weigh the remaining candidates against known good balls to identify the odd one and whether it's heavy or light.

### Q10: A clock's hour and minute hands overlap at 12:00. When do they next overlap?
**A:**
- Minute hand speed: 360° per 60 min = 6°/min
- Hour hand speed: 360° per 12 hours = 30°/hour = 0.5°/min
- Relative speed: 6 - 0.5 = 5.5°/min
- They overlap every 360° / 5.5°/min = 65.4545... minutes = **1 hour 5 minutes 27.27 seconds**
- So after 12:00, next overlap is at approximately **1:05:27**
- They overlap 11 times in 12 hours (not 12, because the 11th to 12th would be at 12:00 again, which starts the next cycle).

### Q11: You have a 3-gallon jug and a 5-gallon jug. How do you measure exactly 4 gallons?
**A:**
1. Fill the **5-gallon jug** completely. (5:0)
2. Pour from 5-gallon into 3-gallon until 3-gallon is full. (2:3)
3. Empty the 3-gallon jug. (2:0)
4. Pour the remaining 2 gallons from 5-gallon into 3-gallon. (0:2)
5. Fill the 5-gallon jug again. (5:2)
6. Pour from 5-gallon into 3-gallon until 3-gallon is full. (4:3)
7. **The 5-gallon jug now has exactly 4 gallons.**

### Q12: What is the next number in the sequence: 1, 11, 21, 1211, 111221, ...?
**A:** (Look-and-say sequence)
- 1 → "one 1" → 11
- 11 → "two 1s" → 21
- 21 → "one 2, one 1" → 1211
- 1211 → "one 1, one 2, two 1s" → 111221
- 111221 → "three 1s, two 2s, one 1" → **312211**

### Q13: You flip a fair coin 10 times. What's the probability of getting exactly 6 heads?
**A:**
- Binomial probability: C(n,k) × pᵏ × (1-p)ⁿ⁻ᵏ
- C(10,6) = 10! / (6! × 4!) = 210
- p = 0.5
- Probability = 210 × (0.5)⁶ × (0.5)⁴ = 210 × (0.5)¹⁰ = 210 / 1024 = **~20.5%**

### Q14: A disease test is 99% accurate. The disease affects 1 in 10,000 people. You test positive. What's the probability you actually have the disease?
**A:** (Bayes' Theorem / Base Rate Fallacy)
- P(Disease) = 0.0001
- P(Positive|Disease) = 0.99
- P(Positive|No Disease) = 0.01
- P(Positive) = P(Positive|Disease)×P(Disease) + P(Positive|No Disease)×P(No Disease)
- P(Positive) = 0.99×0.0001 + 0.01×0.9999 = 0.000099 + 0.009999 = 0.010098
- P(Disease|Positive) = P(Positive|Disease)×P(Disease) / P(Positive)
- = 0.99 × 0.0001 / 0.010098 = **~0.98%**

**Key insight:** Despite a 99% accurate test, you only have ~1% chance of actually having the disease because the disease is so rare. Most positives are false positives.

### Q15: You have 100 doors, all closed. You walk through and toggle every door (open if closed, close if open). Then every 2nd door, then every 3rd, up to every 100th. Which doors are open at the end?
**A:**
- A door is toggled once for each of its divisors.
- Most numbers have an even number of divisors (divisors pair up: 12 = 1×12, 2×6, 3×4).
- **Perfect squares** have an odd number of divisors because one divisor is repeated (36 = 1×36, 2×18, 3×12, 4×9, **6×6**).
- Odd number of toggles = door ends open.
- **Answer:** Doors **1, 4, 9, 16, 25, 36, 49, 64, 81, 100** (perfect squares ≤ 100).

### Q16: Two trains are 200 miles apart, moving toward each other at 50 mph each. A bee flies back and forth between them at 75 mph until they collide. How far does the bee fly?
**A:** (Trick question — don't calculate infinite series!)
- Time until collision: 200 miles / (50 + 50) mph = **2 hours**
- Bee flies at 75 mph for 2 hours → 75 × 2 = **150 miles**
- The infinite back-and-forth is a red herring. Just calculate total time × bee speed.

### Q17: You have a 50-story building and 2 eggs. Find the highest floor from which an egg won't break, minimizing worst-case drops.
**A:**
- If we drop from floor x and it breaks, we linearly test floors 1 to x-1 with the second egg.
- If it doesn't break, we go up x-1 floors (then x-2, x-3, etc.) to keep total drops balanced.
- x + (x-1) + (x-2) + ... + 1 ≥ 50
- x(x+1)/2 ≥ 50 → x² + x - 100 ≥ 0 → x ≈ 9.5 → **x = 14**
- Strategy: Drop from 14, then 27 (14+13), then 39 (27+12), then 49, then 50.
- **Worst case: 14 drops.**

### Q18: You have 100 lockers and 100 students. Student 1 opens all, Student 2 closes every 2nd, Student 3 toggles every 3rd, etc. Which lockers are open?
**A:**
- Same logic as the 100 doors problem (Q15).
- A locker is toggled by students whose numbers are divisors of the locker number.
- Only perfect squares have an odd number of divisors.
- **Answer:** Lockers **1, 4, 9, 16, 25, 36, 49, 64, 81, 100**.

### Q19: You have a biased coin (P(Heads) = 0.6). How do you make a fair decision (50/50) using it?
**A:**
- Flip the coin **twice**.
- If HH or TT: discard and flip again.
- If HT: call it "Option A"
- If TH: call it "Option B"
- P(HT) = 0.6 × 0.4 = 0.24
- P(TH) = 0.4 × 0.6 = 0.24
- P(HH) = 0.36, P(TT) = 0.16 (discarded)
- Conditional on not discarding: P(HT) = P(TH) = 0.5
- **Expected flips:** 1 / 0.48 = ~2.08 pairs = ~4.17 individual flips on average.

### Q20: You have 12 identical-looking coins. One is counterfeit (either heavier or lighter). Find it in 3 weighings.
**A:**
- Similar logic to Q9 but with 12 coins.
- **Weighing 1:** Weigh {1,2,3,4} vs {5,6,7,8}. Leave {9,10,11,12} aside.
  - If equal: counterfeit is in {9,10,11,12}. Weigh {9,10} vs {1,2} (known good).
    - If equal: counterfeit is in {11,12}. Weigh 11 vs 1 to find which and if heavier/lighter.
    - If not equal: counterfeit is in {9,10}, and you know if 9/10 side is heavier or lighter.
  - If not equal: counterfeit is in the 8 weighed coins. You know which side is heavier or lighter.
- **Weighing 2 & 3:** Use the information from weighing 1 to narrow down to 1-2 coins and determine if heavier or lighter.
- **Key:** Each weighing has 3 outcomes, 3³ = 27 possible outcomes. We need to distinguish 24 possibilities (12 coins × 2 possibilities each: heavier or lighter). 27 > 24, so it's theoretically possible.

### Q21: A bat and ball cost $11 total. The bat costs $10 more than the ball. How much does the ball cost?
**A:** (System 1 vs System 2 thinking — Daniel Kahneman)
- Intuitive (wrong) answer: $1
- Correct: Ball = $0.50, Bat = $10.50. Bat costs $10 more than ball. Total = $11.
- **Ball costs $0.50.**
- This tests whether you verify your intuition with math.

### Q22: You have a 5-minute sand timer and a 3-minute sand timer. Measure exactly 7 minutes.
**A:**
1. Start both timers simultaneously.
2. When 3-minute timer runs out (3 min elapsed), **flip it immediately**.
3. When 5-minute timer runs out (5 min elapsed), **flip it immediately**.
4. When 3-minute timer runs out again (6 min elapsed), **1 minute has passed in the 5-minute timer**.
5. **Flip the 5-minute timer immediately.** It will run for 1 minute.
6. When it runs out: 6 + 1 = **7 minutes**.

### Q23: You have 100 people in a room. Each has a hat (black or white). They can see everyone else's hat but not their own. They simultaneously guess their hat color. What's the optimal strategy to maximize correct guesses?
**A:**
- **Strategy:** Agree beforehand that each person counts the number of black hats they see.
  - If even: guess "white"
  - If odd: guess "black"
- **Analysis:** The actual parity (even/odd) of all 100 hats is either even or odd.
- Each person's guess is based on the parity they see. Exactly **50 people will be wrong** and **50 will be right** (or 49/51, etc.), but the strategy guarantees at least 50 correct.
- **Better strategy (guarantees 99 correct):** Use XOR. Person 1 sacrifices themselves by encoding the parity of all other 99 hats. The other 99 use this information to deduce their own hat color. **99 people guaranteed correct, 1 person 50% chance.**

### Q24: You have a 3×3×3 cube made of 27 smaller cubes. If you paint the outside and break it apart, how many cubes have paint on exactly 2 faces?
**A:**
- **3 faces painted:** Corner cubes. A cube has 8 corners → **8 cubes**
- **2 faces painted:** Edge cubes (not corners). A cube has 12 edges, each edge has 3 cubes, minus 2 corners = 1 cube per edge with exactly 2 faces painted → **12 cubes**
- **1 face painted:** Face-center cubes (not on edges). 6 faces, each has 1 center cube → **6 cubes**
- **0 faces painted:** Interior cube. 3×3×3 center = **1 cube**
- Check: 8 + 12 + 6 + 1 = 27 ✓
- **Answer: 12 cubes**

### Q25: You have a deck of 52 cards, face down. You flip cards 1 by 1. What's the probability the first ace appears on the 5th card?
**A:**
- For the first ace to be on card 5, cards 1-4 must be non-aces, and card 5 must be an ace.
- P(card 1 not ace) = 48/52
- P(card 2 not ace | card 1 not ace) = 47/51
- P(card 3 not ace | first 2 not aces) = 46/50
- P(card 4 not ace | first 3 not aces) = 45/49
- P(card 5 is ace | first 4 not aces) = 4/48
- P = (48/52) × (47/51) × (46/50) × (45/49) × (4/48)
- = (48×47×46×45×4) / (52×51×50×49×48)
- = (47×46×45×4) / (52×51×50×49)
- = **~0.0599 or ~6%**

### Q26: You have a circle of 100 people. Person 1 kills Person 2, gives the sword to Person 3. Person 3 kills Person 4, gives to Person 5, etc. Who survives?
**A:** (Josephus Problem)
- Write 100 in binary: 1100100
- Move the leading 1 to the end: 1001001
- Convert back to decimal: 64 + 8 + 1 = **73**
- **Person 73 survives.**
- General formula: If n = 2^m + l where 0 ≤ l < 2^m, survivor = 2l + 1.
- For n = 100: 100 = 64 + 36 → survivor = 2×36 + 1 = 73.

### Q27: You have 10 bags of coins. One bag has counterfeit coins (each 1g lighter). You have a scale. Find the bad bag in one weighing.
**A:**
1. Take **1 coin from Bag 1, 2 coins from Bag 2, ..., 10 coins from Bag 10**.
2. Weigh all 55 coins together.
3. If all were real (10g each), total = 550g.
4. Actual weight will be less. The difference tells you which bag:
   - 1g less → Bag 1
   - 2g less → Bag 2
   - ...
   - 10g less → Bag 10
- **One weighing identifies the bag.**

### Q28: You have a 4-minute sand timer and a 7-minute sand timer. Measure exactly 9 minutes.
**A:**
1. Start both timers simultaneously.
2. When 4-minute timer runs out (4 min), **flip it immediately**.
3. When 7-minute timer runs out (7 min), **3 minutes have elapsed in the 4-minute timer**.
4. **Flip the 7-minute timer immediately.**
5. When 4-minute timer runs out (4 + 4 = 8 min), **1 minute remains in the 7-minute timer**.
6. **Flip the 7-minute timer immediately.** It will run for 1 minute.
7. When it runs out: 8 + 1 = **9 minutes**.

### Q29: You have 3 light switches outside a room and 3 bulbs inside. You can only enter the room once. How do you determine which switch controls which bulb?
**A:**
1. Turn **Switch 1 ON** and leave it on for **10 minutes**.
2. After 10 minutes, turn **Switch 1 OFF**.
3. Turn **Switch 2 ON**.
4. Leave **Switch 3 OFF**.
5. Enter the room:
   - **Bulb that's ON** → controlled by **Switch 2**
   - **Bulb that's OFF but warm** → controlled by **Switch 1** (was on, heated up, then turned off)
   - **Bulb that's OFF and cold** → controlled by **Switch 3** (never turned on)
- **Uses heat as a second "state" beyond just on/off.**

### Q30: You have a 100-floor building and 2 eggs. Find the critical floor (highest floor where egg doesn't break) with minimum drops in worst case.
**A:**
- Same as Q17 but generalized.
- Optimal strategy: Drop from floor x, then x+(x-1), then x+(x-1)+(x-2), etc.
- x + (x-1) + (x-2) + ... + 1 ≥ 100
- x(x+1)/2 ≥ 100 → x² + x - 200 ≥ 0
- x ≈ 13.65 → **x = 14**
- Drop sequence: 14, 27, 39, 50, 60, 69, 77, 84, 90, 95, 99, 100
- **Worst case: 14 drops.**

## Scenario-Based Questions

### Q31: You're asked to estimate the market size for electric scooters in a city you've never visited. Walk through your approach.
**A:**
1. **Define scope:** "Electric scooter rental market" or "Personal electric scooter sales"? Clarify with interviewer. Let's assume rental.
2. **City population:** Ask or estimate — let's say 2 million.
3. **Target demographic:** Ages 18-45, urban, mobile app users → ~30% of population = 600,000
4. **Usage frequency:** Assume 5% use scooters weekly → 30,000 weekly users
5. **Trips per user per week:** 3 trips
6. **Average trip price:** $3
7. **Weekly revenue:** 30,000 × 3 × $3 = $270,000
8. **Annual market:** $270,000 × 52 = **~$14M**
9. **Sanity check:** Compare to known markets (e.g., Lime reported $100M+ globally, so $14M for one major city seems reasonable).

### Q32: How many pizzas are consumed daily in the United States?
**A:**
1. **US population:** ~330 million
2. **Pizza-eating population:** ~80% eat pizza regularly → 264 million
3. **Frequency:** Average person eats pizza ~1.5 times per month → 18 times/year
4. **Annual pizzas:** 264M × 18 = ~4.75 billion pizzas/year
5. **Daily pizzas:** 4.75B / 365 = **~13 million pizzas/day**
6. **Sanity check:** Domino's sells ~3 million pizzas/day globally. US is their largest market. Adding Pizza Hut, Papa John's, local pizzerias, frozen pizzas → 13M seems in the right range.

### Q33: Estimate the number of emails sent globally per day.
**A:**
1. **Global population:** ~8 billion
2. **Email users:** ~4.5 billion (roughly half the world has email)
3. **Business users:** ~1.5 billion, average 50 emails/day → 75 billion
4. **Personal users:** ~3 billion, average 5 emails/day → 15 billion
5. **Automated emails:** Newsletters, notifications, marketing → ~100 billion
6. **Spam:** ~50% of all email is spam → add ~190 billion
7. **Total estimate:** ~**350-400 billion emails/day**
(Actual: ~350 billion/day — close!)

### Q34: How many gas stations are there in the United States?
**A:**
1. **US population:** ~330 million
2. **Cars per capita:** ~0.8 → ~264 million cars
3. **Gas station capacity:** Average station serves ~500 cars/day
4. **Total daily demand:** Assume each car refuels every 7 days → 264M / 7 = ~38M refuels/day
5. **Stations needed:** 38M / 500 = **~76,000 stations**
6. **Sanity check:** Actual is ~115,000. Our estimate is low because: not all stations serve 500/day (rural stations serve fewer), and commercial vehicles refuel more frequently. Adjusting for these: ~100,000-120,000.

### Q35: Estimate the total storage capacity (in TB) of all smartphones sold last year.
**A:**
1. **Global smartphone sales:** ~1.2 billion/year
2. **Average storage:** 128GB (mix of budget 64GB, mid-range 128GB, premium 256-512GB)
3. **Total storage:** 1.2B × 128GB = 153.6 billion GB = **~153.6 million TB**
4. **Alternative check:** ~400 million iPhones sold, avg ~200GB = 80M TB. ~800 million Android, avg ~100GB = 80M TB. Total ~160M TB. Close enough.

### Q36: A company has 100 employees. Each employee must shake hands with every other employee exactly once. How many handshakes?
**A:**
- Each of 100 people shakes hands with 99 others.
- But this counts each handshake twice (A shakes B is the same as B shakes A).
- Total = 100 × 99 / 2 = **4,950 handshakes**
- Formula: C(n,2) = n(n-1)/2

### Q37: You have a 6-sided die. What's the expected number of rolls to see all 6 faces at least once? (Coupon Collector's Problem)
**A:**
- Expected rolls to get first new face: 6/6 = 1
- Expected rolls to get second new face: 6/5 = 1.2
- Expected rolls to get third new face: 6/4 = 1.5
- Expected rolls to get fourth new face: 6/3 = 2
- Expected rolls to get fifth new face: 6/2 = 3
- Expected rolls to get sixth new face: 6/1 = 6
- Total expected rolls: 1 + 1.2 + 1.5 + 2 + 3 + 6 = **14.7 rolls**
- General formula: n × Hₙ where Hₙ is the nth harmonic number. For n=6: 6 × (1 + 1/2 + 1/3 + 1/4 + 1/5 + 1/6) ≈ 6 × 2.45 = 14.7

### Q38: You have a bag with 2 red balls and 3 blue balls. You draw 2 balls without replacement. What's the probability both are red?
**A:**
- P(first red) = 2/5
- P(second red | first red) = 1/4
- P(both red) = (2/5) × (1/4) = 2/20 = **1/10 = 10%**

### Q39: A lily pad doubles in size every day. It covers the entire pond on day 30. What day does it cover half the pond?
**A:**
- If it doubles every day and covers the whole pond on day 30...
- Then on day 29, it covered **half** the pond.
- **Answer: Day 29**
- Tests exponential thinking intuition.

### Q40: You have 100 prisoners and a light switch in a room. Each day a random prisoner is taken to the room. They can toggle the switch. How can they determine when everyone has visited at least once?
**A:**
- **Designate one prisoner as the "counter."**
- **Rule for non-counters:** If you enter the room and the light is OFF, and you have never toggled it ON before, toggle it ON. Otherwise, do nothing.
- **Rule for counter:** If you enter and the light is ON, toggle it OFF and increment your count.
- **When the counter's count reaches 99** (everyone else has toggled it on once), they declare that everyone has visited.
- **Why it works:** Each non-counter toggles the light ON exactly once. The counter counts these ON signals.
- **Expected time:** ~10,000+ days (years), but it's a theoretical solution.

### Q41: You're in a room with 3 switches controlling 3 bulbs in another room. You can only enter the other room once. How do you map switches to bulbs?
**A:**
- Same as Q29:
1. Turn Switch 1 ON for 10 minutes.
2. Turn Switch 1 OFF.
3. Turn Switch 2 ON.
4. Enter the room:
   - **ON** → Switch 2
   - **OFF and warm** → Switch 1
   - **OFF and cold** → Switch 3

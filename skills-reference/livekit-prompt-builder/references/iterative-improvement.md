# Iterative Prompt Improvement Guide

This guide provides a systematic approach to improving voice agent prompts based on real-world feedback, test results, and performance data while maintaining prompt structure and quality.

## Overview

Voice agent prompts are never "done" — they improve through iteration. This guide shows how to:
1. Collect meaningful feedback
2. Analyze test results systematically
3. Make targeted improvements
4. Preserve what works while fixing what doesn't
5. Measure impact of changes

---

## The Improvement Cycle

### Phase 1: Baseline Establishment

Before improving, establish your starting point.

**Step 1: Document current prompt structure**

Create a prompt inventory:
```markdown
## Current Prompt Structure

### Components:
- Identity: [agent name and role]
- Voice optimization rules: [yes/no, details]
- Domain context: [what's included]
- Tool usage instructions: [tools covered]
- Guardrails: [boundaries defined]
- Workflow: [yes/no, structure]

### Word count: [X words]
### Last updated: [date]
### Version: [1.0]
```

**Step 2: Define success metrics**

Choose 3-5 metrics to track:

**Conversation quality metrics:**
- Average conversation length (turns)
- Completion rate (% reaching desired outcome)
- Escalation rate (% transferred to humans)
- User interruption frequency

**Technical metrics:**
- Tool usage accuracy (% correct tool calls)
- Error rate (% failed tool calls)
- Response time (latency)
- Formatting issues (TTS problems per 100 conversations)

**Business metrics:**
- Customer satisfaction (CSAT score)
- Task completion rate
- Containment rate (% resolved without escalation)
- First-call resolution rate

**Step 3: Run baseline tests**

Create test scenarios covering:
- Happy path (everything goes right)
- Common variations (typical user behaviors)
- Edge cases (unusual requests)
- Error scenarios (tool failures, invalid inputs)

Document results:
```
Test Scenario: Restaurant reservation - happy path
Result: ✓ Passed
Notes: Collected all info, confirmed, created reservation
Issues: None

Test Scenario: Restaurant reservation - date unavailable
Result: ✗ Failed
Notes: Agent didn't offer alternatives
Issues: Missing error handling for unavailable dates
```

---

## Phase 2: Feedback Collection

### Test-Based Feedback

**Conversational testing**

Create test scripts for common scenarios:

```markdown
## Test Case: Support - Password Reset

User inputs:
1. "I need to reset my password"
2. [Agent asks for email] "john@example.com"
3. [Agent says check email] "I haven't received it"
4. [Agent response] - EVALUATE

Expected behavior:
- Agent uses password_reset tool with email
- Agent confirms email sent
- Agent provides troubleshooting if user doesn't receive it
- Agent offers escalation if still unresolved

Actual behavior:
- [Record what happened]

Pass/Fail:
- [Mark result]

Issues identified:
- [List problems]
```

**Automated test suite**

Use LiveKit's testing features to create repeatable test cases:

```python
# Example test case structure
test_cases = [
    {
        "name": "reservation_happy_path",
        "user_inputs": [
            "I'd like to make a reservation",
            "Tomorrow at 7pm",
            "Four people",
            "John Smith",
            "555-1234",
            "Yes, that's correct"
        ],
        "expected_tools": ["check_availability", "create_reservation"],
        "expected_outcome": "reservation_confirmed"
    }
]
```

### Human Feedback

**User satisfaction surveys**

After interactions, collect:
- Rating (1-5 stars)
- What worked well
- What was frustrating
- Suggestions for improvement

**Agent performance reviews**

Listen to conversation recordings and note:
- Where agent struggled
- Where user seemed confused
- Awkward phrasing or TTS issues
- Missed opportunities
- Successful interactions

**Feedback collection template:**

```markdown
## Conversation Review

Conversation ID: [id]
Date: [date]
Scenario: [what user wanted]

### What went well:
- [Positive observations]

### What didn't work:
- [Problems identified]

### User experience:
- Confusion points: [where user seemed lost]
- Frustration points: [where user got frustrated]
- Smooth parts: [where conversation flowed naturally]

### Technical issues:
- Tool calling: [any problems]
- TTS: [pronunciation or formatting issues]
- Timing: [interruptions, delays]

### Improvement ideas:
- [Specific suggestions]
```

### Pattern Analysis

**Look for patterns across multiple conversations:**

```markdown
## Pattern Analysis - Week 1

Total conversations: 247

### Common issues (>10% occurrence):
1. Agent uses technical jargon (15% of conversations)
   - Examples: "API error", "database timeout"
   - Impact: User confusion, requests to escalate

2. Responses too long (23% of conversations)
   - Average: 4-5 sentences
   - Impact: User interruptions, impatience

3. Missing error handling for unavailable dates (31% of booking attempts)
   - Impact: Awkward pauses, confused users

### Successful patterns:
1. Confirmation before action (99% success rate)
2. Empathetic acknowledgment of frustration (high satisfaction)
3. Clear next steps at end of call (reduced repeat calls)
```

---

## Phase 3: Targeted Improvements

### Structure-Preserving Modifications

**The golden rule:** Make minimal, targeted changes to address specific issues.

**Bad approach:**
```
Problem: Agent's responses are too long
Solution: Rewrite the entire prompt
```

**Good approach:**
```
Problem: Agent's responses are too long
Solution: Strengthen the brevity instruction in voice optimization section
```

### Improvement Patterns

**Pattern 1: Adding specificity**

When agent behavior is inconsistent:

**Before:**
```
Use tools to help customers.
```

**After:**
```
Use the search_products tool when customers ask about items, prices, or availability.
Use the track_order tool when they ask about order status using their order number.
```

**What changed:** Added specific trigger conditions for each tool
**What stayed:** Overall instruction to use tools

---

**Pattern 2: Adding constraints**

When agent does too much:

**Before:**
```
Keep responses conversational and helpful.
```

**After:**
```
Keep responses conversational and helpful - typically 1-2 sentences. Ask one question at a time.
```

**What changed:** Added specific length constraint
**What stayed:** Conversational and helpful tone

---

**Pattern 3: Adding error handling**

When agent struggles with failures:

**Before:**
```
Use the check_availability tool to verify the date.
```

**After:**
```
Use the check_availability tool to verify the date.

If the date is unavailable:
1. Use suggest_alternatives to find nearby dates
2. Present 2-3 alternatives: "That date is booked, but I have [date1] or [date2] available. Would either work?"
```

**What changed:** Added error handling flow
**What stayed:** Core tool usage instruction

---

**Pattern 4: Refining tone**

When tone doesn't match needs:

**Before:**
```
You are a customer service agent.
```

**After:**
```
You are a customer service agent. You are patient and empathetic, especially when customers are frustrated.

When customers express frustration, respond with understanding: "I understand how frustrating this must be. Let me help you resolve this."
```

**What changed:** Added personality traits and example phrasing
**What stayed:** Core role definition

---

**Pattern 5: Removing ambiguity**

When agent makes wrong choices:

**Before:**
```
Transfer to a specialist when needed.
```

**After:**
```
Transfer to a human specialist when:
- You cannot resolve the issue after 2 attempts
- Customer explicitly requests a human
- Issue involves billing disputes or refunds

For all other issues, continue working toward resolution.
```

**What changed:** Explicit criteria for escalation
**What stayed:** Concept of specialist transfer

---

### Modification Checklist

Before making changes, verify:

- [ ] I've identified the specific problem (not guessing)
- [ ] I have evidence from multiple conversations (not one-off)
- [ ] I know which prompt section needs changing
- [ ] My change is targeted and minimal
- [ ] I'm preserving working elements
- [ ] I'm not adding unnecessary complexity
- [ ] The change addresses root cause (not symptom)

### Version Control

**Track changes systematically:**

```markdown
## Prompt Version History

### Version 1.1 (2025-01-15)
**Changes:**
- Added error handling for unavailable dates in reservation workflow
- Strengthened brevity constraint from "concise" to "1-2 sentences"

**Reason:**
- 31% of booking attempts encountered unavailable dates with poor handling
- 23% of conversations had responses that were too long (avg 4-5 sentences)

**Files modified:**
- main_prompt.txt (lines 23-25, 45-50)

**Expected impact:**
- Reduce awkward pauses when dates unavailable
- Decrease user interruptions

### Version 1.0 (2025-01-01)
**Baseline version**
```

---

## Phase 4: Testing Changes

### A/B Testing

**Run both versions in parallel:**

```markdown
## A/B Test Plan

**Test period:** 1 week
**Traffic split:** 50/50

**Version A (control):** Current prompt v1.0
**Version B (variant):** Updated prompt v1.1 with brevity improvements

**Metrics to compare:**
- Average response length (words)
- User interruption rate
- Conversation completion rate
- Satisfaction scores

**Success criteria:**
- Version B has <3 sentence average responses
- Version B has ≥10% fewer interruptions
- No decrease in completion rate or satisfaction
```

### Regression Testing

**Ensure changes don't break existing functionality:**

```markdown
## Regression Test Suite

Run full test suite on new version:

### Core functionality:
- [✓] Happy path reservation flow
- [✓] Tool calling (all tools)
- [✓] Error handling scenarios
- [✓] Multi-turn conversations
- [✓] Interruption handling

### Voice quality:
- [✓] No markdown or special characters in output
- [✓] Number formatting (spelled out)
- [✓] Response length within limits
- [✓] TTS pronunciation acceptable

### Edge cases:
- [✓] Ambiguous requests
- [✓] Out-of-scope questions
- [✓] Tool failures
- [✓] Invalid inputs

Issues found: [list any problems]
```

### Staged Rollout

**Don't deploy to 100% immediately:**

1. **Internal testing** (1-2 days): Team members test thoroughly
2. **Limited rollout** (10% traffic, 2-3 days): Monitor closely
3. **Expanded rollout** (50% traffic, 1 week): A/B test if possible
4. **Full deployment** (100%): Only after validating success

---

## Phase 5: Measurement and Analysis

### Compare Before/After

**Quantitative comparison:**

```markdown
## Version 1.1 Impact Analysis (2 weeks after deployment)

### Metrics Comparison:

| Metric | v1.0 (baseline) | v1.1 (current) | Change |
|--------|----------------|----------------|--------|
| Avg response length | 4.2 sentences | 2.1 sentences | -50% ✓ |
| User interruptions | 23% | 14% | -39% ✓ |
| Completion rate | 78% | 81% | +3.8% ✓ |
| CSAT score | 4.1/5 | 4.3/5 | +4.9% ✓ |
| Escalation rate | 12% | 11% | -8.3% ✓ |

### Test scenario results:

| Scenario | v1.0 pass rate | v1.1 pass rate | Change |
|----------|---------------|---------------|--------|
| Happy path | 95% | 97% | +2.1% |
| Error handling | 62% | 89% | +43.5% ✓ |
| Edge cases | 71% | 73% | +2.8% |

### Conclusion:
Version 1.1 shows significant improvements in brevity and error handling without negatively impacting other metrics. Recommend keeping changes.
```

### Identify New Issues

**New problems that emerged:**

```markdown
## New Issues Identified (v1.1)

1. Overly brief in complex scenarios
   - Frequency: 5% of conversations
   - Impact: Users ask for clarification
   - Proposed fix: Add context-aware length adjustment

2. Error messages too technical in some cases
   - Frequency: 3% of error scenarios
   - Impact: User confusion
   - Proposed fix: Simplify error message phrasing

Priority: Medium (address in v1.2)
```

---

## Best Practices for Iteration

### Do's

1. **Make one change at a time** - Easier to attribute impact
2. **Test before deploying** - Catch regressions early
3. **Document everything** - Know why changes were made
4. **Use data, not intuition** - Evidence-based improvements
5. **Preserve what works** - Don't fix what isn't broken
6. **Version control prompts** - Track history
7. **Measure impact** - Verify improvements actually help
8. **Iterate continuously** - Ongoing process, not one-time

### Don'ts

1. **Don't make sweeping rewrites** - High risk of breaking things
2. **Don't change multiple things at once** - Can't isolate impact
3. **Don't skip testing** - Small changes can have big effects
4. **Don't ignore edge cases** - They matter
5. **Don't over-optimize** - Diminishing returns exist
6. **Don't forget user experience** - Metrics aren't everything
7. **Don't remove guardrails** - Safety first
8. **Don't assume - validate** - Test your hypotheses

---

## Common Improvement Scenarios

### Scenario 1: Too Verbose

**Symptoms:**
- Users interrupt frequently
- Average response length >3 sentences
- Users seem impatient

**Diagnosis:**
- Review conversations: Are responses actually too long?
- Check if all information is necessary
- See if agent is explaining too much

**Solution:**
```
Before:
"Keep your responses helpful and informative."

After:
"Keep responses very brief - 1-2 sentences maximum. Ask one question at a time. Only provide details if specifically asked."
```

**Test:**
- Measure average sentence count
- Track interruption rate
- Verify completion rate doesn't drop

---

### Scenario 2: Inconsistent Tool Usage

**Symptoms:**
- Agent doesn't use tools when it should
- Agent uses wrong tool for situation
- Agent asks for information tools can infer

**Diagnosis:**
- Review failed tool calls
- Check if tool descriptions are clear
- Verify prompt includes usage guidance

**Solution:**
```
Before:
"You have access to search and booking tools."

After:
"Use the search_product tool when users ask about items, prices, or availability. Estimate product IDs from names - don't ask users for SKU numbers.

Use the create_booking tool only after collecting: date, time, and customer name. Confirm all details before calling the tool."
```

**Test:**
- Tool usage accuracy rate
- Reduction in unnecessary questions
- Correct tool selection rate

---

### Scenario 3: Poor Error Handling

**Symptoms:**
- Awkward silences when tools fail
- Agent repeats failed actions
- No fallback offered

**Diagnosis:**
- Identify which errors occur most
- Review agent responses to errors
- Check if error handling is defined

**Solution:**
```
Add to prompt:

"If a tool call fails:
1. Explain the issue simply: 'I'm having trouble looking that up right now'
2. Don't share technical error messages
3. Offer a fallback: 'Would you like me to try again, or shall I connect you with a specialist?'
4. Don't retry the same call more than once"
```

**Test:**
- Simulate tool failures
- Verify graceful handling
- Check user satisfaction in error scenarios

---

### Scenario 4: Wrong Tone

**Symptoms:**
- User feedback mentions tone issues
- Satisfaction scores lower than expected
- Escalation rate high

**Diagnosis:**
- Listen to conversation recordings
- Note specific phrasing problems
- Compare successful vs unsuccessful interactions

**Solution:**
```
Before:
"You are a customer service agent."

After:
"You are a customer service agent. You are warm, patient, and empathetic.

When customers are frustrated, respond with understanding: 'I understand how frustrating this must be. I'm here to help.'

Avoid:
- Robotic language ('Your request has been processed')
- Defensive responses ('That's not our policy')
- Dismissive tones"
```

**Test:**
- Satisfaction scores
- Tone-related feedback
- Escalation rate

---

## Advanced Techniques

### Prompt Tuning Based on Context

**Different scenarios may need different approaches:**

```
For simple information requests:
- Ultra-brief responses (1 sentence)
- Direct answers

For complex troubleshooting:
- Slightly longer responses (2-3 sentences)
- Step-by-step guidance
- Check understanding frequently

For emotional situations:
- Empathetic acknowledgment first
- Then brief solution-focused response
```

**Implementation:**
```
Adjust your response length based on the situation:
- Simple questions: 1 sentence
- Multi-step tasks: 2-3 sentences, ask for confirmation between steps
- When user is frustrated: Acknowledge their feelings, then briefly address the issue
```

### Personality Calibration

**Fine-tune personality traits based on feedback:**

```
Personality dimensions to calibrate:
- Formality: Casual ↔ Professional
- Enthusiasm: Reserved ↔ Energetic
- Verbosity: Terse ↔ Detailed
- Empathy: Neutral ↔ Highly empathetic

Example adjustment:
If users find agent too casual in healthcare context:

Before: "Hey there! What's up?"
After: "Hello, this is [Name] at [Clinic]. How can I help you today?"
```

### Continuous Learning Loop

**Establish ongoing improvement process:**

```markdown
## Monthly Improvement Cycle

Week 1: Collect and analyze data
- Review conversation metrics
- Identify top 3 issues
- Prioritize based on impact

Week 2: Design improvements
- Draft prompt changes
- Create test cases
- Document expected impact

Week 3: Test and validate
- Internal testing
- Limited rollout (10%)
- Monitor metrics

Week 4: Deploy and measure
- Full deployment if successful
- Measure impact vs baseline
- Document results
```

---

## Summary

Effective prompt improvement is:
- **Data-driven** - Use metrics and feedback, not guesses
- **Targeted** - Fix specific issues, preserve what works
- **Tested** - Validate before full deployment
- **Measured** - Track impact of changes
- **Continuous** - Ongoing process, not one-time effort

By following this framework, you can systematically improve voice agent prompts while maintaining quality, structure, and reliability.

# Prompt Engineering Mastery Report

**Author:** AKANKSHA SEKHAR
**Role:** AI/Gen AI Intern, Valentius Kryptix  
**GitHub:** [@akankshasekhar](https://github.com/akankshasekhar)

## Overview
This report documents a hands-on exploration of core prompt engineering techniques: Zero-Shot vs. Few-Shot prompting, Chain-of-Thought (CoT) reasoning, and Structured JSON output. The objective is to demonstrate how specific prompt structures influence Large Language Model (LLM) accuracy, consistency, and formatting reliability across various tasks.

---

## Phase 1: Zero-Shot vs. Few-Shot Prompting

### Task Description
Classifying customer support tickets by urgency (LOW, MEDIUM, CRITICAL) based on the text.

### 1. Zero-Shot
**Prompt:**
> Classify the urgency of the following customer support ticket as LOW, MEDIUM, or CRITICAL, and provide a 1-sentence reason. 
>
> Ticket: []

**Raw Outputs:**
*   **Ticket A:** Urgency: LOW \n Reason: This is a non-blocking feature request for a user interface preference that does not impact the core functionality or availability of the dashboard.
*   **Ticket B:** Urgency: MEDIUM \n Reason: While a 500 server error breaks the PDF export feature, the availability of a working CSV export provides a viable workaround for accessing the report data.
*   **Ticket C:** Urgency: CRITICAL \n Reason: The complete outage of the payment gateway blocks all customer checkouts and is causing severe, immediate financial loss with no available workaround.

**Analysis:**
The zero-shot approach performed remarkably well on reasoning, correctly identifying the urgency levels for all three tickets based purely on the instructions. The model defaulted to a clean two-line `Urgency:` and `Reason:` structure. However, without examples, we are relying entirely on the model's default formatting preferences, which could easily drift in a production environment if the prompt encounters edge-case inputs.

### 2. Few-Shot
**Prompt:**
> Classify the urgency of the following customer support ticket as LOW, MEDIUM, or CRITICAL, and provide a 1-sentence reason. Format your output exactly like the examples.
>
> **Example 1**
> Ticket: The logo on the login page looks a bit blurry on my phone.
> Classification: LOW
> Reason: This is a minor cosmetic issue that does not prevent user functionality.
>
> **Example 2**
> Ticket: I can't reset my password, the email never arrives.
> Classification: CRITICAL
> Reason: A core user flow is completely broken, preventing account access.
> 
> **Target**
> Ticket: []

**Raw Outputs:**
*   **Ticket A:** Classification: LOW Reason: This is a non-urgent feature request for a user interface preference that does not impair core functionality.
*   **Ticket B:** Classification: MEDIUM Reason: While a 500 server error breaks the PDF export feature, the working CSV export provides a viable workaround for accessing the report data.
*   **Ticket C:** Classification: CRITICAL Reason: A complete outage of the payment gateway blocks all customer checkouts and causes severe, ongoing financial loss.

**Analysis:**
While the few-shot prompt didn't drastically improve the *accuracy* (since the zero-shot baseline was already correct), it proved highly effective at enforcing strict formatting constraints. The model immediately swapped its default `Urgency:` key for the `Classification:` key provided in the examples and collapsed the output onto a single line. This demonstrates that few-shot prompting is essential for ensuring reliable, parsable outputs when integrating LLMs into backend APIs, even when zero-shot reasoning is sufficient.


## Phase 2: Chain-of-Thought (CoT) Reasoning

### Task Description
Solving a multi-step math and logic word problem involving variable daily rates.

### 1. Direct Prompt (No CoT)
**Prompt:**
> Sarah starts reading a 300-page book on Monday. She reads 20 pages a day on weekdays (Monday-Friday), and 40 pages a day on weekends (Saturday-Sunday). What exact day of the week will she finish the book?
> 
> Provide only the final day of the week as your answer, with no additional text or explanation.

**Raw Output:**
Saturday

**Analysis:**
The model correctly provided the answer "Saturday." While modern LLMs have become highly capable at internalizing math for relatively straightforward word problems, forcing a direct answer offers zero visibility into *how* the model reached its conclusion. If the model had hallucinated or miscalculated a day, there would be no way to debug the failure point.

### 2. Chain-of-Thought (CoT) Prompt
**Prompt:**
> Sarah starts reading a 300-page book on Monday. She reads 20 pages a day on weekdays (Monday-Friday), and 40 pages a day on weekends (Saturday-Sunday). What exact day of the week will she finish the book?
>
> Think step by step, tracking the total pages read each day, before giving your final answer.

**Raw Output:**
Here is the day-by-day breakdown of Sarah's reading progress toward the 300-page goal:
Week 1:
  - Monday (Day 1): +20 pages (Total: 20)
  - Tuesday (Day 2): +20 pages (Total: 40)
  - Wednesday (Day 3): +20 pages (Total: 60)
  - Thursday (Day 4): +20 pages (Total: 80)
  - Friday (Day 5): +20 pages (Total: 100)
  - Saturday (Day 6): +40 pages (Total: 140)
  - Sunday (Day 7): +40 pages (Total: 180)
Week 2:
  - Monday (Day 8): +20 pages (Total: 200)
  - Tuesday (Day 9): +20 pages (Total: 220)
  - Wednesday (Day 10): +20 pages (Total: 240)
  - Thursday (Day 11): +20 pages (Total: 260)
  - Friday (Day 12): +20 pages (Total: 280)
  - Saturday (Day 13): Only 20 pages remain (300 − 280 = 20). Since she reads up to 40 pages on weekends, she completes the remaining 20 pages on this day (Total: 300).
Final Answer: Saturday

**Analysis:**
By explicitly prompting the model to think step by step, it generated a complete, verifiable audit trail. While it arrived at the same conclusion as the zero-shot prompt, the CoT approach eliminates the "black box" effect. In a production environment, this granular reasoning drastically reduces hallucinations on complex math and allows developers to trace and correct flawed logic.

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

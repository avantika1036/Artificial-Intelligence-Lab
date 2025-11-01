# Simple Expert System – Medical Diagnosis (Common Lisp)

## Problem Statement
This project implements a **rule-based expert system** in **Common Lisp** for basic **medical diagnosis**.  
It asks the user a series of yes/no questions about symptoms and suggests a possible illness based on the responses.

The goal is to simulate how a simple AI system can apply **expert reasoning rules** to reach conclusions.

---

## Approach
1. **Knowledge Base (Rules)**  
   - Encoded using `cond` statements that check symptom combinations.  
   - Each set of symptoms corresponds to a potential diagnosis:
     - **Flu** → Fever, cough, and tiredness  
     - **Common Cold** → Sore throat, runny nose, sneezing  
     - **Allergies** → Itchy eyes, sneezing, nasal congestion  

2. **User Interaction**  
   - The system interacts with the user using the `ask` function,  
     which prompts yes/no questions and reads user responses from the console.  

3. **Inference Mechanism**  
   - Implements **forward chaining**: the system asks questions and draws conclusions based on the facts collected.  
   - Uses conditional logic to evaluate which rule applies.  

4. **Execution Flow**  
   - `ask` → Queries user about a symptom.  
   - `diagnose` → Applies rule-based reasoning to determine illness.  
   - `solve` → Entry point that initiates diagnosis.

---

## Implementation Details
- **Language**: Common Lisp  
- **Main Functions**:
  - `ask` → Handles user input for each question.  
  - `diagnose` → Core reasoning function containing rules.  
  - `solve` → Starts the system interaction.  

- **Inference Type**: Deterministic rule-based (no uncertainty).  
- **Rule Structure**: Hard-coded logical conditions using `cond` and `and`.

---
---

## Code

```lisp
;; --- Simple Expert System in Common Lisp ---
;; Example: Medical Diagnosis System

(defun ask (question)
  (format t "~%~a (yes/no): " question)
  (string= (string-downcase (read-line)) "yes"))

(defun diagnose ()
  (format t "~%--- Simple Medical Diagnosis System ---~%")
  (cond
    ((and (ask "Do you have a fever?")
          (ask "Do you have a cough?")
          (ask "Do you feel tired?"))
     (format t "Diagnosis: You might have the FLU.~%"))
    
    ((and (ask "Do you have a sore throat?")
          (ask "Do you have a runny nose?")
          (ask "Do you sneeze often?"))
     (format t "Diagnosis: You might have the COMMON COLD.~%"))
    
    ((and (ask "Do you have itchy eyes?")
          (ask "Do you sneeze?")
          (ask "Do you have nasal congestion?"))
     (format t "Diagnosis: You might have ALLERGIES.~%"))
    
    (t
     (format t "Diagnosis: Unable to determine based on symptoms.~%"))))

(defun solve ()
  (diagnose))
```
---
## Time Complexity
- **O(n)** where *n* is the number of questions (symptom checks).  
- Each rule is evaluated sequentially.

## Space Complexity
- **O(1)** — Minimal memory usage for rule evaluation.

---

## Use Cases
- Demonstrates **basic expert systems** in AI.  
- Educational example for **knowledge representation and reasoning**.  
- Can be extended for:
  - Animal disease diagnosis  
  - Fault detection systems  
  - Medical chatbot prototypes  

---

## Limitations
- **Limited Knowledge Base** — Only a few predefined conditions.  
- **No Uncertainty Handling** — Does not account for probabilistic reasoning.  
- **Static Rules** — Rules must be manually modified to add new diseases or symptoms.  
- **No Learning Mechanism** — Does not adapt based on user feedback.

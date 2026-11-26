REASONING AGENT
===============
Decides whether a query follows from a knowledge base written in first-order logic.
Answer: True (the query is entailed) or False (it is not).
Method: the formulas are turned into clauses, and resolution refutation with unification
(fast version) searches for a contradiction between the knowledge base and the negated query.


FILES
-----
reasoning_agent.ipynb   the complete system; prints accuracy and inference time and writes the predictions
submission.csv          predictions (id, flag) of fast resolution on all three public datasets, every split:
                        RuleTaker train/test/ood_test, CounterBench, FOLIO train/validation (4029 examples)
submission_test.csv     created when the notebook is run on a hidden test file (not included)
report.pdf              description of the method and the results
README.txt              this file


REQUIREMENTS
------------
Python 3.9 or newer
    pip install pandas
Only to reproduce the reported results:
    pip install datasets huggingface_hub tqdm


HOW TO RUN
----------
1. Put the test file next to the notebook.
2. In the first code cell (Configuration) set:
       INPUT_CSV = "hidden_test.csv"     (name of the test file)
       RECHECK_RESULTS = False
3. Run all cells.
   The Inference cell (Section 9) prints the number of examples, the total inference time,
   the accuracy (if the file has labels) and the TP / FN / FP / TN table,
   and writes the predictions to submission_test.csv.


INPUT FORMAT
------------
A CSV file with the columns:
    id          example id
    context     the knowledge base, one formula per line
    statement   the query (one formula)
    label       optional: True / False (anything else, e.g. Uncertain, counts as False)

Example row:
    id:        1
    context:   Young(Bob)
               Kind(Bob)
               ∀x (Young(x) → Smart(x))
               ∀x (Smart(x) ∧ Kind(x) → Successful(x))
    statement: Successful(Bob)
    label:     True


LOGIC SYNTAX
------------
    not                ¬     ~
    and                ∧     &
    or                 ∨     |
    exclusive or       ⊕     xor
    implies            →     ->
    if and only if     ↔     <->
    for all            ∀x    forall x
    there exists       ∃x    exists x
    equality           =     ≠
    true / false       ⊤ ⊥   True False

Predicates are written Name(arg1, arg2); a single name such as P or rain is a proposition.
Quantified names are variables. Unquantified x, y, z, w are also variables:
"for all" in the knowledge base, "for some" in the query.


REPRODUCING THE REPORTED RESULTS
--------------------------------
1. pip install datasets huggingface_hub tqdm
2. Hugging Face login (needed once, because FOLIO is gated):
     a. Create a free account at https://huggingface.co
     b. Open https://huggingface.co/datasets/yale-nlp/FOLIO and accept the terms of use.
     c. Create an access token (type "Read") at https://huggingface.co/settings/tokens
     d. Log in once on the computer, in a terminal:   huggingface-cli login
        or in Python:   from huggingface_hub import login; login()
        and paste the token. If no token is saved, the notebook asks for it when it starts
        downloading. Never write the token into the notebook.
   RuleTaker and CounterBench are public and need no login.
3. Set RECHECK_RESULTS = True in the Configuration cell and run all cells.
   This also writes submission.csv: the fast-resolution predictions for all 4029 public examples.
4. Set RECHECK_RESULTS = False again afterwards.

What runs:
    Section 10  English templates: turn RuleTaker and CounterBench into logic
                (FOLIO already contains logic and is used as given)
    Section 11  loads the three datasets, runs plain and fast resolution on the same logic,
                and writes submission.csv
    Section 12  accuracy and time for every split
    Section 13  accuracy and time for each dataset and for all three together
    Section 14  FOLIO with 'Uncertain' removed vs counted as False

The tables are also saved as results_per_split.csv, results_overall.csv and
results_uncertain.csv. Plain resolution is much slower (about 2 minutes on CounterBench alone).

Machine used for the reported results: MacBook Air (Apple M5 chip), macOS, Python 3.9,
single CPU thread. Accuracy is the same on every run and every machine; the times change slightly
from run to run and depend on the machine.


DATASETS
--------
RuleTaker     https://huggingface.co/datasets/jise/ruletaker 
CounterBench  https://huggingface.co/datasets/CounterBench/CounterBench
FOLIO         https://huggingface.co/datasets/yale-nlp/FOLIO
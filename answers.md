# HW9 Practice Answers

## Exercise 1 — Predict the output (Linux)

I predicted the following before running the verification program:

| Item | Prediction | Verified on Linux |
| --- | --- | --- |
| a | `repo/.minigit/HEAD` | `repo/.minigit/HEAD` |
| b | `/HEAD` | `/HEAD` |
| c | `notes.txt` | `notes.txt` |
| d | `docs` | `docs` |
| e | `1` | `1` |
| f | `.txt` | `.txt` |

All six predictions matched the output of `ex1_verify.cpp`. In (b), the right-hand operand begins at the root, so it replaces `repo` instead of being appended to it. The test was run on Linux as required by the question.
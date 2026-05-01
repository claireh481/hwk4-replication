# ECON 470 Peer Review
## Claire Hamilton (Reviewing Sophie Kim's HW 4)

### Question 1: Code Review and Efficiency (10 points): Are there any parts of the code that seem inefficient or redundant? How could these sections be optimized for better performance or clarity?

Overall, Sophie's code is quite efficient and does not include very many redundancies. The main inefficiency I found was not in answering the questions themselves, but rather in the construction of the regression tables. For example, the same formatting function fmt_p() is defined 3 times (questions 6, 8 and 9), and the code extracts coefficients, p-values, observations, and R-squared values manually for each model. This makes the code longer and increases the risk of errors.

A more efficient approach could be to create a reusable helper function that takes a model and a list of variables, then returns the coefficient and p-value rows automatically. This would avoid repeating very similar lines for OLS 1, OLS 2, OLS 3, etc. The table styling code is also repeated across questions, so that could be turned into a reusable table-formatting function. The IV section (question 9) is slightly better because it uses get_coef() and get_p(), but the OLS section could use the same approach. Overall, the main optimization would be to reduce manual table construction and use helper functions or a package like modelsummary or stargazer to generate regression tables more automatically. This would make the code shorter and reduce the risk of errors like extracting the wrong coefficient or p-value.

### Question 2: Comparison of Results (10 points): How well do their results compare with your own? Are there any areas where the two analyses are particularly different?

### Question 3: Replicability and Documentation (10 points): Are you able to clone your peer’s repository and replicate their results?

Yes, I was easily able to clone Sophie's repository and replicate her results using the git clone function. The only extra step I had to take was creating the data/output folder in the cloned repository since this folder was not under version control due to the size of the data. After creating this data folder, I did not have to update any of the file paths in the code. I simply ran her submission1 code (which included her data build), then ran submissions 2 and 3 with no issues.

### Question 4: Diagnosing Replication (10 points): If you were unable to replicate their results using their code, what do you think is the central problem? If you were able to replicate, are there things that could have been changed (e.g., code structure, documentation, commenting, etc) that would have made it easier?

I was able to replicate Sophie's results using her code very easily. The only slight issue I had was that when I first looked at her repository structure, I was confused where she built the data set because her repository did not include a data build folder. However, after reading through her README for homework 4, I realized that she build the data within her submission 4.1 code. I think that for clarity and organizational purposes, it could make more sense to have a separate data build folder rather than indluding this code in submission 1. However, I appreciate that her README noted that the data build was part of her submission 1 code.

Some of her questions (e.g. 4 and 5) did not include any comments, which could have helped me more quickly understand what the code was doing. However, these questions were fairly straightforward and did not require a ton of code, so it was pretty easy to understand without comments.  

### Question 5: Final Thoughts (10 points): How might you update your own code based on your review of your peer’s work?

Reviewing Sophie's code made me realize some inefficiencies and reundancies in my own analysis. 
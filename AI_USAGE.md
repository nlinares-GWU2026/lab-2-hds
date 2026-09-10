# AI Usage Log

## Repository setup
- **Tool:** Claude (Sonnet 5)
- **What Claude helped with:** Walked through initializing the local git repo, configuring git identity and the `main` branch default, authenticating and creating the GitHub repo via the `gh` CLI (I wanted to try using this instead of the GitBash), and scaffolding the initial folder structure (`data/raw/`, `notebooks/`, `rendered/`, `.gitignore`).
**What I did myself:** Ran and verified each command, made the repo-naming and branch decisions.

## Logistic Regression setup and Odds Ratio
- **Tool:** Claude (Sonnet 5)
- **What Claude helped with:** I asked "How to fit the regression model" - after copy and pasting the resulting information about my data. I am not proficient in statistics or statistical coding and needed AI help to write a code for me.
```python
  # Define predictors and outcome
predictors = ['age', 'sysBP', 'totChol', 'currentSmoker', 'diabetes']
X = fram_clean[predictors]
X = sm.add_constant(X)  # adds an intercept term to the model
y = fram_clean['TenYearCHD']

# Fit the logistic regression model
model = sm.Logit(y, X).fit()
print(model.summary())
```
```
A few things about what this does, since it's worth understanding before you see the output:

sm.add_constant(X) adds a column of 1s to your predictor matrix, which lets the model estimate an intercept (the baseline log-odds of CHD when all predictors are 0). Without it, statsmodels forces the regression line through the origin, which isn't what you want here.
sm.Logit(y, X).fit() runs the actual model fitting. Logistic regression estimates coefficients in log-odds units, which is why the raw output looks less intuitive than a linear regression's coefficients — we'll convert those to odds ratios (much easier to interpret) in the next step.
The .fit() call uses maximum likelihood estimation under the hood and will print a line like Optimization terminated successfully before the summary table — that's normal, not an error.

Run it and paste back the summary table output. We'll use it to compute odds ratios and figure out which predictors actually came out significant, before moving on to the visualization.
```
- Claude also helped turn the results of the plot into and Odds ratio for interpretability after I copy and pasted the results into the chat.
```
Nice clean fit — and a genuinely interesting result: age, systolic BP, current smoking, and diabetes are all significant predictors (p < 0.001, except diabetes at p = 0.001), but total cholesterol isn't (p = 0.206) in this model. That's worth flagging now for your interpretation section later — it doesn't mean cholesterol is irrelevant to CHD, just that it's not adding significant predictive power once age, BP, smoking, and diabetes are already in the model (some of its effect is likely captured by those correlated variables).

 Raw logistic regression coefficients are in log-odds, which nobody reasons about intuitively. Exponentiating them converts to odds ratios — "how much the odds of CHD multiply by" for a one-unit increase in that predictor.

# Exponentiate coefficients and their confidence intervals to get odds ratios
odds_ratios = np.exp(model.params)
conf_int = np.exp(model.conf_int())
conf_int.columns = ['2.5%', '97.5%']

results_table = pd.concat([odds_ratios, conf_int], axis=1)
results_table.columns = ['Odds Ratio', '2.5%', '97.5%']
results_table['p-value'] = model.pvalues
print(results_table.round(3))
```
- **What I did myself**: Ran and verified the model worked after Claude wrote a script for the logistic regression model as well as the odds ratio and verified that the numbers made sense and that the output made sense. I also added markdown blocks to explain the logistic regression and the odds ratio. I chose the logistic regression approach, the predictors, ran and verified all code, and interpreted the missing data pattern.

## Visualization
- **Tool:** Claude (Sonnet 5)
- **What Claude helped with:** I asked "Can you help me create a visualization for this data?" I am new to creating visualizations and graphing and do not know how to use matplotlib so to make a nice graph I had Claude code and format one for me.
- **What I did myself:** I wrote out the plot and ran it through Jupyter notebooks to ensure it worked and looked presentable. The interpretation was entirely on my own. 

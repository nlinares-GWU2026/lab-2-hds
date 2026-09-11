# AI Usage Log

## Repository setup - GitHub
- **Tool:** Claude (Sonnet 5)
- **What Claude helped with:** Walked through initializing the local git repo, configuring git identity and the `main` branch default, authenticating and creating the GitHub repo via the `gh` CLI (I wanted to try using this instead of the GitBash), and scaffolding the initial folder structure (`data/raw/`, `notebooks/`, `rendered/`, `.gitignore`).
**What I did myself:** Ran and verified each command, made the repo-naming and branch decisions.

## Logistic Regression setup and Odds Ratio - Jupyter Notebook
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
```
```python
# Exponentiate coefficients and their confidence intervals to get odds ratios
odds_ratios = np.exp(model.params)
conf_int = np.exp(model.conf_int())
conf_int.columns = ['2.5%', '97.5%']

results_table = pd.concat([odds_ratios, conf_int], axis=1)
results_table.columns = ['Odds Ratio', '2.5%', '97.5%']
results_table['p-value'] = model.pvalues
print(results_table.round(3))
```
```
What to expect: age's odds ratio should come out a little above 1 (each additional year of age multiplies CHD odds by a small amount — but that compounds over decades), currentSmoker and diabetes should show more noticeably elevated odds ratios (roughly 1.5–2x territory, given their coefficients), and totChol's confidence interval should cross 1 (consistent with it not being significant above).

Run it and paste back the table.
```
- **What I did myself**: Ran and verified the model worked after Claude wrote a script for the logistic regression model as well as the odds ratio and verified that the numbers made sense and that the output made sense. I also added markdown blocks to explain the logistic regression and the odds ratio. I chose the logistic regression approach, the predictors, ran and verified all code, and interpreted the missing data pattern.

## Visualization - Jupyter Notebook
- **Tool:** Claude (Sonnet 5)
- **What Claude helped with:** I asked: "Can you help me create a visualization for this data?" I am new to creating visualizations and graphing and don't use matplotlib, so I asked Claude to code and format a graph for me.
```
Good — that visualization should build directly on the model you just fit, not be a generic "plot a column" afterthought. Since currentSmoker came out as one of the strongest significant predictors, let's plot predicted CHD risk by age, comparing smokers vs. non-smokers — that shows both a continuous relationship (age) and a categorical one (smoking) in a single plot, and ties straight back to your regression output.
```
```python
# Build a range of ages to predict across, holding other predictors at
# representative values so the plot isolates age and smoking status
age_range = np.arange(fram_clean['age'].min(), fram_clean['age'].max() + 1)
mean_sysBP = fram_clean['sysBP'].mean()
mean_totChol = fram_clean['totChol'].mean()

pred_smoker = pd.DataFrame({
    'const': 1,
    'age': age_range,
    'sysBP': mean_sysBP,
    'totChol': mean_totChol,
    'currentSmoker': 1,
    'diabetes': 0
})
pred_nonsmoker = pred_smoker.copy()
pred_nonsmoker['currentSmoker'] = 0

# Column order must match X exactly, or predict() will misalign the coefficients
pred_smoker = pred_smoker[X.columns]
pred_nonsmoker = pred_nonsmoker[X.columns]

risk_smoker = model.predict(pred_smoker)
risk_nonsmoker = model.predict(pred_nonsmoker)
```
```
Run that cell — no visible output expected, just two arrays of predicted probabilities computed silently.

Add another code cell for the actual plot:
```
```python
plt.figure(figsize=(8, 5))
plt.plot(age_range, risk_smoker, label='Current smoker', color='firebrick')
plt.plot(age_range, risk_nonsmoker, label='Non-smoker', color='steelblue')
plt.xlabel('Age (years)')
plt.ylabel('Predicted 10-year CHD risk')
plt.title('Predicted 10-Year CHD Risk by Age and Smoking Status')
plt.legend(title='Smoking status')
plt.grid(alpha=0.3)
plt.tight_layout()
plt.show()
```
```
A couple of things worth knowing about this one:

plt.tight_layout() prevents axis labels and the title from getting clipped off the edge of the figure — cheap insurance, worth including as a habit.
The gap between the two lines is the visual representation of that currentSmoker odds ratio (1.673) you computed in Step 16 — it should widen somewhat at higher ages, since the model is multiplicative on the odds scale, not the probability scale.
```
- **What I did myself:** I wrote out the plot and ran it through Jupyter notebooks to ensure it worked and looked presentable. The interpretation was entirely on my own.

## Logistic Regression setup and Odds Ratio - R Markdown
- **Tool:** Claude (Sonnet 5)
- **What Claude helped with:** I asked Claude to "help me format the logistic regression for R in RStudio for the rmd notebook." Same issue with my lack of coding experience and statistical tests. In between, I pasted the results of the logistic regression and the odds ratio into the prompt.
```r
model <- glm(TenYearCHD ~ age + sysBP + totChol + currentSmoker + diabetes,
             data = fram_clean, family = binomial)
summary(model)
```
```
A few R-specific things worth knowing here, since this looks quite different from the Python version even though it's doing the same thing:

R's glm() uses formula notation: TenYearCHD ~ age + sysBP + totChol + currentSmoker + diabetes reads as "model TenYearCHD as a function of these five predictors." This is one call, versus Python's multi-step sm.add_constant() + sm.Logit() + .fit() — a real difference worth noting for your comparison section later.
family = binomial is what tells glm() to fit a logistic regression specifically — glm() is a general framework for many model types (Poisson, gamma, etc.), and binomial with the default logit link is what makes this equivalent to statsmodels' Logit().
R automatically includes an intercept — no equivalent of sm.add_constant() needed. That's a real source of a subtle bug if you ever forget it going the other direction (Python) and wonder why your model looks like it's missing a term.

Run the chunk and paste back the output. The coefficients (not yet converted to odds ratios) should be very close to the Python ones — same sign, similar magnitude — though not necessarily bit-for-bit identical, since the two implementations may differ slightly in convergence tolerance or numerical optimization method. Large differences would be a red flag worth investigating; tiny ones in the 4th decimal place are normal.

Matches almost to the decimal — age: 0.065464 (Python: 0.0655), sysBP: 0.016780 (Python: 0.0168), diabetes: 0.735078 (Python: 0.7351), all the way down. That's exactly the confirmation you want: two independent implementations converging on the same answer is good evidence the model itself is solid, not an artifact of one tool's defaults
```
```r
odds_ratios <- exp(coef(model))
conf_int <- exp(confint(model))

results_table <- cbind(`Odds Ratio` = odds_ratios, conf_int)
round(results_table, 3)
```
```
Run it. One thing to expect: R's confint() computes profile likelihood confidence intervals by default, while statsmodels' .conf_int() used Wald intervals (based on standard errors) — these are two different, both statistically valid methods, so your R and Python confidence intervals may differ slightly in the decimals even though the odds ratios themselves should match closely. This is actually a great, concrete thing to mention in your language-comparison section later — a real methodological difference between the two tools' defaults, not a mistake.

You may also see a message like Waiting for profiling to be done... print before the table — that's normal, not an error, just confint() telling you it's working.

Odds ratios match closely — age: 1.068, sysBP: 1.017, currentSmoker: 1.673, diabetes: 2.086, all identical to the Python output out to 3 decimals. And exactly as predicted, the confidence intervals differ slightly at the edges (currentSmoker: 1.374–2.040 here vs. 1.374–2.038 in Python; diabetes: 1.321–3.242 here vs. 1.333–3.263 in Python) — that's the Wald-vs-profile-likelihood difference showing up, not an error.
```
- **What I did myself:** Ran and verified the model worked after Claude wrote a script for the logistic regression model as well as the odds ratio and verified that the numbers made sense and that the output made sense. I also added markdown blocks to explain the logistic regression and the odds ratio. I chose the logistic regression approach, the predictors, ran and verified all code, and interpreted the missing data pattern.

## Visualization - R Markdown
- **Tool:** Claude (Sonnet 5)
- **What Claude helped with:** I asked: "Can you help me create a visualization for this data?" I am new to creating visualizations and graphing in R as well, so I asked Claude to code and format a graph for me.
```r
age_range <- seq(min(fram_clean$age), max(fram_clean$age))
mean_sysBP <- mean(fram_clean$sysBP)
mean_totChol <- mean(fram_clean$totChol)

pred_smoker <- data.frame(age = age_range, sysBP = mean_sysBP,
                           totChol = mean_totChol, currentSmoker = 1, diabetes = 0)
pred_nonsmoker <- pred_smoker
pred_nonsmoker$currentSmoker <- 0

risk_smoker <- predict(model, newdata = pred_smoker, type = "response")
risk_nonsmoker <- predict(model, newdata = pred_nonsmoker, type = "response")
```

```{r plot-risk}
plot(age_range, risk_smoker, type = "l", col = "firebrick", lwd = 2,
     xlab = "Age (years)", ylab = "Predicted 10-year CHD risk",
     main = "Predicted 10-Year CHD Risk by Age and Smoking Status",
     ylim = c(0, max(risk_smoker, risk_nonsmoker)))
lines(age_range, risk_nonsmoker, col = "steelblue", lwd = 2)
legend("topleft", legend = c("Current smoker", "Non-smoker"),
       col = c("firebrick", "steelblue"), lwd = 2, title = "Smoking Status")
grid()
```
```
A few real differences from the Python side worth noticing, since they're the kind of thing your comparison section should mention:

predict(model, newdata = ..., type = "response") is R's equivalent of model.predict(...) — type = "response" specifically means "give me probabilities," not raw log-odds. Leaving that argument off is a common mistake that silently gives you the wrong scale.
Base R plotting is more manual than matplotlib: plot() only draws the first line, lines() adds the second one on top of the existing plot, and legend() is a fully separate call — there's no label= argument that auto-builds a legend the way matplotlib's does. More steps, but also more explicit control over each piece.
```
- **What I did myself:** I wrote out the plot and ran it through RStudio to ensure it worked and looked presentable. The interpretation and comparison between workflows was entirely on my own.

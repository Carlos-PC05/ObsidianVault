#AI 
# Slides

[chapter1](attachment:5aa50c36-163f-4743-94c7-1a5476bd3616:chapter1_\(2\).pdf)

[chapter2](attachment:e0c5da65-d19b-4fda-9c0b-8ae4d8f767c8:chapter2_\(2\).pdf)

[chapter3.pdf](attachment:cd2e9d53-e5b4-4817-9815-526b77ef2ddc:chapter3.pdf)

[chapter4.pdf](attachment:ad471656-8e4a-4cb8-a6b2-95681466c573:chapter4.pdf)

# Notes

## **Foundations of Explainable AI**

### Introduction to explainable AI

#### Decision trees vs. neural networks

Build a decision tree classifier to classify income levels based on multiple features including age, education level, and hours worked per week, and extract the learned rules that explain the decision. Then, compare its performance with an MLPClassifier trained on the same data.

```python
"""Decision Tree"""
model = DecisionTreeClassifier(random_state=42, max_depth=2)
model.fit(X_train, y_train)

# Extract the rules
rules = export_text(model, feature_names = list(X_train.columns))
print(rules)

y_pred = model.predict(X_test)

# Compute accuracy
accuracy = accuracy_score(y_test, y_pred)
print(f"Accuracy: {accuracy:.2f}")

--------------------
"""Neural Network"""
model = MLPClassifier(hidden_layer_sizes=(36, 12), random_state=42)
# Train the MLPClassifier
model.fit(X_train, y_train)

# Derive the predictions on the test set
y_pred = model.predict(X_test)

# Compute the test accuracy
accuracy = accuracy_score(y_test, y_pred)
print(f"Accuracy: {accuracy:.2f}")
```

### Explainability in linear models

### Computing feature impact with linear regression

As a data scientist at an insurance company, your task is to build and explain a linear regression model that estimates insurance charges based on features like age, BMI, and smoking status by analyzing the model's coefficients to determine the impact of each feature on the predictions.

```python
# Standardize the training data
scaler = MinMaxScaler()
X_train_scaled = scaler.fit_transform(X_train)
model = LinearRegression()

# Fit the model
model.fit(X_train_scaled, y_train)

# Derive coefficients
coefficients = model.coef_
feature_names = X_train.columns

# Plot coefficients
plt.bar(feature_names, coefficients)
plt.show()
```

![[Pasted image 20260806113112.png|422]]

#### Computing feature impact with logistic regression

Continuing your work at the insurance company, you built a predictive model to identify whether an individual is a smoker or not. Now, you need to analyze the model to determine the relevant factors influencing smoking status, helping the company assess risk more accurately and tailor insurance policies accordingly.

```python
scaler = MinMaxScaler()
X_train_scaled = scaler.fit_transform(X_train)

model = LogisticRegression()
model.fit(X_train_scaled, y_train)

# Derive coefficients
coefficients = model.coef_[0]
feature_names = X_train.columns

# Plot coefficients
plt.bar(feature_names, coefficients)
plt.show()
```
![[Pasted image 20260806113148.png|440]]
### Explainability in tree-based models

#### Computing feature importance with decision trees

You built a decision tree classifier to identify patients at risk of heart disease using the heart disease dataset. Now you need to explain the model by analyzing feature importance to determine the key factors for predicting heart disease, enabling more targeted healthcare interventions.

```python
model = DecisionTreeClassifier(random_state=42)
model.fit(X_train, y_train)

# Derive feature importances
feature_importances = model.feature_importances_
feature_names = X_train.columns

# Plot the feature importances
plt.barh(X_train.columns, feature_importances)
plt.show()
```


#### Computing feature importance with random forests

As a data scientist at a financial consulting firm, you have developed a random forest classifier that classifies individuals according to their income levels. Now, you need to explain the model by  
analyzing feature importance to determine the key factors for predicting income, enabling more targeted market segmentation and improving strategic decision-making.

```python
model = RandomForestClassifier(random_state=42)
model.fit(X_train, y_train)

# Derive feature importances
feature_importances = model.feature_importances_

# Plot the feature importances
plt.bar(X_train.columns, feature_importances)
plt.show()
```

## Model-Agnostic Explainability

### Permutation importance

# Permutation importance for MLPClassifier

Your task is to use permutation importance to identify which features are most impactful in predicting heart disease with an MLPClassifier.

```python
from sklearn.neural_network import MLPClassifier
from sklearn.inspection import permutation_importance

model = MLPClassifier(hidden_layer_sizes=(10), random_state=1)
model.fit(X, y)

# Compute the permutation importance
result = permutation_importance(
    model,
    X, y,
    n_repeats = 10,
    random_state = 1
)

# Plot feature importances
plt.bar(X.columns, result.importances_mean)
plt.xticks(rotation=45)
plt.show()
```

#### Coefficients vs. permutation importance

Now, you will compare the patterns identified by permutation importance with the model coefficients from a logistic regression trained on the heart disease dataset. A helper function `plot_importances()` is called at the end of the script to plot importances on the same plot.

```python
from sklearn.inspection import permutation_importance

# Extract and store model coefficients
coefficients = model.coef_[0]

# Compute permutation importance on the test set
perm_importance = permutation_importance(
    model,
    X, y,
    n_repeats = 20,
    random_state = 1
)

# Compute the average permutation importance
avg_perm_importance = perm_importance.importances_mean

plot_importances(coefficients, avg_perm_importance)
```

![[Pasted image 20260806113215.png]]

### SHAP Explainability

#### Finding key medical charge predictors with SHAP

SHAP values provide insightful explanations for predictions made by machine learning models. Now, you'll utilize SHAP to decipher the influence of various features in a RandomForestRegressor `model` on predicting insurance charges.

```python
import shap

# Create a SHAP Tree Explainer
explainer = shap.TreeExplainer(model)

# Calculate SHAP values
shap_values = explainer.shap_values(X)

# Calculate mean absolute SHAP values
mean_abs_shap = np.abs(shap_values).mean(axis = 0)

plt.bar(X.columns, mean_abs_shap)
plt.title('Mean Absolute SHAP Values for RandomForest')
plt.xticks(rotation=45)
plt.show()
```

#### Finding key heart disease predictors with SHAP

Your task is to use SHAP to understand how different features in a pre-trained RandomForestClassifier `model` influence predictions for heart disease.

```python
import shap

# Create a SHAP Tree Explainer
explainer = shap.TreeExplainer(model)

# Calculate SHAP values
shap_values = explainer.shap_values(X)

# Calculate mean absolute SHAP values
mean_abs_shap = np.abs(shap_values[:, :, 1]).mean(axis = 0)

plt.bar(X.columns, mean_abs_shap)
plt.title('Mean Absolute SHAP Values for RandomForest')
plt.xticks(rotation=45)
plt.show()
```

### SHAP Kernel explainer

#### Kernel explainer for MLPRegressor

Given your familiarity with the admissions dataset, you will employ SHAP's Kernel Explainer to explain an MLPRegressor trained on this data. This method will allow you to critically assess how different features impact the model's predictions and verify these insights against your existing understanding of the dataset.

```python
import shap

# Create a SHAP Kernel Explainer
explainer = shap.KernelExplainer(
    model.predict,
    shap.kmeans(X, 10)
)

# Calculate SHAP values
shap_values = explainer.shap_values(X)

# Calculate mean absolute SHAP values
mean_abs_shap = np.abs(shap_values).mean()

plt.bar(X.columns, mean_abs_shap)
plt.title('Mean Absolute SHAP Values for MLPRegressor')
plt.xticks(rotation=45)
plt.show()
```

#### Kernel explainer for MLPClassifier

Neural networks can be very accurate, but understanding their decisions can be challenging due to complexity. Now, you'll leverage the SHAP Kernel Explainer to interpret an MLPClassifier trained on the adult income dataset. You will explore which of the three features—age, education, or hours worked per week—is most important for predicting income according to this model.

```python
import shap

# Create a SHAP Kernel Explainer
explainer = shap.KernelExplainer(
    model.predict_proba,
    shap.kmeans(X, 10)
)

# Calculate SHAP values
shap_values = explainer.shap_values(X)

# Calculate mean absolute SHAP values
mean_abs_shap = np.abs(shap_values[:, :, 1]).mean(axis = 0)

plt.bar(X.columns, mean_abs_shap)
plt.title('Mean Absolute SHAP Values for MLPClassifier')
plt.xticks(rotation=45)
plt.show()
```

#### SHAP vs. model-specific approaches

You will compare the explanatory power of SHAP values from a Kernel Explainer with the logistic regression coefficients, both trained on the income dataset. A helper function `plot_importances()` is called at the end of the script to plot importances on the same plot.

```python
import shap

# Extract model coefficients
coefficients = model.coef_[0]

# Compute SHAP values
explainer = shap.KernelExplainer(
    model.predict_proba,
    shap.kmeans(X, 10)
)
shap_values = explainer.shap_values(X)

# Calculate mean absolute SHAP values
mean_abs_shap = np.abs(shap_values[:, :, 1]).mean(axis = 0)

plot_importances(coefficients, mean_abs_shap)
```

![[Pasted image 20260806113246.png|464]]

### Visualizing SHAP explainability

#### Feature Importance plots for admissions analysis

As part of the data science team at a university, your task is to evaluate which factors truly drive admissions decisions and which ones are less important. While the admissions committee knows CGPA plays a key role, they want to confirm this and uncover any other important factors that may influence outcomes. Using a RandomForestRegressor `model`, you'll visualize feature importance to clearly identify which aspects of applicants' profiles matter most and which have less of an impact on the decision process.

```python
model = RandomForestRegressor(random_state=42)
model.fit(X_train, y_train)

# Derive shap values
explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X_train)

# Plot the feature importance plot
shap.summary_plot(shap_values, X_train, plot_type="bar")

```

#### Analyzing feature effects with beeswarm plots

Continuing your role as a data scientist at the university, you now shift focus to a more detailed analysis of individual feature effects on admissions outcomes. While identifying key factors influencing admissions decisions was crucial, delving deeper allows us to understand how variations in these factors specifically affect predictions. This additional insight will help answer questions like how changes in test scores or CGPA influence the likelihood of admission, providing a clearer picture for making informed decisions and policy recommendations.

```python
model = RandomForestRegressor(random_state=42)
model.fit(X_train, y_train)

# Derive shap values
explainer = shap.TreeExplainer(model)
shap_values = explainer.shap_values(X_train)

# Plot the beeswarm plot
shap.summary_plot(shap_values, X_train, plot_type="dot")
```

![[Pasted image 20260806113411.png|433]]

#### Assessing impact with partial dependence plots

Building on your previous contributions, your next task is to explore how 'CGPA' and 'University Rating' influence admissions decisions. Our earlier analysis showed that 'CGPA' is the most important predictor, while 'University Rating' is the least important one. Using a partial  
dependence plot, we can see how changes in these features affect the probability of admission, giving the admissions committee nuanced insights into their impact.

```python
from sklearn.ensemble import RandomForestRegressor
import shap

model = RandomForestRegressor(random_state=42)
model.fit(X_train, y_train)

# Generate the partial dependence plot for CGPA
shap.partial_dependence_plot("CGPA", model.predict, X_train)
```

![[Pasted image 20260806113421.png|400]]

## Local Explainability

### Local Explainability with SHAP

#### SHAP for explaining income levels

Practice using SHAP to analyze and visualize how each feature influences a trained model's predictions on a single sample from the income dataset, using a waterfall plot for deeper insight into feature contributions.

```python
import shap

# Create the SHAP explainer
explainer = shap.KernelExplainer(model.predict_proba, shap.kmeans(X, 10))

# Compute SHAP values for the first instance in the test dataset
shap_values = explainer.shap_values(X.iloc[0,:])

# Plot the SHAP values using a waterfall plot
shap.waterfall_plot(shap.Explanation(values=shap_values[:, 1], 
                    base_values=explainer.expected_value[1], 
                    data=X.iloc[0, :], 
                    feature_names=X.columns))
```

![[Pasted image 20260806113431.png|431]]

### Local explainability with LIME

#### Interpreting regressors locally

You are provided with a KNN regressor model that predicts health insurance costs based on features such as age, sex, BMI, number of children, and smoking status. Your task is to assess how each feature affects the prediction for a given sample.

```python
from lime.lime_tabular import LimeTabularExplainer

sample_data_point = X.iloc[2, :]

# Create the explainer
explainer = LimeTabularExplainer(
    X.values,
    feature_names = X.columns,
    mode = 'regression'
)

# Generate the explanation
exp = explainer.explain_instance(
    sample_data_point.values,
    model.predict
)

# Display the explanation
exp.as_pyplot_figure()
plt.show()
```

![[Pasted image 20260806113440.png|377]]

#### Interpreting classifiers locally

Now you have a KNN classifier model that predicts the presence of heart disease based on features like age, sex, chest pain type, and blood pressure. Your task is to assess how each feature affects the  
prediction for a given sample.

```python
from lime.lime_tabular import LimeTabularExplainer

sample_data_point = X.iloc[2, :]

# Create the explainer
explainer = LimeTabularExplainer(
    X.values,
    feature_names = X.columns,
    mode = 'classification'
)

# Generate the explanation
exp = explainer.explain_instance(
    sample_data_point.values,
    model.predict_proba
)

# Display the explanation
exp.as_pyplot_figure()
plt.show()
```

### Text and image explainability with LIME

#### Explaining sentiment analysis predictions

You are provided with a `model` that classifies product reviews as expressing positive or negative sentiment. Your task is to use LIME to identify which words in a given `text_instance` most influence the model's predictions.

```python
from lime.lime_text import LimeTextExplainer

text_instance = "Amazing battery life and the camera quality is perfect! I highly recommend this smartphone."

# Create a LIME text explainer
explainer = LimeTextExplainer()

# Generate the explanation
exp = explainer.explain_instance(text_instance, model_predict, num_features=5)

# Display the explanation
exp.as_pyplot_figure()
plt.show()
```

#### Explaining food image predictions

You have a `model` that classifies food, and your task is to use LIME to identify the regions the model mostly focuses on while making its prediction for the `image` below.

```python
from lime import lime_image
np.random.seed(10)

# Create a LIME explainer
explainer = lime_image.LimeImageExplainer()

# Generate the explanation
explanation = explainer.explain_instance(image, model.predict, hide_color=0, num_samples=50)

# Display the explanation
temp, _ = explanation.get_image_and_mask(explanation.top_labels[0], hide_rest = True)
plt.imshow(temp)
plt.title('LIME Explanation')
plt.axis('off')
plt.show()
```

![[Pasted image 20260806113500.png|384]]

## Advanced topics in explainable AI

### Explainability metrics

#### Evaluating SHAP explanation consistency

Evaluate the consistency of feature importance explanations using SHAP values across two different subsets of the insurance dataset.

```python
# Calculate SHAP values and feature importance for model1
explainer1 = shap.TreeExplainer(model1)
shap_values1 = explainer1.shap_values(X1)
feature_importance1 = np.abs(shap_values1).mean(axis = 0)

# Calculate SHAP values and feature importance for model2
explainer2 = shap.TreeExplainer(model2)
shap_values2 = explainer2.shap_values(X2)
feature_importance2 = np.abs(shap_values2).mean(axis = 0)

# Consistency calculation
consistency = cosine_similarity([feature_importance1], [feature_importance2])
print("Consistency between SHAP values:", consistency)
```

#### Evaluating faithfulness with LIME

You are provided with a LIME explanation for a sample `X_instance` from the income dataset. Since gender is the most important predictor, you need to change its value and compute `faithfulness` to determine how well the explanation aligns with the model's behavior for that instance.

```python
original_prediction = model.predict_proba(X_instance)[0, 1]
print(f"Original prediction: {original_prediction}")

# Change the gender value to 0 
X_instance['gender'] = 0

# Generate the new prediction
new_prediction = model.predict_proba(X_instance)[0, 1]
print(f"Prediction after perturbing 'gender': {new_prediction}")

# Estimate faithfulness
faithfulness_score = np.abs(original_prediction - new_prediction)
print(f"Local Faithfulness Score: {faithfulness_score}")
```

### Explaining unsupervised models

#### Feature impact on cluster quality

Explore how individual features impact the clustering performance of a KMeans model. The dataset `X` is used for customer segmentation based on three features: income, number of kids, and number of teens in the house.

```python
kmeans = KMeans(n_clusters=5, random_state=10, n_init=10).fit(X)
# Derive the original silhouette score
original_score = silhouette_score(X, kmeans.labels_)

for i in range(X.shape[1]):
  	# Remove feature at index i
    X_reduced = np.delete(X, i, axis = 1)
    kmeans.fit(X_reduced)
    # Compute the new silhouette score
    new_score = silhouette_score(X_reduced, kmeans.labels_)
    # Compute the feature's impact
    impact = original_score - new_score
    print(f'Feature {column_names[i]}: Impact = {impact}')
```

#### Feature importance in clustering with ARI

Leverage the Adjusted Rand Index (ARI) to quantitatively measure the impact of each feature's removal on cluster assignments in the customer dataset you've worked with in the previous exercise, pre-loaded in `X`.

```python
kmeans = KMeans(n_clusters=5, random_state=10, n_init=10).fit(X)
# Derive original clusters
original_clusters = kmeans.predict(X)

for i in range(X.shape[1]):
  	# Remove feature at index i
    X_reduced = np.delete(X, i, axis = 1)
    # Derive reduced clusters
    reduced_clusters = kmeans.fit_predict(X_reduced)
    # Derive feature importance
    importance = 1 - adjusted_rand_score(original_clusters, reduced_clusters)
    print(f'{column_names[i]}: {importance}')
```

### Explaining chat-based generative AI models

#### Chain-of-thought to discover reasoning

You're presented with a common math problem to determine the number of fruits in a basket. You want ChatGPT to solve this problem to you, while providing a step by step reasoning.

```python
# Complete the chain-of-thought prompt
prompt = "In a basket, there are twice as many apples as oranges and three times as many oranges as bananas. If there are 6 bananas in the basket, how many fruits are there in total? Give me a step by step reasoning of how would you handle this problem."

response = get_response(prompt)
print(response)
```

#### Self-consistency to assess confidence

The `prompt` below aims to ask ChatGPT to classify the sentiment of a review. You have to estimate the confidence of ChatGPT in each class using self-consistency.

```python
prompt = """Classify the following review as positive or negative. 
You should reply with either "positive" or "negative", nothing else.
Review: 'The coffee was excellent, but the service left much to be desired.'"""

responses = []
for i in range(5):
    sentiment = get_response(prompt)
    # Append the response to the responses list
    responses.append(sentiment.lower())

# Compute the confidence for each sentiment
confidence = {
    'positive': responses.count('positive') / len(responses),
    'negative': responses.count('negative') / len(responses)
}

print(confidence)
```
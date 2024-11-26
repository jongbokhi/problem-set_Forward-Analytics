# Problem Set – Data Scientist Position – Forward Analytics

## Overview

This project focuses on enriching a dataset with additional company information using external APIs and text processing techniques. Specifically, I aimed to enhance data points like the **Legal Entity Identifier (LEI)**, **Entity Type**, **Industry Classification**, and **Company Size**. I conducted two experiments, each building on a different approach to improve data quality and enrichment. Here, I detail the methodology, tools, and results of each experiment, along with the challenges encountered.

## Experiment 1: Basic API-Based Enrichment

### Objective

The first experiment was designed as a simple approach to explore the feasibility of using APIs for data enrichment.

### Methodology

1. **Data Standardization**: 
   - Created a list of common suffixes associated with companies, such as `"Ltd", "PLC", "Corp", "LLC", "Inc", "Limited", "Co"`.
   - Cleaned and standardized the `Entity_Name` column by removing these suffixes, along with extra whitespace and non-alphabetic characters (e.g., `",", ".", "\""`) for consistent naming.
   
2. **Duplicate Removal**:
   - After standardizing entity names, I dropped duplicate entries based on the cleaned names to simplify the dataset and avoid redundant API requests.

3. **Data Enrichment Using APIs**:
   - **GLEIF LEI API**: Retrieved and added the **Legal Entity Identifier (LEI)** for entities with available data.
   - **People Data Labs (PDL) Company Enrichment API**: Used to gather additional data points such as **Entity Type**, **Industry Classification**, **Company Size**, and a secondary **Name** column (from PDL’s data) for validation purposes.

4. **Result Validation**:
   - Added a column for `PDL_Name` (name retrieved from People Data Labs) to cross-check and validate entity names.
   - Visualized the **enrichment rate** for each column using a bar graph, comparing the increase in populated values before and after enrichment.

### Results

- **LEI** column showed the highest increase in enrichment, indicating that the GLEIF API provided more comprehensive data than other attributes.
- **Entity Type**, **Industry Classification**, and **Company Size** columns showed less significant improvements.
- Due to API usage limits, I exhausted the free credit for People Data Labs and could not perform full enrichment.

![increase_rate](https://github.com/user-attachments/assets/acb18f07-5fe4-484f-9ca4-ffcfaf395401)

### Insights

While this initial approach effectively used APIs to enrich the dataset, the coverage was limited, particularly for industry classifications and entity types, likely due to API constraints and data availability.

---

## Experiment 2: Text-Based Classification for Industry Type

### Objective

After observing limited enrichment in industry classifications in Experiment 1, I developed a text-based method to classify **Industry Type** based on the `Entity_Name` column.

### Methodology

1. **Data Tokenization**:
   - Preprocessed the `Entity_Name` column by removing non-alphabetic characters.
   - Tokenized each company name into individual words to facilitate keyword-based industry classification.

2. **Suffix Analysis**:
   - Analyzed and updated the list of suffixes in `Entity_Name` based on frequency, removing rare or unnecessary words from the tokenized data.

3. **Industry Classification**:
   - Created a list of common keywords associated with industries (e.g., "Technology," "Finance," "Healthcare") to classify entities based on keywords.
   - Developed a simple rule-based classifier that assigns an **Industry Type** to each entity if its name contains any of these industry-related keywords.

4. **Iterative Improvement**:
   - Identified unique values in the `Industry Classification` column and refined the suffix removal list and industry keyword list to reduce noise and improve classification accuracy.

### Challenges

- **Ambiguity in Keywords**: Some industry keywords overlapped with generic company names, leading to misclassifications.
- **Incomplete Information**: The keyword-based method worked for common industries but struggled with niche industries or less descriptive company names.

### Insights

The text-based approach helped fill gaps in industry classification that the API-based method left. This approach can be useful in conjunction with API methods, especially for datasets where external data is limited or unavailable.


## Experiment 3: Advanced Classification Approaches

### Objective

The goal of this experiment was to explore advanced classification methods to improve the accuracy of **Industry Classification**. This included using fuzzy string matching for data cleaning and employing various machine learning and pre-trained transformer models for text classification. Additionally, hyperparameter optimization techniques, such as Optuna, were tested to enhance model performance.

### Methodology

1. **Duplicate Removal Using Fuzzy String Matching:**

   - Fuzzy string matching was applied to detect and remove duplicate entries in the dataset.
   - Different methods like `ratio`, `partial_ratio`, and `token_sort_ratio` were tested with a similarity threshold of 90%.
   - The approach was ultimately deemed ineffective in significantly improving data quality for classification tasks.

2. **Text Multi-classification Using Pre-trained Models:**

   **(1) DistilBERT**  
   - A pre-trained DistilBERT model was fine-tuned on the company dataset for text classification tasks.  
   - Despite fine-tuning, the model exhibited poor generalization and overfitting, as seen in the training and validation metrics.
   

   **(2) Zero-shot Classification (Facebook BART-large-MNLI)**  
   - Zero-shot classification was explored for industry group prediction using "facebook/bart-large-mnli."  
   - While predictions aligned to candidate labels, the overall accuracy was low, demonstrating limited effectiveness for the dataset.  
   
   ![before smote_acc](https://github.com/user-attachments/assets/87ff40d9-2f28-4f92-86f6-64729c8e7865)

   ![before smote_loss](https://github.com/user-attachments/assets/afdab58b-73f4-4373-b795-a551c2d09292)

   **(3) XGBoost**  
   - Text features were likely converted into numerical representations and classified using XGBoost.  
   - Optuna was used for hyperparameter optimization to improve model performance. Despite this effort, the results remained suboptimal, suggesting the method was not significantly better than the transformer-based approaches.  

###  Updated Challenges
- **Too many class of industry**
  Currently, the **Industry Classification** contains too many labels, making it necessary to establish specific criteria for consolidation. Based on my own judgment, I merged some classes into broader categories, while those that were not consolidated were    grouped under the "Other" label. This process can be reviewed in the **preprocessed_data** notebook.
- **The machine's physical limits:** The dataset is too large, resulting in limitations on the local machine.
- **Ambiguity in Keywords**: Some industry keywords overlapped with generic company names, leading to misclassifications.
- **Incomplete Information**: The keyword-based method worked for common industries but struggled with niche industries or less descriptive company names.

---

### Updated Challenges

1. **Excessive Industry Labels**:  
   The **Industry Classification** currently contains an overwhelming number of labels, necessitating consolidation for practical use. Classes were merged into broader categories based on logical groupings, while remaining unclassified labels were grouped under "Other." Details of this process can be found in the **preprocessed_data** notebook.

2. **Computational Limitations**:  
   The size of the dataset exceeds the processing capacity of a local machine, leading to significant constraints in computation and analysis.

3. **Keyword Ambiguity**:  
   Some industry-related keywords overlapped with generic terms in company names, resulting in misclassifications and reducing the accuracy of text-based approaches.

4. **Data Incompleteness**:  
   While the keyword-based method was effective for common industries, it struggled with niche industries or company names lacking descriptive terms, highlighting the need for supplemental data sources or advanced models.

---

## Conclusion and Future Directions

The experiments outlined complementary approaches to data enrichment and classification:

1. **Experiment 1** demonstrated the potential of APIs like GLEIF and People Data Labs for enriching datasets with structured information such as LEI and company attributes. However, API-based methods showed limited coverage and scalability issues, particularly for attributes like industry classification and company size.

2. **Experiment 2** revealed that text-based classification methods could fill gaps left by API-based enrichment, particularly for industry classification. However, these methods require further refinement to address ambiguity and ensure consistent accuracy, especially for less descriptive or niche entities.

3. **Experiment 3** explored advanced classification techniques, including fine-tuned transformer models, zero-shot classification, and machine learning approaches like XGBoost. Despite these efforts, performance improvements were limited, even with hyperparameter optimization. This underscores the challenges of handling unstructured text data and the importance of high-quality labeled datasets for training.

### Future Directions:

1. **Hybrid Enrichment Pipeline**:  
   Combine API-based methods with advanced text-based classification models, such as fine-tuned transformer models, to maximize enrichment coverage and accuracy.

2. **Data Consolidation and Simplification**:  
   Continue refining classification labels by consolidating redundant or ambiguous categories. Simplifying the dataset will improve model performance and usability.

3. **Scalability Enhancements**:  
   Move processing tasks to cloud-based platforms or distributed systems to handle larger datasets efficiently. This will overcome the limitations of local machine resources.

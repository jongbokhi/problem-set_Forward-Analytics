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

---

## Conclusion and Future Directions

The two experiments provided complementary approaches to data enrichment. **Experiment 1** demonstrated that APIs like GLEIF and People Data Labs can be highly effective for specific data points like LEI, but they have limitations, especially with coverage for all attributes. **Experiment 2** showed that text-based classification is a viable alternative for categorizing industry types but may require further refinement for accuracy.

### Next Steps

- **Hybrid Approach**: Combining API enrichment with a refined text-based classification can maximize coverage and accuracy for each data point.
- **API Quota Management**: Explore other data sources or request increased API quotas for large datasets.
- **Enhanced Classification Algorithms (In progress)**: Use machine learning models to classify industries based on company names with greater precision.




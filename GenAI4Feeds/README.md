# GenAI4Feeds: AI-Powered Product Feed Optimization 

![Python Version](https://img.shields.io/badge/python-3.7+-blue.svg)
![Google Vertex AI](https://img.shields.io/badge/Google%20Vertex%20AI-Gemini%20Integrated-blueviolet)
![Google Merchant Center](https://img.shields.io/badge/Google%20Merchant%20Center-Optimized-green)
![Google BigQuery](https://img.shields.io/badge/Google%20BigQuery-Supported-yellow)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

GenAI4Feeds is a powerful Python-based solution that leverages Google's Gemini Large Language Models (LLMs) via Vertex AI to automate and significantly enhance the generation of product information for e-commerce and digital marketing. It specializes in creating high-quality, optimized product feeds, with a strong focus on Google Merchant Center compliance.

This tool is invaluable for digital marketers, e-commerce managers, and data scientists looking to scale their operations, improve the quality of their product listings, and boost ad performance on platforms like Google Shopping, Facebook Catalogs, and other advertising channels.

## Table of Contents

1.  [Key Features](#key-features)
2.  [Why Use GenAI4Feeds?](#why-use-genai4feeds)
3.  [Prerequisites](#prerequisites)
4.  [Setup and Installation](#setup-and-installation)
5.  [Configuration](#configuration)
    *   [GCP & Vertex AI Settings](#gcp--vertex-ai-settings)
    *   [LLM Generation Configurations](#llm-generation-configurations)
    *   [Safety Settings](#safety-settings)
    *   [Authentication](#authentication)
6.  [Core Components](#core-components)
    *   [Prompts (System Instructions)](#prompts-system-instructions)
    *   [Utility Functions](#utility-functions)
7.  [Usage Workflow](#usage-workflow)
    *   [Step 1: Initialize Vertex AI (Important for Batching)](#step-1-initialize-vertex-ai-important-for-batching)
    *   [Step 2: Load Input Data](#step-2-load-input-data)
    *   [Step 3: Generate Content](#step-3-generate-content)
    *   [Step 4: Output Data](#step-4-output-data)
8.  [Roadmap / Future Enhancements](#roadmap--future-enhancements)
9.  [Important Notes](#important-notes)
10. [Contributing](#contributing)
11. [License](#license)
12. [Acknowledgements](#acknowledgements)

## Key Features

*   🚀 **AI-Powered Generation**: Utilizes Google's advanced Gemini models (e.g., `gemini-2.0-flash-lite-001`) via Vertex AI for high-quality content.
*   🎯 **Targeted Attribute Extraction**: Intelligently identifies and extracts crucial product attributes from raw data.
*   ✨ **Optimized Title Creation**: Generates compelling, SEO-friendly product titles designed for maximum impact and click-through rates.
*   ✍️ **Engaging Description Writing**: Crafts rich, detailed, and persuasive product descriptions.
*   🔎 **Built-in Quality Scoring**: Includes a self-critique mechanism where the LLM evaluates generated descriptions based on predefined criteria (score and reasoning).
*   ⚖️ **Merchant Center Compliance Focus**: Offers specialized prompts to generate descriptions compliant with Google Merchant Center policies, particularly for sensitive categories (e.g., alcoholic beverages, personal hardship, legal restrictions).
*   ☁️ **Cloud Integration**: Native support for Google Cloud Platform services, especially Google BigQuery for data input and output.
*   📄 **Flexible Input**: Supports loading product data from Google BigQuery or local CSV files.
*   🇪🇸 **Spanish-First Prompts**: Pre-defined prompts are primarily in Spanish, adaptable for various needs.
*   ⚙️ **Configurable LLM Parameters**: Allows fine-tuning of LLM behavior (temperature, top_p, max output tokens) and response formats (text/plain or application/json).

## Why Use GenAI4Feeds?

*   **Save Time and Resources**: Automate the labor-intensive task of writing and optimizing product listings, especially for large catalogs.
*   **Improve Ad Performance**: Generate titles and descriptions that are more relevant and engaging, leading to better conversion rates.
*   **Enhance SEO**: Create content rich in relevant keywords and attributes, improving organic visibility.
*   **Maintain Consistency**: Ensure a uniform tone, style, and quality across all product listings.
*   **Scale Content Production**: Effortlessly generate high-quality content for thousands of products.
*   **Reduce Merchant Center Disapprovals**: Proactively address common policy issues by generating compliant content from the start.

## Prerequisites

*   **Python**: Version 3.7+ (check with `!python --version` in your environment).
*   **Google Cloud Platform (GCP) Project**: An active GCP project is required.
*   **APIs Enabled**:
    *   Vertex AI API
    *   BigQuery API (if using BigQuery for data I/O)
*   **`gcloud` CLI**: Google Cloud SDK command-line tool configured for your GCP project (if not using Colab authentication or service accounts directly).

## Setup and Installation

1.  **Create a Virtual Environment (Recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Linux/macOS
    # venv\Scripts\activate   # On Windows
    ```

2.  **Install Dependencies:**
    The provided Jupyter Notebook (`.ipynb`) file contains cells to install these. For a standalone Python project, run:
    ```bash
    pip install --upgrade pip
    pip install pandas google-auth google-cloud-aiplatform google-cloud-bigquery google-cloud-bigquery-storage db-dtypes
    ```

## Configuration

All primary configuration variables are located at the beginning of the script/notebook. Review and update them according to your setup.

### GCP & Vertex AI Settings

*   `MODEL_NAME`: The specific Gemini model ID (e.g., `'gemini-2.0-flash-lite-001'`).
*   `GCP_LOCATION`: The GCP region for Vertex AI operations (e.g., `'us-central1'`).
*   `GCP_PROJECT`: Your Google Cloud Project ID.
*   `LANGUAGE`: Target language for generated content (current prompts favor `'Spanish'`).
*   `DATASET_NAME`: BigQuery dataset for input/output (e.g., `'feedgen'`).
*   `INPUT_TABLE`: (If using BigQuery input) Name of the input table (e.g., `'input_table'`).
*   `OUTPUT_TABLE`: BigQuery table for storing generated content (e.g., `'output_table'`).
*   `API_KEY`: Generally **not recommended for Vertex AI SDK with GCP**. Use ADC or service accounts. (Variable present in code).
*   `BQ_SERVICE_ACCOUNT`: (Optional) Path to your BigQuery service account JSON key file if not using ADC or Colab auth.
*   `VERTEXAI_SERVICE_ACCOUNT`: (Optional) Path to your Vertex AI service account JSON key file (Vertex AI SDK typically uses ADC or Colab's authenticated user).

### LLM Generation Configurations

The script defines several `GENERATION_CONFIG_*` dictionaries to control LLM behavior:

*   **For JSON Output** (e.g., `GENERATION_CONFIG_PRODUCT_ATTRIBUTES_JSON`):
    *   `temperature`, `top_p`, `max_output_tokens`
    *   `response_mime_type: "application/json"`
    *   `response_schema`: Defines the expected JSON structure for the LLM to follow. This provides more reliable structured output.
*   **For Text Output** (e.g., `GENERATION_CONFIG_PRODUCT_ATTRIBUTES_TEXT`):
    *   `temperature`, `top_p`, `max_output_tokens`
    *   `response_mime_type: "text/plain"` (Default used by current `gen_*` functions).

**Note:** The current utility functions (`gen_product_attributes`, `gen_title`, `gen_description`) are configured to use the `_TEXT` configurations. While prompts might request JSON-like structures in text, for more robust and directly parsable JSON, modify these functions to use the corresponding `_JSON` configurations.

### Safety Settings

`SAFETY_SETTINGS` configure content harm blocking thresholds for the LLM. The provided code sets several harm categories to `SafetySetting.HarmBlockThreshold.OFF`.

```python
from vertexai.generative_models import SafetySetting

SAFETY_SETTINGS = [
    SafetySetting(category=SafetySetting.HarmCategory.HARM_CATEGORY_HATE_SPEECH, threshold=SafetySetting.HarmBlockThreshold.OFF),
    SafetySetting(category=SafetySetting.HarmCategory.HARM_CATEGORY_DANGEROUS_CONTENT, threshold=SafetySetting.HarmBlockThreshold.OFF),
    # ... other settings ...
]
```
**⚠️ Important:** Review and adjust these safety settings based on your organization's policies and Google's Responsible AI practices for your specific use case. Turning off safety filters can lead to undesirable or harmful content generation.

### Authentication

*   **In Google Colab:**
    ```python
    from google.colab import auth
    auth.authenticate_user()
    print('Authenticated')
    ```
*   **Locally or other environments (Application Default Credentials - ADC):**
    1.  Authenticate via `gcloud` CLI:
        ```bash
        gcloud auth application-default login
        gcloud config set project YOUR_GCP_PROJECT_ID
        ```
    2.  The Python SDKs for GCP services will typically pick up these credentials automatically.
*   **Service Accounts:**
    If `BQ_SERVICE_ACCOUNT` is specified, the BigQuery client will use it. For Vertex AI, ADC or Colab auth is often preferred for simplicity, but service accounts can also be explicitly configured.

## Core Components

### Prompts (System Instructions)

The effectiveness of GenAI4Feeds heavily relies on well-crafted prompts that guide the LLM. The key system instructions provided are:

*   `title_system_instruction`: Guides the LLM to act as a digital marketer, extract attributes, and construct a high-performing product title following specific rules for formatting, length, and de-duplication.
*   `description_system_instruction`: Instructs the LLM to create detailed product descriptions, avoid certain content (SKU, price), adhere to length requirements, and then self-critique the generated description by providing a score and reasoning.
*   `product_attributes_system_instruction`: Focuses specifically on extracting and formatting relevant product attributes from the input `Context`, with examples for various product types.
*   **Specialized Merchant Center Issue Prompts**:
    *   `description_prompt_textonly_merchant_center_issue_alcoholic_spanish`: Tailored for alcoholic beverages, emphasizing neutrality and avoiding health claims.
    *   `description_prompt_textonly_merchant_center_issue_personal_hardship_spanish`: For products related to personal difficulties, focusing on objective features and avoiding exploitative language.
    *   `description_prompt_textonly_merchant_center_issue_legal_restrictions_spanish`: For products with legal restrictions, ensuring descriptions are precise and compliant.

These prompts are primarily in **Spanish**. They define the persona, task, input context, and expected output format for the LLM. To use specialized prompts for descriptions, you would need to modify the `gen_description` function or the main loop to pass the appropriate `system_instruction`.

### Utility Functions

*   `gen_product_attributes(row)`: Generates product attributes for a given product row.
*   `gen_title(row)`: Generates a product title and related information.
*   `gen_description(row)`: Generates a product description along with a score and reasoning.
*   `get_title(generated_title_raw_output)`: Parses the raw text output from `gen_title` to extract the final "generated title".
*   `get_description(generated_description_raw_output)`: Parses the raw text output from `gen_description` to extract the "description" text.
*   `create_dataset_and_output_table()`: Creates the specified BigQuery dataset and output table if they don't exist.
*   `feedgen2BQ(...)`: Inserts a row of generated product data into the BigQuery output table.
*   `delete_all_rows()`: Deletes all rows from the BigQuery output table (use with caution).

## Usage Workflow

### Step 1: Initialize Vertex AI (Important for Batching)

The current `gen_product_attributes`, `gen_title`, and `gen_description` functions initialize Vertex AI (`vertexai.init(...)`) on each call. For processing multiple products, it's more efficient to initialize Vertex AI once globally at the beginning of your script:

```python
import vertexai

# Initialize Vertex AI globally (use your configured GCP_PROJECT and GCP_LOCATION)
vertexai.init(project='YOUR_GCP_PROJECT_ID', location='YOUR_GCP_LOCATION')
# Then, you would remove the vertexai.init() call from within each gen_* function.
```

### Step 2: Load Input Data

Product data should be prepared as a Pandas DataFrame.

Example Query: This query leverages the output data from Shopping Insider. See: [Shopping Insider](https://github.com/google/shopping_insider "Shopping Insider") is a Google project that provides a dataset of product listings and their performance metrics on Google Shopping. It is designed to help retailers and advertisers understand how their products are performing on the platform and to optimize their listings for better visibility and sales.

#### From BigQuery
```python
from google.cloud import bigquery
# ... (configure BigQuery client as shown in the Configuration section) ...

# Example Query:
query = f"""
SELECT
  offer_id as sku, title, brand, description, item_url,
  custom_label_0, custom_label_1, custom_label_2, custom_label_3, custom_label_4,
  product_type_l1, product_type_l2,product_type_l3,
  google_product_category_l1, google_product_category_l2, google_product_category_l3
FROM `{GCP_PROJECT}.{DATASET_NAME}.{INPUT_TABLE}`
WHERE clicks_30_days < 5 AND days_has_impressions > 28 -- Example filter
LIMIT 10; -- Adjust limit as needed
"""
df_input = client.query(query).to_dataframe()
df_input.dropna(axis=1, how='all', inplace=True) # Remove fully empty columns
print("Input data loaded from BigQuery:")
print(df_input.head())
```

#### From CSV File
```python
import pandas as pd

# df_input = pd.read_csv('input_data.csv') # Uncomment and use your CSV file path
# print("Input data loaded from CSV:")
# print(df_input.head())
```

### Step 3: Generate Content

#### Generating for a Single Product (Example)
```python
if not df_input.empty:
    product_row = df_input.iloc[0] # Get the first product row as a Pandas Series

    print(f"\n--- Processing SKU: {product_row.get('sku', 'N/A')} ---")

    # Generate Product Attributes
    # Note: The prompt product_attributes_system_instruction asks for JSON output,
    # but gen_product_attributes uses GENERATION_CONFIG_PRODUCT_ATTRIBUTES_TEXT.
    # The output will be a string that looks like JSON.
    raw_attributes_output = gen_product_attributes(product_row)
    print("\nRaw Attributes Output (String):")
    print(raw_attributes_output)
    # For actual JSON, you might parse it:
    # import json
    # try:
    #   attributes_dict = json.loads(raw_attributes_output)
    # except json.JSONDecodeError:
    #   attributes_dict = {"error": "Failed to parse attributes JSON string"}
    #   print(attributes_dict)

    # Generate Title
    raw_title_output = gen_title(product_row)
    print("\nRaw Title Output:")
    # The raw output contains multiple fields as per title_system_instruction
    # print(raw_title_output)
    final_title = get_title(raw_title_output)
    print(f"Extracted Title: {final_title}")

    # Generate Description
    # To use a specialized Merchant Center prompt, you'd need to modify
    # gen_description or pass the chosen system_instruction to it.
    raw_description_output = gen_description(product_row)
    print("\nRaw Description Output (includes score and reasoning as text):")
    # print(raw_description_output)
    final_description = get_description(raw_description_output)
    print(f"Extracted Description: {final_description[:200]}...") # Print snippet
    # To get score/reasoning separately, more robust parsing of raw_description_output is needed
    # or switch gen_description to use GENERATION_CONFIG_DESCRIPTION_JSON.

else:
    print("Input DataFrame is empty. Cannot process a single product.")
```

#### Generating for Multiple Products (Batch Processing)
```python
import pandas as pd

generated_feed_list = []

# Ensure df_input is loaded and not empty
if 'df_input' in locals() and not df_input.empty:
    # Process a sample or all products
    # for i, product_row in df_input.sample(min(5, len(df_input))).iterrows(): # For a sample
    for i, product_row in df_input.iterrows(): # For all products in df_input
        try:
            print(f"\n--- Batch Processing SKU: {product_row.get('sku', 'N/A')} ---")

            attributes_str = gen_product_attributes(product_row)
            title_raw_str = gen_title(product_row)
            desc_raw_str = gen_description(product_row)

            parsed_title = get_title(title_raw_str)
            parsed_description = get_description(desc_raw_str)

            generated_feed_list.append({
                'sku': product_row.get('sku'),
                'item_url': product_row.get('item_url'), # Used as 'id' in BQ schema
                'product_attributes': attributes_str, # Storing raw string output
                'generated_title_raw': title_raw_str, # Optional: store raw for debugging
                'generated_title': parsed_title,
                'generated_description_raw': desc_raw_str, # Optional: store raw for debugging
                'generated_description': parsed_description,
                'original_title': product_row.get('title'),
                'original_description': product_row.get('description')
            })
        except Exception as e:
            print(f"ERROR processing product {product_row.get('sku', 'N/A')}: {e}")

    generated_feed_df = pd.DataFrame(generated_feed_list)
    print("\n--- Generated Feed DataFrame (Sample) ---")
    print(generated_feed_df.head())
else:
    print("Input DataFrame 'df_input' is not available or empty for batch processing.")

```

### Step 4: Output Data

#### Creating BigQuery Dataset and Table (Run Once if Needed)
```python
# create_dataset_and_output_table()
```
The schema defined in `create_dataset_and_output_table` is:
`generated_at (TIMESTAMP), sku (STRING), id (STRING), product_attributes (STRING), generated_title (STRING), generated_description (STRING), title (STRING), description (STRING)`

#### Saving Generated Content to BigQuery
```python
# Ensure generated_feed_df exists and has data
if 'generated_feed_df' in locals() and not generated_feed_df.empty:
    print("\n--- Saving data to BigQuery ---")
    for index, row_data in generated_feed_df.iterrows():
        feedgen2BQ(
            sku=row_data['sku'],
            id=row_data['item_url'], # 'id' in BQ schema corresponds to item_url here
            product_attributes=row_data['product_attributes'], # Storing raw string
            generated_title=row_data['generated_title'],
            generated_description=row_data['generated_description'],
            title=row_data['original_title'],
            description=row_data['original_description']
        )
else:
    print("No generated data to save to BigQuery.")
```

## Roadmap / Future Enhancements

*   🌀 **Prompt Refinement & Management**:
    *   Consolidate system instructions for easier management.
    *   Further test and iterate on specialized prompts for Merchant Center issue resolution.
    *   Allow easier selection of different system instructions for generation functions (e.g., passing prompt names or types as parameters).
*   🌐 **Input Flexibility - Web Scraping**:
    *   Implement web scraping capabilities to directly ingest product data from URLs to form the input `Context`.
*   📦 **Code Structure & Packaging**:
    *   Refactor utility functions, prompts, and core logic into an installable Python library for streamlined use outside of Jupyter Notebooks/Colab.
*   🤖 **Advanced Workflows - Agent-Based Architecture**:
    *   Explore agent-based architectures (e.g., one agent for attribute extraction, another for evaluation, etc.) for more sophisticated and robust content generation pipelines.
*   🛡️ **Error Handling and Robustness**:
    *   Improve parsing of LLM outputs, especially when `text/plain` is used but a structured format is expected (or enforce JSON output via `GENERATION_CONFIG_*_JSON`).
    *   Add more comprehensive error handling and retry mechanisms for API calls.
*   🌍 **Multi-Language Support**:
    *   Extend prompt engineering to support content generation in multiple languages beyond Spanish.
*   ⚡ **Performance Optimization**:
    *   Optimize for processing very large product catalogs, including efficient batching and API call management.
*   🔧 **Enhanced Disapproval Resolution System**:
    *   Develop a more systematic approach to identify and address potential Merchant Center disapprovals using LLM feedback loops.
*   🔧 **Enhanced Initialization of Vertex AI**:
    *   Initialize Vertex AI globally. Specially useful for batch mode.
    

## Important Notes

*   **LLM Variability**: Outputs from LLMs can vary even with identical inputs, especially with higher `temperature` settings. Experiment to find optimal parameters.
*   **Cost**: Using Vertex AI Generative AI models incurs costs based on input and output token usage. Monitor your GCP billing closely.
*   **Prompt Engineering is Key**: The quality of the generated content is highly dependent on the prompts. Continuous iteration and testing of prompts are crucial for optimal results.
*   **JSON Output vs. Text Output**:
    *   Using `response_mime_type: "application/json"` with a `response_schema` in the `GENERATION_CONFIG` is generally more reliable for getting structured, directly parsable JSON from the LLM.
    *   The current `gen_*` functions in the provided code use `_TEXT` configurations (MIME type `text/plain`). While prompts might ask for JSON-like structures, the LLM output is still a plain text string and requires careful parsing (as done by `get_title`, `get_description`).
*   **Rate Limits**: Be aware of API rate limits when processing a very large number of products in a short period. Implement batching and appropriate delays if necessary.
*   **Language**: The current prompts are primarily in Spanish. Adapt them for other languages as needed.
*   **Safety Settings**: The default safety settings in the provided code are permissive (some set to `OFF`). **It is your responsibility to configure these according to your use case and Google's Responsible AI policies to prevent the generation of harmful or inappropriate content.**

## Contributing

Contributions are welcome! If you have suggestions, bug reports, or want to contribute code, please feel free to:
1.  Open an issue to discuss the change.
2.  Fork the repository and submit a pull request.

## License

This project is licensed under the **MIT License**. See the `LICENSE` file for details (if one is created for the project).

## Acknowledgements

Made in Chile with ❤️.
# Collectible Car Inventory Management

Welcome to a detailed exploration of a unique dataset featuring collectible cars from my father's cherished collection. This notebook chronicles our efforts to transform a cluttered assortment of data into a streamlined, structured inventory. Such a transformation is essential, forming the foundation for a sophisticated inventory management system designed to enhance both the efficiency and effectiveness of how these collectible gems are handled.

# Project Outline

1. **Data Exploration:** Dive into the dataset to grasp its basic layout and constituent elements.
2. **Data Restructuring:** Tailor the dataset to fit the practical needs of an inventory management system, enhancing usability.
3. **Data Cleaning:** Tackle any data inconsistencies, fill missing values, and correct inaccuracies to polish the dataset.

Our ultimate objective is to refine the dataset to such an extent that it not only offers a transparent snapshot of the collection but also becomes fully integrated with an inventory management application, built with Google Appsheet. This integration will facilitate straightforward updates and maintenance, bolstering both the enjoyment of the collection in the present and its legacy in the future.

Embark on this journey with me as we turn a raw dataset into an indispensable tool for managing a collection of automotive treasures!


# 1. Data Exploration

## 1.1 Initial Data Loading and Inspection

We'll begin our journey by loading the dataset from an Excel file—kindly provided by my father, who in this case, plays the role of both client and collector. Our first step is to gain a preliminary understanding of the dataset's structure and content. This initial peek will help us identify the type of data we're dealing with and set the stage for the detailed analysis that follows.

```python
import pandas as pd

### Filepath
file_path = '/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx'

### Load the data from the Excel file
data = pd.read_excel(file_path)

### Display the structure of the DataFrame
print("Data Info:")
data.info()

### Display the first few rows to understand the data better
print("First few rows of the dataset:")
display(data.head())

```

## 1.1.1 Brief overview based on the first 5 rows:

- The dataset from the "Cars catalogue Main_RAW.xlsx" file comprises **410 entries** across **181 columns**.
- Each column represents a distinct collection or series of car models.
- The dataset showcases an eclectic mix of car collections, including "Altaya," "Opel Collection journal," "Opel Collection Designer-Serie," "Deutsche Liebhaber-autos," "Auto Plus - Les classiques de l'automobile," "Mercedes-Benz journal series (Ixo)," among others.
- Entries detail specific car models, occasionally accompanied by particulars such as model year, color, or specific edition information, albeit without uniform formatting. Examples include "Matra Simca Rancho 1978", "Opel 10/40 PS Modell 80 1925-1929", "Mercedes-Benz 280 SL 1963 Roadster", etc.

## 1.1.2 Observations from the Data

- **Sparsity:** A significant number of columns exhibit missing values, suggesting incomplete records.
- **Inconsistency:** There is considerable variation in descriptions, blending information about the model, year, and color in differing formats.

## Next Steps:

- **Data Structuring:** The plan involves reformatting the data into a more analysis-conducive layout, potentially merging similar columns or restructuring the dataset based on specific characteristics like model year, make, or collection.
- **Data Cleaning:** This step will address the missing data, standardize textual entries, and possibly reformat the data to enhance usability.
- **Analysis/Visualization Preparation:** We will identify crucial variables for detailed analysis and prepare the dataset for integration into visualization tools like Looker Studio.

# 2. Data Restructuring

We are now set to restructure the dataset into a more streamlined format. The new structure will feature just two columns: one for the manufacturer's name and the other for the full item name. This simplification will make the dataset not only easier to handle but also more amenable for deeper analysis and visualization.

In this process, we will create a new DataFrame where each row represents a unique collectible, clearly identified by its manufacturer and full name. This step is designed to facilitate the extraction of key details such as brand, model, year, and color, enhancing the dataset's utility for detailed analysis and efficient data management.


```python
import pandas as pd

### Filepath
file_path = '/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx'

### Load the dataset
data = pd.read_excel(file_path)

### Melt the DataFrame to reformat it into two columns: 'Model_Manufacturer_name' and 'Collectible_item_full_name'
melted_data = data.melt(var_name='Model_Manufacturer_name', value_name='Collectible_item_full_name')

### Remove rows where 'Collectible_item_full_name' is null as they do not provide useful information
cleaned_data = melted_data.dropna(subset=['Collectible_item_full_name'])

### Show the first few rows of the newly structured DataFrame and summary information
print("Summary Information of Cleaned Data:")
cleaned_data_info = cleaned_data.info()

print("\nFirst Few Rows of Cleaned Data:")
cleaned_data_head = cleaned_data.head()

### If you need to save or export the cleaned data:
cleaned_data.to_csv('/kaggle/working/cleaned_data_v1_2columns.csv', index=False)

### Display the cleaned data head
display(cleaned_data_head)

```

The transformation is complete! Our dataset now neatly organizes **4,045 entries** into two distinct columns: **'Model_Manufacturer_name'** and **'Collectible_item_full_name'**. This format sharpens the focus, with each entry distinctly representing a collectible item. The 'Model_Manufacturer_name' column tags each item with its manufacturer or series, while the 'Collectible_item_full_name' provides enriched details, encompassing elements like the brand name, model, and occasionally the year and color. This restructured dataset lays a solid foundation for extracting precise attributes and diving deeper into data analysis.

## 2.1 Extracting Relevant Information

To further refine our dataset, we will now focus on extracting key details such as the brand, model, and, where possible, the year and color of each collectible item. This step is crucial as it enhances the dataset's utility for detailed analysis and reporting. The structured data will be outlined as follows:

* **Model_Manufacturer_name:** Identifies the manufacturer or series.
* **Collectible_item_full_name:** Lists the complete name of the collectible item as it appears in the records.
* **Brand:** The brand name derived from the full item description.
* **Model:** Detailed model information extracted from the item description.
* **Year:** The year or range of years associated with the collectible item, when available.
* **Color:** Color information, extracted if specified within the item description.


```python
import pandas as pd
import re
from IPython.display import display

### Read the dataset
data = pd.read_excel('/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx')

### Function to extract brand, model, and year from the collectible item full name
def extract_info(text):
    pattern = r'^(?P<Brand>[\w\s-]+?)\s(?P<Model>.*?)\s(?P<Year>\d{4}(?:-\d{4})?)'
    match = re.match(pattern, str(text))
    if match:
        return match.group('Brand').strip(), match.group('Model').strip(), match.group('Year')
    else:
        return None, None, None

### Initialize an empty list to store the reshaped data
reshaped_rows = []

### Iterate through each column of the original DataFrame
for col in data.columns:
    # Extract manufacturer name from the column name
    manufacturer_name = col.split('_')[0]
    ##Iterate through each item in the column
    for item in data[col]:
        ##Check if the item is not empty
        if pd.notna(item):
            brand, model, year = extract_info(item)
            ##Append a new row to the reshaped data list
            reshaped_rows.append({'Model_Manufacturer_name': manufacturer_name,
                                  'Collectible_item_full_name': item,
                                  'Brand': brand, 'Model': model, 'Year': year})

### Convert the list of dictionaries to a DataFrame
reshaped_data = pd.DataFrame(reshaped_rows)

### Filter for valid entries where 'Model_Manufacturer_name' is not missing
valid_data = reshaped_data[reshaped_data['Model_Manufacturer_name'].notna()]

### Count missing 'Brand' values in the valid data
missing_brands_count = valid_data[valid_data['Brand'].isnull()].shape[0]
print(f"Number of entries with missing 'Brand', excluding missing 'Model_Manufacturer_name': {missing_brands_count}")

### Count missing 'Year' values in the valid data
missing_years_count = valid_data[valid_data['Year'].isnull()].shape[0]
print(f"Number of entries with missing 'Year', excluding missing 'Model_Manufacturer_name': {missing_years_count}")

### If you need to save or export the cleaned data:
cleaned_data.to_csv('/kaggle/working/cleaned_data_v2_multiCol_1675-missing-brand.csv', index=False)

### Display the reshaped data
display(reshaped_data)
```

## 2.2 Filtering Missing Values

Upon closer inspection, we encounter a significant challenge: 1675 entries still lack complete information in the 'Brand', 'Model', and 'Year' columns. This issue stems from the varied formats of the item names, which our initial regular expression failed to uniformly interpret. Instead of removing these entries, we'll take a different approach:

1. **Identify and Isolate Incomplete Entries:** We will filter and isolate entries missing essential data, which will help in pinpointing the inconsistencies.
2. **Analyze Naming Variations:** By analyzing these variations, we can adjust our data extraction techniques to capture more diverse naming conventions.

### Filtered Data:

We will provide a list of these filtered entries to further analyze and refine our regular expression, ensuring a more comprehensive data capture in future iterations.


```python
import pandas as pd
import re
from IPython.display import display

### Read the dataset
data = pd.read_excel('/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx')

### Function to extract brand, model, and year from the collectible item full name
def extract_info(text):
    pattern = r'^(?P<Brand>[\w\s-]+?)\s(?P<Model>.*?)\s(?P<Year>\d{4}(?:-\d{4})?)'
    match = re.match(pattern, str(text))
    if match:
        return match.group('Brand').strip(), match.group('Model').strip(), match.group('Year')
    else:
        return None, None, None

### Initialize an empty list to store the reshaped data
reshaped_rows = []

### Iterate through each column of the original DataFrame
for col in data.columns:
    ##Extract manufacturer name from the column name
    manufacturer_name = col.split('_')[0]
    ##Iterate through each item in the column
    for item in data[col]:
        ##Check if the item is not empty
        if pd.notna(item):
            brand, model, year = extract_info(item)
            ##Append a new row to the reshaped data list
            reshaped_rows.append({'Model_Manufacturer_name': manufacturer_name,
                                  'Collectible_item_full_name': item,
                                  'Brand': brand, 'Model': model, 'Year': year})

### Convert the list of dictionaries to a DataFrame
reshaped_data = pd.DataFrame(reshaped_rows)

### Filter for valid entries where 'Model_Manufacturer_name' is not missing
valid_data = reshaped_data[reshaped_data['Model_Manufacturer_name'].notna()]

### Count missing 'Brand' values in the valid data
missing_brands_count = valid_data[valid_data['Brand'].isnull()].shape[0]
print(f"Number of entries with missing 'Brand', excluding missing 'Model_Manufacturer_name': {missing_brands_count}")

### Count missing 'Year' values in the valid data
missing_years_count = valid_data[valid_data['Year'].isnull()].shape[0]
print(f"Number of entries with missing 'Year', excluding missing 'Model_Manufacturer_name': {missing_years_count}")

### Filter entries without a brand
entries_without_brand = valid_data[valid_data['Brand'].isnull()]

### Display entries without a brand
print("Entries without a brand:")
display(entries_without_brand)

### If you need to save or export the cleaned data:
cleaned_data.to_csv('/kaggle/working/cleaned_data_v2_list_missing-brand-only.csv', index=False)


```

With all entries lacking 'Brand', 'Model', and 'Year' neatly catalogued, we face a daunting task. Manually reviewing the 1675 records would be both time-consuming and inefficient. To streamline this process, we've opted for a more technologically savvy approach:

### Leveraging AI for Data Enhancement:

We will export this list of incomplete entries to a file specifically for analysis by ChatGPT4. This advanced AI model will swiftly analyze the naming variations and suggest enhancements to our regular expression. This step aims to minimize the missing values significantly, optimizing our dataset for further analysis and use.

## 2.2 Reducing the Missing Values

### Enhancing Data Integrity:

Following a thorough analysis, we've incorporated new variations into our regular expression patterns. This enhancement is designed to capture more detailed attributes from each entry, thereby reducing the number of missing values.

```python
import pandas as pd
import re
from IPython.display import display

### Read the dataset
data = pd.read_excel('/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx')

### Function to extract brand, model, and year, with enhanced regex
def extract_info_improved(text):
    ##Normalize the text by removing known noise patterns and handling edge cases
    text = str(text)
    text = re.sub(r'\s+\(.*?\)', '', text)  ##Remove any content inside parentheses
    text = re.sub(r'\s-\s.*', '', text)     ##Remove descriptions after a dash
    text = re.sub(r"\b(?<!\d)(?!\d{4})\d+\b", "", text)  ##Remove isolated numbers that are not part of a four-digit year
    text = text.replace(',', '')  ##Remove commas that might be used as separators

    ##Enhanced regex pattern to handle various cases
    pattern = (
        r'^(?P<Brand>\D+?)'             ##Capture the brand as non-digit characters at the start
        r'\s+(?P<Model>.*?)'            ##Capture the model which might include numbers
        r'(\s+(?P<Year>\d{4}))?$'       ##Optionally capture a four-digit year at the end
    )
    match = re.match(pattern, text)
    if match:
        brand = match.group('Brand').strip()
        model = match.group('Model').strip()
        year = match.group('Year') if match.group('Year') else None
        return brand, model, year
    return None, None, None

### Process each item, reshape data
reshaped_rows = []
for col in data.columns:
    manufacturer_name = col.split('_')[0]
    for item in data[col]:
        if pd.notna(item):
            brand, model, year = extract_info_improved(item)
            reshaped_rows.append({
                'Model_Manufacturer_name': manufacturer_name,
                'Collectible_item_full_name': item,
                'Brand': brand, 'Model': model, 'Year': year
            })

### Convert reshaped_rows into a DataFrame
reshaped_data = pd.DataFrame(reshaped_rows)

### Filter for valid entries where 'Model_Manufacturer_name' is not empty
valid_data = reshaped_data[reshaped_data['Model_Manufacturer_name'].notna()]

### Count and print missing 'Brand' and 'Year' values excluding missing 'Model_Manufacturer_name'
missing_brands_count = valid_data[valid_data['Brand'].isnull()].shape[0]
missing_years_count = valid_data[valid_data['Year'].isnull()].shape[0]
print(f"Number of entries with missing 'Brand', excluding missing 'Model_Manufacturer_name': {missing_brands_count}")
print(f"Number of entries with missing 'Year', excluding missing 'Model_Manufacturer_name': {missing_years_count}")

### Convert to DataFrame, export, and display
reshaped_data.to_csv('/kaggle/working/cleaned_data_v3_multiCol_292-missing-brand.csv', index=False)

display(reshaped_data.head())

```

We've made significant progress in addressing incomplete data. The count of entries missing the 'Brand' detail has been reduced dramatically to just 292. This improvement provides a more solid dataset to work with moving forward.

```python
import pandas as pd
import re
from IPython.display import display

### Read the dataset
data = pd.read_excel('/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx')

### Common color names for regex (this list can be extended)
colors = "black|white|red|green|blue|yellow|silver|grey|orange|purple|gold|bronze|brown"

### Function to extract brand, model, year, and color, with enhanced regex
def extract_info_improved(text):
    ##Normalize the text by removing known noise patterns and handling edge cases
    text = str(text)
    text = re.sub(r'\s+\(.*?\)', '', text)  ##Remove any content inside parentheses
    text = re.sub(r'\s-\s.*', '', text)     ##Remove descriptions after a dash
    text = re.sub(r"\b(?<!\d)(?!\d{4})\d+\b", "", text)  ##Remove isolated numbers that are not part of a four-digit year
    text = text.replace(',', '')  ##Remove commas that might be used as separators

    ####Enhanced regex pattern to handle various cases including color
    pattern = (
        rf'^(?P<Brand>\D+?)'             ##Capture the brand as non-digit characters at the start
        r'\s+(?P<Model>.*?)'             ##Capture the model which might include numbers
        r'(\s+(?P<Year>\d{{4}}))?'       ##Optionally capture a four-digit year at the end
        rf'(\s+(?P<Color>{colors}))?$'   ##Optionally capture a color at the end
    )
    match = re.match(pattern, text)
    if match:
        brand = match.group('Brand').strip()
        model = match.group('Model').strip()
        year = match.group('Year') if match.group('Year') else None
        color = match.group('Color') if 'Color' in match.groupdict() and match.group('Color') else None
        return brand, model, year, color
    return None, None, None, None

####Process each item, reshape data
reshaped_rows = []
for col in data.columns:
    manufacturer_name = col.split('_')[0]
    for item in data[col]:
        if pd.notna(item):
            brand, model, year, color = extract_info_improved(item)
            reshaped_rows.append({
                'Model_Manufacturer_name': manufacturer_name,
                'Collectible_item_full_name': item,
                'Brand': brand,
                'Model': model,
                'Year': year,
                'Color': color
            })

####Convert reshaped_rows into a DataFrame
reshaped_data = pd.DataFrame(reshaped_rows)

####Filter for valid entries where 'Model_Manufacturer_name' is not empty
valid_data = reshaped_data[reshaped_data['Model_Manufacturer_name'].notna()]

####Count and print missing 'Brand' and 'Year' values excluding missing 'Model_Manufacturer_name'
missing_brands_count = valid_data[valid_data['Brand'].isnull()].shape[0]
missing_years_count = valid_data[valid_data['Year'].isnull()].shape[0]
print(f"Number of entries with missing 'Brand', excluding missing 'Model_Manufacturer_name': {missing_brands_count}")
print(f"Number of entries with missing 'Year', excluding missing 'Model_Manufacturer_name': {missing_years_count}")

####Display the head of the DataFrame to confirm the extraction
display(reshaped_data.head())

```

```python
import pandas as pd
import re
from IPython.display import display

####Read the dataset
data = pd.read_excel('/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx')

####Common color names for regex (this list can be extended)
colors = "black|white|red|green|blue|yellow|silver|grey|orange|purple|gold|bronze|brown"

####Function to extract brand, model, year, and color, with enhanced regex
def extract_info_improved(text):
    ##Normalize the text by removing known noise patterns and handling edge cases
    text = str(text)
    text = re.sub(r'\s+\(.*?\)', '', text)  ##Remove any content inside parentheses
    text = re.sub(r'\s-\s.*', '', text)     ##Remove descriptions after a dash
    text = re.sub(r"\b(?<!\d)(?!\d{4})\d+\b", "", text)  ##Remove isolated numbers that are not part of a four-digit year
    text = text.replace(',', '')  ##Remove commas that might be used as separators

    ####Enhanced regex pattern to handle various cases including color
    pattern = (
        rf'^(?P<Brand>\D+?)'             ##Capture the brand as non-digit characters at the start
        r'\s+(?P<Model>.*?)'             ##Capture the model which might include numbers
        r'(\s+(?P<Year>\d{{4}}))?'       ##Optionally capture a four-digit year at the end
        rf'(\s+(?P<Color>{colors}))?$'   ##Optionally capture a color at the end
    )
    match = re.match(pattern, text)
    if match:
        brand = match.group('Brand').strip()
        model = match.group('Model').strip()
        year = match.group('Year') if match.group('Year') else None
        color = match.group('Color') if 'Color' in match.groupdict() and match.group('Color') else None
        return brand, model, year, color
    return None, None, None, None

####Process each item, reshape data
reshaped_rows = []
for col in data.columns:
    manufacturer_name = col.split('_')[0]
    for item in data[col]:
        if pd.notna(item):
            brand, model, year, color = extract_info_improved(item)
            reshaped_rows.append({
                'Model_Manufacturer_name': manufacturer_name,
                'Collectible_item_full_name': item,
                'Brand': brand,
                'Model': model,
                'Year': year,
                'Color': color
            })

####Convert reshaped_rows into a DataFrame
reshaped_data = pd.DataFrame(reshaped_rows)

####Filter for valid entries where 'Model_Manufacturer_name' is not empty
valid_data = reshaped_data[reshaped_data['Model_Manufacturer_name'].notna()]

####Count and print missing 'Brand', 'Year', and 'Color' values excluding missing 'Model_Manufacturer_name'
missing_brands_count = valid_data[valid_data['Brand'].isnull()].shape[0]
missing_years_count = valid_data[valid_data['Year'].isnull()].shape[0]
missing_colors_count = valid_data[valid_data['Color'].isnull()].shape[0]
print(f"Number of entries with missing 'Brand', excluding missing 'Model_Manufacturer_name': {missing_brands_count}")
print(f"Number of entries with missing 'Year', excluding missing 'Model_Manufacturer_name': {missing_years_count}")
print(f"Number of entries with missing 'Color', excluding missing 'Model_Manufacturer_name': {missing_colors_count}")

####Display the head of the DataFrame to confirm the extraction
display(reshaped_data.head())

```

## 2.3 Adding IDs with More Year Information

In this phase, our goal is to enrich the dataset by focusing on extracting year information more reliably. Using regular expressions, we'll parse out year data from the entries wherever available. To streamline further operations and ensure each record can be uniquely identified, we will also introduce a unique ID for each item. This step is crucial for simplifying the integration of data across the following processing stages.

```python
import pandas as pd
import re
from IPython.display import display

### Simulating the loading of the dataset
data = pd.read_excel('/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx')

### Function to extract brand, model, and year with robust error handling
def extract_info_improved(text):
    text = str(text)  ##Ensure text is a string
    pattern = (
        r'^(?P<Brand>\D+?)'             
        r'\s+(?P<Model>.*?)'            
        r'(\s+(?P<Year>\d{4}))?$'      
    )
    match = re.match(pattern, text)
    if match:
        brand = match.group('Brand').strip() if match.group('Brand') else None
        model = match.group('Model').strip() if match.group('Model') else None
        year = match.group('Year') if match.group('Year') else None
        return brand, model, year
    else:
        return None, None, None

### Initialize counter for unique ID generation
counter = 1

### Process each item, reshape data
reshaped_rows = []
for col in data.columns:
    manufacturer_name = col.split('_')[0]
    for item in data[col]:
        if pd.notna(item):
            brand, model, year = extract_info_improved(item)
            reshaped_rows.append({
                'Model_Manufacturer_name': manufacturer_name,
                'Collectible_item_full_name': item,
                'Brand': brand, 'Model': model, 'Year': year,
                'id': f"{manufacturer_name}-{item}-{counter:04d}"
            })
            counter += 1

### Convert to DataFrame
df_a = pd.DataFrame(reshaped_rows)

##Reporting missing data and total entries
total_entries = len(df_a)
missing_brand = df_a['Brand'].isnull().sum()
missing_year = df_a['Year'].isnull().sum()

print(f"Total number of entries: {total_entries}")
print(f"Number of entries with missing 'Brand': {missing_brand}")
print(f"Number of entries with missing 'Year': {missing_year}")

##Display the DataFrame to verify results
display(df_a.head())

```

## 2.4 Adding IDs with More Color Information

Here, we extend our data extraction efforts to include color details, enhancing the dataset's descriptive power. By refining our regex patterns, we effectively capture color names embedded within the entries. This addition not only enriches the dataset with visual descriptors of the collectible cars but also boosts the overall appeal and functionality of the catalogue.

```python
import pandas as pd
import re
from IPython.display import display

### Read the dataset
data = pd.read_excel('/kaggle/input/cars-catalogue-main-raw/Cars catalogue Main_RAW.xlsx')

### Common color names for regex (this list can be extended)
colors = "black|white|red|green|blue|yellow|silver|grey|orange|purple|gold|bronze|brown"

### Function to extract brand, model, year, and color, with enhanced regex
def extract_info_improved(text):
    ##Normalize the text by removing known noise patterns and handling edge cases
    text = str(text)
    text = re.sub(r'\s+\(.*?\)', '', text)  ##Remove any content inside parentheses
    text = re.sub(r'\s-\s.*', '', text)     ##Remove descriptions after a dash
    text = re.sub(r"\b(?<!\d)(?!\d{4})\d+\b", "", text)  ##Remove isolated numbers that are not part of a four-digit year
    text = text.replace(',', '')  ##Remove commas that might be used as separators

    ####Enhanced regex pattern to handle various cases including color
    pattern = (
        rf'^(?P<Brand>\D+?)'             ##Capture the brand as non-digit characters at the start
        r'\s+(?P<Model>.*?)'             ##Capture the model which might include numbers
        r'(\s+(?P<Year>\d{{4}}))?'       ##Optionally capture a four-digit year at the end
        rf'(\s+(?P<Color>{colors}))?$'   ##Optionally capture a color at the end
    )
    match = re.match(pattern, text)
    if match:
        brand = match.group('Brand').strip()
        model = match.group('Model').strip()
        year = match.group('Year') if match.group('Year') else None
        color = match.group('Color') if 'Color' in match.groupdict() and match.group('Color') else None
        return brand, model, year, color
    else:
        return None, None, None, None

### Initialize counter for unique ID generation
counter = 1

### Process each item, reshape data
reshaped_rows = []
for col in data.columns:
    manufacturer_name = col.split('_')[0]
    for item in data[col]:
        if pd.notna(item):
            brand, model, year, color = extract_info_improved(item)
            reshaped_rows.append({
                'Model_Manufacturer_name': manufacturer_name,
                'Collectible_item_full_name': item,
                'Brand': brand, 'Model': model, 'Year': year, 'Color': color,
                'id': f"{manufacturer_name}-{item}-{counter:04d}"
            })
            counter += 1

### Convert reshaped_rows into a DataFrame
df_b = pd.DataFrame(reshaped_rows)

### Reporting missing data and total entries
total_entries = len(df_b)
missing_brand = df_b['Brand'].isnull().sum()
missing_year = df_b['Year'].isnull().sum()
missing_color = df_b['Color'].isnull().sum()

print(f"Total number of entries: {total_entries}")
print(f"Number of entries with missing 'Brand': {missing_brand}")
print(f"Number of entries with missing 'Year': {missing_year}")
print(f"Number of entries with missing 'Color': {missing_color}")

### Display the DataFrame to verify results
display(df_b.head())

```

## 2.5 Merging and Filling Gaps for Year and Color

This phase bridges the datasets that have been separately enhanced with year and color details. By merging these enriched tables, we systematically fill the informational gaps in our entries, ensuring a more comprehensive and complete dataset. This step is crucial for assembling a unified catalogue where each record is as detailed and accurate as possible, providing a seamless integration of temporal and visual data.

```python
import pandas as pd

### Merge df_a and df_b using the 'id' field
### We take all columns from df_a and only the 'Color' column from df_b
merged_data = pd.merge(df_a, df_b[['id', 'Color']], on='id', how='left')

### Reorder the columns to place 'id' before 'Model_Manufacturer_name'
column_order = ['id', 'Model_Manufacturer_name'] + [col for col in merged_data.columns if col not in ['id', 'Model_Manufacturer_name']]
merged_data = merged_data[column_order]

### We already have the Year information filled in df_a as needed, so we just need to add Color information
### Color from df_b will overwrite the Color in df_a where it exists
merged_data['Color'] = merged_data['Color'].combine_first(merged_data['Color'])

### Now you have a DataFrame with all information merged where the 'Year' comes from df_a and 'Color' from df_b
### Let's calculate missing data statistics to ensure everything aligns with expectations
total_entries = len(merged_data)
missing_brand = merged_data['Brand'].isnull().sum()
missing_year = merged_data['Year'].isnull().sum()
missing_color = merged_data['Color'].isnull().sum()

print(f"Total number of entries: {total_entries}")
print(f"Number of entries with missing 'Brand', excluding missing 'Model_Manufacturer_name': {missing_brand}")
print(f"Number of entries with missing 'Year', excluding missing 'Model_Manufacturer_name': {missing_year}")
print(f"Number of entries with missing 'Color', excluding missing 'Model_Manufacturer_name': {missing_color}")

### Export the merged data to an Excel file
merged_data.to_excel('cleaned_data_cars_catalogue_v5_final.xlsx', index=False)

### Display the DataFrame to verify results
display(merged_data.head())

```

# 3. Data Cleaning

## 3.1 Standardizing Brand Names

In this initial phase of cleaning our data, we turn our attention to the 'Brand' names within our collectible car dataset. To establish a consistent and reliable catalog, it's crucial to ensure that brand names are standardized—free from typos and formatting inconsistencies. By extracting a comprehensive list of all unique brand names, we enable a thorough review and normalization process.

### Here's how we approached this:

1. **Extraction of Unique Brand Names:** We began by gathering all unique brand names from our dataset, ensuring we captured the full variety of entries without repetition.

```python
import pandas as pd

### Assuming 'merged_data' is your DataFrame containing the data
### Extract unique brands and sort them alphabetically
unique_brands = merged_data['Brand'].dropna().unique()
unique_brands_sorted = sorted(unique_brands)

### Display the total number of unique brands
total_unique_brands = len(unique_brands_sorted)
print(f"Total number of unique brand names: {total_unique_brands}")

### Show only the first 15 and last 15 sorted unique brands
print("\nUnique Brands (First 15):")
for brand in unique_brands_sorted[:15]:
    print(brand)

print("\nUnique Brands (Last 15):")
for brand in unique_brands_sorted[-15:]:
    print(brand)

```

2. **Identification of Inconsistencies:** With the list in hand, we employed ChatGPT to scrutinize each brand name. This step was pivotal in identifying any typos or multiple formatting variations of the same brand name, which could lead to discrepancies in data analysis. This code snippet uses the fuzzywuzzy library, which provides functions for string matching and similarity. It calculates the similarity score between each pair of brands and prints out pairs with similarity scores above a certain threshold (in this case, 80).

```python
from fuzzywuzzy import fuzz
from itertools import combinations

### List to store potential typos
potential_typos = []

### Iterate through combinations of brands
for brand1, brand2 in combinations(unique_brands_sorted, 2):
    ##Compute Levenshtein distance between pairs of brands
    similarity_score = fuzz.ratio(brand1.lower(), brand2.lower())
    ##If similarity score is above a certain threshold, consider them potential typos
    if similarity_score > 80:  ##You can adjust this threshold as needed
        potential_typos.append((brand1, brand2, similarity_score))

### Print potential typos
print("Potential Typos:")
for typo_pair in potential_typos:
    print(f"{typo_pair[0]} - {typo_pair[1]} (Similarity Score: {typo_pair[2]})")

```

3. **Standardization Plan:** Following the identification process, we prepared to rectify these inconsistencies by mapping incorrect entries to their correct forms, thus standardizing brand names across the dataset.

```python
import pandas as pd

### Assuming 'merged_data' is your DataFrame containing the data

##Define the correction dictionary with wrong and correct spellings
corrections = {
    "ALFA": "Alfa",
    "ASTON": "Aston",
    "AWZ": "AWZ",
    "Autobianch": "Autobianchi",
    "BUGATTI": "Bugatti",
    "Betliet": "Berliet",
    "Brbham": "Brabham",
    "CHEVROLET": "Chevrolet",
    "CHRYSLER": "Chrysler",
    "CITROËN": "Citroën",
    "Cadillac": "Cadillac",
    "Citroen": "Citroën",
    "Duesemberg": "Duesenberg",
    "FERRARI": "Ferrari",
    "FIAT": "Fiat",
    "FORD": "Ford",
    "GAZ": "GAZ",
    "HUMMER": "Hummer",
    "ISO": "ISO",
    "ISUZU": "Isuzu",
    "Iveco": "IVECO",
    "LAMBORGHINI": "Lamborghini",
    "LOLA": "Lola",
    "MERCEDES-BENZ": "Mercedes-Benz",
    "Mercede-Benz": "Mercedes-Benz",
    "MINI": "Mini",
    "Maserati": "Maserati",
    "Mclaren": "McLaren",
    "Moskvitch": "Москвич",
    "Moskwitch": "Москвич",
    "Oldsmobil": "Oldsmobile",
    "PANHARD": "Panhard",
    "PORSCHE": "Porsche",
    "Plimouth": "Plymouth",
    "RENAULT": "Renault",
    "Red": "Red",
    "Saab": "SAAB",
    "SAVA": "Sava",
    "SAVIEM": "Saviem",
    "SEAT": "Seat",
    "SIAM": "Siam",
    "SIMCA": "Simca",
    "VW": "Volkswagen",
    "ЗИС": "ЗИС"
}

##Apply corrections to the 'Model_Manufacturer_name' column
merged_data['Model_Manufacturer_name'] = merged_data['Model_Manufacturer_name'].replace(corrections)

##Reorder the columns to place 'id' before 'Model_Manufacturer_name'
column_order = ['id', 'Model_Manufacturer_name'] + [col for col in merged_data.columns if col not in ['id', 'Model_Manufacturer_name']]
merged_data = merged_data[column_order]

##Now you have a DataFrame with corrected brand names
##Let's calculate missing data statistics to ensure everything aligns with expectations
total_entries = len(merged_data)
missing_brand = merged_data['Brand'].isnull().sum()
missing_year = merged_data['Year'].isnull().sum()
missing_color = merged_data['Color'].isnull().sum()

print(f"Total number of entries: {total_entries}")
print(f"Number of entries with missing 'Brand', excluding missing 'Model_Manufacturer_name': {missing_brand}")
print(f"Number of entries with missing 'Year', excluding missing 'Model_Manufacturer_name': {missing_year}")
print(f"Number of entries with missing 'Color', excluding missing 'Model_Manufacturer_name': {missing_color}")

##Export the merged data to a CSV file
merged_data.to_csv('cleaned_data_cars_merged.csv', index=False)

##Display the DataFrame to verify results
display(merged_data.head())  ##Modify this to display(merged_data) if you want to see the entire DataFrame

```

This methodical cleanup is the foundation for more complex data analysis and ensures that our inventory system will operate smoothly and accurately. As we progress, this attention to detail in data quality reinforces the reliability and usability of the collected information, paving the way for insightful analyses and robust reporting.

### 3.1.1 Standartizing Brand Names: Additional extractions


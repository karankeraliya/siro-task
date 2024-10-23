import json
import pandas as pd

# Function to flatten a nested JSON structure
def flatten_json(y):
    out = {}

    def flatten(x, name=''):
        if type(x) is dict:
            for a in x:
                flatten(x[a], name + a + '_')
        elif type(x) is list:
            i = 0
            for a in x:
                flatten(a, name + str(i) + '_')
                i += 1
        else:
            out[name[:-1]] = x

    flatten(y)
    return out

# Function to load JSON and convert to DataFrame
def json_to_df(json_file_path, output_csv_path=None, encoding='utf-8'):
    # Load the JSON file with specified encoding
    with open(json_file_path, 'r', encoding=encoding) as file:
        data = json.load(file)

    # If the data is a list of instances, flatten each
    if isinstance(data, list):
        flattened_data = [flatten_json(item) for item in data]
    else:
        # If it's a single instance, just flatten that one
        flattened_data = [flatten_json(data)]

    # Convert to DataFrame
    df = pd.DataFrame(flattened_data)
    
    # Save to CSV if a path is provided
    if output_csv_path:
        df.to_csv(output_csv_path, index=False)
    
    return df

# Example usage
json_file_path = r'D:\karan\interview task\siro\ctg-studies.json'  # Corrected file path
output_csv_path = 'data.csv'    # Optional: Replace with your desired CSV file path

# Convert JSON to DataFrame and optionally save as CSV
df = json_to_df(json_file_path, output_csv_path, encoding='utf-8')
print(df.head())  # Display the first few rows of the DataFrame

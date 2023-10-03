import pandas as pd

def process_name(name):
    """Reverse the order of first and last name and remove commas."""
    parts = name.split(', ')
    if len(parts) == 2:
        return f"{parts[1]} {parts[0]}"
    return name

# Read the two CSV files
df1 = pd.read_csv('file1.csv')
df2 = pd.read_csv('file2.csv')

# Process names in file1
df1['Name'] = df1['Name'].apply(process_name)

# Extract the 'Name' columns
names_1 = set(df1['Name'].dropna())
names_2 = set(df2['Name'].dropna())

# Find the common names, and the names unique to each list
common_names = names_1.intersection(names_2)
unique_to_1 = names_1 - names_2
unique_to_2 = names_2 - names_1

# Print out the results
print(f"Total number of entries in file1.csv: {len(df1)}")
print(f"Total number of entries in file2.csv: {len(df2)}")
print(f"Total number of unique names in file1.csv: {len(names_1)}")
print(f"Total number of unique names in file2.csv: {len(names_2)}")
print(f"Number of names in both files: {len(common_names)}")
print(f"Number of names unique to file1.csv: {len(unique_to_1)}")
print(f"Number of names unique to file2.csv: {len(unique_to_2)}")


import csv

def extract_oc_value(text):
    # Find the position of "OC="
    start_index = text.find('OC=')
    
    # If "OC=" is not found, return None
    if start_index == -1:
        return None
    
    # Extract the value after "OC="
    start_index += 3  # Move the start index after "OC="
    end_index = text.find(',', start_index)  # Find the next comma
    if end_index == -1:  # If no comma found, use the end of the string
        end_index = len(text)
    
    return text[start_index:end_index]

def separate_by_oc(input_csv_file):
    # Dictionary to group rows by the OC value
    oc_dict = {}
    
    with open(input_csv_file, 'r') as file:
        reader = csv.reader(file)
        
        # Read header
        header = next(reader)
        
        for row in reader:
            # Extract the OC value from the third column
            oc_value = extract_oc_value(row[2])
            
            # If OC value is not found, use 'None' as the key
            if not oc_value:
                oc_value = 'None'
            
            # Group rows by the OC value
            if oc_value not in oc_dict:
                oc_dict[oc_value] = []
            oc_dict[oc_value].append(row)
    
    # Print grouped rows for demonstration purposes
    for key, rows in oc_dict.items():
        print(f"Group by OC value: {key}")
        print(header)
        for row in rows:
            print(row)
        print("\n")

if __name__ == "__main__":
    separate_by_oc("your_file.csv")


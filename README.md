import csv
import os

def extract_oc_value(text):
    start_index = text.find('OC=')
    if start_index == -1:
        return None
    start_index += 3
    end_index = text.find(',', start_index)
    if end_index == -1:
        end_index = len(text)
    return text[start_index:end_index]

def separate_by_oc(input_csv_file):
    oc_dict = {}

    with open(input_csv_file, 'r') as file:
        reader = csv.reader(file)
        header = next(reader)

        for row in reader:
            oc_value = extract_oc_value(row[2])
            if not oc_value:
                oc_value = 'None'
            if oc_value not in oc_dict:
                oc_dict[oc_value] = []
            oc_dict[oc_value].append(row)

    # Create an output directory for the grouped CSV files
    output_dir = "grouped_csvs"
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)

    # Write each group to a new CSV file
    for key, rows in oc_dict.items():
        output_file = os.path.join(output_dir, f"group_{key}.csv")
        with open(output_file, 'w', newline='') as file:
            writer = csv.writer(file)
            writer.writerow(header)
            writer.writerows(rows)

if __name__ == "__main__":
    separate_by_oc("your_file.csv")


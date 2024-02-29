import os
import re
import pacparser

# Path for the PAC files
PACFILE = ["cba2.pac", "wrong.pac"]

def validate_pac_logic():
    # pacparser package used to validate pac file
    pacparser.init()
    for i in PACFILE:
        pac_file = os.path.join(os.path.dirname(__file__), f"{i}")
        if os.path.isfile(pac_file):
            try:
                pacparser.parse_pac_file(pac_file)
                print(f"Pacparser check {i} - {'PASS'}")
            except Exception:
                print(f"Pacparser check {i} - {'FAILED'}")
        else:
            print(f"Pacparser check {i} - {'not found'}")
    pacparser.cleanup()


def syntax_check():
    # This pattern attempts to match lines that may contain sequences of shExpMatch calls
    # and checks for the presence of single '|' or '&' operators between any characters or words,
    # which might suggest an incorrect use of logical operators.
    # Note: This pattern is a simplification and might need adjustments for complex PAC files.
    pattern = re.compile(r'shExpMatch\([^)]+\)\s*(\||&)\s*shExpMatch\([^)]+\)')

    for filename in PACFILE:
        filepath = os.path.join(os.path.dirname(__file__), filename)
        if os.path.isfile(filepath):
            with open(filepath, 'r') as file:
                content = file.read()
                # Search for the pattern in the file content.
                if pattern.search(content):
                    print(f"Syntax check for {filename} - FAILED (Incorrect operator usage detected)")
                else:
                    print(f"Syntax check for {filename} - PASS")
        else:
            print(f"Syntax check for {filename} - File not found")


def main():
    validate_pac_logic()  
    syntax_check()
    
if __name__ == "__main__":
    main()

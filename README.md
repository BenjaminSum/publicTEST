import os
import re
import pacparser

# Constants
PAC_FILES = ["cba2.pac", "wrong.pac"]
PAC_FILES_PATHS = [os.path.join(os.path.dirname(__file__), pac_file) for pac_file in PAC_FILES]

# Regex pattern for checking bitwise operators (|, &)
SYNTAX_PATTERN = re.compile(
    r"""
    (!)?\s*                                 # Optional NOT (!) before function
    (shExpMatch|dnsDomainIs|isResolvable)   # Function literal name(s)
    \([^)]+\)                               # Function arguments
    \s*                                     # Optional whitespace
    (?!.*?(?:\|\||&&))                      # Negative lookahead for logical operators
    .*?                                     # Ignore content until operators
    ([|&])(?!\1)                            # Match single | or &, not followed by itself
    (?:\s*//[^\n]*)?                        # Optional comment (to ignore comments after operator)
    \s*                                     # Optional whitespace
    (?:                                     # Optional group for nested conditions
        \(\s*                                   # Opening parenthesis for nested condition
        (.*?)                                   # Match all characters
        \s*\)                                   # Closing parenthesis
    |                                       # OR
        (!)?\s*                                 # Optional NOT (!) before function
        (shExpMatch|dnsDomainIs|isResolvable)   # Second function literal name(s)
        \([^)]+\)                               # Second Function arguments
    )
    """,
    re.VERBOSE
)

def validate_pac_logic(pac_file_path):
    """Validate PAC file logic using the pacparser library."""
    pacparser.init()
    try:
        pacparser.parse_pac_file(pac_file_path)
        return True
    except Exception as e:
        print(f"Pacparser check {os.path.basename(pac_file_path)} - FAILED with error: {e}")
        print("\n")
        return False
    finally:
        pacparser.cleanup()

def syntax_check_logical_operation(pac_file_path):
    """Check for incorrect logical operator usage."""
    if os.path.isfile(pac_file_path):
        with open(pac_file_path, 'r') as file:
            content = file.read()
            match = SYNTAX_PATTERN.search(content)
            if match:
                matched_string = match.group(0)
                start_pos = match.start()
                line_loc = content[:start_pos].count('\n') + 1  # + 1 for a closer estimate line number
                print(f"Syntax check for {os.path.basename(pac_file_path)} - FAILED")
                print(f"Incorrect operator usage detected around line {line_loc}: {matched_string.strip()}")
                print("\n")
                return False
            else:
                return True
    else:
        print(f"Syntax check for {os.path.basename(pac_file_path)} - File not found")
        print("\n")
        return False

def check_pac_files():
    """Check each PAC file and if all checks passed, print a passed message"""
    for pac_file_path in PAC_FILES_PATHS:
        validation_passed = validate_pac_logic(pac_file_path)
        syntax_passed = syntax_check_logical_operation(pac_file_path)
        if validation_passed and syntax_passed:
            print(f"Checks for {os.path.basename(pac_file_path)} - PASSED")
            print("\n")

def main():
    """Main function to check each PAC file."""
    check_pac_files()

if __name__ == "__main__":
    main()


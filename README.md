import os
import re
import pacparser

# Constants
PAC_FILES = ["cba2.pac", "wrong.pac"]
PAC_FILES_PATHS = [os.path.join(os.path.dirname(__file__), pac_file) for pac_file in PAC_FILES]

# Regex pattern for checking bitwise operators (|, &)
SYNTAX_PATTERN_AND_OR = re.compile(
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

# Regex pattern for validating proxy and DIRECT statements within return statements
SYNTAX_PATTERN_PROXY_RETURN_STATEMENTS = re.compile(
    r"""
    ^\s*return\s+["']                          # Start with 'return', mandatory whitespace, and opening quote
    (                                          # Begin main content group
        DIRECT                                 # Match 'DIRECT'
        |                                      # OR
        PROXY\s+[\w.-]+:\d+                    # Match 'PROXY', address/domain, and port
        (                                      # Begin group for multiple PROXY configurations
            \s*;\s*                            # Semicolon with optional surrounding spaces
            PROXY\s+[\w.-]+:\d+                # Additional 'PROXY' configuration
        )*                                     # Repeat group for additional configurations
    )                                          # End main content group
    ["']\s*;                                   # Closing quote, optional whitespace, and a MANDATORY semicolon outside quotes
    \s*$                                       # Optional trailing whitespace at the end
    """,
    re.VERBOSE | re.IGNORECASE
)


def validate_pac_logic(pac_file_path):
    """Validate PAC file logic using the pacparser library."""
    pacparser.init()
    try:
        pacparser.parse_pac_file(pac_file_path)
        return True
    except Exception as e:
        print(f"Pacparser check {os.path.basename(pac_file_path)} - FAILED with error: {e}\n")
        return False
    finally:
        pacparser.cleanup()


def syntax_check_return_statements(pac_file_path):
    """ Checks for correct semicolon usage, DIRECT/PROXY usage and Add/IP/Domain usage on return statements"""
    with open(pac_file_path, 'r') as file:
        content = file.readlines()
        line_loc = 0 # Line counter for help debugging PAC file
        syntax_error_found = False # Flag to track syntax errors
            
        # Ignore commented lines and lines without a return statement.
        for i in content:
            line_loc += 1
            if i.strip().startswith('//') or 'return' not in i:
                continue

            # Validate the return statement using the defined regex pattern
            if not SYNTAX_PATTERN_PROXY_RETURN_STATEMENTS.match(i.strip()):
                print(f"Syntax check for {os.path.basename(pac_file_path)} - FAILED")
                print(f"Incorrect format in return statement around line {line_loc}: {i.strip()}\n")
                syntax_error_found = True
                break # Stop checking after the first syntax error is found (Potentially upgrade here to append all errors in some table)
                                          
        if not syntax_error_found:
            return True
        else: 
            return False


def syntax_check_logical_operation(pac_file_path):
    """Check for incorrect logical operator usage"""
    with open(pac_file_path, 'r') as file:
        content = file.read()
        match = SYNTAX_PATTERN_AND_OR.search(content)
        if match:
            matched_string = match.group(0)
            start_pos = match.start()
            line_loc = content[:start_pos].count('\n') + 1  # + 1 for a closer estimate line number
            print(f"Syntax check for {os.path.basename(pac_file_path)} - FAILED")
            print(f"Incorrect operator usage detected around line {line_loc}: {matched_string.strip()}\n")
            return False
        else:
            return True


def check_pac_files():
    """Check each PAC file and if all checks passed, print a passed message"""
    for pac_file_path in PAC_FILES_PATHS:
        if not os.path.isfile(pac_file_path):
            print(f"File {os.path.basename(pac_file_path)} not found.\n")
            continue
        
        validation_passed = validate_pac_logic(pac_file_path)
        syntax_passed_semicolon = syntax_check_return_statements(pac_file_path)
        syntax_passed_and_or = syntax_check_logical_operation(pac_file_path)
        if validation_passed and syntax_passed_and_or and syntax_passed_semicolon:
            print(f"Checks for {os.path.basename(pac_file_path)} - PASSED\n")

def main():
    """Main function to check each PAC file."""
    check_pac_files()

if __name__ == "__main__":
    main()


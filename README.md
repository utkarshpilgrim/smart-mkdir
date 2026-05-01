# `tdir` - Smart Tagged Directory Creator & Searcher

The Document holds structure description for the `tdir` utility, its a smart combination of mkdir and tags utility built for those who lives and thrive in directories and uses it for their daily task. The utility has depth index of 5, meaning it could search for only 5 depth, but feel free to develop your own algorithms to work around with various searching techniques as the directory size grows more complex. 

# About `tdir`

The `tdir` script is a self-contained Bash utility created specifically for macOS that acts as an intelligent replacement for the standard `mkdir` command while also providing powerful tagging and search capabilities for directories. It allows users to create folders and instantly attach one or more descriptive tags to them, stores those tags in a hidden file inside each folder, and later enables quick searching across the filesystem for folders matching any of the provided tags. 

# Scripting `tdir`

The script defines two configuration variables at the top of the file so that future customization is simple and centralized without having to hunt through the code. The variable TAG_FILE is set to the string .tags, which determines the exact name of the hidden file that will be created inside every tagged directory to hold the list of tags, one per line. This hidden file approach keeps the tags associated directly with the folder they describe, ensures they travel with the folder if it is moved or copied, and remains invisible in normal Finder views on macOS. The second configuration variable, SEARCH_DEPTH, is set to the integer 5 and controls the maximum number of subdirectory levels the search function will traverse when looking for tagged folders. This value prevents the search from becoming excessively slow or resource-intensive on very large directory trees while still covering typical project structures; users can edit this number directly in the script if they need deeper or shallower recursion for their particular workflow.

# Helper Functions

Helper Functions explains the four main ways to call `tdir`, including creating a folder with tags in shorthand form, using the explicit create subcommand, performing a tag-based search, listing tags on a specific folder, or displaying help. After printing the message the function immediately calls exit 1 to terminate the script with a failure status so that the user is not left hanging in an ambiguous state. Input to this function comes indirectly from the main logic when no arguments are supplied or when an unrecognized command is given, and its output always goes to the terminal so the user receives immediate feedback about correct syntax.

The next helper function, save_tags, is responsible for persisting the list of tags that the user provides when creating or updating a folder. It receives the target directory path as its first argument and then shifts the remaining arguments into an array called tags so that every word supplied after the folder name is treated as an individual tag. Inside the function a quick check determines whether any tags were actually passed; if the array is empty the function simply returns without doing anything. When tags exist, the function pipes the array contents through printf to output each tag on its own line, then pipes that through sort -u to remove duplicates and alphabetize the list for consistency. The final sorted unique list is redirected into a new file at `${dir}/${TAG_FILE}` inside the target folder, overwriting any previous tags file if one already existed. A friendly confirmation message is printed to standard error so it does not interfere with command substitution or piping, informing the user exactly which tags were saved. This function is called automatically by the create logic once the directory has been successfully made, and it receives its input directly from the command-line arguments that the user typed after the folder name.

The third helper function, `get_tags`, serves as the counterpart to save_tags by retrieving the stored tags for any given folder. It accepts a single argument which is the path to the directory being inspected. The function first checks whether the hidden .tags file exists inside that directory using a simple test with `[[ -f ... ]]`. If the file is present, the function outputs its entire contents to standard output using cat, allowing the calling code to capture or display the tags. If the file does not exist the function simply echoes an empty string so that downstream logic can gracefully handle untagged folders without throwing errors. This design keeps the function lightweight and idempotent, and its output is consumed by both the search logic and the list-tags command to display human-readable tag information.

# Test it now

In order to test it locally in the local environment you can use the `./tdir` followed with helper functions in a way given below: 

```
# Create folder with tags
./tdir security-folder security cloud python aws
./tdir create client-folder client finance compliance

# Search
./tdir search security cloud
./tdir search python

# List tags of current folder
./tdir list-tags <folder-name>
```

Save the script once you feel like using it and move the script to binary folder. 

```

mv tdir /usr/local/bin/

chmod +x /usr/local/bin/tdir
```



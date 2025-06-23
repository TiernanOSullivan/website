---
title: "Open Anonymisation tools"
description: "Review of different open tools that can be used for anonymisation of qualitative data."
keywords: "anonymization, string processing, pseudonymization, text, regexp"
draft: false
weight: 1
author: "Tiernan O'Sullivan"
aliases:
  - /open-anonymisation-tools
  - /anonymisation-tools
---

## Overview

Anonymisation of data is an important step in making data reusable and abiding by commitments to the privacy of research participants. Recognising what elements of your data pose a privacy risk is one task. It is another task to develop a workflow to apply these changes to your data set. This can be especially complicated if your data is stored accross multiple folders or in different formats. 

In this article we explain how to install and use open source tools for manipulating qualitative data, which you may find useful when creating an anonymized or pseudonymized version of your research data. 


## AnonymoUUs

Developed by Utrecht University,  the AnonymoUUs Python package is used in identifying and replacing strings in multiple files and folders at once. As you collect data for analysis it is typically structured in multiple files and formats. With AnonymoUUs, you can read through an entire file tree regardless of the format, identify keywords and make replacements. Importantly, the keywords can be replaced in the files contents, file names and also the folder names. 

To use the tool, you specify a replacement mapping - a set of keywords to replace and the substitutes that will replace them - and the name of a file tree or zipped archive where you want the package to make replacements.  AnonymoUUs will open each file in the tree, and will substitute any found keyword in the files. As well as this, the package will change the text in file/folder paths. 

If you have multiple files which contain personal data, such as a person’s name or ID, you can use this package to substitute that the personal data with a different string. For example, you can use the package to review a number of files and folders to identify specific personal names and replace them with pseudonyms. 

The package has a number of methods that allow you to substitute a string in this way and it has been developed to work with text in different filetypes, provided that there is UTF-8 encoding.  

{{% tip %}}
### Key points
- AnonymoUUs is a Python package that provides methods for replacing text in files and folders.  

- It can process text-based files like .txt, .html, .json, and .csv. 

- It supports (nested) zip archives, which will be unpacked and processed in a temp folder, and zipped again.

- It provides options to use a dictionary, a CSV file, or a function for the replacement mapping

- It can overwrite file contents or create a modified copy in a new location.

- It can handle file and folder path substitutions in addition to file contents.
{{% /tip %}}

## Installation

To install AnonymoUUs, you can use the following command in your terminal:

{{% codeblock %}} 
```python
pip install anonymoUUs
```
{{% /codeblock %}}

## Replacing Text with AnonymoUUs

To replace text using AnonymoUUs: 
- you must specify keywords - what text string(s) or patterns of text should be changed - and substitutes - what text should replace each keyword or pattern.
- You must indicate what folders or files the operation should run on. 

AnonymoUUs has three methods generating a replacement mapping. 

## Replacing text using a dictionary 
A dictionary is written in python using the curly brackets:

example_dictionary = {keys: values}

In this scenario, the dictionary keys will represent the text you want to replace and the values represent the pseudonyms or replacement text. 

We can instruct the program to follow these key-values pairings to substitute text in a single file or in a group of files. We can overwrite the file or create a modified copy in a new location.


```python

from anonymouus import Anonymize

# Create a dictionary whose key-value pairs map to text and substitute
dictionary = {
    "Alex": "Arizona Worker 1", 
    "Emily": "Arizona Worker 2",
    "Frank" : "Texas Worker 1"
    } 

# Creating an instance of the class Anonymize
anonymize_dict = Anonymize(dictionary)

# Process the files in 'interview_transcripts'and 
# write a copy to the location 'pseudonymised_files'
original_file = '/Users/osulliva/Documents/interview_files'
new_file = '/Users/osulliva/Desktop/pseudonymised_files'

anonymize_dict.substitute(original_file, new_file)
```
###

In this example:

- We imported the Anonymize class from the anonymoUUS package

- We created a dictionary mapping the text we want to replace with its replacement values.

- We instantiated the Anonymize class as 'anonymize_dict' allowing us to use the substitute() method

- We assigned the file path of the original documents to the variable 'original_file'

- We assigned the destination folder path to the variable 'new_File'

- We ran the substitute method which open and read all documents in 'original_file' and wrote pseudonymized versions based on our dictionary to the 'new_file' location.

## Replacing text using a CSV file

In the previous example, the keys and values of a dictionary written in the python console instructed what keywords to replace and the substitutions to use for each keyword. 
Rather than writing a dictionary in the console, we can create a csv file that records the text and its replacement.
AnonymoUUs will will interpret a csv file as a dictionary of keywords and their substitutes. 

- When AnonymoUUs reads a csv file it assumes it consists of two columns

- The left column is treated as the keys, and the right column is treated as the values. 

- It is necessary to include column headers

In this way, it is possible to write the list of keywords and substitutes elsewhere and keep your code shorter. 

{{% codeblock %}} 
```python
from anonymouus import Anonymize

# select a csv file to be used as the dictionary of 
# keywords and substitutes
csv = '/Users/osulliva/Documents/interview_substitutions.csv'
anonymize_csv = Anonymize(csv)

# write a copy with the substitutes to the location 'pseudonymised_files'
original_file = '/Users/osulliva/Documents/interview_files'
new_file = '/Users/osulliva/Documents/pseudonymised_files'

anonymize_csv.substitute(original_file, new_file)


```
{{% /codeblock %}}

{{% tip %}}
AnonymoUUS is a flexible tool that allows you to treat string patterns as keys. Let's imagine we wanted to write a copy of our files where all email addresses have been redacted. To do this, we could use a regular expression as our key and value as [email redacted]. 

The regular expression

'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' 

will pick up on a string that consists of:

- [letters a-z or A-Z, numbers, or the expressions ._%+-]

- @

- [letters a-z or A-Z, numbers, or the expressions .-]

- .

- [letters a-z or A-Z] which is at least two characters long

For more information regarding regular expressions visit [Learn Regular Expressions](https://tilburgsciencehub.com/topics/Manage-manipulate/manipulate-clean/textual/learn-regular-expressions/)

To apply this, we could write this key in our dictionary or as an entry in column 1 of a CSV.

**Create a dictionary whose key-value pairs map to text and substitute**

{{% codeblock %}}
```python
dictionary = {
    "Alex": "Arizona Worker 1", 
    "Emily": "Arizona Worker 2",
    "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" : "[email redacted]"
    } 
```
{{% /codeblock %}}

{{% /tip %}}


## Replacing text using a function

It is possible to pass a custom function to the substitute method. This is useful whenever we want to apply different substitutions to strings based on whether they meet certain criteria.
In the example below, we find two-digit numbers in our files (e.g., ages) and replace them with a generalised age band.  
We use the regular expression `\b\d{2}\b` to match any standalone two-digit number. We then return a different substitute based on value of the string when it has been converted to an integer.


### Substituting Ages
{{% codeblock %}} 
```python
from anonymouus import Anonymize

#Make a function to substitute two digit numbers with an age band
def map_to_age_band(matched_text):
    number = int(matched_text)
    if 10 <= number < 20:
        return '[between 10 and 20]'
    elif 20 <= number < 30:
        return '[between 20 and 30]'
    elif 30 <= number < 40:
        return'[between 30 and 40]'
    elif 40 <= number < 50:
        return '[between 40 and 50]'
    else:
        return '[over 50]'
    

# Create an Anonymize instance
anon = Anonymize(map_to_age_band, pattern=r'\b\d{2}\b')

#Set file paths
input_file = 'C:\\Users\\osulliva\\Documents\\interview_files\\interviewee_profile.csv'
replacement_file = 'C:\\Users\\osulliva\\Documents\\interview_files\\inteviewees_with_age_bands.csv'

# Call the substitute method to replace ages in the input file and save to the replacement file
anon.substitute(input_file, replacement_file)
```
{{% /codeblock %}}

### Randomised ages
We can  use AnonymoUUS to randomize a numerical entry. The substitute can be based off an existing list or parameter, or it can be based off of a standard list of integers. Lets imagine we have a folder containing files from our survey or interview where age is recorded.    

{{% codeblock %}}
```python
from anonymouus import Anonymize
from random import randint

#Make a function to return a random two digit number for ages
def random_age(matched_text):
    number = int(matched_text)
    return randint(18,99) 

# Create an Anonymize instance
anon = Anonymize(random_age, pattern=r'\b\d{2}\b')

#Set file paths
input_file = 'C:\\Users\\osulliva\\Documents\\interview_files\\interviewee_profile.csv'
replacement_file = 'C:\\Users\\osulliva\\Documents\\interview_files\\inteviewees_with_age_bands.csv'

# Call the substitute method to replace ages in the input file and save to the replacement file
anon.substitute(input_file, replacement_file)
```
{{% /codeblock %}}


### Redacting based on patterns: email addresses

You can also use AnonymoUUS to scan your file tree for patterns like email addresses, and replace them with a null value, pseudonym, or redaction tag.

Below is an example where we redact all email addresses found in the file tree by replacing them with [email redacted].

{{% codeblock %}} 
```python
from anonymouus import Anonymize

# Define a function that returns a replacement value
def redact_email(text):
    return '[email redacted]'

# Create an Anonymize instance with a regex pattern for email addresses
anon = Anonymize(redact_email, pattern=r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}')

# Specify the file paths (replace these with your actual locations)
input_file = '.../original_file.txt'
output_file = '.../redacted_file.txt'

# Run the substitution
anon.substitute(input_file, output_file)
```
{{% /codeblock %}} 


More examples of functions written for AnonymoUUs can be found on the GitHub page.

## To be developed further
Avoid substition mistakes
False positives e.g. Ted and creaTed
False negatives e.g. Tde 


Substitute method in AnonymoUUS - log of changes(?) e.g. number of occurances
-Could you use Git to track differences

Any other methods or scripts that could be added to improve testing/validation
Testing

Rather than replacements or redaction, can AnonymoUUS apply perturbation to a range of variables - e.g. perturbation of any string preceeded by a $ or €


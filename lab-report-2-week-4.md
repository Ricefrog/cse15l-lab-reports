# Lab Report 2

## Topic: Debugging

<br>

**Note:** *Although we were supposed to showcase three bug-fixes, our group only had to fix one.*

### Our Program
Our program's goal was to take in a markdown file as input and output the URLs of all links within that file.
<br>
<br>
### Failure-Inducing Input
This the test file that caused a failure in our program. 
<br>
<br>
![test-file6](./screenshots/week4/test-file6.png)
<br>
[https://github.com/JessalynWang/markdown-parse/blob/main/test-file6.md](https://github.com/JessalynWang/markdown-parse/blob/main/test-file6.md)
<br>
<br>
The problem was that our program was including the URLs/sources of images, when the expected behavior was for images to be ignored, as they are not links. 
<br>
<br>

### The Symptom
```
java MarkdownParse.java test-file.md
[https://something.com, some-page.html]
java MarkdownParse.java test-file2.md
[https://something.com, some-page.html, https://something.com, some-page.html]
java MarkdownParse.java test-file3.md
[]
java MarkdownParse.java test-file4.md
[]
java MarkdownParse.java test-file4.md
[]
java MarkdownParse.java test-file5.md
[]
java MarkdownParse.java test-file6.md
[page.com]
java MarkdownParse.java test-file7.md
[]
java MarkdownParse.java test-file8.md
[a link on the first line]
```
All of these outputs are correct, except for when the program is run on test-file6.md. The output includes 'page.com', which is a URL for an image. Because we expected the output to be an empty list, this was a sign that showed us that our program had a bug.

<br>
<br>

### The Bug
Our program used a stack to iterate through the characters in a file and add URLs to a list of strings if a certain pattern was found.
This pattern was `[placeholder](URL)`, where 'placeholder' and 'URL' are arbitrary strings. 
The cause of our bug lay in the fact that the format for images in markdown is 
`![placeholder](src)`, which is a superset of the 'link' patterns that our program was looking for. 
Because the format of images fit the pattern we were searching for, the program included the URLs/sources of images in its output.
<br>
<br>
### The Fix
These are the changes we made:
<br>
![diff](./screenshots/week4/diff.png)
<br>
The key change is in lines 54-55. We added a condition where if the current character 
is an exclamation mark, we are not at the end of the file, and the next character is 
a right-bracket we increment the current index by two and move onto the next iteration
of the loop. This will invalidate sequences that start with `![` from being considered
as potential patterns for links in our program.

<br>
<br>

### In conclusion:
- We first noticed a *symptom* in the output of our program -> our program was incorrectly treating images as links. 
- The failure-inducing input was the only test file that contained an image. It was also the only test file that did result in a correct output.
- We walked through the logic of our program to determine the *bug* that was causing or symptom. It was due to the fact that we had no checks in place to make sure that we ignored patterns in the file that indicated images, which have similar patterns to links in markdown.

<br>
<br>
[Home](/index.html)

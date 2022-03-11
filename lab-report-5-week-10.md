# Lab Report 5

## Topic: Comparing Test Outputs

For this week's lab report we are tasked with picking out
any two tests from the commonmark-spec tests where
our group's implementation has different answers than the 
implementation provided to us in week 9.

<br>

### __Finding Differences__

<br>
For both implementations, I ran the bash script below
and redirected the output into a test file. 

```
for file in test-files/*.md;
do
        echo -n $file
        echo -n ": "
        java MarkdownParse $file
done
```

I then used 'diff' to show the differences between
the files.
<br>
<br>
<br>


*In the diffs displayed, the first line is from our 
implementation and the second line is from the week 9
implementaiton.*

### __Test 1__

diff:
```
46c46
< test-files/14.md: []
---
> test-files/14.md: [/foo]
```

14.md:
```
\*not emphasized*
\<br/> not a tag
\[not a link](/foo)
\`not code`
1\. not a list
\* not a list
\# not a heading
\[foo]: /url "not a reference"
\&ouml; not a character entity
```

Based on the CommonMark demo site, the week 9 output
is incorrect. There are no links within the file, so the
correct output should be an empty list, not `[/foo]`.

The bug is that week 9's implementation does not 
have any checks in place for when square brackets are escaped with
a backslash. If any brackets/parentheses have a backslash 
immediately in front of them, they will be displayed in markdown
as characters, and should not be included when considering potential links.

The cause of this bug is on line 57.

```
int nextOpenBracket = markdown.indexOf("[", currentIndex)
```

There should be an if statement that checks if there is a
backslash in front of character at the 'nextOpenBracket' 
index and increments
currentIndex before iterating over the loop again
if that condition is true.

<br>
<br>

### __Test 2__

diff:
```
527c527
< test-files/573.md: []
---
> test-files/573.md: [/url]
```

573.md:
```
![foo ![bar](/url)](/url2)
```

Based on the CommonMark demo site, the week 9 output
is incorrect. There are no links within the file, so the
correct output should be an empty list, not `[/url]`.

The bug is that week 9's implementation does not 
have any checks in place for when there are exclamation marks 
immediately before forward square brackets, signifying an image 
rather than a link. 


The cause of this bug is on line 57.

```
int nextOpenBracket = markdown.indexOf("[", currentIndex)
```

There should be an if statement that checks if there is an
exclamtion mark in front of the character at the 'nextOpenBracket' 
index and increments
currentIndex before iterating over the loop again
if that condition is true.

<br>
<br>


[Home](/index.html)
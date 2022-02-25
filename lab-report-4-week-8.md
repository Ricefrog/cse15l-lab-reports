# Lab Report 4

## Topic: More Debugging

For this week's lab report we were asked to run three snippets of 
markdown on both our groups's program and the program of another group, 
which we had reviewed last week.

Our repo: [https://github.com/Ricefrog/markdown-parse](https://github.com/Ricefrog/markdown-parse)

The other group's repo: [https://github.com/floatboat/markdown-parse](https://github.com/floatboat/markdown-parse)

<br>

### __The Snippets__

<br>
snippet1.md

```
`[a link`](url.com)

[another link](`google.com)`

[`cod[e`](google.com)

[`code]`](ucsd.edu)
```
<br>

Expected Output: 
```
[`google.com, google.com, ucsd.edu]
```
<br>
snippet2.md

```
[a [nested link](a.com)](b.com)

[a nested parenthesized url](a.com(()))

[some escaped \[ brackets \]](example.com)
```
<br>

Expected Output: 
```
[a.com, a.com(()), example.com]
```

<br>
snippet3.md

```
[this title text is really long and takes up more than 
one line

and has some line breaks](
    https://www.twitter.com
)

[this title text is really long and takes up more than 
one line](
    https://ucsd-cse15l-w22.github.io/
)


[this link doesn't have a closing parenthesis](github.com

And there's still some more text after that.

[this link doesn't have a closing parenthesis for a while](https://cse.ucsd.edu/



)

And then there's more text
```
<br>

Expected Output: 
```
[https://ucsd-cse15l-w22.github.io/]
```

<br>
<br>

### __JUnit Tests__

These are the tests I added to my JUnit file to test the outputs of both 
programs when using these snippets as input.

```
        @Test
        public void snippet1() throws IOException, NoSuchFileException {
        List<String> correctOutput = List.of("`google.com", "google.com", "ucsd.edu");
        Path fileName = Path.of("snippet1.md");
        // read the file contents into a string
            String contents = Files.readString(fileName);
        // run getLinks on the contents of the file
        ArrayList<String> links = MarkdownParse.getLinks(contents);
        assertEquals(correctOutput, links);
        }

        @Test
        public void snippet2() throws IOException, NoSuchFileException {
        List<String> correctOutput = List.of("a.com", "a.com(())", "example.com");
        Path fileName = Path.of("snippet2.md");
        // read the file contents into a string
            String contents = Files.readString(fileName);
        // run getLinks on the contents of the file
        ArrayList<String> links = MarkdownParse.getLinks(contents);
        assertEquals(correctOutput, links);
        }

        @Test
        public void snippet3() throws IOException, NoSuchFileException {
        List<String> correctOutput = List.of("https://ucsd-cse15l-w22.github.io/");

        Path fileName = Path.of("snippet3.md");
        // read the file contents into a string
            String contents = Files.readString(fileName);
        // run getLinks on the contents of the file
        ArrayList<String> links = MarkdownParse.getLinks(contents);
        assertEquals(correctOutput, links);
        }

```

<br>
<br>

### __The Results of Runnning These Tests__

<br>

Our implementation (failed):
```
There were 3 failures:
1) snippet1(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.snippet1(MarkdownParseTest.java:139)
2) snippet2(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, a.com((, example.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.snippet2(MarkdownParseTest.java:150)
3) snippet3(MarkdownParseTest)
java.lang.AssertionError: expected:<[https://ucsd-cse15l-w22.github.io/]> but was:<[
    https://www.twitter.com
,
    https://ucsd-cse15l-w22.github.io/
]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.snippet3(MarkdownParseTest.java:162)

FAILURES!!!
Tests run: 10,  Failures: 3
```

Their implementation (failed):
```
There were 3 failures:
1) snippet1(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com, ucsd.edu]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.snippet1(MarkdownParseTest.java:37)
2) snippet2(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, a.com((, example.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.snippet2(MarkdownParseTest.java:48)
3) snippet3(MarkdownParseTest)
java.lang.AssertionError: expected:<[https://ucsd-cse15l-w22.github.io/]> but was:<[, , ]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.snippet3(MarkdownParseTest.java:60)

FAILURES!!!
Tests run: 5,  Failures: 3
```

<br>
<br>

### __Brainstorming Fixes__

I will speculate as to whether or not each bug fix will be small (<10 LOC) 
or more involved.

<br>

**Snippet 1**

These are the lines that are creating problems for our implementation:
```
`[a link`](url.com)
```
```
[`code]`](ucsd.edu)
```

The underlying issue is that our implementation is not disregarding
square brackets that are between two backticks. I believe 
this is an easy fix; 
whenever we encounter a square bracket we can check if there is a backtick
before it and another after it on the same line. If that is the case, we 
disregard the square bracket and continue as usual.

<br>

**Snippet 2**

This is the line that was creating a problem for our implementation:
```
[a nested parenthesized url](a.com(()))
```

The issue is that our program was saving `a.com((` instead of `a.com(())`.
This is because our program will match the first pair of parentheses it 
finds and store the characters in between them as a potential link. Because
we are already using a stack-based implementation, this is an easy fix. We
just need to start adding *all* forward parentheses on the stack after 
finding the first, and popping the stack after each closing bracket is
found. When we pop the final forward bracket off the stack, we know that 
the characters between them are a potential link.

<br>

**Snippet 3**

These are the lines that were creating a problem for out implementation:
```
[this title text is really long and takes up more than 
one line

and has some line breaks](
    https://www.twitter.com
)
```

The issue is that our implementation was counting this as a valid link when
it is not. According to the [commonmark website](https://spec.commonmark.org/dingus/) it appears that newlines in between square brackets are 
allowed in links, but contiguous newlines are not. The lines in question
contain two newlines in a row between the square brackets, making it an invalid
link. Because our program is a stack-based implementation, this is an easy fix.
We should just check if we encounter contiguous newlines while a forward 
square bracket is on the top of the stack. If we encounter two newlines
in a row and a forward bracket is on the top of the stack, 
we just pop the stack.

<br>

[Home](/index.html)
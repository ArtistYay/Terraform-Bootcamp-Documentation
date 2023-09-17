# Writing Good Documentation

## Step 1 - Using Codeblocks

Codeblocks in markdown make it *very easy* for tech people to **copy, paste, and share** code. A good engineer uses Codeblocks whenever possible.

Why? becuase it allows others to copy and paste their code to replicate or research isssues.

- In order to create codeblocks in markdown you need to use three backticks. (```)

![Backtick image](

```
Print ("Hello, World!")
```
- When you can you should attempt to apply syntax highlighting to you codeblocks.

```python
Print ("Hello, World!")
```

- Good engineers use codeblocks for errors that appear in the console.

```bash
Traceback (most recent call last):
File "example.py", line 4, in <module>
result = divide(10, 0)
File "example.py", line 2, in divide
return a / b
ZeroDivisionError: division by zero
```
 > Here is an example of using a codeblock for an error that appears in bash.
  
## Step 2 - Use Github Flavored Markdown Task Lists

Github extends markdown to have a list where you can check off items. Using lists can show that your doing the work and people can see your making an effort making it easier to help you. [<sup>[2]</sup>](#references)  

- [x] Finish step 1 :tada: [<sup>[3]</sup>](#references)
- [ ] Finish step 2
- [ ] Finish step 3

## Step 3 - Use Emojis (Optional)

Github Flavored Markdown (GFM) supports emoji shortcodes.
Here are some examples: [<sup>[4]</sup>](#references)

| Name  | Shortcode | Emoji |
| --- | --- | --- |
| Cloud | `:cloud:` | :cloud: |



## References

A good way to write documentation is to always site your references if you have ever used some.

- [Basic writing and formatting syntax (Github Flavored Markdown)](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#quoting-code) <sup>[1]<sup>

- [Github Flavored Markdown - Task List](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/about-task-lists) <sup>[2]</sup>

- [Github Flavored Markdown - Emoji Cheat Sheet](https://github.com/ikatyang/emoji-cheat-sheet/blob/master/README.md) <sup>[3]</sup>

- [Github Flavored Markdown - Create a table](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/organizing-information-with-tables#creating-a-table) <sup>[4]</sup>

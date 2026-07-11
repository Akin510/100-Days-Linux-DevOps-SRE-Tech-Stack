# MODULE 02/07 – Practice Lab: Pattern Matching Files Using Shell Expansions
> **Hands-on Practice Lab – Pattern Matching (Globbing) in Bash**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Use shell pattern matching (globbing).
- Match filenames using wildcard characters.
- Search files based on prefixes and patterns.
- Match filenames of a specific length.
- Understand Bash shell expansions.
- Prepare for advanced shell features such as tilde expansion, brace expansion, variable expansion, and command substitution.

---

# 📖 Introduction

The **Bash shell** provides several types of command-line expansions that make working with files and commands much easier.

Some of the most common shell expansions include:

- Pattern Matching (Globbing)
- Tilde Expansion
- Brace Expansion
- Variable Expansion
- Command Substitution

In this lab, we will focus on **Pattern Matching**, also known as **Globbing**.

Pattern matching allows you to search for files using wildcard characters instead of typing every filename manually.

---

# What is Pattern Matching?

Pattern matching allows the Bash shell to automatically match filenames using special wildcard characters.

Instead of typing every filename individually, you can use patterns to match multiple files.

For example:

```bash
ls *.txt
```

This displays every file ending with `.txt`.

---

# Common Wildcards

| Wildcard | Meaning |
|----------|---------|
| `*` | Matches zero or more characters |
| `?` | Matches exactly one character |
| `[abc]` | Matches any one character inside the brackets |
| `[a-z]` | Matches any character within a range |

---

# 🔬 Lab 1 – Create a Practice Directory

Create a directory named **glob**.

```bash
mkdir glob
```

Move into the directory.

```bash
cd glob
```

Verify your location.

```bash
pwd
```

---

# 🔬 Lab 2 – Create Practice Files

Create several files using the `touch` command.

```bash
touch able alpha baker bravo cast charlie delta dog easy echo
```

Verify the files.

```bash
ls
```

Expected Output:

```text
able
alpha
baker
bravo
cast
charlie
delta
dog
easy
echo
```

---

# 🔬 Lab 3 – Display All Files

List every file in the directory.

```bash
ls
```

This displays all available files.

---

# 🔬 Lab 4 – Find Files Starting with "a"

Suppose you want to display only the files whose names begin with the letter **a**.

Command:

```bash
ls a*
```

Expected Output:

```text
able
alpha
```

### Explanation

| Pattern | Meaning |
|----------|---------|
| `a*` | Any filename that starts with the letter **a** |

---

# 🔬 Lab 5 – Find Files Containing the Letter "a"

Suppose you want to display every filename that contains the letter **a** anywhere in its name.

Command:

```bash
ls *a*
```

Expected Output:

```text
able
alpha
baker
bravo
cast
charlie
delta
easy
```

### Explanation

| Pattern | Meaning |
|----------|---------|
| `*a*` | Match every filename containing the letter **a** |

---

# 🔬 Lab 6 – Find Files Starting with "a" or "c"

Suppose you want filenames that start with either:

- a
- c

Use square brackets.

```bash
ls [ac]*
```

Expected Output:

```text
able
alpha
cast
charlie
```

### Explanation

| Pattern | Meaning |
|----------|---------|
| `[ac]*` | Match filenames beginning with either **a** or **c** |

---

# 🔬 Lab 7 – Find Filenames with Exactly Four Characters

Suppose you want to display only filenames that contain **exactly four characters**.

Command:

```bash
ls ????
```

Expected Output:

```text
able
cast
easy
echo
```

### Explanation

Each `?` matches **exactly one character**.

```text
???? = 4 characters
```

---

# 🔬 Lab 8 – Find Filenames with Exactly Five Characters

Display filenames containing **exactly five characters**.

Command:

```bash
ls ?????
```

Expected Output:

```text
alpha
baker
bravo
delta
```

### Explanation

```text
????? = Exactly 5 characters
```

---

# Understanding Wildcards

## The Asterisk (`*`)

Matches:

- Zero characters
- One character
- Many characters

Examples:

```bash
ls *.txt
```

All text files.

```bash
ls a*
```

Everything beginning with **a**.

---

## The Question Mark (`?`)

Matches:

Exactly one character.

Examples:

```bash
ls ?
```

Matches filenames containing exactly one character.

```bash
ls ????
```

Matches filenames with exactly four characters.

---

## Square Brackets (`[]`)

Match one character from a given set.

Example:

```bash
ls [ab]*
```

Matches filenames beginning with:

- a
- b

---

# Practice Examples

| Command | Result |
|----------|--------|
| `ls *` | Display all files |
| `ls a*` | Files starting with **a** |
| `ls *a*` | Files containing **a** |
| `ls [ac]*` | Files starting with **a** or **c** |
| `ls ????` | Files with exactly four characters |
| `ls ?????` | Files with exactly five characters |

---

# Directory Structure

```text
glob
├── able
├── alpha
├── baker
├── bravo
├── cast
├── charlie
├── delta
├── dog
├── easy
└── echo
```

---

# 🧪 Practice Exercises

---

## Exercise 1

Create a practice directory.

```bash
mkdir glob
cd glob
```

---

## Exercise 2

Create the following files.

```bash
touch apple alpha bat ball cat cake dog eagle
```

---

## Exercise 3

Display files beginning with **a**.

```bash
ls a*
```

---

## Exercise 4

Display files containing **a**.

```bash
ls *a*
```

---

## Exercise 5

Display files beginning with **b** or **c**.

```bash
ls [bc]*
```

---

## Exercise 6

Display filenames with exactly four characters.

```bash
ls ????
```

---

## Exercise 7

Display filenames with exactly five characters.

```bash
ls ?????
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

No files match the pattern.

Command:

```bash
ls z*
```

Output:

```text
ls: cannot access 'z*': No such file or directory
```

There are no filenames beginning with **z**.

---

### Scenario 2

You accidentally use too many question marks.

Example:

```bash
ls ???????
```

Only filenames with exactly seven characters will match.

---

### Scenario 3

You want to match multiple starting letters.

Command:

```bash
ls [abc]*
```

This matches filenames beginning with:

- a
- b
- c

---

### Scenario 4

You want to match every file.

Command:

```bash
ls *
```

The `*` wildcard matches all filenames.

---

# 📌 Quick Revision

| Pattern | Meaning |
|----------|---------|
| `*` | Zero or more characters |
| `?` | Exactly one character |
| `[ab]` | Match either **a** or **b** |
| `a*` | Starts with **a** |
| `*a*` | Contains **a** |
| `????` | Exactly four characters |
| `?????` | Exactly five characters |

---

# 📖 Key Takeaways

- Pattern matching is performed by the Bash shell.
- `*` matches zero or more characters.
- `?` matches exactly one character.
- `[]` matches one character from a specified set.
- Pattern matching makes working with multiple files much faster and easier.
- Globbing is an essential Linux skill used daily by system administrators.

---

# 💡 Remember

> **Think of Bash Pattern Matching as a search filter.**
>
> - `*` means **"match anything."**
> - `?` means **"match exactly one character."**
> - `[abc]` means **"match one character from this list."**
>
> **Golden Rules:**
>
> ```text
> *      = Many characters
> ?      = One character
> [abc]  = One character from the list
> ```
>
> **Mastering pattern matching will save you a lot of time when working with files in Linux.**
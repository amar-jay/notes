Usually I would say this isn't one of the regular tutorials but I can't say so for this.

- [ ]  Basic WildCards
- [ ]  Assertions [`^`, `$`, `x(?=y)`, `x(?!y)`, `(?<=y)x`, `(?<!y)x`, `\b`, `\B`]
- [ ]  Character classes [`\`, `.`, `\cX`, `\d`, `\D`, `\f`, `\n`, `\r`, `\s`, `\S`, `\t`, `\v`, `\w`, `\W`, `\0`, `\xhh`, `\uhhhh`, `\uhhhhh`, `[\b]`]
- [ ]  Groups and ranges [`(x)`, `(?:x)`, `(?<Name>x)`, `x|y`, `[xyz]`, `[^xyz]`, `\*Number*`]
- [ ]  Quantifiers [`*` ,  `+`, `?`, `x{*n*}` , `x{*n*,}` , `x{*n*,*m*}`]
- [ ]  Unicode Property escapes [`\p{*UnicodeProperty*}`,  `\P{*UnicodeProperty*}`]

Regular expressions are search patterns used to match character combinations in text. In JavaScript, regular expressions are also objects. In python it is the “re ” module and similar to that provided in PERL. You can use think of regex as wildcards on steriods. 

You might be familiar with “ *.txt ”. Its equivalent in regex is “ .**\.txt ”

```jsx
// Other examples

let list=["user@example,com", "user@example.com"]
list.search(/\b[A-Z0-9._%+-]+@[A-Z0-9.-
]+\.[A-Z]{2,4}\b/g)
//filters user@example,com

```

Do not worry if you don’t understand this. It would be discussed further as we go on.

## How to create Regular Expressions in JS

In JavaScript all regex values are enclosed in two back slashes or as  an instance of the regex object(argument in string format).

```jsx
let re-text_files = /.*\.txt/g
let Re-text_files = new RegExp("/\.*\.txt/\g")
```

Regex is processed from left to right.

### Characters

- **.**
 matches any character (except for line terminators)
- *****
 matches the previous token between zero or ****times
- **\.**
 matches the character ” .  “ . Since “ . ” matches any character, if you want to match “ . ” literally, escape it with a backslash.
- txt matches the characters “txt” literally (case sensitive)

### Modifier

Other characters that appear after the backslash are “GLOBAL PATTERN FLAGS”.

g modifier- represents return more than one match if found.

i modifier - case insensitive modifier

x modifier- ignore whitespace.

s modifier- Single line modifier and dot matches new line

 

## ㊙️ Character Classes

| Characters | Meaning | Examples and matches |
| --- | --- | --- |
| . | Match any character | /./ ⇒  b, z, 1, _ |
| [abc] | Matches any character within the list(either a, b or c) | /[acd]/ ⇒  |
| \d | Matches any digits(Indian-Arabic numbers).  | * [0-9] |
| \D | Matches any non-digit  | [^0-9] |
| \w | Matches any alphanumeric character from the basic Latin alphabet, including the underscore. | [A-Za-z0-9_] |
| \W | Matches no alphanumeric character from the basic Latin alphabet, including the underscore. | [^A-Za-z0-9_] |
| \s | Matches a single space |  |
| \S | Matches a single character except space |  |
| \t | Matches a tab space |  |
| \r  | Matches a carriage return space |  |
| \v | Matches a vertical tab |  |
| \0 | Matches a null character |  |

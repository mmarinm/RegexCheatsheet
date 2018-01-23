
# REGEX CHEATSHEET
```
pattern = /somepattern/flag
```
## Flags
```i``` - With this flag the search is case-insensitive: no difference between A and a
```g``` - With this flag the search looks for all matches, without it – only the first one
```m``` - Multiline mode
```u``` - Enables full unicode support. The flag enables correct processing of surrogate pairs. More about that in the chapter The unicode flag.
```y``` - Sticky mode (covered in the next chapter)

## Methods
**str.search(regexp)** - Returns the position of the first match or -1 if none found

**str.match(regexp), no g flag** - Looks for the first match and additional properties:
    index – the position of the match inside the string,
    input – the subject string.
    The result is an array with that match and additional properties

**str.match(regexp), with g flag -** When there’s a "g" flag, then str.match returns an array of all matches. There are no additional properties in that array, and parentheses do not create any elements.
We can use special characters in it:
| Symbol |  Inserts |
|------- | :------- |
$$     | "$"        |   
$&     | the whole match |
$`     | a part of the string before the match |
$'     | a part of the string after the match |
$n     | if n is a 1-2 digit number, then it means the contents of n-th parentheses counting from left to right |

**str.split(regexp|substr, limit)** - Splits the string using the regexp (or a substring) as a delimiter.

**str.replace(str|regexp, str|func )** - Looks for any match and returns true/false whether he found it.

**regexp.exec(str)** - Allows to search all matches with parentheses and positions.
    It behaves differently depending on whether the regexp has the g flag:
        - If there’s no g, then regexp.exec(str) returns the first match, exactly as str.match(reg).
        - If there’s g, then regexp.exec(str) returns the first match and remembers the position after it in regexp.lastIndex property. The next call starts to search from regexp.lastIndex and returns the next match. If there are no more matches then regexp.exec returns null and regexp.lastIndex is set to 0.
 
      
 ## Character classes
```\d``` (“d” is from “digit”) - A digit: a character from 0 to 9.
```\s``` (“s” is from “space”) - A space symbol: that includes spaces, tabs, newlines.
```\w (“w” is from “word”)``` - A “wordly” character: either a letter of English alphabet or a digit or an underscore. Non-english letters (like cyrillic or hindi) do not belong to \w
```\b``` - is a special character class it does not denote a character, bu rather a boundary between the characters
```.``` – any character except a newline.

## Reverse classes
  ```\D``` - Non-digit: any character except \d, for instance a letter.
  ```\S``` - Non-space: any character except \s, for instance a letter.
  ```\W```- Non-wordly character: anything but \w.
  ```\B``` - Non-boundary: a test reverse to \b.

## Special Characters
   Here’s a full list of them: ``` [ \ ^ $ . | ? * + ( )```  and they need to be escaped with ```\```
   
   The slash symbol ```/``` is not a special character, but in JavaScript it is used to open and close the regexp: ```/...pattern.../```, so we should escape it too.

## Sets and ranges […]
   * **Set**  for instance, ```[eao]``` means any of the 3 characters: ```'a', 'e', or 'o'```
   * **Range**  for instance, ```[a-z]``` is a character in range from ```a to z```, and ```[0-5]``` is a digit from ```0 to 5````
   * **Excluding ranges**  besides normal ranges, there are “excluding” ranges that look like ```[^…]```
   * **No escaping in […]**  usually when we want to find exactly the dot character, we need to escape it like ```\.``` And if we need a backslash, then we use ```\\```
        * In square brackets the vast majority of special characters can be used without escaping:
            * A dot ```.```
            * A plus ```+```
            * Parentheses ```( )```
            * Dash ```-``` in the beginning or the end (where it does not define a range).
            * A caret ```^``` if not in the beginning (where it means exclusion).
            * And the opening square bracket ```[```

## Quantifiers
   **Quantity {n}:**
   The most obvious quantifier is a number in figure quotes: ```{n}``` 
   A quantifier is put after a character (or a character class and so on) and ***specifies exactly how many we need***.
   * **Exact count:**  ```\d{5}``` denotes exactly 5 digits, the same as ```\d\d\d\d\d```
   * **The count from-to:** to find numbers from 3 to 5 digits we can put the limits into figure brackets: ```\d{3,5}```
    
   **Shorthands**
        ```+```  “one or more”, the same as {1,}.
        ```?``` “zero or one”, the same as {0,1}. In other words, it makes the symbol optional.
        ```\```  “zero or more”, the same as {0,}. That is, the character may repeat any times or be absent.

## Greedy and lazy quantifiers


## Capturing Groups 
 
A part of the pattern can be enclosed in parentheses (...). That’s called a “capturing group”.
That has two effects:
   **1. It allows to place a part of the match into a separate array item when using String#match or RegExp#exec methods.**
   **2. If we put a quantifier after the parentheses, it applies to the parentheses as a whole, not the last character.**

**Content of parentheses**
Parentheses are numbered from left to right. The search engine remembers the content of each and allows to reference it in the pattern or in the                replacement string.
    
**Nested Groups:**
Parentheses can be nested. In this case the numbering also goes from left to right.
Example:
```javascript 
    const str = '<span class="my">';
    const reg = /<(([a-z]+)\s*([^>]*))>/;
    const result = str.match(reg);
    alert(result); // <span class="my">, span class="my", span, class="my"
``` 

**Non-capturing groups:**
Sometimes we need parentheses to correctly apply a quantifier, but we don’t want their contents in the array.
 A group may be excluded by adding ```?:``` in the beginning.
        For instance, if we want to find (go)+, but don’t want to put remember the contents (go) in a separate array item, we can write: ```(?:go)+```
        Example:
```javascript
        const str = "Gogo John!";
        // exclude Gogo from capturing
        const reg = /(?:go)+ (\w+)/i;

        const result = str.match(reg);

        alert( result.length ); // 2
        alert( result[1] ); // John
```
## Backreferences:  \n and $n

**Group in replacement: $n**

When we are using replace method, we can access n-th group in the replacement string using ```$n```
```javascript
    let name = "John Smith";
    name = name.replace(/(\w+) (\w+)/i, "$2, $1");
    alert( name ); // Smith, John
```         

**Group in pattern: \n**

A group can be referenced in the pattern using \n.
To make things clear let’s consider a task. We need to find a quoted string: either a single-quoted ```'...'``` or a double-quoted ```"..."```  both variants need to match.
* We can put two kinds of quotes in the pattern:``` ['"](.*?)['"]``` That finds strings like ```"..."``` and``` '...'```, but it gives incorrect matches when one quote appears         inside another one, like the string "She's the one!":
```javascript
    const str = "He said: \"She's the one!\".";
    const reg = /['"](.*?)['"]/g;

    // The result is not what we expect
    alert( str.match(reg) ); // "She'
```

As we can see, the pattern found an opening quote ```"```, then the text is consumed lazily till the other quote ```'```, that closes the match.

To make sure that the pattern looks for the closing quote exactly the same as the opening one, let’s make a group of it and use the backreference:
```javascript
    let str = "He said: \"She's the one!\".";
    let reg = /(['"])(.*?)\1/g;
        
    alert( str.match(reg) ); // "She's the one!"
```

Now everything’s correct! The regular expression engine finds the first quote ```(['"])``` and remembers the content of ```(...)```, that’s the first capturing group.
**Further in the pattern \1 means “find the same text as in the first group”.**

## Alternation (OR) |
Alternation is the term in regular expression that is actually a simple ```OR```
In a regular expression it is denoted with a vertical line character ```|```.

For instance, we need to find programming languages: HTML, PHP, Java or JavaScript.
The corresponding regexp: ```html|php|java(script)?```

T**o separate a part of the pattern for alternation** we usually enclose it in parentheses, like this: ```before(XXX|YYY)after```

## String start ^ and finish $

The caret ```^``` and dollar ```$``` characters have special meaning in a regexp. They are called “anchors”.
The caret ```^``` matches at the beginning of the text, and the dollar ```$```  in the end.

## Multiline mode, flag "m"

The multiline mode is enabled by the flag ```/.../m```
It only affects the behavior of ```^``` and ```$```
In the multiline mode they match not only at the beginning and end of the string, but also at start/end of line.
```javascript   
    let str = `1st place: Winnie
    2nd place: Piglet
    33rd place: Eeyore`;

    alert( str.match(/^\d+/gm) ); // 1, 2, 33

    let str = `1st place: Winnie
    2nd place: Piglet
    33rd place: Eeyore`;

    alert( str.match(/\w+$/gim) ); // Winnie,Piglet,Eeyore
```

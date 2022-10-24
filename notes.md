# ft_printf learing notes

## What is `printf`

`printf` is a library function to send formatted output to the screen. It prints the string inside quotation. `printf` is a variadic function.

## `printf` working principle

`printf` takes a formatting string (mandatory argument) and couple of optional variables (optional arguments) as input and outputs string to console while **converting input variables to strings**.

![printf working principle](https://www.equestionanswers.com/c/images/printf-block-diagram.png)

`printf` iterates through each characters of user string and copies the character to the output string. `printf` only stops at `%`. `%` means there is an argument to convert.

Arguments are in the form of:

- `char`
- `int`
- `long`
- `float`
- `double`
- `string`

It converts them **into string** and appends to output buffer.

Finally, when `printf` reach at the end of the string, it copies the entire buffer to the stdout file.

## Implement printf

`printf` takes in **one string argument** and **rest are variable arguments**. These variable arguments are managed by macros like `va_start`, `va_arg`, `va_end`. Besides, there should be a **temporary buffer** to construct the output buffer.

A **while loop** is required to scan each characters in the input string. Iterate through the string and check whether the current character is '%' or not. Once we encounter it, don't copied to output string and check the next character of it. The character after '%' is called **formatting character** or format specifier.

Formatting character tells how to format the argument. `printf` supports varieties of formatting.

- `%c` - Prints a single character.
- `%s` - Prints a string (as defined by the common C convention).
- `%p` - The void * pointer argument has to be printed in hexadecimal format.
- `%d` - Prints a decimal (base 10) number.
- `%i` - Prints an integer in base 10.
- `%u` - Prints an unsigned decimal (base 10) number.
- `%x` - Prints a number in hexadecimal (base 16) lowercase format.
- `%X` - Prints a number in hexadecimal (base 16) uppercase format.
- `%%` - Prints a percent sign.

`printf` match the formatting and pick the argument variable using `va_arg()`. Then converts them into string format and appends to output string.

The process of coping characters and conversion of arguments repeats until the string is null-terminated.

## Some facts/behaviors of `printf` that might be useful

Reference: [fprintf.html](https://pubs.opengroup.org/onlinepubs/9699919799/functions/fprintf.html)

1. The results are undefined if there are insufficient arguments for the format.
2. If the format is exhausted while arguments remain, the excess arguments shall be evaluated but otherwise ignored.
3. Upon successful completion, `printf` functions shall return the number of bytes transmitted.

---

## Format placeholder specification

Formatting take place via placeholders within the format string. For example, if a program wanted to print out a person's age, it could present the output by prefixing it with "Your age is ", and using the signed decimal specifier character `d` to denote that we want the integer for the age to be shown immediately after that message, we may use the format string:

```c
printf("Your age is %d", age);
```

Syntax of the format placeholder

```c
%[parameter][flags][width][.precision][length]type
// %[optional][optional][optional][optional][optional]required
```

### Parameter

This field can be ommitted. The character that represent Parameter is `n$`, where `n` is the number of the parameter to be displayed using the following specifier. It means that it allows the arguments provided to be output multiple times, using varying format specifiers or in different orders. One thing **CRUCIAL** is that if **ANY** single placeholder specifies a parameter, **ALL THE REST** of the placeholders **MUST** also specify a parameter.

For example:

```c
printf("%2$d %2$#x; %1$d %1$#x",16,17);
```

### Flags

The flag fields can be ommitted as well. Can use more than one flag, the order doesn't matter.

#### `-` (minus)

Left-align the output of this placeholder. (The default is to right-align the output)

For example:

```c
printf("%10d\n", 1);
// output:           1 (has nine space in front of the numeric value, right-align)
printf("%-10d\n", 1);
// output: 1           (left-align, nine space behind the numeric value)
```

#### `+` (plus)

Prepends a `+` for positive signed-numeric types. The default doesn't prepend anything in front of positive numbers.

For example:

```c
printf("%d\n",1);
// output: 1
printf("%+d\n",1);
// output: +1
```

#### (space)

Prepends a space for positive signed-numeric types. The default doesn't prepend anything in front of positive numbers. This flag is ignored if the `+` exists. `+` > (space).

For example:

```c
printf("%d\n",1);
// output: 1
printf("% d\n",1);
// output: +1
```

#### 0 (zero)

When the **'width' option is specified**, prepends zeros for **numeric types**. The default prepends spaces.

This is ignored when `-` is present.

> Width includes the numeric number itself.

For example:

```c
printf("%4d", 2);
// output:     1 (there's 3 space in front of 1)
printf("%04d", 2);
// output: 00001 (there's 3 '0' in front of 2)
```

#### # (hash)

Alternate form:

For `g` and `G` types, trailing zeros are not removed.

For `f`, `F`, `e`, `E`, `g`, `G` types, the ouput always contains a decimal point.

For `o`, `x`, `X` types, the text `0`, `0x`, `0X`, respectively, is prepended to non-zero numbers.

Prepends `0` means the number is being written in octal. `0x` means it's being written in hexadecimal. `0X` is just uppercase hexadecimal.

### Width

This field is optional. The width field specifies a minimum number of characters to ouput, it's typically used to pad fixed-width fields in tabulated output, where fileds would otherwise be smaller, although it does not cause truncation of oversized fields.

The width can be specified using a numeric integer value, like this:

```c
printf("%5d", 10);
// output:    10
```

...or, using a dynamic value when passed as another argument when indicated by an `*` (asterisk).

```c
printf("%*d", 5, 10);
// output:    10
// width of 5, the first arguement of the optional argument is the width
```

### Precision (`.`)

Precision field is used to specifies a **maximum** limit on the output, depending on the particular formatting type.

For `float` (floating point numeric types), it specifies the number of digits to the right of the decimal point that the output should be **rounded** (rounded up).

For `string`, it limits the number of characters that should be output, after which the string is truncated.

This field can be omitted, or a numeric integer value, like this:

> before numeric integer, add `.`

```c
printf("%.3s", "abcdef");
// output: abc
printf("%.3f", 123.45658);
// output: 123.457
```

... or use a dynamic value by passing as another argument when indicated by an `*` (asterisk).

```c
printf("%.*s", 3, "abcdef);
// output: abc
printf("%.*f", 3, 123.45648);
// output: 123.457
```

For integer specifers (`d`,`i`,`o`,`u`,`x`,`X`): precision specifies the minimum number of digits to be written. If **the value to be written is shorter than this number**, the result is **padded with leading zeros**. The value is not truncated even if the result if longer. A precision of `0` that no character is written for the value `0`.

For `s`: this is the maximum number of characters to be printed. By default all characters are printed until the ending null character is encountered.

If the **period is specified without an explicit value for precision**, `0` is assumed.

### Length

The length field can be omitted or be any of:

**hh**: For `int` types, causes `printf` to expect an `int`-sized integer argument which was promoted from a `char`.

**h**: For `int` types, causes `printf` to expect an `int`-sized integer argument which was promoted from a `short`.

**l**: For `int` types, causes `printf` to expect an `long`-sized integer argument. For *floating point* types, this is ignored. `float` arguments are always promoted to `double` when used in a **varargs** call.

**ll**: For `int` types, causes `printf` to expect an `long long`-sized integer argument.

**L**: For *floating point* types, causes `printf` to expect an `long double`-sized integer argument.

**z**: For `int` types, causes `printf` to expect an `size_t`-sized integer argument.

**j**: For `int` types, causes `printf` to expect a `intmax_t`-sized integer argument.

**t**: For `int` types, causes `printf` to expect a `ptrdiff_t`-size integer argument.

### Type

The type field in **necessary**.

Usage: `%[character]`

> Here only covers these characters: cspdiuxX%

#### % (percent)

Prints the **'%'** character. When using this, flags, width, precision, length shall be omitted.

#### d, i

`int` as a signed integer. `%d` and `%i` are synonymous for output, but are different when used with `scanf` for input. (*can represent both positive and negative values*)

#### u

Print decimal as `unsigned int`. (*integer that can never be negative*)

#### f, F (no need to do)

`double` in normal [**fixed-point** notation](https://stackoverflow.com/questions/10067510/fixed-point-arithmetic-in-c-programming).

`f` and `F` only differs in how the strings of an infinite number or `NaN` are printed (`inf`, `infinity` and `nan` for `f`; `INF`, `INFINITY` and `NAN` for `F`).

#### x, X

`unsigned int` as a hexadecimal number. `x` use lowercase letters and `X` uses uppercase letters.

#### s

**null-terminated** string.

#### c

`char` (character)

#### p

`void*` (pointer to void). Pointer address.

Variadic functions are functions that can take a variable number of arguments. So, it needs to take at least one fixed argument and then any number of arguments can be passed. The syntax of a variadic function will be like this:

```c
int var_fun(int count, ...);
```

Variadic functions are typically used when we don't know the total number of arguments that will be used for a function. One of the example of variadic function is `printf`.

Example: add 3 number, add 4 number

In order to use a variadic functions, there are some macros that you need to know.

1. va_list: data type, contains the list of arguments
2. va_start: initialize the va_list. takes in two arguments, the va_list, the last named argument
basically tells the system that from which parameter onwards, the rest will be the member of the argument list.
3. va_arg: retrieve argument from the arguments list, takes in 2 argument, the va_list, the data type
4. va_end: tells the system that you want to stop using the va_list. free va_list
5. va_copy: copy contents of va_list to another

explain until 4 only, 5 not used.

Overall process:

1. Iterate through the string
2. If encounters '%',
    extracts the flags
    extracts the width
    extracts the precision
    extracts the conversion type
3. depends on the conversion type, use the corresponding function to print out the string

## Variadic functions

Variadic functions are functions that can take an arbitrary number of arguments. It takes **one fixed argument** and then any number of arguments can be passed. The syntax of a variadic function consists of at least one fixed variable and then an ellipsis (...) as the last parameter.

Prototype:

```c
data_type function_name(data_type variable_name, ...);
```

As discussed, variadic functions are typically used when *we don't know the total number of arguments that will be used for a function*. One of the example of variadic function is `printf`.

For example, when we want to print one number using printf, we do something like this.

```c
printf("one number: %d", the_number);
```

When we want to print two or more numbers, we still use the same printf function as shown below:

```c
printf("first num: %d, second num: %d", firstnum, secondnum);
```

Notice that we don't have to use another `printf` function that accepts two `int` as argument to print out the numbers. Both are using the same `printf` function. Kinda cool isn't it?

## Variadic Macros

In order to use variadic functions, we need to understand these macros:

- `va_list`: type for iterating arguments
- `va_start`: Start iterating arguments with a `va_list`
- `va_arg`: Retrieve an argument
- `va_end`: Free a `va_list`
- `va_copy`: Copy contents of one `va_list` to another

These macros can be accessed through the `<stdarg.h>` header file.

Variadic function has two parts:

1. Mandatory arguments (at least one)
2. Optional arguments

The order is crucial. **Mandatory arguments first then optional arguments**.

### Accessing the arguments

To access the unnamed arguments, we need to declare a variable of type `va_list` in the variadic function.

```c
va_list theArgumentList
```

The `va_start` macro is then called with two arguments:

1. Variable declared of the type `va_list`.
2. Name of the last named parameter of the function. (*What?*)

Check this to understand `va_start` better: [`va_start`](https://www.tutorialspoint.com/c_standard_library/c_macro_va_start.htm)

> Basically, the 2nd parameter of va_start is known as the **fixed argument** being passed to the function. i.e: the argument before the ellipsis.

After this, each invocation of the `va_arg` macro yields the next argument. The first argument to `va_arg` is the `va_list` and the second is the type of the next argument passed to the function.

Finally, the `va_end` macro must be called on the `va_list` **before the function returns**. (It is not required to read in all the arguments.)

Notes: ***THERE IS NO MECHANISM DEFINED FOR DETERMINING THE NUMBER OF TYPES OF THE UNNAMED ARGUMENTS PASSED TO THE FUNCTION.***

---

## Format specifier

- `%c` - Prints a single character.
- `%s` - Prints a string (as defined by the common C convention).
- `%p` - The void * pointer argument has to be printed in hexadecimal format.
- `%d` - Prints a decimal (base 10) number.
- `%i` - Prints an integer in base 10.
- `%u` - Prints an unsigned decimal (base 10) number.
- `%x` - Prints a number in hexadecimal (base 16) lowercase format.
- `%X` - Prints a number in hexadecimal (base 16) uppercase format.
- `%%` - Prints a percent sign.

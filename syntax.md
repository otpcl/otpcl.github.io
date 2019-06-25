---
title: Syntax
---

# Syntax

OTPCL's syntax takes inspiration from (and derives from) that of Tcl, itself notable for its simple syntax of space-delimited lists of words and "everything is a string" attitude.  OTPCL does make several significant departures from Tcl's syntax in order to better integrate with OTP applications and programming styles.  This page follows [Tcl's own "Dodekalogue"](https://wiki.tcl-lang.org/page/Dodekalogue) closely in both structure and content (albeit with some deviations to add clarity given the inherent differences between the two languages).

**WARNING:** OTPCL is not yet stable, and neither is its syntax.  This document is subject to frequent change, and is not yet guaranteed to align exactly with the actual OTPCL implementation.  OTPCL versions prior to 1.0.0 may introduce significant changes that may break OTPCL programs (after 1.0.0, any breaking changes to this syntax - like any other breaking change - will correspond with a new major version number).

## Commands

An OTPCL program is a string containing one or more commands.  Semicolons and newlines are command separators unless escaped with an immediately-preceding backslash (`\`).  Closing square brackets ( `]`) are command terminators during command substitution unless escaped with an immediately-preceding backslash.

## Evaluation

A command is evaluated in two steps.  First, the OTPCL interpreter breaks the command into words and performs variable and command substitutions.  The first word is used to locate a registered local function to carry out the command, then all of the words of the command are passed to the function.

## Words

Words of a command are separated by whitespace (if said whitespace is not already interpreted as a command separator).

## Comments

If a word starts with an octothorpe (`#`) (a.k.a. "hash" or "pound"), then the word is terminated by the next newline and is interpreted as a comment; all characters in the comment (including the octothorpe and newline) are ignored.

## Command substitutions

If the first character of a word is an opening square bracket (`[`) then the word is terminated by the matching closing square bracket (`]`) and is interpreted as a command substitution.  Any number of commands may appear within the command substitution; OTPCL will evaluate each command in order and replace the command substitution with the return value of the last command.

## Variable substitutions

If the first two characters of a word are a dollar sign (`$`) followed immediately by an opening curly brace (`{`) then the word is terminated by the matching closing curly brace (`}`) and is interpreted as a variable substitution.  Any otherwise-special characters appearing within the curly braces (excluding the curly braces themselves and the dollar sign) are included as-is in the name of the variable to be substituted; OTPCL will lookup the variable by name and replace the variable substitution with the value of that variable.

If the first character of a word is a dollar sign (`$`) which is *not* followed immediately by an opening curly brace, then the word is interpreted as a variable substitution.  Any otherwise-special characters appearing between the start of the substitution and the next unescaped whitespace, closing parenthesis, greater-than sign, or closing square brace (excluding the dollar sign) are included as-is in the name of the variable to be substituted; OTPCL will lookup the variable by name and replace the variable substitution with the value of that variable.

## Strings

If the first character of a word is either a double-quote (`"`) or opening curly brace (`{`) then the word is terminated by the next unescaped double-quote or matching unescaped closing curly brace (`}`) character (respectively) and is interpreted as a string (Erlang type: binary string / "binstring").  Any otherwise-special characters appearing within the string's quotes are included as-is in the resulting string.  The starting and ending characters will not be included in the resulting string.

As mentioned previously, in the case of strings which begin with an opening curly brace, braces "nest" within the string; for each additional unescaped opening curly brace there must be an additional unescaped closing curly brace.

## Charlists

If the first character of a word is a backquote (`` ` ``) then the word is terminated by the next unescaped backquote and is interpreted as a list of characters or "charlist" (Erlang type: normal string / character list).  Any otherwise-special characters appearing within the charlist's quotes are included as-is in the resulting charlist.  The starting and ending backquotes will not be included in the resulting charlist.

## Lists

If the first character of a word is an open parenthesis (`(`) then the word is terminated by the next matching closing parenthesis (`)`) and is interpreted as a list of words (Erlang type: list).  Any number of words of any types may appear as elements in the list, including other lists.

## Tuples

If the first character of a word is a less-than sign (`<`) then the word is terminated by the next matching greater-than sign (`>`) and is interpreted as a tuple of words (Erlang type: tuple).  Any number of words of any types may appear as elements in the tuple, including other tuples.

## Integers

If a word consists entirely of digits then the word is interpreted as an integer (Erlang type: integer).

## Floats

If a word consists entirely of digits and a single period (`.`), and the period is not the first or last character in the word, then the word is interpreted as a floating-point number or "float" (Erlang type: float).

## Atoms

If the first character of a word is a single-quote (`'`) then the word is terminated by the next unescaped single-quote and is interpreted to be an atom (Erlang type: atom).  Any otherwise-special characters appearing within the atom's quotes are included as-is in the resulting atom's name.  The starting and ending single-quotes will not be included in the resulting atom's name.

If a word does not match any of the above rules then the word is interpreted to be an atom (Erlang type: atom).  Any otherwise-special characters appearing between the start of the atom and the next unescaped whitespace, closing parenthesis, greater-than sign, or closing square brace are included as-is in the resulting atom's name.

If the first character of an (unquoted) atom is a pipe character (`|`) then it is treated as the beginning of a new command (thus terminating the previous command).  This enables the use of "pipe commands" in OTPCL to chain commands together in interesting ways.

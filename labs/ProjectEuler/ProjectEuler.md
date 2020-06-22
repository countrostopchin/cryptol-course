```
module labs::ProjectEuler::ProjectEuler where

import labs::ProjectEuler::cipher1
import labs::ProjectEuler::keylog
import labs::ProjectEuler::cipher2
```

# Properties in Cryptol, or How I Learned to Stop Worrying and Love SAT Solvers

You have learned in the other labs that you can use Cryptol to find
answers to problems you coded using the `:sat` command.  As a
refresher, let's look at a simple example.  Suppose we want to find
the integer that is one less than 1001 (as we said, it's very simple).
We do this by creating a function and a `property`:

```
inc : Integer -> Integer
inc x = x + 1

inc1001 : Integer -> Bit
property inc1001 x = inc x == 1001
```

Now that we have our function and a property about our function, we
can load this lab directly into Cryptol and find an answer!  You
should get a response like this:

```sh
[cryptol-course]$ cryptol labs/ProjectEuler/ProjectEuler.md 
┏━╸┏━┓╻ ╻┏━┓╺┳╸┏━┓╻  
┃  ┣┳┛┗┳┛┣━┛ ┃ ┃ ┃┃  
┗━╸╹┗╸ ╹ ╹   ╹ ┗━┛┗━╸
version 2.8.1 (e914cef)
https://cryptol.net  :? for help

Loading module Cryptol
Loading module labs::ProjectEuler::cipher1
Loading module labs::ProjectEuler::keylog
Loading module labs::ProjectEuler::cipher2
Loading module labs::ProjectEuler::ProjectEuler
labs::ProjectEuler::ProjectEuler> :sat inc1001 
inc1001 1000 = True
(Total Elapsed Time: 0.028s, using "Z3")
```
Let's do a more complicated example: use Cryptol to factor 3,000,013.

```
factor3000013 : Integer -> Integer -> Bit
factor3000013 x y =
    x * y == 3000013 /\
    x > 1            /\
    y > 1
```

Note that if we don't include the `x > 1 /\ y > 1` clauses we get a
trivial factorization.  Now we can use Cryptol to factor our number:

```sh
labs::ProjectEuler::ProjectEuler> :sat factor3000013 
factor3000013 773 3881 = True
(Total Elapsed Time: 0.215s, using Z3)
```

Clearly we don't need SAT solvers to figure out how to subtract one.
And writing simple factorization code in Python factored 3,000,013
just as fast.  But there is something really interesting here: to
solve the problem, instead of figuring out how to write a search
algorithm which would solve the problem for us, we just had to write a
solution checker in Cryptol and let a SAT solver find a valid solution
for us!  Usually it's much easier to write a solution checker than
write a search algorithm. We can't always expect the SAT solver to
find an answer quickly (this is known as as the [P vs. NP
problem](https://en.wikipedia.org/wiki/P_versus_NP_problem)), but
there are many times where it will come back with an answer quickly.
We like to say that it doesn't cost much to ask a SAT solver.


## Project Euler Problems

[Project Euler](https://projecteuler.net/) is a collection of
mathematical problems that can be solved using relatively simple
computer code (i.e. the solution shouldn't require optimized code to
run on a supercomputer). Some of the questions lend themselves to
querying a SAT solver. We've collected a few here for you to try your
hand at.

**ONE IMPORTANT NOTE**: We have reworded the questions to make them
  more Cryptol-friendly. The Project Euler questions often ask you to
  find _all_ of the numbers which satisfy some property and then
  combine them in some way (e.g. take their collective sum). For most
  of the exercises here, it is enough to write a property that will
  allow the SAT solver to find at least one answer.


### [Problem 9](https://projecteuler.net/problem=9)

> A Pythagorean triplet is a set of three natural numbers, a < b < c,
> for which a^2 + b^2 = c^2.
>
> For example, 3^2 + 4^2 = 9 + 16 = 25 = 5^2.
>
> There exists exactly one Pythagorean triplet for which a + b +
> c = 1000. Find this triple.


### [Problem 34](https://projecteuler.net/problem=34)

> 145 is a curious number, as 1! + 4! + 5! = 1 + 24 + 120 = 145.
>
> Find all numbers which are equal to the sum of the factorial of
> their digits.  Note: as 1! = 1 and 2! = 2 are not sums they are not
> included.

(Aside: these numbers are called
[factorions](https://en.wikipedia.org/wiki/Factorion).

*Hints*: the factorial function is usually defined recursively, but
 that tends to make SAT solving difficult. Since you only need to
 calculate the factorial of the numbers 0-9, make your function just
 do a case by case calculation. To get the digital representation of
 the number, create a function which takes in a number and a list of
 numbers and returns `True` exactly when the list is the base 10
 representation. Finally, it can be shown that the most number of
 digits a factorion can have is 6.


### [Problem 36](https://projecteuler.net/problem=36)

> The decimal number, 585 = 1001001001 (binary), is
> [palindromic](https://www.dictionary.com/browse/palindromic) in both
> bases. Find at least three numbers which are palindromic in base 10
> and base 2. (Please note that the palindromic number, in either
> base, may not include leading zeros.)


### [Problem 43](https://projecteuler.net/problem=43)

> The number, 1406357289, is a 0 to 9 pandigital number because it is
> made up of each of the digits 0 to 9 in some order, but it also has
> a rather interesting sub-string divisibility property.
>
> Let d_(1) be the 1^(st) digit, d_(2) be the 2^(nd) digit, and so on. In this way, we note the following:
>
>    * d_(2)d_(3)d_(4)=406 is divisible by 2
>    * d_(3)d_(4)d_(5)=063 is divisible by 3
>    * d_(4)d_(5)d_(6)=635 is divisible by 5
>    * d_(5)d_(6)d_(7)=357 is divisible by 7
>    * d_(6)d_(7)d_(8)=572 is divisible by 11
>    * d_(7)d_(8)d_(9)=728 is divisible by 13
>    * d_(8)d_(9)d_(10)=289 is divisible by 17
>
> Find at least two 0 to 9 pandigital numbers with this property.


### [Problem 52](https://projecteuler.net/problem=52)

> It can be seen that the number, 125874, and its double, 251748,
> contain exactly the same digits, but in a different order.
>
> Find the smallest positive integer, x, such that 2x, 3x, 4x, 5x, and
> 6x, all contain the same digits.


### [Problem 59](https://projecteuler.net/problem=59)

> Each character on a computer is assigned a unique code and the
> preferred standard is
> [ASCII](https://en.wikipedia.org/wiki/ASCII). For example, uppercase
> A = 65, asterisk (*) = 42, and lowercase k = 107.
>
> A modern encryption method is to take a text file, convert the bytes
> to ASCII, then XOR each byte with a given value, taken from a secret
> key. The advantage with the XOR function is that using the same
> encryption key on the cipher text, restores the plain text; for
> example, 65 XOR 42 = 107, then 107 XOR 42 = 65.
>
> For unbreakable encryption, the key is the same length as the
> plaintext message, and the key is made up of random bytes. The user
> would keep the encrypted message and the encryption key in different
> locations, and without both "halves", it is impossible to decrypt
> the message.
>
> Unfortunately, this method is impractical for most users, so the
> modified method is to use a password as a key. If the password is
> shorter than the message, which is likely, the key is repeated
> cyclically throughout the message. The balance for this method is
> using a sufficiently long password key for security, but short
> enough to be memorable.
>
> Your task has been made easy, as the encryption key consists of
> three lower case characters. Using cipher1.cry, a file containing
> the encrypted ASCII codes, and the knowledge that the plain text
> must contain common English words, decrypt the message and find the
> sum of the ASCII values in the original text.
>

Note: cipher1.cry contains a different cipher encrypted under a
different key from the original. The original Project Euler problem
can be found in cipher2.cry.


### [Problem 79](https://projecteuler.net/problem=79)

> A common security method used for online banking is to ask the user
> for three random characters from a passcode. For example, if the
> passcode was 531278, they may asked for the 2nd, 3rd, and 5th
> characters; the expected reply would be: 317.
>
> The text file, keylog.cry, contains fifty successful login attempts.
>
> Given that the three characters are always asked for in order,
> analyse the file so as to determine the shortest possible secret
> passcode of unknown length.


### Throwback

> Find a four-digit number (greater than 999 and less than 10000) such
> that the least significant four digits of the square is that number.
>
> EXTRA CHALLENGE:
> What about five-digit numbers? Other numbers of digits?
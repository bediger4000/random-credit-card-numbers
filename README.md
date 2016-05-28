# random-credit-card-numbers
## Random credit card numbers that pass the Luhn check

I wrote this when I was working on a Visa Level 1 merchant's credit card processing system,
circa 2005. I needed arbitrary amounts of credit card numbers that passed the Luhn checksum test,
but did not duplicate. At the time, our payment processor, Paymentech, had a test/development
environment that would return an authorization status based on the dollar amount: a $100
dollar payment would give you a "100 - payment authorized" response code. Unfortunately,
the merchant's credit card processing system had a "duplicate check" - the same dollar amount
and credit card number would trigger a "duplicate", and the system would return the same
response code and 6-digit auth code from the "original" payment request. Since I needed a large
proportion of "100 - payment authorized" responses, I had to vary the credit card number.

I was also using Python at the time, and I decided to see if I could write confusing code in
Python. The answer is yes, you can write confusing, complicated code in Python. The proof is
in the single line of Python that calculates the check digit of a credit card number:

    return (10-(sum(((((2-(p&1))*d)%10)+((2-(p&1))*d)/10) for p, d in enumerate(digits[::-1]))%10))%10

In the above code `digits` is an array of integers representing an entire
credit card number. It can handle 16-digit MasterCard, Discover or Visa, and
15-digit American Express card numbers. By proceeding backwards from the last non-checkdigit
digit, the code doesn't have to know that it's working with an Amex-length card number
or an all-the-others 16-digit-length card number. The digit immediately preceeding the checkdigit
is an odd numbered digit in all formats, even the very rare 13-digit Visa card numbers. 
The checksum weight of card number digits is eash to calculate the same way for every
card type if you realize that the even-odd progression is the same working backwards through
the card number.

## Usage

If you want to generate random credit card numbers with real Bank Identification Numbers, you can
run the progra like this:

    $ ./randomccnum 412138 16
    4121384200711905
    $

That invocation uses 412138 as the BIN, which is a Chase Bank Visa card number. The program
returns a "card number" that has 9 random digits, and a valid Luhn checkdigit, the trailing "5".

To check the Luhn digit, you could invoke it with the non-checkdigit prefix of an existing 
card number:

    $ ./randomccnum 412138420071190 16
    4121384200711905

## A Curiosity

You can use the program to calculate a pseudo-credit card number that for any prefix
has a valid Luhn check digit:

    $ ./randomccnum 4 2
    42
    $ ./randomccnum 42 3
    422
    $ ./randomccnum 422 4
    4226
    $ ./randomccnum 4226 5
    42267
    ...
    $ ./randomccnum 422675918342675 16
    4226759183426759

Apparently, the BIN [422675](https://binlists.com/422675) is associated with the
Pjsc Bta Bank in Ukraine.  I don't know if that's a credit card number that's associated
with a real account or not, but all 14 prefixes of more than 1 digit have a valid
Luhn checksum.

# Authentication Notes

## [12 Best Practices For User Account, Authorization and Password Management](https://cloud.google.com/blog/products/gcp/12-best-practices-for-user-account) By Ian Maddox

Authentication/AuthN is when our website verifies the identity of the user
Authorization/AuthZ is when our servers knows who you are, and is determining whether you have permission to access the requested resources.

# Core Principles for Authentication

- requiring strong passwords
- properly storing passwords
- preventing brute force attacks

## hashing

hash("hello world") == "5fauidhay4t89qy4t8qn-4q-84tq4394ugis"
hashing is not encryption, it is a one way, irreversible process. Think of it like a finger print for a small string of data. It's unique.

## examples of hashers (probably not spelled correctly)

MD5
shaw1
shaw256
bcrypt
argon

Red Flag - when an app tries to limit the length of your password. They are likely not hashing. No matter the length of the string, the hash is the same length. They are probably storing it encrypted (bad because it is reversable, if hackers get the secret key they can reverse the encryption) or as plain text.

## Brute Forcing

Attackers attempt to guess a password over and over and over again. They use programs that can do this millions of guesses per seconds.

Hashing alone isn't enough. Hackers create what is known as a Rainbow Table. It is essentially a table with every possible password hashed with the same algorthim the server is using. (there are many other kinds of brute force attacks)

One of the things we can do is to try to intentionally slow down our code. We can introduce a `time complexity`. Essentially hash-ception. This makes the Rainbow Tables take so long, the become unworth the trouble.

100,000,000 hashes at 2 milliseconds per hash = 55 hours
100,000,000 hashes at 2 seconds per hash = 6 years

## Key Derivation Function

Hash + Time = New Hash

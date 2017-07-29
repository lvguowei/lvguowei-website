+++
categories = ["Design and Architecture"]
description = "Ways not to store user passwords and why"
keywords = ["password", "security", "hashing", "rainbow table"]
featured = "featured-password.png"
featuredpath = "/img"
featuredalt = "Design and Architecture"
title= "How (Not) To Store Password"
date= 2017-07-28T21:07:55+03:00
+++

If you are a programmer, especially backend programmer, sooner or later you will face the problem of storing users' passwords. Even though at first sight this seems like an entry level problem that we should hand over to an intern, it is really not.

If you ask me how to do it. My first answer would be **DON'T DO IT**! I'm serious, try to avoid it as much as you can, just use Google or Facebook signin and your life goes on.

What? You really have to do it yourself? Then I have to say follow the latest best practice of your language or framework. But as for now, I can list what I know that do NOT work:

## Plain Text
Come on, do I need to even explain why this is a bad idea. So pay attention next time to some online service that provides to send your password back through email when you forget it. Unregister immediately!

## Two way encryption
A slightly better way is to encrypt the password before storing. Here we are talking about two-way encryption, meaning that given the key, the encrypted password can be decrypted. This may sounds convenient and secure, but it has still big security problems. The biggest problem is that given the encryption key, one can still get the original password back, so any insider can still steal all password if he/she wishes.

{{< img-post "/img" "two-way-encrypt.png" "Two Way Encryption" "center" >}}

## One way encryption (Hashing)
This is better than two-way encryption in a way that now given the hash, it is not possible to get back the original password any more. And to verify the password, we can just calculate the hash of the user input and compare it to the hash that is stored. But impossible to get the password from the hash, doesn't mean it is impossible to guess it quickly. So the hacker can do this: Guess a password, calculate its hash, and see if it matches any hashes in the database, if yes, he cracked a password, if not, just try more times. This will take a lot of computing resource, but nowadays there is this [Rainbow Table](https://en.wikipedia.org/wiki/Rainbow_table) to help. It is basically a massive amount of precalculated hashes of some common passwords. Using it, one can very quickly crack any common password in just seconds. The other problem is that given the same password, the hashes will look identical. This will speed up the cracking process even more.

{{< img-post "/img" "hashing.png" "Hashing" "center" >}}


So given all that, what is the best practice to store password nowadays, well, we are not that far from it actually.

## Hashing + salting
This is exactly like hashing, except that before we do the hashing, we put in some randomly generated string called *salt*, so we run the hashing algorithm against the password + salt instead of the naked password. The salt can be stored in plain text along with the hash. So all of a sudden, rainbow table is not working anymore, cause no one can precalculate the hashes of some very long random string. So the hacker is back to the origin, meaning if he is trying to guess the password, he has to literally try out every single character combination. And now the hashes will look different even if the passwords are the same.

{{< img-post "/img" "hash-salt.png" "Two Way Encryption" "center" >}}


What we have discussed so far is just the basics, there are more advanced techniques. So do look around and follow the latest and coolest.

Last note, do not use dummy password like *1234abcd*, no one can help you if you choose a weak password yourself.

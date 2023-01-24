# BIP-85A
Ideas on an extension of BIP-85 to address vanilla passwords

[BIP-85](https://github.com/bitcoin/bips/blob/master/bip-0085.mediawiki)

I've been playing around with an idea to extend BIP-85 as a way to generate plain-vanilla passwords.

Essentially, the idea is to reserve the BIP-85 application (maybe 20880' the base58 of PW). The further derivation path would be at the user's option.

At that point, the user (or their password manager) would choose (arbitrarily) a third derivation location, and associate that with some service (website, program, &c.). Further derivation locations would be {utility}/{key rotation}. So if there's multiple usernames or logins, then increment {utility}. If a specific login needs a new key due to a key rotation requirement (hack or timeout, &c.), then increment {key rotation}.

Finally, the password manager stores the password generation script - could be to grab a set of BIP39 mnemonic words, or the first n-characters (base58, hex, whatever), append or interleave special characters, &c.

So, in the end, a password manager would store:

`google.com

{user_id}

loc: m/83696968'/20880'/231'/0/0

Pattern: [BIP-39]-First 3 words-Capitalize word 1-Delimit with '^'

ie: s/^(\w*?)\s(\w*?)\s(\w*).*/uc($1)^$2^$3/ge`

Or

`ebay.com

{user_id}

Loc: m/83696968'/20880'/9467'/0/0

Pattern: [First 17 characters of pub key as Base58]-slice every 5 and delimit with '!' = 20 character pw

ie: s/^(.{5})(.{5})(.{5})(.{2})/$1!$2!$3!$4/`


Then, a Hardware or other signing device could be sent either:
* a packet requesting the pubkey from, for example, m/83696968'/20880'/231'/0/0   or
* a packet requesting the password, using the pubkey at m/83696968'/20880'/231'/0/0 and the pattern s/^(\w*?)\s(\w*?)\s(\w*).*/uc($1)^$2^$3/ge

Ideally these could be communicated with a QR code or NFC, so devices like a SeedSigner or ColdCard could integrate this functionallity if they want.

---
layout: post
title: "Encrypting like it's 1553"
date: 2016-08-26 15:57:54 +0100
published: false
comments: true
categories: [crypto, rust, toy]
---

## The set up

Italy, 1553, a book is published: La Cifra del Sig. Giovan Battista Bellaso. The book describes a
new cipher consisting on a table of character substitutions that, with the help of a previously
agreed countersign (key), could be used to encrypt a text.

{% img right https://upload.wikimedia.org/wikipedia/commons/thumb/b/bb/Bellaso_1553.JPG/373px-Bellaso_1553.JPG Bellaso transposition table %}

This system is a polysubstitution cypher that has been reinvented many times and that it's
known as the Vigenère cypher. Vigenère was a contemporary cryptographer who invented a similar,
stronger cipher, but later, in the 19th century, the Bellaso cipher was misatributed to him.

Even when the stronger cipher didn't become as famous the Bellaso/Vigenère cipher, it did quite well
for the next three centuries until a method for breaking it was published in 1863.

## The problem

All this historical details where there to introduce what is a really simple programming project
design to help me practice some Rust. Times have changed and we don't have to use a table to
encrypt each character in our messages before sending them, now we have computers!. This also
means that all this schemas are now broken trivially with one of them but this doesn't mean that
we can't have fun implementing it.

The way to translate this cipher to our world of ones an zeros is quite simple. You choose a key,
let's say "KEY", and using its binary representation (e.g. ASCII) just apply XOR[^1] operations
repeteadly to each group of three characters in your message. Decoding it it's just as easy.
Literally just as easy, just apply the same XOR operations as before over the cypher text and you
get the original message!

You can see a basic implementation here:

``` rust Repeating xor
fn repeating_xor(input: &[u8], key: &[u8]) -> Vec<u8> {
    let mut out: Vec<u8> = Vec::with_capacity(input.len());
    unsafe {
        out.set_len(input.len());
    }
    for (i, in_val) in input.iter().enumerate() {
        out[i] = in_val ^ key[i % key.len()];
    }
    return out;
}
```

[^1]: This is really an XOR cipher, another substitution cipher with similar security. It's the same concept but it operates with bits instead of letters.

[alpha-table]: https://upload.wikimedia.org/wikipedia/commons/thumb/b/bb/Bellaso_1553.JPG/373px-Bellaso_1553.JPG

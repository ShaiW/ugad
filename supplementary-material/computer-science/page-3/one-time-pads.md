# One Time Pads

TODO: rewrite after the structure of the appendix is clearer

A _one time pad_ is possibly the first encryption protocol that is actually secure in some modern sense. The idea is very simple, and we can demonstrate it for images.

Say you and I want to send each other an encrypted $$100\times 100$$ image at some time in the future. We prepare for this by meeting up, and flipping $$100\cdot 100$$ coins. We encode the results of our coin flips into a $$100\times 100$$ image, with a black pixel for heads and a white pixel for tails, resulting in a picture somewhat like this:

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Such a picture is called a _one-time pad_. We both keep a copy of this image, so we can use it to send an encrypted message.

How do we do so? Well, say I drew this circle:

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

and it made me feel accomplished and excited, so I want to share it with you. But given how perfect this circle is, I don't want anyone else to see it yet. I can take our one time pad and do the following: for any _black_ pixel in the pad, _change the color_ of the corresponding pixel in my drawing, and send the result. In our case it would look like this:

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

As you can see, it does not look extremely different than the secret-key, and for a good reason. Recall that the original key was _random_. So essentially, what I did is _exactly the same_ as flipping a coin ten-thousand times on the spot to choose what pixels to flip. Now here's the gist: doing this will result in _completely random noise_ regardless of what the initial image was. It doesn't matter. Each pixel is still equally likely to be either black or white.

The symmetry between you, who is supposed to decrypt the message, and the eavesdropper who tries to recover it, is that _you know the results of the coin flips_. Without this information, there is _nothing_ that can be recovered from the cipher.

OK, so that sounds terrific, right? An encryption protocol that is secure against arbitrary adversaries, no matter how powerful, what more can you want?

Well, there is a reason it is called a _one-time pad_. Say that tomorrow you draw this beautiful square:

<figure><img src="../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

and you want to share it with me. Problem: I already used the pad to send you the circle. What happens if you disregard this and use the same pad to send me the square? Well, the encrypted square looks like this:&#x20;

<figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

which also looks like a random pile of garbage, so the attacker never saw our key and only got access to two piles of garbage, where's the problem here?

Well, while it is true that _each separate_ cipher looks like garbage, the ciphers are actually _correlated_: they both got their randomness from the same source. In particular, the _same coin flips_ were used for both. So what happens if we use _one of the images_ on the other? That is, for each black pixel in the first cipher, we flip the corresponding pixel in the second cipher?

Consider a single pixel. If it had the same color on both images, then it will have the same color in both ciphers, so it will be _white_ on the result. Similarly, if it was different colors on both images, it will have different colors in both ciphers, so it will be _black_ on the result. The upshot is that by taking two ciphers and flipping one according to the other, we learn _where the original messages were the same, and where they were different_. While that does not reveal the cipher _completely_ (for example, if you invert both image you would get the same result), it leaks quite a bit of knowledge:

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

An attacker can use this knowledge to correctly guess portions of the secret key, making our scheme very insecure for using more than once.

So we find the disadvantage in one-time pads, that they are _one time_. Consequentally, the secret-key must be at least as large as all messages you intend to send _combined_.

{% hint style="info" %}
This is a special case of a more general theorem, stating that any _information theoretically secure_ encryption schemes _must_ have secret-key as long as the _combined_ length of messages that can be sent securely
{% endhint %}


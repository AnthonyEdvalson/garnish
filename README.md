# Garnish

Garnish is the simple way to get to know your data better. A human readable hashing function.

Garnish takes a holistic view of your data. Instead of treating your data as a cold soulless numbers, garnish tells you the data's name, personality, and aspirations.

A boring way to identify database entried would be with a UUID:

|Id                                  |Title      |Complete|
|------------------------------------|-----------|--------|
|1103685e-b133-4f56-bdf4-35ecc2805314|Wash dishes|False   |
|f8494bde-fafb-4938-8a49-91358d0a99b8|Rake leaves|True    |
|c64a000f-243c-4b6f-8b8d-0b1804bddbe6|Groceries  |False   |

Using garnish, you can turn your boring identifiers into somethign more unique. Garnish puts you on a first name basis with your database entries:

|Id     |Title      |Complete|
|-------|-----------|--------|
|Collin |Wash dishes|False   |
|Katy   |Rake leaves|True    |
|Rebecka|Groceries  |False   |

You can chose how well you want to know your data. By default you just get a first name, but if you want to know them more intimately, you can get increasingly detailed accounts of their lives (and more bits of entropy, reducing risk of collisions)

```
garnish(x, detail=0) -> Sylvie
garnish(x, detail=1) -> Sylvie Tyler
garnish(x, detail=2) -> Sylvie Tyler is a juggler
garnish(x, detail=3) -> Sylvie Tyler is an unsightly juggler
```

# Installing

It's one file, just copy it.

If people want me to add it to package managers I will. 

# Technical Details

Garnishing is starts with the output of `hash(x)` for the given data. However, in later steps it's useful to have a more rigorous hashing function that also guarantees that similar inputs produce dissimilar outputs. The default hash implementations don't always follow this rule, especially with integers, where `hash(x) == x`.

So, the result of `hash(x)` is scrambled using some [bit twiddling](https://stackoverflow.com/a/12996028). Without this step the outputs would all be very similar to one another:

```
garnish(0, detail=3) -> Abbed Smith is an academic abolitionist
garnish(1, detail=3) -> Abbey Smith is an academic abolitionist
garnish(2, detail=3) -> Abbie Smith is an academic abolitionist
garnish(3, detail=3) -> Abby Smith is an academic abolitionist
```

To turn the result of our scrambled hash into a sentence we use modular arithmetic. This is done using mixed bases, and is the same method used for formatting times into something like 16:23:10. In the case of timess, the seconds in the day is being converted to base 24:60:60. In this case we are turning the scrambled hash into base 2789:3999:1423:1171. Those bases come from the lengths of the word lists for the first name, last name, occupation, and adjective, and each value correcponds to a word in the list. So 10:20:30:40 corresponds to first name `Adelaide`, last name `Lee`, occupation of `anaconda`, and adjective of `appreciative`. Formatting it all gives `Adelaide Lee is an appreciative anaconda`.

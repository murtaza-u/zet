# Fermat's Factorization Method

* Let n be a +ve odd integer. Using Fermat's factorization method we
  can efficiently factorise n into p and q (n = p * q) provided
  the **difference between p and q is small**.
* n can be of any length.
* In badly implemented RSA, it may be possible that the difference
  between the 2 randomly selected prime numbers is small. Fermat's
  factorization method can hence be used to break such RSA keys.

```text
n = a^2 - b^2
n = (a + b) * (a - b)  - I

n = p * q              - II
```

Comparing equation I and II,

```text
p = a + b
q = a - b
```

```python
#!/usr/bin/python3

from math import floor, sqrt

def factorize(n):
    # since even nos. are always divisible by 2, one of the factors will always
    # be 2
    if (n & 1) == 0:
        return (n/2, 2)

    a = floor(sqrt(n))

    # if n is a perfect square the factors will be ( sqrt(n), sqrt(n) )
    if a * a == n:
        return (a, a)

    # n = (a - b) * (a + b)
    # n = a^2 - b^2
    # b^2 = a^2 - n
    while True:
        a += 1
        _b = a * a - n
        b = int(sqrt(_b))
        if (b * b == _b):
            break

    return (a + b, a - b)

print(factorize(105327569))
```

    #factorization #math #algorithm

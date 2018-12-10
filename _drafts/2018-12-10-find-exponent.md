---
layout: post
title:  Find the exponent
date:   2018-12-09 01:34:00 -0700
comments: true
categories: Algorithm
---

Find the largest int value for `x` to satisfy `(2**x - 1) * n <= m; n, m are constant int numbers`
<!--more-->

I need to find the value of x according to this equation above. It's finding the exponent, still, it's simple to a computer. All I have to do is to recursively increment x and monitor each round the value of the left hand side of the equation, easy peasy, this is how I did it.
```C
while(((1 << x) - 1) * n < m) {
  x++;
}
return x;
```

Then I came across this solution. I have to say it's not that intuitive at first glance, which took me some time to figure out how it actually works. When I got the idea, I believed it's neat and with better performance because it doesn't have that multiplication, here is how it looks.
```C
int k = n;
while(k < m) {
  k <<= 1;
  k += n;
  x++;
}
return x;
```

Here is a simple program to compare the performance:
```C
#include <time.h>
#include <stdint.h>
#include <stdio.h>
#include <limits.h>

void my_exp() {
  clock_t t = clock();
  const uint32_t n = 3, m = UINT_MAX >> 10;
  uint32_t x = 1;

  while(((1 << ++x) - 1) * n < m) {}
  printf("x: %d\n", x);

  t = clock() - t;
  double sec = ((double)t) / CLOCKS_PER_SEC;
  printf("my_exp took (%f) sec to exe\n", sec);
}

void better_exp() {
  clock_t t = clock();
  const uint32_t n = 3, m = UINT_MAX >> 10;
  uint32_t x = 1;
  uint32_t k = n;

  while(k < m) {
    k <<= 1;
    k += n;
    x++;
  }
  printf("x: %d\n", x);

  t = clock() - t;
  double sec = ((double)t) / CLOCKS_PER_SEC;
  printf("better_exp took (%f) sec to exe\n", sec);
}

int main() {
  my_exp();
  better_exp();
  return 0;
}
```

And the results are expected, the second one is even an order of magnitude faster than the first one.
```
x: 21
my_exp took (0.000037) sec to exe
x: 21
better_exp took (0.000003) sec to exe
```


---
title: "BigDecimal.divide throws ArithmeticException on divide-by-zero (unlike double)"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24 ecomart-java analysis"
tags: [java, bigdecimal, gotcha, arithmetic]
---

# BigDecimal.divide throws ArithmeticException on divide-by-zero (unlike double)

`BigDecimal.divide(divisor, roundingMode)` throws `java.lang.ArithmeticException: Division by zero` when the divisor is zero — it does **not** produce `Infinity`/`NaN` the way primitive `double` division does. So any cost/average/rate calc that divides a `BigDecimal` by a value that can be zero is a latent runtime crash (HTTP 500 in a web service).

Common trigger: an "elapsed time" or "count" computed from user data that turns out to be zero (e.g. a single data point → time span of 0, or an empty denominator).

**Guard before dividing:**
```java
if (divisor.compareTo(BigDecimal.ZERO) == 0) {
    return BigDecimal.ZERO;   // or reject upstream / throw a typed domain error
}
return numerator.divide(divisor, RoundingMode.HALF_UP);
```

Use `compareTo(BigDecimal.ZERO) == 0` (not `equals(BigDecimal.ZERO)`, which is false for `0.00` due to differing scale).

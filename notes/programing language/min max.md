


```go 
water = int(math.Max(float64(smallwall * dist), float64(water)))
water = int(math.Min(float64(smallwall * dist), float64(water)))



// in build 
water = max(smallwall * dist ,water )
water = min(smallwall * dist ,water )

```

- **Type Declaration**: Added `smallwall` variable with `int` type.
- **Math Functions**: Converted values to `float64` for `math.Min` and `math.Max` and then cast them back to `int` after computation.
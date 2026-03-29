# Math Rendering Test

## Test 1: Dollar sign inline (no space after $)
Result: $x^2+y^2=z^2$

## Test 2: Dollar sign inline (with space)
Result: $ x^2+y^2=z^2 $

## Test 3: Double dollar display
$$x^2+y^2=z^2$$

## Test 4: Math code block
```math
x^2 + y^2 = z^2
```

## Test 5: Math code block - Big O
```math
O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)
```

## Test 6: Math code block - mask
```math
\text{mask}_t \in \{0,1\}
```

## Test 7: Math code block - indicator
```math
\mathbb{I}\{R_{\text{diversity}}=1.0\}
```

## Test 8: Math code block - reward
```math
R(\tau) \geq 0
```

## Test 9: Math code block - stats
```math
(\mu_{\text{src}}, \sigma_{\text{src}})
```

## Test 10: Inline with backslash-paren
Inline: \(x^2 + y^2 = z^2\)

## Test 11: Inline dollar no space strict
Word$x^2$word and also mid $y$ sentence.

## Test 12: Double dollar on own lines

$$
O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)
$$

## Test 13: HTML approach
<img src="https://latex.codecogs.com/svg.image?O(|\mathcal{A}_{\text{type}}|+|\mathcal{O}|)" />

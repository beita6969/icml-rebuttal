# Math Rendering Test

## Test 1: Simple inline math
Inline: $x^2 + y^2 = z^2$

## Test 2: Subscript with text
Mask: $\text{mask}_t$

## Test 3: Set notation with lbrace/rbrace
Set: $\text{mask}_t \in \lbrace 0,1 \rbrace$

## Test 4: Set notation with escaped braces
Set2: $\text{mask}_t \in \{0,1\}$

## Test 5: Big-O with lvert/rvert
Complexity: $O(\lvert\mathcal{A}_\text{type}\rvert + \lvert\mathcal{O}\rvert)$

## Test 6: Big-O with pipe |
Complexity2: $O(|\mathcal{A}_{\text{type}}| + |\mathcal{O}|)$

## Test 7: Big-O simple
Complexity3: $O(|A_{\text{type}}| + |\mathcal{O}|)$

## Test 8: Indicator with lbrace
Indicator: $\mathbb{I}\lbrace R_\text{diversity}=1.0\rbrace$

## Test 9: Indicator with escaped braces
Indicator2: $\mathbb{I}\{R_{\text{diversity}}=1.0\}$

## Test 10: Greek and comparison
Reward: $R(\tau) \geq 0$

## Test 11: mu sigma with text subscript
Stats: $(\mu_{\text{src}}, \sigma_{\text{src}})$

## Test 12: Variance comparison
Var: $\text{Var}_{\text{mask}} < \text{Var}_{\text{no-mask}}$

## Test 13: Display math block
$$O(\lvert\mathcal{A}_\text{type}\rvert + \lvert\mathcal{O}\rvert)$$

## Test 14: Big-O with backtick protection
Complexity4: $O\left(\lvert\mathcal{A}\_{\text{type}}\rvert + \lvert\mathcal{O}\rvert\right)$

## Test 15: Escaped underscore
Escaped: $\text{mask}\_t \in \lbrace 0,1\rbrace$

## Test 16: R_control simple
Control: $R_{\text{control}}$

## Test 17: Simple fraction
Fraction: $\frac{a}{b}$

## Test 18: Multiple dollar signs separation
First $x^2$ then $y^2$ and $z^2$

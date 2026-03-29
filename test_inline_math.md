# Inline Math Test

## Test 1: Backtick syntax for Big-O

The factored action space yields $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$ complexity (Proposition 1).

## Test 2: Backtick syntax for Big-O comparison

Yielding $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$ vs. the naive $`O(\lvert\mathcal{A}_{\text{type}}\rvert \times \lvert\mathcal{O}\rvert)`$ complexity.

## Test 3: Backtick syntax for Var inequality

Proposition 6c proves variance reduction: $`\text{Var}_{\text{mask}} < \text{Var}_{\text{no-mask}}`$ (Eqs. 63–64).

## Test 4: Multiple formulas with underscores on same line

The indicator $`\mathbb{I}\{R_{\text{diversity}}=1.0\}`$ creates sign-separated rewards — feasible trajectories get $`R(\tau) \geq 0`$ (Eq. 48), non-feasible get $`R(\tau) < 0`$ (Eq. 46).

## Test 5: Simple inline (should still work)

A binary mask $`\text{mask}_t \in \{0,1\}`$ is applied inside the policy gradient.

## Test 6: Source statistics

Advantages use within-source statistics $`(\mu_{\text{src}}, \sigma_{\text{src}})`$ partitioned by data source.

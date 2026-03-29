# Math Rendering Test v3

## Inline tests (exact formulas from rebuttals)

### A: Big-O with braces around subscript
yielding $O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)$ complexity

### B: Big-O without braces (as in ALL file)
yielding $O(\lvert\mathcal{A}_\text{type}\rvert + \lvert\mathcal{O}\rvert)$ complexity

### C: Two Big-O formulas on same line (as in ALL file)
yielding $O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)$ vs. $O(\lvert\mathcal{A}_{\text{type}}\rvert \times \lvert\mathcal{O}\rvert)$ complexity

### D: mask formula
A binary mask $\text{mask}_t \in \{0,1\}$ is applied

### E: mask with lbrace
A binary mask $\text{mask}_t \in \lbrace 0,1\rbrace$ is applied

### F: Indicator
The indicator $\mathbb{I}\{R_{\text{diversity}}=1.0\}$ creates rewards

### G: R(tau)
feasible trajectories get $R(\tau) \geq 0$ and non-feasible get $R(\tau) < 0$

### H: mu sigma
Advantages use $(\mu_{\text{src}}, \sigma_{\text{src}})$ partitioned by source

### I: Variance
Proposition 6c proves $\text{Var}_{\text{mask}} < \text{Var}_{\text{no-mask}}$

### J: R_control
giving higher weight to $R_{\text{control}}$ to reflect importance

### K: Simple inline
This is $x^2$ and $R_{control}$ and $\tau$

### L: Big-O with simple pipe
yielding $O(|\mathcal{A}_{\text{type}}| + |\mathcal{O}|)$ vs. $O(|\mathcal{A}_{\text{type}}| \times |\mathcal{O}|)$

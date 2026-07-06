# Feeling vega — why an option's vol-sensitivity lives at the strike

> The trick is to stop looking at $d_1, d_2$ as formula-guts and look at what vol _physically does_ to the payoff. Once you see the picture, you never need the algebra again.

---

## 1. The one idea: vol spreads mass, and only the kink cares

Vega asks a simple question: if I add a bit of volatility, how much more is the option worth?

Adding vol means **spreading the probability distribution of $S_T$ wider while keeping its mean (the forward) fixed** — a _mean-preserving spread_.

Now look at the payoff of a call, $(S_T - K)^+$. It is **perfectly linear everywhere except one point** — the kink at the strike $K$. And a mean-preserving spread has, by definition, **zero effect on the expectation of any linear function**: push mass symmetrically outward along a straight line and the average doesn't move. So across the entire flat region _and_ the entire linear-rising region, extra vol does **nothing**.

The only place spreading changes the expected payoff is where the payoff **bends** — the kink at $K$. Vol is convexity's friend, and a vanilla option has exactly one lump of convexity, sitting at the strike.

So vega must be:

$$ \text{Vega} =\underbrace{\big(\text{probability mass near the kink}\big)}_{\propto\ \varphi(d_1)} \times \underbrace{\big(\text{how far a vol bump can push it}\big)}_{\propto\ \sqrt{T}} = S\sqrt{T},\varphi(d_1). $$

The $\varphi(d_1)$ is not a formula artifact — it is literally _how much of the distribution is parked at the strike. The $\sqrt{T}$ is the lever arm of a vol change over the option's remaining life.

---

## 2. The picture: vega = the height of the terminal distribution at the strike

![[vega_vs_moneyness_vol.png]]

Read vega straight off the curve: **the vega of a strike is how tall the terminal distribution of $S_T$ is at that strike.**

- A strike sitting at the forward (**ATM**) reads off the _peak_ of the distribution — lots of mass at the kink — so vega is large.
- A strike far out in the **tail** (deep OTM or ITM) reads off a spot where the curve has flattened to the floor — almost no mass at the kink, and the payoff there is a straight line anyway — so vega is nearly zero.

This single reading settles everything below.

---

## 3. Why puts and calls share the same vega

Same strike $\Rightarrow$ same point on that curve $\Rightarrow$ same height $\Rightarrow$ same vega. Nothing left to compute.

The formal version is put–call parity:

$$ C - P = S - Ke^{-rT}. $$

There is **no $\sigma$ on the right** — it's a static forward-minus-bond relationship. Differentiate in $\sigma$ and the right side is zero, so $\text{Vega}_{\text{call}} = \text{Vega}_{\text{put}}$. The two legs that turn a call into a put — long stock, short bond — carry no vega, and vol can't touch the linear function $S_T - K$ that separates the two payoffs.

---

## 4. What $d_1$ and $d_2$ are really doing

Write them as a **center** plus a **half-width**:

$$ d_1 = \underbrace{\frac{\ln(S/K) + rT}{\sigma\sqrt{T}}}_{\text{center } m} + \underbrace{\tfrac{1}{2}\sigma\sqrt{T}}_{w/2}, \qquad d_2 = m - \tfrac{1}{2}\sigma\sqrt{T}. $$

So $d_1$ and $d_2$ straddle the _same_ center $m$, split apart by the full width

$$ d_1 - d_2 = \sigma\sqrt{T}, $$

which is exactly the **standard deviation of the log terminal price** — the physical width of the distribution.

When you bump $\sigma$, two things happen:

1. **The center $m$ drifts toward 0** ($\sigma$ is in its denominator). Both $d_1$ and $d_2$ slide together — in standardized units, the strike is now "fewer sigmas away" because the distribution got wider.
2. **The gap $d_1 - d_2 = \sigma\sqrt{T}$ widens.** The distribution physically spreads.

The **common drift is a wash** — it is the self-financing re-hedge. The identity

$$ S,\varphi(d_1) = Ke^{-rT},\varphi(d_2) \equiv A $$

says the two marginal trades (buy a little more stock, short a little more bond) exactly fund each other, so moving both legs together costs nothing. Only the **widening** survives. That is the entire collapse, in one line:

$$ \text{Vega} = A\left(\frac{\partial d_1}{\partial \sigma} - \frac{\partial d_2}{\partial \sigma}\right) = A,\frac{\partial(d_1 - d_2)}{\partial \sigma} = A,\frac{\partial(\sigma\sqrt{T})}{\partial \sigma} = A\sqrt{T} = S\sqrt{T},\varphi(d_1). $$

The messy $\partial d_1/\partial\sigma$ never mattered: it moves both density terms equally, and they carry equal weight $A$, so it cancels.

> **In words:** vega = (mass at the strike) $\times$ (the rate at which the distribution widens with vol).

---

## 5. Moneyness dependence falls straight out

Since $\varphi(d_1)$ _is_ the curve's height, the moneyness profile of vega is just the shape of that bell:

- **Peak near the forward-ATM.** $\varphi$ is maximal when $d_1 = 0$, i.e. $S \approx K e^{-(r + \sigma^2/2)T}$. Vega is a **forward** object, not a spot object.
- **Dies in both wings.** Far from the strike, the strike sits where the payoff is a straight line, and vol does nothing to a straight line.

This is a property of _where the kink is relative to the mass_, not of $d_1$ the algebraic symbol.

---

## 6. Changing vol reshapes vega — it does not scale it

![How a volatility change reshapes vega across strikes](https://claude.ai/chat/vega_vs_moneyness_vol.png)

Overlay the vega-vs-moneyness curve for a low-vol world and a high-vol world. Three separate moves happen, each a different Greek talking:

1. **The peak barely moves.** Low-vol peak $\approx 37.6$, high-vol peak $\approx 38.0$ — essentially pinned. That flat peak _is_ **volga $\approx 0$ at the money**: ATM vega $\approx 0.4,S\sigma\sqrt{T}\big/\sigma = 0.4,S\sqrt{T}$ has no $\sigma$ in it, so tripling vol does almost nothing to ATM vega.
2. **The wings fatten enormously.** At $S/K = 1.5$, vega jumps from $\sim 0.6$ to $\sim 28$ — roughly $50\times$. Raising vol doesn't make the ATM option much more vol-sensitive; it **recruits the wing strikes into the vega pool.** At low vol a $1.5$-moneyness strike sits dead in the tail; at high vol the distribution is wide enough that the same strike is back in play — mass has reached its kink.
3. **The peak slides left.** The top drifts from $S/K \approx 0.95$ toward $\approx 0.87$. That is the $\sigma^2/2$ drift term in $d_1$: the peak tracks forward-ATM, and higher vol pushes the spot-terms forward down.

So a vol change is a **reshaping**, not a rescaling. All the extra vega shows up in the wings, which is exactly where the volga (and the smile's curvature) lives.

---

## 7. Vega vs gamma — the same face, a different profile

Both Greeks are $\varphi(d_1)$ wearing different clothes:

$$ \Gamma = \frac{\varphi(d_1)}{S\sigma\sqrt{T}}, \qquad \text{Vega} = S\sqrt{T},\varphi(d_1). $$

- **In moneyness:** near-twins. Same $\varphi(d_1)$ core, so the same bell — same peak near ATMF, same death in the wings. Both ask "how much density sits at the kink."
- **In time:** mirror images. The $\sqrt{T}$ sits **upstairs** in vega, **downstairs** in gamma:

$$ \Gamma \propto \frac{1}{\sqrt{T}}\ \text{(explodes as } T \to 0\text{)}, \qquad \text{Vega} \propto \sqrt{T}\ \text{(vanishes as } T \to 0\text{)}. $$

The felt reason: **gamma is curvature right now** — as expiry nears, the terminal distribution collapses inward, piling all its curvature onto the strike (the value curve hardens toward the raw hockey-stick kink), so gamma blows up. **Vega is sensitivity to vol over the remaining life** — kill the time and you kill the runway vol needs to act, so vega decays away.

This is the split every desk lives on: **short-dated = gamma instruments** (trading realized vol and hedging P&L); **long-dated = vega instruments** (trading implied vol and the surface). The _same strike_ is a gamma trade or a vega trade depending only on $T$.

The bridge between them:

$$ \text{Vega} = \Gamma, S^2 \sigma, T, $$

i.e. vega is _dollar gamma banked over the option's life_. They are proportional only when $S^2\sigma T$ is held fixed — and that $T$ in the bridge is precisely what makes their time behavior run opposite.

---

## Cheat sheet

|Object|Formula|One-line feel|
|---|---|---|
|Call|$C = S,N(d_1) - Ke^{-rT}N(d_2)$|forward $\times$ P(exercise), discounted|
|$d_1,, d_2$|$d_1 = \dfrac{\ln(S/K) + (r+\sigma^2/2)T}{\sigma\sqrt{T}},\quad d_2 = d_1 - \sigma\sqrt{T}$|center $m \pm$ half-width $\tfrac12\sigma\sqrt{T}$|
|Density identity|$S,\varphi(d_1) = Ke^{-rT}\varphi(d_2) \equiv A$|re-hedge is self-financing|
|**Vega**|$\dfrac{\partial C}{\partial\sigma} = S\sqrt{T},\varphi(d_1) = A\sqrt{T}$|mass at the strike $\times$ how fast the width grows|
|Gamma|$\dfrac{\varphi(d_1)}{S\sigma\sqrt{T}}$|curvature at the strike, now|
|Bridge|$\text{Vega} = \Gamma,S^2\sigma T$|vega = dollar gamma over the life|

**The whole thing in one sentence:** vol spreads probability mass; a vanilla payoff only bends at the strike; so vega is the amount of mass sitting at that bend times how far vol can push it — $S\sqrt{T},\varphi(d_1)$ — which is why it peaks at the forward, dies in the wings, is identical for puts and calls, and grows with $\sqrt{T}$ while gamma shrinks.
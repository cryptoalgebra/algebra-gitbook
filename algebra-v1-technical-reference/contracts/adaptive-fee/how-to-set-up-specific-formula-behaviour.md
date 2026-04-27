# How to set up specific formula behaviour

### How to make a fixed fee?

set `alpha1` and `alpha2` as 0, set `baseFee` as you want

### How to increase/decrease max fee?

change `alpha1` and/or `alpha2`

### How to increase/decrease min fee?

change `baseFee`

### Adaptive fee is overreacting

Increase `beta1` and/or `beta2`. Also it's better to change the `gamma` accordingly

### Adaptive fee is weakly responsive to volatility

Decrease `beta1` and/or `beta2`. Also it's better to change the `gamma` accordingly

### When will adaptive fee “calm down”?

Formulas take into account volatility and volume over the last 24 hours

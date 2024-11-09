# Snake sampler

Implementation of the grid-based posterior exploration algorithm described in [Mikkelsen et al. 2013](https://doi.org/10.1088/0004-637x/777/2/172).

Essentially a smart grid sampler, it's effective behavior is:
1. It moves towards the maximum likelihood point;
2. From there it expands outwards, mapping the posterior distribution;
3. It stops when all the points within reach have loglike < max(loglike) – threshold.

<img src="https://otavioalves.com/images/snake.gif" width="200px" height="200px"/>

Samples are uniformily spaced and should be weighted by the posterior for one to obtain posterior densities.

## Credits

Forked from the [CosmoSIS framework](https://github.com/joezuntz/cosmosis). Credits to the authors of the method and the CosmoSIS team.

## Installation

```
pip install git+https://github.com/otavioalves/snake-sampler
```

## Example

```python
import numpy as np
from snake_sampler import Snake

def logpost(params):
    ...
    # derived parameters can be saved as extra_output
    return logpost, extra_output


sampler = Snake(
    posterior=logpost,
    origin=(0, 0, ...), # starting point
    spacing=(0.001, 0.1, ...), # grid spacing
    threshold=4, # stops when delta chi2 with respect to the maxlike point reaches threshold
    pool=None, # can be run in parallel if pool object is provided
    # pool object has to implement pool.size and pool.map(function, list)
)

while not sampler.converged:
    sampler.iterate()

print(f'Snake: done {sampler.iterations} iterations.')

param1, param2, ...,  post = sampler.samples.T
extra_output = sampler.blobs

param1_mean = np.average(param1, weights=np.exp(post))
```

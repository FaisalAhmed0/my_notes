
### Non Parametric entropy estimation

The basic idea is to measure the sparsity of the distrbution by computing the distance between the partilce and its k nearest neighbour, the equation has the following form.
$\mathcal{H}(z) \propto \sum_{i=1}^{N} \log \Vert z_i - z_i^{(k)} \Vert$.

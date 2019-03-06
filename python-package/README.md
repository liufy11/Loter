# Loter Python package

Loter can be used for haplotype phasing and local ancestry inference.

Loter is free for academic use only.

Copyright 2017 All rights reserved - Inria, UGA, CNRS

If you encounter any problem or if you have questions regarding Loter, 
please open an issue on [Github](https://github.com/bcm-uga/Loter.git), 
or you can contact ut at <loter.dev@inria.fr>.

# Installation

The package requires OpenMP (only mandatory for parallel computing), Python and a C++ compiler (tested with g++).

To install Loter, please follow these instructions:

1. Get Loter sources:
```bash
git clone https://github.com/bcm-uga/Loter.git
```

2. Install the `loter` Python package:
```bash
cd Loter/python-package/
python setup.py install
```

**Note:** The following Python packages will be installed during the process 
as dependencies: `numpy`, `pandas`, `scikit-learn`, `scipy`. If not, you may have to
install them before installing `loter`, for instance with the command 
`pip install numpy pandas scikit-learn scipy`.

To install `loter` locally and avoid messing with you system, you can do:
```bash
python setup.py install --user
```
or you can use a Python virtual environment or a specific Python distribution like Anaconda.

If you do not have OpenMP on your system (especially for MacOS users), you can do:
```bash
python setup.py install --no_openmp
# or
python setup.py install --user --no_openmp
```


# Run the method

## Local Ancestry Inference (LAI)

Local ancestry inference with loter in Python is explained in the following Jupyter notebook tutorial: 
[Local Ancestry Example](./Local_Ancestry_Example.ipynb). To access it, you can do:
```bash
cd Loter/python-package/
jupyter notebook
```

**Note:** The tutorial requires the following additional Python packages: `matplotlib` and `scikit-allel` 
(you can run `pip install matplotlib scikit-allel` to get them).

In addition, here is a small example of local ancestry inference with Loter:
```python
import os
import numpy as np

# admixed haplotypes
H_adm = np.load(os.path.expanduser("FILE1")) # replace FILE1 by your data file name
# ref 1 haplotypes
H_ref1 = np.load(os.path.expanduser("FILE2")) # replace FILE2 by your data file name
# ref 2 haplotypes
H_ref2 = np.load(os.path.expanduser("FILE3")) # replace FILE3 by your data file name

# Loter local ancestry inference module
import loter.locanc.local_ancestry as lc

## Loter with bagging and phase correction module
res_loter = lc.loter_smooth(l_H=[H_ref1, H_ref2], h_adm=H_adm, num_threads=8) ## set the number of threads
## Loter with bagging only
res_loter = lc.loter_local_ancestry(l_H=[H_ref1, H_ref2], h_adm=H_adm, num_threads=8) ## set the number of threads
```

**Note:** More details are given in the [notebook](./Local_Ancestry_Example.ipynb), 
especially how to load data from VCF files if your data are not available as Numpy arrays.

**Reference:** Dias-Alves, T., Mairal, J., Blum, M.G.B., 2018. Loter: A Software Package to Infer Local Ancestry for a Wide Range of Species. Mol Biol Evol 35, 2318–2326. https://doi.org/10.1093/molbev/msy126

**Simulations of admixed individuals:** informations about data simulation are available  [here](https://github.com/BioShock38/aede).


## Phasing

Two methods to run the package for phasing

```python
import os
import numpy as np

# Directly run the C++ function

import haplophase.wrapper_cpp as hap

G = np.load(os.path.expanduser("FILE")) # replace FILE by your data file name
G_res = np.copy(G)
H = hap.wrapper_all(G=G_res, k=k, nb_iter=20, nb_run=10, w=100, penalty=2.0)

# You get the imputed genotype matrix in G_res and H the haplotypes.
```

```python
# You can create your own pipeline or select one already existing

import haplophase.pipeline as pipeline
G = np.load(os.path.expanduser("FILE"))

l_res = pipeline.pipelines["classic_pipeline"].run(np.copy(G), nbrun=10, nb_iter=20, nb_run=10, w=100, penalty=2.0)
# You get a list of results that you can combine.

G_res = combine.combiner_G["G vote"](l_res)
H_res = combine.combiner_H["H_mean"](l_res)
```

**Reference:** Dias Alves, T., 2017. Modélisation du déséquilibre de liaison en génomique des  populations par méthodes l’optimisation. PhD manuscript. Grenoble Alpes University. http://www.theses.fr/2017GREAS052
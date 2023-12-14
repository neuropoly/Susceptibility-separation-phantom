# <div align="center">**Susceptibility-separation-phantom**</div>
### Theory:
This repository can create an in-silico brain phantom containing positive and negative magnetic susceptibility ($\chi^+$ and $\chi^-$) values, where $\chi^+$ and $\chi^-$ represent the iron and myelin susceptibility contribution to the total susceptibility. The code to simulate $\chi^+$ and $\chi^-$ maps is based on an extension to the [QSM validation phantom](https://data.ru.nl/collections/di/dccn/DSC_3015069.02_542?0) $^1$ developed for the second QSM reconstruction challenge $^1$. Our phantom offers the option of considering the anisotropic nature of WM susceptibility, i.e., its orientation-dependent behavior relative to the main magnetic field. 

### Phantom creation:
Simulated $\chi^+$ and $\chi^-$ maps were generated by assigning susceptibility values from the literature $^{2–4}$ to respective tissue segments (Table 1). Subsequently, $\chi^+$ and $\chi^-$ were adjusted to ensure that their sum aligns with the $\chi^{tot}$ values specified in the QSM challenge 2.0, which represents the total magnetic susceptibility.
| Region           | $\chi^{total}$ (ppm) | $\chi^{positive}$ (ppm) | $\chi^{negative}$ (ppm) |
| ---------------- | --------------------- | ------------------------ | ------------------------ |
| Caudate nucleus  | 0.044                | 0.0527                   | -0.0087                 |
| Globus pallidus  | 0.131                | 0.1437                   | -0.0132                 |
| Putamen          | 0.038                | 0.0471                   | -0.0091                 |
| Red nucleus      | 0.1                  | 0.01109                  | -0.0109                 |
| Dentate nucleus   | 0.152                | 0.1684                   | -0.0164                 |
| Substantia nigra | 0.111                | 0.1224                   | -0.0114                 |
| Thalamus         | 0.02                 | 0.0509                   | -0.0309                 |
| White matter     | -0.03                | 0.0059                   | -0.0359                 |
| Grey matter      | 0.02                 | 0.0392                   | -0.0192                 |
| CSF              | 0.019                | 0.0275                   | -0.0085                 |

**Table 1:**  Simulated average $\chi^{tot}$, $\chi^{+}$, and $\chi^{-}$ values (“ground truth” ) for different regions of the brain.
 
Pre-scanned $R_{1}$ and $R^{\*}_ {2}$ maps (from the original QSM phantom) were used to create an anatomically valid intensity modulation for susceptibility values using the same approach as that of Marques et al. $^1$:

$$
\bar{\chi}^{\pm}_ {\text{tissue}}(r) = \bar{\chi}^{\pm}_ {\text{tissue}}(r) + a_{\text{tissue}}(R^{\*}_ {2}(r) - \bar{R}^{\*}_ {2}) + b_{\text{tissue}}(R_{1}(r) - \bar{R}_{1})
$$



where $\bar{\chi}^{\pm}_ {\text{tissue}}$ are the values from Table 1, $R_{1}$ and $R^{\*}_ {2}$  are the longitudinal and observed transverse relaxation maps with $\bar{R}_ {1}$  and $\bar{R}^{\*}_ {2}$  being their mean value for a given brain region and $a_{\text{tissue}}$ and $b_{\text{tissue}}$ are weighting factors. 
Our custom phantom offers the possibility of including WM’s susceptibility anisotropy by modeling $\chi^-$ as:

$$
(\chi_{||} - \chi_{\perp}) \cos^2 \theta + \chi_{0}
$$

Where $\chi_{||}$ and $\chi_{\perp}$ are the susceptibility of myelinated fibers along and perpendicular to their principal axis, $\theta$ is the fiber-to-field angle, and $\chi_{0}$ represents any orientation-independent susceptibility. $\chi_{||}$, $\chi_{\perp}$, and $\chi_{0}$ maps were generated from literature values $^{5–7}$ (Table 2) that were subsequently weighted using the $R_{1}$ map and subjected to Gaussian noise in order to add texture to the tissue regions.

| Region                                  | $\delta\chi(\chi_{\|\|} - \chi_{\perp}) (ppm)$ | $\chi_{0} (ppm)$  | 
| --------------------------------------- | -------------------------------------    | ----------- | 
| Body of the corpus callosum             | 0.032                                    |-0.0512      |
| Splenium of the corpus callosum         | 0.024                                    | -0.0522      |
| Genu of the corpus callosum             | 0.014                                    | -0.0382     | 
| Anterior limb of the internal capsule   | 0.016                                      | -0.0512     | 
| Posterior thalamic radiations           | 0.016                                    | -0.0592      |
| Superior corona radiata                 | 0.005                                    | -0.0442      | 
| Posterior corona radiata                | 0.008                                     | -0.0542      | 
| Anterior corona radiata                 | 0.006                                    | -0.0462      | 
| Posterior limb of the internal capsule  | -0.015                                     | -0.0382      |
| Superior longitudinal fascicle          | -0.015                                    | -0.0372      |


**Table 2:**  Simulated average anisotropy values, $\delta\chi(\chi_{\|\|} - \chi_{\perp})$, and isotropic susceptibility, $\chi_{0}$, derived from the literature for different white matter regions.

Finally, our phantom allows us to simulate a transversal relaxation time $R_2$ map $\(\frac{1}{T_2}\)$. This map was also simulated based on literature values $^{8,9}$ (Table 3) and then weighed using $R^{\*}_ {2}$ and $M_0$ to create realistic tissue textures.


| Region           | $T_2$ (ms) |
| ---------------- | --------------------- | 
| Caudate nucleus  | 57.46               | 
| Globus pallidus  | 41.47                |
| Putamen          | 50.44                | 
| Red nucleus      | 44.07                  | 
| Dentate nucleus   | 71.71                | 
| Substantia nigra | 47.26                | 
| Thalamus         | 56.62                 | 
| White matter     | 45.54                | 
| Grey matter      | 84.71                 | 
| CSF              | 1029                | 

**Table 3:**  Simulated average $T_2$ values derived from the literature for different regions of the brain.


### Gradient echo simulation:
The created phantom can be used to simulate gradient-echo (GRE) data using the following equation:
 
$$S=M_0\sin(\alpha)\frac{1-e^{-TR.R_1}}{1-\cos(\alpha)e^{-TR.R_1}}e^{-TE.(R_2+D_r(|\chi^+|+|\chi^-|))+i(\Phi_0+2\pi{\gamma}TE.B_0(D(\chi^++\chi^-)))}$$

where D is the magnetic dipole kernel, $\alpha$ is the flip angle, TR is the repetition time, TE is the echo time, $\Phi_0$ is a phase offset, $M_0$ is the net magnetization, $B_0$ is the field strength, $$\gamma is the gyromagnetic ratio, and $D_r$ maps represents the proportionality between $R^{\'}_ {2}$ $(=R^{\*}_ {2}-R_ 2)$ and absolute susceptibility. $D_r$ was modeled as $\tfrac{2\pi}{9\sqrt{3}}{\gamma}B_0$ in gray matter and as $\tfrac{1}{2}\gamma B_0sin^2(\theta)$ in white matter.

The phantom and accompanying code can be used to simulate MR signals and evaluate various magnetic susceptibility-separation techniques, such as [&chi;-separation](https://github.com/SNU-LIST/chi-separation) $^{10}$, [APART-QSM](https://github.com/AMRI-Lab/APART-QSM) $^{11}$, or DECOMPOSE QSM $^{12}$.

### Requirements:

- Susceptibility-separation-phantom repository.
- Matlab license.
- [Nifti toolbox.](https://www.mathworks.com/matlabcentral/fileexchange/8797-tools-for-nifti-and-analyze-image) 
- [QSM challenge 2.0 repository.](https://data.ru.nl/collections/di/dccn/DSC_3015069.02_542?1)

### Pre-Usage:

After downloading the QSM challenge 2.0 repository and this repository, replace the following files from the QSM Challenge 2.0 repository with the corresponding files from this repository:
```
     QSM Challenge 2.0 version                                      Our version               
 -------------------------------                                ---------------------      
 MacroCreateSusceptibilityPhantom.m           ------->            PhantomCreation.m          
 CreateOwnRealisticPhantom.m                  ------->            PhantomCreationFunction.m 
 MacroCreateSimulationData.m                  ------->            DataSimulation.m           
 CreateSimulatedData.m                        ------->            DataSimulationFunction.m   
 DataSimulation.m                             ------->            GRESimulation.m            
 parameters.mat                               ------->            SusceptibilityValues.mat   
```

In addition, make sure to add these files to the main QSM challenge file directory: **Mask.m, Anisotropy.m, T2_star.nii.gz, white_matter_mask.nii.gz**.

### Usage:
Once you have all of the required files, follow these steps to create positive and negative susceptibility maps and simulate gradient-echo data:
1) To create susceptibility phantoms: run **PhantomCreation.m** 
 - This script will create susceptibility phantoms: $\chi^{tot}$ from QSM challenge 2.0 and additionally $\chi^{+}$ and $\chi^{-}$ phantoms with and without anisotropy.  In addition, it will simulate an $R_2$ map $\(\frac{1}{T_2}\)$. This script will also generate spatially variable and constant $D_r$ maps (to include or exclude anisotropy)

2) To simulate GRE magnitude and phase data: run **DataSimulation.m**
When simulating consider checking the input of the Anisotropy.m function
- if you want to simulate data with anisotropy select **Asotropy** as “true”.
-  if you want to simulate without anisotropy select **Anisotropy** as “ false”.
### Recommendations:
 
1) We recommend using this phantom for validating susceptibility separation algorithms. 

For this you will need the following script:
- CalculateR2prime.m: Calculate $R^{\'}_ {2}$ map from $R_2$ and $R^{\*}_ {2}$.
At this point, an $R_2$ map is already simulated. To obtain an $R^{\*}_ {2}$ map, we recommend applying a mono-exponential fitting to the simulated magnitude data using [**qMRLab software**](https://qmrlab.org/). 

2) To visualize a color-coded white matter fiber orientation map run the script: **GenerateRgbMap.m** 

### Results:

All the resulting maps are available in the repository as essential reference points for anyone seeking to reproduce the work.
![image](https://github.com/neuropoly/Susceptibility-Separation-Phantom/assets/77506715/1cdaa86d-b1dd-4529-b91f-87bfe247ed57)

### Question:
If you have any questions or encounter issues, please don't hesitate to open an issue, and we will respond as soon as possible to assist you.


### References:

1.	Marques, J. P. et al. QSM reconstruction challenge 2.0: A realistic in silico head phantom for MRI data simulation and evaluation of susceptibility mapping procedures. Magn. Reson. Med. 86, 526–542 (2021).
2.	Deistung, A. et al. Toward in vivo histology: a comparison of quantitative susceptibility mapping (QSM) with magnitude-, phase-, and R2*-imaging at ultra-high magnetic field strength. Neuroimage 65, 299–314 (2013).
3.	Buch, S. et al. Susceptibility mapping of air, bone, and calcium in the head. Magn. Reson. Med. 73, 2185–2194 (2015).
4.	Hallgren, B. & Sourander, P. The effect of age on the non-haemin iron in the human brain. J. Neurochem. 3, 41–51 (1958).
5.	Li, X. et al. Mapping magnetic susceptibility anisotropies of white matter in vivo in the human brain at 7 T. Neuroimage 62, 314–330 (2012).
6.	Sibgatulin, R., Güllmar, D., Deistung, A., Ropele, S. & Reichenbach, J. R. In vivo assessment of anisotropy of apparent magnetic susceptibility in white matter from a single orientation acquisition. Neuroimage 241, 118442 (2021).
7.	Sibgatulin, R. et al. Magnetic susceptibility anisotropy in normal-appearing white matter in multiple sclerosis from single-orientation acquisition. Neuroimage Clin 35, 103059 (2022).
8.	Kumar, R., Delshad, S., Macey, P. M., Woo, M. A. & Harper, R. M. Development of T2-relaxation values in regional brain sites during adolescence. Magn. Reson. Imaging 29, 185–193 (2011).
9.	Kumar, R., Delshad, S., Woo, M. A., Macey, P. M. & Harper, R. M. Age-related regional brain T2-relaxation changes in healthy adults. J. Magn. Reson. Imaging 35, 300–308 (2012).
10.	Shin, H.-G. et al. χ-separation: Magnetic susceptibility source separation toward iron and myelin mapping in the brain. Neuroimage 240, 118371 (2021).
11.	Li, Z. et al. APART-QSM: An improved sub-voxel quantitative susceptibility mapping for susceptibility source separation using an iterative data fitting method. Neuroimage 274, 120148 (2023).
12.	Chen, J., Gong, N.-J., Chaim, K. T., Otaduy, M. C. G. & Liu, C. Decompose quantitative susceptibility mapping (QSM) to sub-voxel diamagnetic and paramagnetic components based on gradient-echo MRI data. Neuroimage 242, 118477 (2021).







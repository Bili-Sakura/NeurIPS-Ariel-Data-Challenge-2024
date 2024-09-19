# Overview

Are you ready to embark on a journey that pushes the boundaries of astronomical data analysis?

The Ariel Data Challenge 2024 invites you to develop machine learning models to solve one of the most formidable challenges in the field—extracting faint exoplanetary signals from simulated observations of the upcoming ESA Ariel Mission🚀!

## Description

![](./assets/ariel_description_image.png)

The discovery of exoplanets—planets orbiting stars other than our Sun—has transformed our cosmic perspective, challenging conventional notions about Earth's uniqueness and the potential for life elsewhere. As of today, we are aware of over 5,600 exoplanets. Detecting these worlds is the initial step; we must also comprehend and characterise their nature by studying their atmospheres. In 2029, [ESA Ariel Mission](https://arielmission.space/) will conduct the first comprehensive study of 1,000 extrasolar planets in our galactic neighbourhood.

Observing these atmospheres is one of the hardest data-analysis problems in contemporary astronomy. When an exoplanet transits its host star in our line of sight, a tiny fraction of starlight (50–200 photons per million) passes through the planet's atmospheric annulus and interacts with its chemistry, clouds, and winds. These faint signals typically range from 50ppm (for Super-Earth like planets) to 200ppm (for Jupiter like planets) in magnitude and are regularly corrupted by the noise of the instrument. A major component of this noise is due to the inevitable vibration of the spacecraft in space, known as ‘jitter noise’. This noise arises from the difficulties of maintaining precise pointing in low-gravity environments, as the spacecraft relies on spinning momentum wheels for stability. Akin to taking long-exposure images with a shaky hand, this noise poses a far greater challenge than the motion blur encountered in commercial photography applications. The photometric variation (∼200 ppm) caused by jitter noise alone is comparable to the variation exhibited by the planetary signal we aim to detect, undermining signals from small planets like Earths and super-Earths. Coupled with other sources of correlated and uncorrelated noises, it is proving difficult for us to achieve the strict technical requirement of the Ariel Payload design.

The task of this competition is to extract the atmospheric spectra from each observation, with an estimate of its level of uncertainty. In order to obtain such a spectrum, we require the participant to detrend a large number of sequential 2D images of the spectral focal plane taken over several hours of observing the exoplanet as it eclipses its host star. Performing this detrending process to extract atmospheric spectra and their associated errorbars from raw observational data is a crucial and common prerequisite step for any modern astronomical instrument before the data can undergo scientific analysis.

## Possible Approaches

![](./assets/inbox_18942071_d43f0c5cd9f93fc2c334ec42f0cdd95b_data_reduction.jpg)

This is a multimodal supervised learning task. Participants can choose to detrend this jitter noise in either modality (i.e. the image, time or spectral domains) or combinations thereof. Each modality bears different advantages. Here we outline two common training strategies.

Approach 1: Train directly on the full 3D data cube and extract the corresponding spectra. This approach leverages the rich information content but as a consequence requires a lot of computing resources (See Image --> Spectral Domain on the above figure).
Approach 2: Make the data lighter by summing up the fluxes along the pixel y-axis, for each wavelength, resulting in 2D images of dimension (`$N_{times}$`, `$N_{wavelengths}$`), and transform the images in order to enhance transit depth variations between wavelengths.
However, neither approach is optimal for denoising jitter time series and we anticipate the winning solutions to include information from all three domains.

## Evaluation

This competition evaluates predicted spectra ($μ\_{user}$) and corresponding uncertainties ($σ\_{user}$) for different wavelengths against the ground truth pixel level spectrum (y) using using the Gaussian Log-likelihood (GLL) function.

$$
GLL = -\frac{1}{2} \left( \log(2\pi) + \log(\sigma_{user}^2) + \frac{(y - \mu_{user})^2}{\sigma_{user}^2} \right)
$$

The GLL values from each pair will be summed across all wavelengths and across the entire test set to produce a final GLL value (L). The final GLL value will be transformed into a score using the following conversion function:

$$
score = \frac{L- L_{ref}}{L_{ideal} - L_{ref}}
$$

We define `$L\_{ideal}$` as the case where the submission perfectly matches the ground truth values, with an uncertainty of 10 parts per million (ppm). This ideal case is defined based on Ariel's Stability Requirement. For `$L\_{ref}$` is defined using the mean and variance of the training dataset as its prediction for all instances.

The score will return a float in the interval [0, 1], with higher scores corresponding to better performing models. Any score below 0 will be treated as 0.

[The full metric implementation is available here.](https://www.kaggle.com/code/metric/ariel-gaussian-log-likelihood)

## Submission File

You must predict a mean and uncertainty for each `planet_id`. An example submission file is included in the Data Files. Each submission row must include 567 columns, so we will not attempt to provide an example here. The leftmost column must be the `planet_id`, the next 283 columns must be the spectra, and the remaining columns the uncertainties.

## Timeline

* August 1, 2024 - Start Date.
* October 24, 2024 - Entry Deadline. You must accept the competition rules before this date in order to compete.
* October 24, 2024 - Team Merger Deadline. This is the last day participants may join or merge teams.
* October 31, 2024 - Final Submission Deadline.

All deadlines are at 11:59 PM UTC on the corresponding day unless otherwise noted. The competition organizers reserve the right to update the contest timeline if they deem it necessary.

## NeurIPS 2024

This competition is part of the [Competition Track](https://blog.neurips.cc/2024/06/04/neurips-2024-competitions-announced/) at [NeurIPS 2024](https://neurips.cc/Conferences/2024). Top submissions for the competition will be invited to give talks at the workshop. Attending the workshop is not required to participate in the competition, however only teams that are attending the workshop will be considered to present their work.

Attendees presenting in person are responsible for all costs associated with travel, expenses, and fees to attend [NeurIPS 2024](https://neurips.cc/Conferences/2024).

Attendees will also have the opportunity to be invited to present their work at the Ariel Consortium Meeting, however, attendees are responsible for all costs associated with travel, expenses, and fees to attend the conference.

## Prize

* 1st Place - $15,000
* 2nd Place - $10,000
* 3rd Place - $8,000
* 4th Place - $7,000
* 5th Place - $5,000
* 6th Place - $5,000

## Code Requirements

This is a Code Competition
Submissions to this competition must be made through Notebooks. In order for the "Submit" button to be active after a commit, the following conditions must be met:

* CPU Notebook <= 9 hours run-time
* GPU Notebook <= 9 hours run-time
* Internet access disabled
* Freely & publicly available external data is allowed, including pre-trained models
* Submission file must be named submission.csv
* Submission runtimes are slightly obfuscated.

Please see the Code Competition FAQ for more information on how to submit. And review the code debugging doc if you are encountering submission errors.

## Acknowledgements

Ariel Data Challenge 2024 Organising Team:

Dr. Kai Hou Yip (UCL)
Dr. Lorenzo V. Mugnai (Cardiff University & UCL)
Ms. Rebecca L. Coates (UCL)
Dr. Andrea Bocchieri (Sapienza Università di Roma)
Dr. Andreas Papageorgiou (Cardiff University)
Mr. Orphée Faucoz (CNES)
Ms. Tara Tahseen (UCL)
Dr. Virginie Batista (IAP)
Ms. Angèle Syty (IAP)
Mr. Arun Nambiyath Govindan (UCL)
Dr. Ingo P. Waldmann (UCL)


We would like to acknowledge the advice and support from the following individuals:
Sudeshna Boro Saikia, Quentin Changeat, Pascale Danto, Sohier Dane, Maggie Demkin, Billy Edwards, Paul Eccleston, Clare Jenner, Ryan King, Theresa Lueftinger, Nikolaos Nikolaou, Enzo Pascale, Luís F. Simões and Giovanna Tinetti.

The Ariel Data Challenge 2024 is created by the following institutes:

The Ariel Data Challenge 2024 is sponsored by

with support from the Kaggle Competition Research Program and the following institutions and agencies:

## Citation

Kai Hou Yip, Lorenzo V. Mugnai, Rebecca L. Coates, Andrea Bocchieri, Andreas Papageorgiou, Orphée Faucoz, Tara Tahseen, Virginie Batista, Angèle Syty, Arun Nambiyath Govindan, Sohier Dane, Maggie Demkin, Enzo Pascale, Jean-Philippe Beaulieu, Quentin Changeat, Pierre Drossart, Billy Edwards, Paul Eccleston, Clare Jenner, Ryan King, Theresa Lueftinger, Nikolaos Nikolaou, Pascale Danto, Sudeshna Boro Saikia, Luís F. Simões, Giovanna Tinetti, Ingo P. Waldmann (2024). (2024). NeurIPS - Ariel Data Challenge 2024. Kaggle. https://kaggle.com/competitions/ariel-data-challenge-2024. (2024). NeurIPS - Ariel Data Challenge 2024. Kaggle. https://kaggle.com/competitions/ariel-data-challenge-2024

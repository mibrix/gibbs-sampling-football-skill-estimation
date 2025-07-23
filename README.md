# Project assignment Advanced Probabilistic Machine Learning 2024

Short project description: Ranking systems are essential for estimating player skill based on match outcomes, and the TrueSkill system, developed by Microsoft Research, is a widely used Bayesian approach for this task. In out project, we implement a Bayesian method based on TrueSkill to estimate player skills as Gaussian distributions and update them after each match. Using match data from the 2018/2019 Italian football league we apply Gibbs sampling and Assumed Density Filtering (ADF) to infer skills and predict match results.Additionally, we utilize message passing and factor graphs to compute posterior distributions in the model. We also explore possible extensions to improve the model; excluding Juventus’ last three matches, as their performance was affected by having already secured the championship, allowing for a better reflection of their overall season skill.

## How to set up virtual environment
1. Create a virtual environment:

`python -m venv venv`

2. Activate the virtual environment:

`source venv/bin/activate`

3. Install required packages

`pip install -r requirements.txt`


## Running the code

To run the code form  Jupyter notebook, use the following command:
`jupyter notebook runme.ipynb`


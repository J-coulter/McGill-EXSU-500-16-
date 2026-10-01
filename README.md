# McGill EXSU 500 (16): Drug–Target Binding Affinity Prediction

Applications of Artificial Intelligence in Experimental Surgery — Drug Development Project.

## Project Overview

This project aims to develop a machine-learning model that predicts the binding affinity between a small molecule and a protein target using information about the molecule's chemical structure and the target's characteristics.

**Task type:** Regression

**Dataset:** BindingDB

**Dataset source:** [BindingDB](https://www.bindingdb.org/rwd/bind/index.jsp), a public database of experimentally measured binding affinities between proteins and small molecules.

## Problem Statement

Early-stage drug discovery requires researchers to evaluate large numbers of candidate molecules to identify those that may interact with a biological target of interest. Experimental screening is essential, but it can be resource-intensive when applied to large compound libraries.

The goal of this project is to use molecular structure and protein-target features to estimate binding affinity. Because binding affinity is a continuous quantity, the problem is framed as a **regression task**.

## Why This Matters

A computational model for binding-affinity prediction could help:

- **Reduce screening costs:** prioritize compounds before committing resources to laboratory experiments.
- **Improve screening efficiency:** narrow large candidate libraries to a more manageable set for experimental evaluation.
- **Support early-stage drug discovery:** help researchers prioritize compounds for follow-up testing against a selected biological target.
- **Inform downstream research:** provide an additional source of evidence when considering potential candidates and planning further experiments.

The intended application is to support, not replace, experimental testing. Predictions are estimates and would need to be evaluated against held-out data and validated experimentally. Although better prioritization may contribute to a more efficient drug-development process, this model alone cannot establish clinical efficacy, prognosis, or the likelihood of side effects.

## Dataset

BindingDB provides experimentally measured protein–small-molecule binding data. These measurements will be used as the basis for training and evaluating a regression model.

The project will use the molecular structure of each small molecule and relevant characteristics of its protein target as input features, with the measured binding-affinity value as the prediction target. Data preparation will need to account for missing or inconsistent records and ensure that training and evaluation are performed appropriately.

## Intended Outcome

The intended outcome is a computational approach that estimates small-molecule/protein binding affinity and can help prioritize compounds for subsequent laboratory screening. Model performance will be assessed using suitable regression metrics, with the results interpreted in the context of the available dataset and its limitations.

## Data Source

- [BindingDB — Binding Affinity Database](https://www.bindingdb.org/rwd/bind/index.jsp)

# Explainable_AI_Genomics_Study

![Chromosomes](chromosomes.jpg)

# Run the code

To run the code, unzip the following in the input folder:
OncoGAN_synthetic_CNA_SV.zip
OncoGAN_synthetic_VCFs.zip

Next, run the scripts in this order:
1. load.ipynb
2. eda.ipynb*
2. feature_engineering.ipynb
3. modeling.ipynb
4. cancer_driver_mapping.ipynb

*eda is optional for the pipeline, but the graphs are fun to interpret!

*If you get lost at any point, refer to the info.txt in the following folders: input/interim/feature_eng
The info.txt's are useful since these datafiles are too large to put into git, so this way you can perform a sanity check to see if the files loaded from each step look correct with the general file architecture.

# Background

For decades, cancer has plagued the world with its mystique. The law of large numbers tells us that the more we observe something, the more we should understand its distribution and underlying patterns. But, if we have lost so many lives to cancer and have hundreds of thousands of cancerous patient records, how come we don't seem to know that much about it?

Researchers are eager to explore the patterns of cancer genomes, but many are not able to dig into the data and hypothesize without having a career in a medical company working for a pharmaceutical company or hospital. HIPAA has any cancerous genome held on tight, creating a data science bottleneck of the possibilities that public cancer genomes can offer.

While public cancer genomes are not ethical, ![OncoGAN](https://github.com/LincolnSteinLab/oncoGAN) has come up with a solution. The solution is creating synthetic tumor genome cancer variants, which represent the deidentified underlying patterns learned from 2,658 whole genome sequences (WGS) from the The Pancancer Analysis of Whole Genomes (PCAWG) study. 

These synthetic genomes express the patterns of these 2,658 cancerous genomes that it is trained on. Now, our research question is:

### To what extent do the synthetic genomes represent known mutation drivers across cancer types reported in medical literature?

This is the question that this Repository, Explainable_AI_Genomics_Study, is aiming to answer. Let me walk you through each script. 

## load_vcf

This notebook loads in the CNAs, VCFs and SVs of all the synthetic genomes. These genomes are created from OncoGAN, where we have 800 simulated genomes. The goal of this notebook is to parse and save 3 dataframes containing all the data to folder /data/inputs. See 'Running the code' section for more details on this.

## eda

Our research question is begging to know key drivers for the 8 different cancer tumors. So, we decided we should train a model to figure out which features carry the highest weight and, in turn, drive cancer. To help us with this process, exploring the data and creating visualizations for potential features we may want to feature engineer later is extremely helpful to our data science pipeline.

Some of our findings from our EDA are:
1. The SB85 mutational signature is uniquely prone to have a very large number of mutations in tumor type Eso-AdenoCa.
2. Contrary to our expectations, Breast-AdenoCa (Breast cancer) does not have a significant amount of mutational variants (VCFs, Variant Call Formats) specific to chromosome X, AKA, the sex chromosome in woman. Instead, it has similar amounts of mutational variants across chromosome X and chromosomes 1 through 10. 
3. There are significantly less (half) mutational variants on chromosomes 17, 19, 22 and Y in comparison to chromosome 1 through 8. The Y is to be expected since Y, and the later pairs, are smaller than the earlier chromosomes. We observe a proportional decrease in mutations with respect to chromsome length in base pairs!

These observations, as well as specific explainations on VCFs, SVs and CNAs, are documented in eda.ipynb.

## feature_engineering

Applying what we learned from EDA, we feature engineered the following fields:

### With VCFs:
* snv_total: total SNVs per donor
* snv_chr_*: counts per chromosome
* snv_ms_driver_*: one-hot encoded mutation signature

### CNAs:
* cna_n_segments: number of CNAs per sample
* cna_gain_Mp: Mb of gained DNA
* cna_loss_Mp: Mb of lost DNA
* cna_frac_altered: (gained + lost DNA) / avg size of genome ≈ fraction of genome affected by CNAs

### SVs:
* sv_total: total Structural Variants (SVs) per donor
* sv_DEL: total deleted SVs per donor
* sv_DUP: total duplicated SVs per donor
* sv_TRA: total translocation SVs per donor
* sv_h2hINV: total head-to-head inversions
* sv_t2tINV: tail-to-tail inversion

## Modeling

Combining the feature engineered fields with our initial CNAs, SVs, and VCFs we have a robust dataset for modeling. The script utalizes 3 models, Logistic Regression and Random forest. Note the F1 scores and test accuracies.

### Our Innovation:

OncoGAN came with its own classifier for the 8 tumor types, however it was a Deep Nueral Network (DNN) ran through Docker. It was not interpretible, and a black box for extracting cancerous patterns. So, we created our own with differing feature inputs and models. We receieved a really high test and train score, with our highest on Logistic Regression. The rest of this paper is interpreting the learned weights from our best model, logreg_multi.  

| Features       | Model               | Accuracy | Macro-F1 |
|----------------|---------------------|----------|----------|
| SNV-only       | LogisticRegression  | 0.98750  | 0.987469 |
| SNV + CNA + SV | LogisticRegression  | 0.99375  | 0.993746 |
| SNV + CNA + SV | RandomForest        | 0.98125  | 0.981246 |

### /data/explainable_ai

With these CSV files produced from modeling, we can now do our independent research to see if these highly weighted variables are recognized in the medical field as being drivers for that specific cancer. If we find a result from a peer-reviewed journal, we will put a '1' in that category. If not, we will put a 0. 

# Running the code

The original dataframes are not uploaded to git as the files are too large, however there are toy files loaded which are subsamples of the true data (first 3 simulated genomes) that we used that you can use to run the code. Please note that running the code with these files will not reproduce the final results, however they can 'run' the code. If you want

# Conclusion:




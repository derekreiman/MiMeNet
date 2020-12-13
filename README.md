## MiMeNet: Exploring Microbiome-Metabolome Relationships using Neural Networks
MiMeNet predicts the metabolomic profile from microbiome data and learns undelrying relationships between the two.

### Prerequisites
* MiMeNet is tested to work on Python 3.7+
* MiMeNet requires the following Python libraries:
  - Tensorflow 1.14
  - Numpy 1.17.2
  - Pandas 0.25.1
  - Scipy 1.3.1
  - Scikit-learn 0.21.3
  - Scikit-bio 0.5.2
  - Matplotlib 3.0.3
  - Seaborn 0.9.0
  

### Usage

```bash
usage: MiMeNet_train.py [-h] -micro MICRO -metab METAB
                        [-external_micro EXTERNAL_MICRO]
                        [-external_metab EXTERNAL_METAB]
                        [-annotation ANNOTATION] [-labels LABELS] -output
                        OUTPUT [-net_params NET_PARAMS]
                        [-background BACKGROUND]
                        [-num_background NUM_BACKGROUND]
                        [-micro_norm MICRO_NORM] [-metab_norm METAB_NORM]
                        [-threshold THRESHOLD] [-num_run_cv NUM_RUN_CV]
                        [-num_cv NUM_CV] [-num_run NUM_RUN]

```

```
  -h, --help                        Show this help message and exit
  -micro MICRO                      Comma delimited file representing matrix of samples by microbial features
  -metab METAB                      Comma delimited file representing matrix of samples by metabolomic features
  -external_micro EXTERNAL_MICRO    Comma delimited file representing matrix of samples by microbial features
  -external_metab EXTERNAL_METAB    Comma delimited file representing matrix of samples by metabolomic features
  -annotation ANNOTATION            Comma delimited file annotating subset of metabolite features
  -labels LABELS                    Comma delimited file for sample labels to associate clusters with
  -output OUTPUT                    Output directory
  -net_params NET_PARAMS            JSON file of network hyperparameters
  -background BACKGROUND            Directory with previously generated background
  -num_background NUM_BACKGROUND    Number of background CV Iterations
  -micro_norm MICRO_NORM            Microbiome normalization (RA, CLR, or None)
  -metab_norm METAB_NORM            Metabolome normalization (RA, CLR, or None)
  -threshold THRESHOLD              Define significant correlation threshold
  -num_run_cv NUM_RUN_CV            Number of iterations for cross-validation
  -num_cv NUM_CV                    Number of cross-validated folds
```
|Parameter|Description
|---|---|
|micro| CSV file of microbial count values|
|metab| CSV file of metabolite count values|
|external_micro| CSV file of microbial count values for external test set|
|external_metab| CSV file of metabolite count values for external test set|
|annotation| CSV file of metabolite annotations|
|lables| CSV file of sample labels used for module enrichment|
|output| Directory to store output of MiMeNet run|
|net_params| JSON file containing neural network number of layers, layer size, L2 penalty, and dropout rate|
|background| Directory with previously run background results|
|num_background| Integer for number of iterations of 10-fold cross-validation to run on shuffled data in order to generate empirical background (Recommend at least 10)|
|micro_norm| Transform the microbial features into relative abundance (RA) or center log-ratio (CLR). If the data is already transformed, apply 'None' to skip transformation.|
|micro_norm| Transform the metabolomic features into relative abundance (RA) or center log-ratio (CLR). If the data is already transformed, apply 'None' to skip transformation.|
|threshold| Set predefined correlation cutoff for determining well-predicted metabolites.|
|num_run_cv| Parameter to specify how many iterations of cross-validated evaluation to perform.|
|num_cv| Number of partitions to divide the data into during cross-validation (Recommend at least 5).|

### Example for provided dataset

```bash
python MiMeNet_train.py -micro data/IBD/microbiome_PRISM.csv -metab data/IBD/metabolome_PRISM.csv \
                        -external_micro data/IBD/microbiome_external.csv -external_metab data/IBD/metabolome_external.csv \
                        -micro_norm None -metab_norm CLR -net_params results/IBD/network_parameters.txt \
                        -annotation data/IBD/metabolome_annotation.csv -labels data/IBD/diagnosis_PRISM.csv \
                        -num_run_cv 10 -output IBD
```

The provided command will run MiMeNet on the IBD dataset and store results in the directory _results/output_dir_. 

### Version
1.0.0 (2020/07/28)

### Publication
TBA

### MiMeNet Workflow

#### Data Preprocessing
MiMeNet will perform a compositional transformation to relative abundance or centered log-ratio and filter low abundant microbial and metabolite features. 

#### Cross-Validated Evaluation
MiMeNet uses microbial features to predict metabolite output features. To do so, neural network hyper-parameters are first tuned. Then models are evaluated in a cross-validated fashion resulting in Spearman correlation coefficients (SCC) for each metabolite representing how well they could be predicted.

#### Identifying Well-Predicted Metabolties
MiMeNet generates a background of SCC values using a similar approach as in _Cross-Validated Evaluation_. However, to generate the background distribution of SCCs, the samples are randomly shuffled for each cross-validated iteration. MiMeNet will then take any metabolite with a SCC evaluation value above the 95th percentile to be well-predicted.

#### Constructing Microbe and Metabolite Modules
Using the set of models trained during the _Cross-Validated Evaluation_, MiMeNet constructs a microbe-metabolite interaction-score matrix. This interaction score matrix is biclustered into microbe and metabolite modules, grouping sets of microbes and metabolites with similar interaction patterns. These groupings may help illuminate the functions and structure of unannotated metabolites based on annotated members of the module.

### Contact
* Please contact Derek Reiman <dreima2@uic.edu> or Yand Dai <yangdai@uic.edu> for any questions or comments.

### License
Software provided to academic users under MIT License

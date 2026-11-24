# Results data: experimental evidence of generalization in quantum machine learning in the small-data regime

This deposit contains the numerical results (training logs and confusion matrices)
accompanying the manuscript:

  "Experimental evidence of generalization in quantum machine learning in
  small-data regime", Leena Anthony et al.

  This dataset (Zenodo): [10.5281/zenodo.22820877]

The study trains quantum convolutional neural networks (QCNNs) and parameter-matched
classical convolutional neural networks (CNNs) and measures how test performance grows
with the number of training samples, on two binary image-classification tasks.

This deposit holds the results only. The raw images are public and are not redistributed
here (see "Datasets" below); the training code is described in the paper.

## Datasets and tasks

1. Handwritten digits: scikit-learn `load_digits`, classes 0 vs 1. Images are 8x8,
   flattened to 64 values and L2-normalized. The QCNN amplitude-encodes them into 6 qubits.
   Positive class = digit 1; negative class = digit 0. About 100 test images per repetition.

2. BreastMNIST (breast ultrasound), from the MedMNIST collection, at native 28x28
   resolution, flattened to 784 values, zero-padded to 1024, and L2-normalized. The QCNN
   amplitude-encodes them into 10 qubits. Positive class = malignant; negative class =
   normal/benign. Evaluated on a fixed, class-balanced test set of 84 images
   (42 malignant + 42 benign) per repetition.

Both datasets load through their own libraries (`scikit-learn`, `medmnist`) and carry their
own licenses and citations; please cite them as those projects request.

## Models

- QCNN (digits): 45 trainable parameters, 6 qubits. Implemented in Qiskit as a dynamic
  circuit (mid-circuit measurement with classical feed-forward for pooling), trained under
  the FakeKyiv device-noise model with finite-difference gradients and the Adam optimizer,
  50 optimization steps per repetition.
- QCNN (BreastMNIST): 57 trainable parameters, 10 qubits. Implemented in PennyLane with
  statevector simulation and analytic gradients, trained with Adam and cross-entropy loss,
  100 optimization steps per repetition.
- CNN, parameter-matched (`CNN_tiny45` for digits, `CNN_tiny57` for BreastMNIST): a small
  classical CNN whose parameter count matches the QCNN (45 and 57 respectively).
- CNN, unconstrained (`CNN_25666`): a larger classical CNN (25,666 parameters), included as
  a high-capacity reference.

## Experimental design (common to all files)

- `n_train`: number of training samples. Digits: 2, 5, 10, 20, 40, 80. BreastMNIST: 20, 40,
  80, 160, 294.
- `rep`: repetition index (0-based). Digits: 10 repetitions (0 to 9). BreastMNIST: 20
  repetitions (0 to 19). Each repetition uses an independently drawn training set. A value
  `rep = -1` marks a row that is aggregated over all repetitions (used only in the digits
  QCNN confusion file).

### Metric definitions

- `test_acc`: test accuracy.
- `bal_acc`: balanced accuracy (mean of the per-class recalls). This is the primary metric
  for BreastMNIST, which is evaluated on the balanced test set.
- `f1`, `f1_malignant`: F1 score of the positive class (digit 1, or malignant).
- `auc`, `auc_malignant`: area under the ROC curve for the positive class.
- `train_cost`, `test_cost`: training and test loss (cross-entropy).
- `train_acc`: training accuracy.
- `step`: optimization step index within a repetition (QCNN training logs).
- `lr`: learning rate at that step (Adam schedule with decay).
- `best_epoch`: the epoch whose model was used for the reported metrics (classical CNN,
  best-epoch model selection).
- `rep_runtime_sec`, `total_runtime_sec`, `avg_rep_runtime_sec`, `std_rep_runtime_sec`:
  wall-clock runtimes.

### Confusion-matrix counts

TP, TN, FP, FN are counts of test predictions with respect to the positive class defined
above (digit 1 for digits, malignant for BreastMNIST). Important: the four confusion files
list these four quantities in different column orders, so read the header of each file.
The orderings are:

- Digits QCNN:        `TP, TN, FP, FN`   (pooled over the 10 repetitions)
- Digits CNN:         `TN, FP, FN, TP`   (per repetition)
- BreastMNIST CNN:    `malig_TP, malig_FN, benign_FP, benign_TN`   (per repetition)
- BreastMNIST QCNN:   `TP, FP, TN, FN`   (per repetition)

For BreastMNIST CNN the names are explicit: `malig_TP` = malignant correctly called
malignant, `malig_FN` = malignant missed, `benign_FP` = benign called malignant,
`benign_TN` = benign correctly called benign.

## Folder structure and files

```
Digits QCNN/
  complete_results_dynamic_circuit_corrected_Adam_denselayer12_digits_postdebug.csv
  confusion_matrix_fakekyiv_reps_aggregate_denselayer12_digits_postdebug.csv
Digits CNN/
  digits_classical_results_10reps.csv
  digits_classical_confusion_10reps.csv
BreastMNIST QCNN/
  qcnn_breastmnist_10q_results_ce_baltest_fullrun.csv
  qcnn_breastmnist_10q_confusion_matrices_ce_baltest_fullrun.csv
BreastMNIST CNN/
  classical_breastmnist_results_20reps_baltest.csv
  classical_breastmnist_confusion_20reps_baltest.csv
```

### Digits QCNN

`complete_results_...digits_postdebug.csv` (3000 rows): per-step QCNN training log.
Columns: `step, train_cost, train_acc, test_cost, test_acc, lr, rep_runtime_sec, n_train,
rep`. Rows = 6 training sizes x 10 repetitions x 50 steps.

`confusion_matrix_fakekyiv_reps_aggregate_...digits_postdebug.csv` (6 rows): one row per
training size, with the confusion counts pooled over the 10 repetitions (hence `rep = -1`).
Columns: `n_train, rep, TP, TN, FP, FN, total_runtime_sec, avg_rep_runtime_sec,
std_rep_runtime_sec`. The four counts sum to about 1000 per row (about 100 test images times
10 repetitions).

### Digits CNN (classical)

`digits_classical_results_10reps.csv` (120 rows): per-repetition test metrics for the two
CNNs. Columns: `model, n_train, rep, test_acc, bal_acc, f1, auc`. Rows = 2 models x 6
training sizes x 10 repetitions.

`digits_classical_confusion_10reps.csv` (120 rows): per-repetition confusion counts.
Columns: `model, n_train, rep, TN, FP, FN, TP`.

### BreastMNIST QCNN

`qcnn_breastmnist_10q_results_ce_baltest_fullrun.csv` (10000 rows): per-step QCNN training
log. Columns: `step, train_cost, train_acc, test_cost, test_acc, lr, rep_runtime_sec,
n_train, rep`. Rows = 5 training sizes x 20 repetitions x 100 steps.

`qcnn_breastmnist_10q_confusion_matrices_ce_baltest_fullrun.csv` (100 rows): per-repetition
confusion counts plus AUC. Columns: `n_train, rep, TP, FP, TN, FN, AUC`. The four counts sum
to 84 per row (the balanced test set).

### BreastMNIST CNN (classical)

`classical_breastmnist_results_20reps_baltest.csv` (200 rows): per-repetition test metrics
for the two CNNs. Columns: `model, n_train, rep, test_acc, bal_acc, f1_malignant,
auc_malignant, best_epoch`. Rows = 2 models x 5 training sizes x 20 repetitions.

`classical_breastmnist_confusion_20reps_baltest.csv` (200 rows): per-repetition confusion
counts. Columns: `model, n_train, rep, malig_TP, malig_FN, benign_FP, benign_TN`.

## License

Licensed under CC BY 4.0. Please cite the paper and this dataset if you use these results.

## Contact

Leena Anthony, FHNW / EPFL. Correspondence via the contact address given in the paper.

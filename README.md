# GARN3 User Manual

GARN3 is a Java application and it requires Java version 17 or higher.

GARN3 uses Fasta file for primary/secondary structures. Example:

```txt
>1E8O
gGGCCGGGCGCGGUGGCGCGCGCCUGUAGUCCCAGCUACUCGGGAGGCUC
((((((((((((......)))))))....((((........)))))))))
```

## Convert PDB file to GARN model

To create a file containing a GARN model from a PDB file:
`java -jar ./GARN3.jar PDBTOGARN ‹Name of molecule› ‹Fasta File› ‹Location of the PDB file›`

Example:

```shell
java -jar ./GARN3.jar PDBTOGARN 1E8O 1E8O_fasta.txt 1E8O.pdb
```

Output two files : ‹Name of molecule›\_GARN.csv with the position of nodes in 3D space, and ‹Name of molecule›\_listNucleo.csv, to indicate the correspondance between nodes and nucleotides.

## Predict molecules (run simulations)

To run the simualations and compute the sampling space:
`java -jar ./GARN3.jar GAME ‹Name of molecule› ‹Fasta File› ‹Number of solutions›`

Example:

```shell
java -jar ./GARN3.jar GAME 1E8O 1E8O_fasta.txt 10
```

Output 10 files : GARN*‹Name of molecule›\_0.csv, GARN*‹Name of molecule›_1.csv, ..., GARN_‹Name of molecule›\_9.csv

If you wish to use an specific scoring algorithm for the molecule, please set the option EXP3 or UCB like this:

```shell
java -jar ./GARN3.jar GAME 1E8O 1E8O_fasta.txt 10 UCB
```

If you wish to compute the sampling space and also compute RMSD, you need to specify the GARN model file:

```shell
java -jar ./GARN3.jar GAME 1E8O 1E8O_fasta.txt 10 0 1E8O_GARN.csv
```

> Please note that the "0" following the number of solutions means that we don't want to specify the scoring algorithm (it will be automatically chosen by the GARN algorithm).
> In this argument, ANY value different from "EXP3" and "UCB" will be ignored.

Output 11 files : GARN*‹Name of molecule›\_0.csv, .. , & GARN*‹Name of molecule›\_9.csv, ... and a ‹Name of molecule›\_out.csv with the RMSD and the Maximum distance (to sort the sampling).

## Calculate RMSD between predicted molecules

To compute the RMSD between GARN files (compare to one file): `java -jar ./GARN3.jar GAME ‹Name of molecule› ‹Fasta File› ‹Number of solutions› ‹GARN file of native structure›`

Example:

```shell
java -jar ./GARN3.jar RMSD 1E8O 1E8O_GARN.csv GARN_1E8O_1.csv GARN_1E8O_2.csv GARN_1E8O_3.csv GARN_1E8O_4.csv
```

Output 1 file: ‹Name of molecule›\_rmsd.csv with the RMSD between the first file and others files.

## Calculate minimum and maximum distance

To compute the maximum distance in structure and sort the sampling:
`java -jar ./GARN.jar SORT ‹Name of molecule› ‹GARN file of molecule 1› ‹GARN file of molecule 2› ...`

Example:

```shell
java -jar ./GARN3.jar SORT GARN_1E8O_1.csv GARN_1E8O_2.csv GARN_1E8O_3.csv GARN_1E8O_4.csv
```

Output 1 file: ‹‹Name of molecule››\_sort.csv with the maximum distance for each solution.

When in doubt, you can contact jhonatansilva@usp.br or daniel.cordeiro@usp.br

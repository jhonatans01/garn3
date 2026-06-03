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

To run the simulations and compute the sampling space:
`java -jar ./GARN3.jar GAME ‹Name of molecule› ‹Fasta File› ‹Number of solutions› [‹nbTour›] [EXP3|UCB] [‹GARN file of native structure›]`

Notes:
- `nbTour` (optional): numeric override for the number of rounds per game. If omitted the program uses a default of 500 rounds (for large RNAs with >=100 players the default becomes 300). The value is parsed as a number (double) so integers like `500` or `750` are fine.
- Argument order: `numberTest` then optional `nbTour`, then optional algorithm (`EXP3` or `UCB`), then optional reference GARN file for RMSD.

Simple example (no override):

```shell
java -jar ./GARN3.jar GAME 1E8O 1E8O_fasta.txt 10
```

Example overriding the number of rounds per game:

```shell
java -jar ./GARN3.jar GAME 1E8O 1E8O_fasta.txt 10 750
```

Example specifying algorithm and reference file (with nbTour omitted):

```shell
java -jar ./GARN3.jar GAME 1E8O 1E8O_fasta.txt 10 UCB 1E8O_GARN.csv
```

Example specifying both nbTour and algorithm and reference file:

```shell
java -jar ./GARN3.jar GAME 1E8O 1E8O_fasta.txt 10 750 EXP3 1E8O_GARN.csv
```

Output 10 files : GARN*‹Name of molecule›_0.csv, GARN*‹Name of molecule›_1.csv, ..., GARN_‹Name of molecule›_9.csv

If you wish to compute the sampling space and also compute RMSD, you need to provide a reference GARN model file as the final argument (see examples above). Use `0` in the algorithm position to let the program auto-select the algorithm, e.g.:

```shell
java -jar ./GARN3.jar GAME 1E8O 1E8O_fasta.txt 10 0 1E8O_GARN.csv
```

Output 11 files : GARN*‹Name of molecule›_0.csv, .. , & GARN*‹Name of molecule›_9.csv, ... and a ‹Name of molecule›_out.csv with the RMSD and the Maximum distance (to sort the sampling).

## Calculate RMSD between predicted molecules

To compute the RMSD between GARN files (compare to one file): `java -jar ./GARN3.jar RMSD ‹Name of molecule› ‹Reference GARN file› ‹GARN file 1› [GARN file 2 ...]`

Example:

```shell
java -jar ./GARN3.jar RMSD 1E8O 1E8O_GARN.csv GARN_1E8O_1.csv GARN_1E8O_2.csv GARN_1E8O_3.csv GARN_1E8O_4.csv
```

Output 1 file: ‹Name of molecule›_rmsd.csv with the RMSD between the first file and others files.

## Calculate TM-Score between predicted molecules

To compute the TM-Score between GARN files (compare to one file): `java -jar ./GARN3.jar TMSCORE ‹Name of molecule› ‹GARN file of molecule 1› ‹GARN file of molecule 2› ...`

Example:

```shell
java -jar ./GARN3.jar TMSCORE 1E8O 1E8O_GARN.csv GARN_1E8O_1.csv GARN_1E8O_2.csv GARN_1E8O_3.csv GARN_1E8O_4.csv
```

Output 1 file: ‹Name of molecule›\_tmscore.csv with the RMSD between the first file and others files.

## Calculate minimum and maximum distance

To compute the maximum distance in structure and sort the sampling:
`java -jar ./GARN3.jar SORT ‹Name of molecule› ‹GARN file of molecule 1› ‹GARN file of molecule 2› ...`

Example:

```shell
java -jar ./GARN3.jar SORT 1E8O GARN_1E8O_1.csv GARN_1E8O_2.csv GARN_1E8O_3.csv GARN_1E8O_4.csv
```

Output 1 file: ‹‹Name of molecule››\_sort.csv with the maximum distance for each solution.

## Generate PDB/align/PyMOL outputs from GARN CSVs

To reproduce the GARNTOPDB flow:

`java -jar ./GARN3.jar GARNTOPDB [‹Full PDB file› ‹listNucleo.csv file›] ‹game1.csv› [game2.csv ...]`

Modes:

1. With PDB reference (using some full-atom experimental structure):

```shell
java -jar ./GARN3.jar GARNTOPDB 1E8O.pdb 1E8O_listNucleo.csv GARN_1E8O_0.csv GARN_1E8O_1.csv
```

2. Without PDB reference (first game file is used as reference):

```shell
java -jar ./GARN3.jar GARNTOPDB 1E8O_GARN.csv GARN_1E8O_0.csv GARN_1E8O_1.csv
```

Example:

```shell
java -jar ./GARN3.jar GARNTOPDB 1E8O.pdb 1E8O_listNucleo.csv 1E8O_GARN.csv GARN_1E8O_0.csv
```

Generated files:

1. `GARN_*.pdb`: PDB files generated from game CSVs.
2. `<reference>.align`: RMS alignment output for each generated structure against the selected reference.
3. If full reference mode is used:
   `*_CG.pdb`: reduced coarse-grained reference structure.
4. If full reference mode and PyMOL are available:
   `*_CG.pse`: optional PyMOL session file.

## Contacts

When in doubt, you can contact jhonatansilva@usp.br or daniel.cordeiro@usp.br

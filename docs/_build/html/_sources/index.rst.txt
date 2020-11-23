TEST

.. NiPTUNE documentation master file, created by
   sphinx-quickstart on Fri Nov 13 12:41:21 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to NiPTUNE's documentation!
===================================

NiPTUNE is a python library giving tools to analyse NIPT data.
This library has been developed in python3.

.. toctree::
   :maxdepth: 2
   :caption: Contents:

Get started
===================================

Each module can be used as a python stand alone.




Installation
===================================

 ::

   pip install NiPTUNE




Dependencies
===================================

* python 3.7:

   •	matplotlib 3.3.2
   •	numpy 1.19.2
   •	pandas 1.1.3
   •	pysam 0.16.0.1
   •	statsmodels 0.12.0
   •	scikit-learn 0.23.2

* R 3.5.2:

   •	ggfortify 0.4.11
   •	ggplot2 3.3.2
   •	DNAcopy 1.62.0
   •	Rsamtools 2.4.0



Configuration File
===================================

First you need to set the configuration file and to create a new directory where you will store all your results.

 By example::

   mkdir niptune_results

Then you have to set this directory path in your file `niptune.conf` in the section `[folders][home]`

In addition to this directory the user has to set 2 others directories that will be used to store reference data and thus in routine mode.
These directories are defined in section::

[proteus][reference_bins]
[halimede][gender_prediction]

For the other sections, the niptune.conf file is already filled, we don't recommend to modify the parameters (except for the one cited above).

**Set path for NiPTUNE output**

Becareful that for every new analysis the user has to modify the parameter [folders][home] in order to not overwrite results already existing, but not the reference folders (cited above).



**Subdirectories of NiPTUNE output:**

* output_tables::

   output_dir/tables
 here you will find the outputs of despina.py, proteus.py, halimede.py, laomedeida.py, neso.py, nereid.py, sao.py that have a tables format

* output_triton::

   output_dir/converted_files
  here you will find the converted files (.gcc, .pickle, .npz) from the module triton.py

* output_proteus::

   output_dir/PCA_plots
  here you will find the PCA plots from the module proteus.py

* output_proteus_bin::

   output_dir/PCA_bins
  here you will find the distribution of reads count gc normalized by bin used as input fot PCA for the module proteus.py

* output_halimede::

   output_dir/gender_prediction
  here you will find the threshold estimated by the gaussian mixture model from the module halimede.py to separate female/male populations.

* output_halimede_Yfrac::

   output_dir/gender_prediction_MagicYcounts
  here you will find the estimated MagicY counts for each sample.

* output_larissa::

   output_dir/reference
  here you will find the list of samples selected by larissa.py to be used as reference for defrag and WisecondorX

* output_larissa_wisecondorX::

   output_dir/reference/npz
  here you will find the npz files (symbolic link to the folder output_triton) correspondent to the samples selected by larissa.py to be used as reference for WisecondorX

* output_larissa_defrag::

   output_dir/reference/boydir & output_dir/reference/girldir
  here you will find the gcc and pickle files (symbolic link to the folder output_triton) correspondent to the samples selected by larissa.py to be used as reference for Defrag

* output_nereid::

   output_dir/read_len_dist
  here you will find the read length distribution for each sample

* output_sao_data::

   output_dir/copy_number_alteration_data
  here you will find the bin visualization plots and the additional files created by WisecondorX  through sao.py.


How to use NiPTUNE
===================================

First step consist in setting your configuration file.
You need to create a new directory where you will store all your results.
Then you have to set this directory path in your file `niptune.conf` in the section `[folders][home]`

Once it's done go in the directory where you created this file.
Then from here you can launch every NiPTUNE stand alone.

You can use NiPTUNE in 2 different ways : reference mode and routine mode.

If it's the first time you are using NiPTUNE you need to create references for several tools:
 - proteus
 - halimede
 - laomedeida
 - sao

For the others tools there is no need of reference.


Usage example:

You need to create a folder in which you will place

* Triton::

   triton list_of_files.txt

* Despina::

   despina list_of_files.txt

* Proteus:
To create a reference::

   proteus list_of_prefix.txt

In routine::

   proteus -r list_of_prefix.txt

* Halimede:
To create a threshold of reference::

   halimede -s list_of_files.txt

In routine ::

   halimede list_of_files.txt


* Larissa::

   larissa list_of_files.txt

* Neso::

   neso list_of_files.txt

* Laomedeida::

   laomedeida list_of_prefix.txt

* Sao::

   sao list_of_prefix.txt

* Nereid::

   nereid list_of_files.txt

* Thalassa::

   thalassa



Triton
==================
A package allowing to convert file from .bam format to other formats (.gcc, .pickle, .npz) which serve as input for downstream processing in NiPTUNE.
.gcc, .pickle and .npz are binned files.

Input: A list in .txt format containing list of samples in bam format. Indicate the absolute path of files.

For example see ``tests/data/list_bam_files.txt``

Output: Files are stored in the directory ``converted_files``

Arguments::

 -p, --binSizePickle, default=1Mb, to set the size of bins for gcc and pickle files
 -n, --binSizeNpz, default=5Kb, to set the size of bins for npz files

Despina
==================
This module calculates the sequencing depth (sd) of the sample(s) provided.

Input : A list in .txt format containing list of samples in bam format. Indicate the absolute path of files.

For example see ``tests/data/list_bam_files.txt``

Output: one table with sample names and associated sd. Results are stored in the directory ``tables``

Arguments: none


Proteus
==================
Principal component analysis. This module performs a principal component analysis on all the samples provided and produce a visual output to check whether there are outlier samples or subpopulations into the cohort.

Input: list of samples, only prefix file name (no path), previously converted in gcc format.

For example see ``tests/data/list_prefix.txt``

Output: one table with sample names and associated PCA plot. Results are stored in the directory ``tables`. All PCA plots are stored in the directory ``PCA_plots``. The distributions of reads count GC normalized by bin used as input for PCA for are stored in the directory ``PCA_bins``.

Arguments:::

 -r, --routine, default = False, to choose whether you need to use PCA in routine. By default this option is set to False and compute a PCA of reference with all the cohort data.

Halimede
==================
Fetal gender prediction with MagicY.

This module implements the MagicY method for fetal gender prediction. It is based on the quantification of reads in seven region on chrY and a gaussian mixture model to separate female and male population.

Input : A list in .txt format containing list of samples in bam format. Indicate the absolute path of files.

Output: one table with sample names and associated gender predicted (M/F). Results are stored in the directory ``tables``. In its first application, MagicY needs to estimate a threshold to separate female and male population using a **gaussian mixture model**. This threshold is stored in the directory ``gender_prediction``. In routine NIPT, you do not need to re-estimate this threshold, thus the only output will be the table stored in the directory ``tables`. The estimated MagicY counts for each sample are stored in the directory ``gender_prediction_MagicYcounts``.

Arguments::

 -s, --setThreshold, default = False, to choose whether you need to calculate the threshold or not.
 -t, --thresholdValue, default = automatically uses the threshold estimated when the option s=True, to set a different threshold compared to the one estimated by the gaussian mixture model (in general you do not need to set this value, we suggest to use this option only for testing purposes).


Larissa
==================
References creation for Defrag and WisecondorX. This module randomly selects n samples to be used as reference for Defrag and WisecondorX while keeping the gender ratio stable.

Input: list of samples, only prefix file name (no path), previously converted in gcc, pickle, npz formats and with gender predicted by MagicY

Output: list of samples selected as reference (stored in the directory reference), correspondent npz converted files (stored in the directory reference/npz), correspondent gcc and pickle converted files (stored in the directories reference/boydir and reference/girldir accordingly to sample gender).

Arguments::

 -n, --numberSamples, default=100, to set the number of samples to create reference.
 -b, --binSizeNpz, default=1Kb, to set the size of bins for npz conversion
 -c, --cpus, default=1, to set the number of threads to use


Laomedeida
==================
ff estimation with Defrag a.

The main code of Defrag is maintained.

Input: list of samples, only prefix file name (no path); reference will be automatically found in reference/boydir and reference/girldir as determined by larissa.py.

Output: one table with sample names and associated ff estimated by Defrag a method. Results are stored in the ``directory tables``.

Arguments::

 -g, --GCcorrection, default=True, to set whether to apply GC correction before Defrag calculation.


Neso
==================

ff estimation with Seqff

Input: list of samples, only prefix file name (no path)

Output: one table with sample names and associated ff estimated by Seqff method. Results are stored in the ``directory tables``.

Arguments: none


Nereid
==================

Epsilon value estimation

Input : A list in .txt format containing list of samples in bam format. Indicate the absolute path of files.

Output: one table with sample names and associated e values for chr18 and chr21 each for ff estimated by Defrag a or Seqff method. Results are stored in the directory ``tables``. Read length distribution are stored in directory ``read_len_dist``.

Arguments::

 -r18, --fetalRange_chr18, default=“121:165”, to set the range of reads length to be considered as most likely fetal for chr18
 -r21, --fetalRange_chr21, default==“109:164”, to set the range of reads length to be considered as most likely fetal for chr18


Sao
==================

Copy number alteration prediction with WisecondorX.

Input list of samples, only prefix file name (no path). Reference files will be automatically found in ``reference/npz`` as determined by ``larissa.py``.

Output: one table with sample names and associated Z-score for all chromosomes estimated by WisecondorX method. Results are stored in the directory ``tables``. Bin visualization and additional outputs by WisecondorX are stored in the directory `copy_number_alteration_data``.

Arguments: none


Thalassa
==================
Output preparation.

It collects results from upstream modules in a table.

Input: Automatically finds all tables stored in the directory ``tables``.

Output: A table in which each sample (line) is described by the following columns: quality control (visual output), sd, gender prediction (magicY), ff (2 columns one for each method), e value for chr18 (2 columns one for each ff method), e value for chr21 (2 columns one for each ff method),  and the Z-score for chromosome 13, 18 and 21.

Arguments: none



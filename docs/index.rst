.. NiPTUNE documentation master file, created by
   sphinx-quickstart on Fri Nov 13 12:41:21 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to NiPTUNE's documentation!
===================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

Triton
==================
A package allowing to convert file from .bam format to other formats (.gcc, .pickle, .npz) which serve as input for downstream processing in NiPTUNE.
.gcc, .pickle and .npz are binned files.

Input: A list in .txt format containing list of samples in bam format. Indicate the absolute path of files.

For example see ``tests/data/list_bam_files.txt``

Output: Files are stored in the directory ``converted_files``

Arguments :

* -p, --binSizePickle, default=1Mb, to set the size of bins for gcc and pickle files
* -n, --binSizeNpz, default=5Kb, to set the size of bins for npz files

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

Arguments:

* -r, --routine, default = False, to choose whether you need to use PCA in routine. By default this option is set to False and compute a PCA of reference with all the cohort data.

Halimede
==================
Fetal gender prediction with MagicY.

This module implements the MagicY method for fetal gender prediction. It is based on the quantification of reads in seven region on chrY and a gaussian mixture model to separate female and male population.

Input : A list in .txt format containing list of samples in bam format. Indicate the absolute path of files.

Output: one table with sample names and associated gender predicted (M/F). Results are stored in the directory ``tables``. In its first application, MagicY needs to estimate a threshold to separate female and male population using a **gaussian mixture model**. This threshold is stored in the directory ``gender_prediction``. In routine NIPT, you do not need to re-estimate this threshold, thus the only output will be the table stored in the directory ``tables``. The estimated MagicY counts for each sample are stored in the directory ``gender_prediction_MagicYcounts``.

Arguments:

* -s, --setThreshold, default = False, to choose whether you need to calculate the threshold or not.
* -t, --thresholdValue, default = automatically uses the threshold estimated when the option s=True, to set a different threshold compared to the one estimated by the gaussian mixture model (in general you do not need to set this value, we suggest to use this option only for testing purposes).


Larissa
==================
References creation for Defrag and WisecondorX. This module randomly selects n samples to be used as reference for Defrag and WisecondorX while keeping the gender ratio stable.

Input: list of samples, only prefix file name (no path), previously converted in gcc, pickle, npz formats and with gender predicted by MagicY

Output: list of samples selected as reference (stored in the directory reference), correspondent npz converted files (stored in the directory reference/npz), correspondent gcc and pickle converted files (stored in the directories reference/boydir and reference/girldir accordingly to sample gender).

Arguments:

* -n, --numberSamples, default=100, to set the number of samples to create reference.
* -b, --binSizeNpz, default=1Kb, to set the size of bins for npz conversion
* -c, --cpus, default=1, to set the number of threads to use


Laomedeida
==================
ff estimation with Defrag a.

The main code of Defrag is maintained.

Input: list of samples, only prefix file name (no path); reference will be automatically found in reference/boydir and reference/girldir as determined by larissa.py.

Output: one table with sample names and associated ff estimated by Defrag a method. Results are stored in the ``directory tables``.

Arguments:

* -g, --GCcorrection, default=True, to set whether to apply GC correction before Defrag calculation.


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

Arguments:

* -r18, --fetalRange_chr18, default=“121:165”, to set the range of reads length to be considered as most likely fetal for chr18
* -r21, --fetalRange_chr21, default==“109:164”, to set the range of reads length to be considered as most likely fetal for chr18


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

.

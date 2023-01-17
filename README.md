## READ ME 


#### BacterialCore: tool for detection of PCGs from compositional data

First published as "Detection of phylogenetic core groups in diverse microbial ecosystems" Parras-Moltó & Aguirre de Cárcer, 2019

This document describes the use of BacterialCore.py. The procedure consists on the estimation of "core" OTUs from 16S amplicon reads clusters and nodes in a phylogenetic tree.

### Installation
The installation of Python2.7 (https://www.python.org/downloads/), Qiime (http://qiime.org/) and Prinseq (http://prinseq.sourceforge.net/) is required.

### Input
The files needed are the 16S sequences to be analyzed in multifasta format and the 16S phylogenetic tree and aligned reference sequences (here 97_otus_nodes.tree/99_otus_nodes.tree and 97_otus.fasta/99_otus.fasta from Greengenes gg_13_5 https://greengenes.secondgenome.com/?prefix=downloads/greengenes_database/gg_13_5/). These files should be in the same folder where the script is executed.

The datasets should be rarefied to the minimun read depth in the dataset, after removing low quality reads.

The tree file and aligned reference sequences file need to be the same version as the one loaded by pick_outs.py

The node names of the trees should be modified, and a genuine name should be given for all. This could be done for example using R with the function "makeNodeLabel" from "ape" package. 

### Notes
Three additional R scripts are needed to

1. Calculate the statistics for the OTUs picking part (OTUs_statistics.R)
2. Calculate a Venn diagram between the reads belonging to core OTUs/nodes from both methods (Venn_OTUS_Tree.R)
3. calculate intra-node average 16S distances (Distances_16S.pl).

Reported nodes, leaf information (names, average distance,...) relate only to leaves detected during analysis for that node, not to all leaves in that node in the original tree.

### Parameters

- Input file; -f file
- Output file; -o
- Process; -p (default: 0 ;possible values 1-4) #There are 4 different processes that can be executed:
	1. Complete pipeline; Detection of non-overlapping OTUs present in 100% of the samples, followed by the detection of nodes in the phylogenetic tree present in 100% of the samples. Finally this script will perform a Venn diagram to look for the shared core reads that exists between the OTUs and Tree approaches.
	2. Only detection of non-overlapping OTUs present in 100% of the samples. 
	3. Only detection of nodes present in 100% of the samples. 
	4. Only venn diagram to look for the shared core reads that exists between the OTUs and Tree approaches previously generated.

- Initial clustering threshold with pick_otus; -initial_level #This is the first clustering threshold that will be performed by pick_otus (default 0.97).

- Last clustering threshold with pick_otus; -final_level #This is the last clustering threshold that will be evaluated.

- Minimum percentage of samples to declare a core; -min_core_percentage #This value can be modified so that a core is not needed to be present in all the samples (default 1, meaning presence in all samples).

- Minimum relative abundance needed; -cutoff #Minimum percentage to consider a node to be 'present' in one sample. Cutoff of 0.01 means only consider nodes with total abundance of at least 1%. If min_core_percentage is 0.9, that means our output will consist in a list of nodes present with an abundance reaching at least 1% in at least 90% of the given samples (Tree approach, process==3; default 0).

- Tree clustering level;-tree_level #Reference tree to be used for pick_otus analysis in the tree study (default 97). 

- Tree type analysis; -tree_type_analysis #Core detection can be evaluated using three different methods and a last option to only see the abundance of each leaf and node (default 1; possible values 1-4). 
	1. All samples must have at least one read mapping to the leaf or node.
	2. Binomial analysis ('binomial_value' and 'p_value' are both set to 0.05 by default, but can be modified within the script)
	3. Custom percentage. 'percentage' can modify the custom percentage needed to considered a leaf or node as core (to be modified within the script. deafualt is 0.9).
	4. Show tree info, structure and abundances.
		
- Finally, the parameter 'taxo_p' can be modified within the script to establish the minimun support to define the consensus taxonomy of that node (default 0.80).

### Output files

The possible output files depending on the parameters used are:

- OTUs (folder):
	- Statistics.txt: Total number, pooled mean abundance, standard deviation, minimun and maximun of core groups for all clustering thresholds. 
	- General.txt: Number of core OTUs per clustering thresholds.
	- filt.txt: Reads belonging to core OTUs.
	- Distr.txt: Frequency mean, Frequency standard deviation, Frequency CV, ecdf-mean and ecdf-CV for each core at each clustering level. (ecdf: rank of the value when compared to all OTUs at the same clustering thresholds)
	- Core_Otus_freq.txt: Sample abundance table for each core OTU at each thresholds.
	- control_random_table_cores: Number of core OTUs obtained at each thresholds after randomizing the OTU abundance table in each thresholds 1000 times.
	
- Tree (folder):
	- abundances.txt: Sample abundance table for each core node/leaf.
	- results.txt: Core name, absolute abundances (vector), relative abundances (vector), min relative abundance, max relative abundance, average relative abundance, SD relative abundance, consensuns taxonomy, number of leaves. 
	- results_stats.txt: Number of leaves, mean 16S distance between leaves, estandard deviation of 16S distances between leaves, and max 16S distance between leaves, for each node and leaf core.

- Venn diagram in .png format

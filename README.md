![logo](/img/logo.png)

# racoonFlyFish
racoonFlyFish: Automated multi-round genome assembly polishing with long reads using combinations of RaCon, Flye, and Medaka.


# Version = 0.0.1
- 4 pipelines can be controlled by the main racoonFlyFish program.
	- Any number of RaCon polishing rounds (default = 4).
	- Any number of Flye polishing rounds (default = 4).
	- Any number of Medaka polishing rounds (default = 1).
	- The chimera pipeline for "duo" and "trio" polishing.
		- "duo" runs RaCon polishing followed by Flye polishing.
		- "trio" additionally runs Medaka polishing (only recommended for nanopore data).
		- At the moment, the pipeline runs the entire "trio" (TODO: allow stopping after "duo").
		- By default, only one round of each polisher is run.
		- At the moment, the ordering is always: all RaCon rounds, all Flye rounds, all Medaka rounds.


# Usage


	racoonFlyFish : Automated multi-round genome assembly polishing with long reads using combinations of RaCon, Flye, and Medaka.

	Usage: 
		racoonFlyFish subcommand options

		Each subcommand activates a different pipeline (RaCon, Flye, Medaka, or all 3).
		Each pipeline has 2-3 subcommand keywords to activate it: 
			racoon or racon, fly or flye, fish or medaka, chimera or trio for all 3, or duo for the first 2. 

	RaCon Polishing :
		racoonFlyFish [racoon|racon] [options]

	Flye Polishing :
		racoonFlyFish [fly|flye] [options]

	Medaka Polishing :
		racoonFlyFish [fish|medaka] [options]

	Dup/Trio/Chimera Polishing :
		racoonFlyFish [chimera|trio|duo] [options]


	Common Options to all pipelines:
	

        -i      FOFN	: Required.
        -R      READS	: Required.
	-t	THREADS	: Default = 16.
	-r	ROUNDS	: Defaults = When run separately, 4 for RaCon, 4 for Flye, 1 for Medaka. In chimera-mode, defaults to 1 round of each polisher.
	-S	SLURM	: Use to launch SLURM batch scripts (one per assembly in FOFN) instead of processing serially in current env. Default = false.
	-T	TIME	: Time limit for SLURM. Default = 12:00:00 .
	-M	MEM	: Mem limit for SLURM. Default = 80G .
	-v	VERBOSE	: Default = false.


	RaCon Pipeline Options:
	-x	X 	: -x option for minimap2. Mapping presets. Examples: map-ont, map-pb .
	-H	MOREHELP: Show version and help message from RaCon program. Can also do: racon --version and racon --help.


        Flye Pipeline Options:
	-G	GSIZE	: Size of haploid genome (can use diploid size if assemblies closer to that). Shorthand works. E.g. 400m for 400 Mb genome.
	-d	READTYPE: Read type used by Flye. Use -H to bring up flye help message. 
			  Options here are: nano-raw, nano-corr, nano-hq, pacbio-raw, pacbio-corr, pacbio-hifi.
	-H	FLYEHELP: Show version and help message from Flye program. Can also do: flye --version and flye --help.


	Medaka Pipeline Options:
	-m	MODEL 	: -m option for medaka_consensus. Specify nanopore model. Use -H for more info. Example MinION models:  
			- r103_min_high_g345
			- r103_min_high_g360
			- r10_min_high_g303
			- r10_min_high_g340
			- r941_min_fast_g303
			- r941_min_fast_g507
			- r941_min_hac_g507
			- r941_min_high_g303
			- r941_min_high_g330
			- r941_min_high_g340_rle
			- r941_min_high_g344
			- r941_min_high_g351
			- r941_min_high_g360
			- r941_min_sup_g507
	-H	MOREHELP: Show version and help message from medaka_consensus. Can also do: medaka --version and medaka_consensus -h.



	Chimera Pipeline Options:

	Number of polishing rounds for each polisher:
        -r      ROUNDS  : Number of polishing rounds for RaCon. Default = 1. 
        -f      ROUNDS  : Number of polishing rounds for Flye. Default = 1. 
        -k      ROUNDS  : Number of polishing rounds for Medaka. Default = 1. 

		Racon-specific options:
		-x	X 	: -x option for minimap2. Mapping presets. Examples: map-ont, map-pb .

		Flye-specific options:
		-G	GSIZE	: Size of haploid genome (can use diploid size if assemblies closer to that). Shorthand works. E.g. 400m for 400 Mb genome.
		-d	READTYPE: Read type used by Flye. Use -H to bring up flye help message. 
			  Options here are: nano-raw, nano-corr, nano-hq, pacbio-raw, pacbio-corr, pacbio-hifi.

		Medaka-specific options:
		-m	MODEL 	: -m option for medaka_consensus. Specify nanopore model. Use -H for more info. Example MinION models:  
				- r103_min_high_g345
				- r103_min_high_g360
				- r10_min_high_g303
				- r10_min_high_g340
				- r941_min_fast_g303
				- r941_min_fast_g507
				- r941_min_hac_g507
				- r941_min_high_g303
				- r941_min_high_g330
				- r941_min_high_g340_rle
				- r941_min_high_g344
				- r941_min_high_g351
				- r941_min_high_g360
				- r941_min_sup_g507



	SLURM Advice (assuming 400-800Mb genome with 5-10X read coverage, 16 threads):
		- Ranking the pipelines by speed:
			- RaCon (a couple of hours for 4 iterations). An 8 hour limit is very safe.
			- Flye (a little bit longer than RaCon). An 8 hour limit is very safe.
			- Medaka 8-12 hours (longest by 2-4 fold). Am 8 hour limit will have mixed results, a 12 hour limit is safe.
			- The trio-chimera pipeline with 1 round each (RaCon->Flye->Medaka) will need 12 hours to be safe as well.
		- Ranking pipelines by memory needed:
			- RaCon uses the least memory. A limit of 24G is a safe bet.
			- Flye and Medaka use much more memory. A limit of something in the range of 50-80G may be necessary.
			- The duo- and trio-chimera pipelines with 1 round each (RaCon->Flye or RaCon->Flye->Medaka) will need 50-80G as well.

	SLURM Advice (assuming 400-800Mb genome with 15-30X read coverage, 16 threads):
		- Ranking the pipelines by speed:
			- RaCon (a couple of hours for 4 iterations). An 8 hour limit is very safe.
			- Flye (longer and more resources than RaCon). An 12-24 hour limit may be warranted.
			- Medaka 12-24 hours is warranted.
			- The trio-chimera pipeline with 1 round each (RaCon->Flye->Medaka) will need 12 hours to be safe as well.
		- Ranking pipelines by memory needed:
			- RaCon uses the least memory. A limit of 24G is still a safe bet.
			- Flye and Medaka use much more memory. A limit of something in the range of 100-120G may be necessary.
			- The duo- and trio-chimera pipelines with 1 round each (RaCon->Flye or RaCon->Flye->Medaka) will need 100-120G as well.






# Dependencies
- Minimap2	(all three pipelines depend on Minimap2)
- Flye		(if aiming for Flye polishing or a combination using Flye)
- RaCon 	(if aiming for RaCon polishing or a combination using RaCon)
- Medaka	(if aiming for Medaka polishing or a combination using Medaka)
- Samtools	(note - this is installed when installing medaka with conda)
- Tabix		(note - this is installed when installing medaka with conda)
- Bgzip		(note - this is installed when installing medaka with conda)
- Python3
- biopython package in python3

# Install everything with Conda or Mamba:
	conda create -n racoonFlyFishDeps -c conda-forge -c bioconda medaka racon minimap2 flye samtools tabix

# Mamba is recommended as it is much faster than Conda at doing installations:
	conda install -y -c conda-forge mamba

	mamba create -n racoonFlyFishDeps -c conda-forge -c bioconda medaka racon minimap2 flye samtools tabix

# Install dependencies individually and manually:
- Minimap2
	git clone https://github.com/lh3/minimap2
	cd minimap2 && make

- Flye
	git clone https://github.com/fenderglass/Flye
	cd Flye && make

- RaCon
	git clone --recursive https://github.com/lbcb-sci/racon.git racon
	cd racon
	mkdir build
	cd build
	cmake -DCMAKE_BUILD_TYPE=Release ..
	make
- Medaka
	conda create -n medaka -c conda-forge -c bioconda medaka
	or
	pip install medaka

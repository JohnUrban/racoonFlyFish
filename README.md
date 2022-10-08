![logo](/img/logo.png)

# racoonFlyFish
racoonFlyFish: Automated multi-round genome assembly polishing with long reads using combinations of RaCon, Flye, and Medaka.


# Version = 0
#	Not yet active or working.
#	Individual pipelines for slurm can be used separately though.

# Usage

	Usage




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

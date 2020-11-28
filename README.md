# comparative-genomics

Кудрявцева Полина Сергеевна	
Assemblies:
ASM83482v1	ASM83484v1	ASM118871v1	ASM118873v1	ASM379820v1	ASM379822v1	ASM1519065v1	ASM2284v1	ASM906v1	ASM929594v1

Organism: Yersinia pestis (enterobacteria)

#### 0.filter out the plasmids
Скачаем все геномы из базы ncbi и удалим из них плазмиды с помощью команды sed
```
sed -n '/plasmid.*/q;p' GCF_000834825.1_ASM83482v1_genomic.fna > ASM83482v1.fasta
sed -n '/plasmid.*/q;p' GCF_000834845.1_ASM83484v1_genomic.fna > ASM83484v1.fasta
sed -n '/plasmid.*/q;p' GCF_001188715.1_ASM118871v1_genomic.fna > ASM118871v1.fasta
sed -n '/plasmid.*/q;p' GCF_001188735.1_ASM118873v1_genomic.fna > ASM118873v1.fasta
sed -n '/plasmid.*/q;p' GCF_003798205.1_ASM379820v1_genomic.fna > ASM379820v1.fasta
sed -n '/plasmid.*/q;p' GCF_003798225.1_ASM379822v1_genomic.fna > ASM379822v1.fasta
sed -n '/plasmid.*/q;p' GCA_015190655.1_ASM1519065v1_genomic.fna > ASM1519065v1.fasta 
sed -n '/plasmid.*/q;p' GCF_000022845.1_ASM2284v1_genomic.fna > ASM2284v1.fasta
sed -n '/plasmid.*/q;p' GCF_000009065.1_ASM906v1_genomic.fna > ASM906v1.fasta
sed -n '/plasmid.*/q;p' GCF_009295945.1_ASM929594v1_genomic.fna > ASM929594v1.fasta
```
#### 1.construct synteny blocks using Sibelia software [1] for two threshold of minimum block length (5kb and 1kb). Put the blocks files in Supplements.
Сольем все файлы в один и запустим программу Sibelia
```
cat *.fasta > Yersinia_pestis.fasta
Sibelia -s loose -m 5000 Yersinia_pestis.fasta
Sibelia -s loose -m 1000 Yersinia_pestis.fasta
```
Получившиеся файлы лежат в папке Sibelia

#### 2.calculate the number of all blocks, common blocks, repeated blocks. Compare the results for different threshold. Add figures to the report. 

#### !Then for 1kb calculations
#### 3.visualize the plots where each point represents a block, y coordinate – it’s length, x – it’s frequency. Add figures in the report.
#### 4.for the longest common block and the longest rare block (found in two strains) describe the gene composition. Do the genes form operons? Add figures to the report.
#### 5.construct pairwise distance matrix using number of common synteny blocks as a evolutionary distance, visualize the heat map. Add the figure to the report.
#### 6.Find a pair of the most distant genomes (use random one in case of several equal meanings). Visualize whole-genome alignment using dotplot [2], reconstruct scenario of inversions [3]. Calculate length of inversions. For the longest one find the repeats that might be substrates of recombination.

[1] http://bioinf.spbau.ru/sibelia
Ключи запуска
-m отвечает за длину блока,
-s fine используется для близких геномов,
-a оставляет только общие блоки.

[2] https://bioinfo.lifl.fr/yass/yass.php
Запуск со стандартными параметрами

[3] http://grimm.ucsd.edu/cgi-bin/grimm.cgi
Алгоритм реконструирует только инверсии, поэтому на вход принимает только общие однокопийные блоки.

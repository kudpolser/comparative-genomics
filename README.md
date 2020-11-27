# comparative-genomics

### filter out the plasmids
### construct synteny blocks using Sibelia software [1] for two threshold of minimum block length (5kb and 1kb). Put the blocks files in Supplements.
### calculate the number of all blocks, common blocks, repeated blocks. Compare the results for different threshold. Add figures to the report. Then for 1kb calculations
### visualize the plots where each point represents a block, y coordinate – it’s length, x – it’s frequency. Add figures in the report.
### for the longest common block and the longest rare block (found in two strains) describe the gene composition. Do the genes form operons? Add figures to the report.
### construct pairwise distance matrix using number of common synteny blocks as a evolutionary distance, visualize the heat map. Add the figure to the report.
### Find a pair of the most distant genomes (use random one in case of several equal meanings). Visualize whole-genome alignment using dotplot [2], reconstruct scenario of inversions [3]. Calculate length of inversions. For the longest one find the repeats that might be substrates of recombination.

[1] http://bioinf.spbau.ru/sibelia
Ключи запуска
-m отвечает за длину блока,
-s fine используется для близких геномов,
-a оставляет только общие блоки.
[2] https://bioinfo.lifl.fr/yass/yass.php
Запуск со стандартными параметрами
[3] http://grimm.ucsd.edu/cgi-bin/grimm.cgi
Алгоритм реконструирует только инверсии, поэтому на вход принимает только общие однокопийные блоки.

# comparative-genomics

Кудрявцева Полина Сергеевна	

Assemblies: ASM83482v1	ASM83484v1	ASM118871v1	ASM118873v1	ASM379820v1	ASM379822v1	ASM1519065v1	ASM2284v1	ASM906v1	ASM929594v1

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
Все вычисления сделаем на питоне. Ноутбук с кодом хранится в этом же репозитории.
Создадим функцию, которая распарсит файлы blocks_coords.txt
```
def parse_blocks(f):
    block = {}
    for i in range(len(f)):
        if f[i].find('Block') == 0:
            idx = f[i][:-1]
            block[idx] = []
            skip_one_row = 1
        elif skip_one_row == 1:
            skip_one_row = 0
        elif f[i][:3] == '---':
            continue
        else:
            block[idx].append(f[i].split('\t')[0])
    return block
    
f = open("5kb/blocks_coords.txt",)
f = f.readlines()
f = f[12:]
block_5kb = parse_blocks(f)

f = open("1kb/blocks_coords.txt",)
f = f.readlines()
f = f[12:]
block_1kb = parse_blocks(f)
```

Посчитаем общее число блоков:
```
len(block_5kb), len(block_1kb)
```
(133, 257)

Посчитаем число блоков, которые есть в каждом геноме:
```
def common_block(block):
    n = 0
    for i in range(1, len(block)+1):
        if len(set(block['Block #' + str(i)])) == 10:
            n += 1
    return n

common_block(block_5kb), common_block(block_1kb)
```
(116, 204)

Посчитаем число блоков, которые повторяются хотя бы в одном геноме:
```
def repeated_block(block):
    n = 0
    for i in range(1, len(block)+1):
        if len(set(block['Block #' + str(i)])) != len(block['Block #' + str(i)]):
            n += 1
    return n

repeated_block(block_5kb), repeated_block(block_1kb)
```
(2, 25)

В итоге, как и ожидалось, при минимальном размере блока в 5kb было найдено меньшее число блоков, но большая доля из них представлена в каждом геноме. Также очень малое количество блоков длиной более 5kb повторяются в геномах.

#### !Then for 1kb calculations
#### 3.visualize the plots where each point represents a block, y coordinate – it’s length, x – it’s frequency. Add figures in the report.

Достанем из файла длину блоков и вычислим среднюю длину каждлго блока и уникальное число геномов, в которых он представлен:
```
def parse_blocks_length(f):
    block = {}
    for i in range(len(f)):
        if f[i].find('Block') == 0:
            idx = f[i][:-1]
            block[idx] = []
            skip_one_row = 1
        elif skip_one_row == 1:
            skip_one_row = 0
        elif f[i][:3] == '---':
            continue
        else:
            block[idx].append(int(f[i].split('\t')[4][:-1]))
    return block
    
block_1kb_len = parse_blocks_length(f)

for i in range(1, len(block_1kb_len)+1):
    block_1kb_len['Block #' + str(i)] = int(sum(block_1kb_len['Block #' + str(i)])/len(block_1kb_len['Block #' + str(i)]))

block_1kb_freq = {}
for i in range(1, len(block_1kb)+1):
    block_1kb_freq['Block #' + str(i)] = len(set(block_1kb['Block #' + str(i)]))
```

Получилась такая визуализация:
![GitHub Logo](number_of_genome_and_length.png)

#### 4.for the longest common block and the longest rare block (found in two strains) describe the gene composition. Do the genes form operons? Add figures to the report.

Наиболее редкий и длинный блок 2, самый длинный и частый блок 84. Сохраним их координаты в словарь и загрузим аннотацию генома, чтобы посмотреть какие гены находятся в этих блоках.

```
in_handle = open('GCF_000022845.1_ASM2284v1_genomic.gff')
for rec in GFF.parse(in_handle):
    print(rec)
in_handle.close()

#block 2
for i in range(len(rec.features)):
    if rec.features[i].location.start > 1987298 and rec.features[i].location.end < 2069521:
        print(rec.features[i].qualifiers['Name'])
 
block 84
for i in range(len(rec.features)):
    if rec.features[i].location.start > 192081 and rec.features[i].location.end < 193950:
        print(rec.features[i].qualifiers['Name'], '\n')
```
Во самом популярном блоке нашлись гены, составляющие опероны:
- rstA и rstB (two-component system response regulator)
- pntA и pntB (Re/Si-specific NAD(P)(+) transhydrogenase subunit alpha and beta)
и другие

В самом редком блоке есть ген:
- cAMP (activated global transcriptional regulator CRP)
Блок короткий, поэтому генов немного. В целом, вероятно этот реултор транскрипции может быть полезен в при особых условиях, поэтому он нашелся только в дву геномах из 10.

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

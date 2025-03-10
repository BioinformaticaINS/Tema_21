# Tema 22: Análisis metataxonómico

## Estructura de la práctica:

1. Instalación de programas
2. Obtención de datos
3. Análisis metataxonómico utilizando datos Illumina
4. Análisis metataxonómico utilizando datos Nanopore

## Metodología:

## 1. Instalación de programas

### Instalar los siguientes programas en un ambiente de conda

```bash
conda env create -n qiime2-amplicon-2024.10 --file https://data.qiime2.org/distro/amplicon/qiime2-amplicon-2024.10-py310-linux-conda.yml

conda create -n metataxonomic -c bioconda kraken2 krakentools kraken-biom
```

> **Comentario:** 
> - `qiime2`: Es una plataforma integral para el análisis de microbiomas, abarcando desde el control de calidad de las secuencias hasta la generación de visualizaciones y análisis estadísticos; su arquitectura modular y su amplia gama de herramientas permiten a los investigadores realizar análisis complejos de diversidad microbiana, filogenia y función, consolidándose como una herramienta indispensable en el campo de la ecología microbiana.
> - `kraken2`: Se destaca por su rapidez y precisión en la clasificación taxonómica de secuencias de ADN, permitiendo la identificación de microorganismos presentes en muestras complejas mediante la comparación con extensas bases de datos genómicas; su eficiencia lo hace ideal para el análisis de grandes conjuntos de datos de secuenciación de alto rendimiento, proporcionando información detallada sobre la composición microbiana de diversas muestras ambientales.
> - `krakentools`: Actúa como un complemento esencial para Kraken2, ofreciendo una variedad de utilidades para el procesamiento y la manipulación de los resultados obtenidos; estas herramientas facilitan la conversión de datos a formatos más manejables, la generación de informes resumidos y la extracción de información taxonómica específica, simplificando así el análisis y la interpretación de los datos de clasificación.
> - `kraken-biom`: Se especializa en la conversión de los resultados de Kraken2 al formato BIOM, un estándar fundamental en el análisis de microbiomas; al transformar los datos de abundancia taxonómica en una matriz estructurada, esta herramienta permite la integración fluida de los resultados de Kraken2 con otras plataformas de análisis, como QIIME 2, facilitando el análisis comparativo y la visualización de la diversidad microbiana.

## 2. Obtención de los datos 

```bash
cd

mkdir metataxonomic

cd metataxonomic

mkdir tmp

export TMPDIR=/home/ins_user/metataxonomic/tmp

mkdir raw_data

cd raw_data
```

```bash
gdown https://drive.google.com/uc?id=1sdOtHjQQOB5AZLHTuRSgi3eLp5wnym8l

unzip illumina.zip
```

> **Comentario:** El archivo illumina.zip contiene datos de secuenciación de 16S (región V3-V4) del artículo científico "From the Andes to the desert: 16S rRNA metabarcoding characterization of aquatic bacterial communities in the Rimac river, the main source of water for Lima, Peru" (https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0250401).

```bash
gdown https://drive.google.com/uc?id=1WmKIRuYEcckk5BLqKYLqReobwhetBLKT

unzip nanopore.zip
```

> **Comentario:** El archivo nanopore.zip contiene datos de secuenciación de 16S (completo) del artículo científico "The clinical utility of Nanopore 16S rRNA gene sequencing for direct bacterial identification in normally sterile body fluids" (https://www.frontiersin.org/journals/microbiology/articles/10.3389/fmicb.2023.1324494/full).

```bash
gdown https://drive.google.com/uc?id=13NDPF99mz8FxZ-eLtJpy6GbpaP4D4v64
```

## 3. Análisis metataxonómico utilizando datos Illumina

```bash

### Crear los archivos metadata.txt y manifest.txt con las siguientes informaciones:

cd ~/metataxonomic

mkdir illumina

cd illumina

conda activate qiime2-amplicon-2024.10

nano manifest.txt

sample-id	forward-absolute-filepath	reverse-absolute-filepath
01.Chicla  /home/ins_user/metataxonomic/raw_data/illumina/SRR12217142_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217142_2.fastq.gz
02.San_Mateo  /home/ins_user/metataxonomic/raw_data/illumina/SRR12217141_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217141_2.fastq.gz
03.Tamboraque	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217133_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217133_2.fastq.gz
04.Huanchor	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217132_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217132_2.fastq.gz
05.Chacahuaro	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217130_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217130_2.fastq.gz
06.Santa_Eulalia	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217131_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217131_2.fastq.gz
07.Chaclacayo	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217129_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217129_2.fastq.gz
08.Huachipa	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217128_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217128_2.fastq.gz
09.Libertadores	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217127_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217127_2.fastq.gz
10.Nuevo	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217126_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217126_2.fastq.gz
11.Universitaria	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224171_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224171_2.fastq.gz
12.Faucett	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217139_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217139_2.fastq.gz
13.Gambetta	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217138_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217138_2.fastq.gz
14.Chicla	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224180_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224180_2.fastq.gz
15.San_Mateo	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224179_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224179_2.fastq.gz
16.Tamboraque	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224178_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224178_2.fastq.gz
17.Huanchor	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224177_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224177_2.fastq.gz
18.Santa_Eulalia	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224176_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224176_2.fastq.gz
19.Chaclacayo	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224175_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224175_2.fastq.gz
20.Huachipa	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224174_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224174_2.fastq.gz
21.Libertadores	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224173_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224173_2.fastq.gz
22.Nuevo	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224172_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224172_2.fastq.gz
23.Faucett	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217140_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR12217140_2.fastq.gz
24.Gambetta	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224170_1.fastq.gz	/home/ins_user/metataxonomic/raw_data/illumina/SRR13224170_2.fastq.gz

nano metadata.txt

Sample ID	cuenca	altitud
01.Chicla	Upper_Rimac	3441
02.San_Mateo	Parac-Upper_Rimac	3422
03.Tamboraque	Parac-Upper_Rimac	3023
04.Huanchor	Santa_Eulalia-Parac	2868
05.Chacahuaro	Santa_Eulalia-Parac	2526
06.Santa_Eulalia	Santa_Eulalia	948
07.Chaclacayo	Jicamarca-Santa_Eulalia	657
08.Huachipa	Jicamarca-Santa_Eulalia	396
09.Libertadores	Lower_Rimac	213
10.Nuevo	Lower_Rimac	200
11.Universitaria	Lower_Rimac	69
12.Faucett	Lower_Rimac	33
13.Gambetta	Lower_Rimac	8
14.Chicla	Upper_Rimac	3441
15.San_Mateo	Parac-Upper_Rimac	3422
16.Tamboraque	Parac-Upper_Rimac	3023
17.Huanchor	Santa_Eulalia-Parac	2868
18.Santa_Eulalia	Santa_Eulalia	948
19.Chaclacayo	Jicamarca-Santa_Eulalia	657
20.Huachipa	Jicamarca-Santa_Eulalia	396
21.Libertadores	Lower_Rimac	213
22.Nuevo	Lower_Rimac	200
23.Faucett	Lower_Rimac	33
24.Gambetta	Lower_Rimac	8

### Importar las lecturas de todas las muestras en archivo demuxed_seqs.qza:

qiime tools import --type 'SampleData[PairedEndSequencesWithQuality]' --input-path manifest.txt --output-path demuxed_seqs.qza --input-format PairedEndFastqManifestPhred33V2

### Crear la carpeta visualización y generar el reporte de calidad del archivo demuxed_seqs.qza:

mkdir visualization

qiime demux summarize --i-data demuxed_seqs.qza --o-visualization visualization/seqs_quality.qzv

### Exportar el archive generado utilizando winscp y visualizarlo en https://view.qiime2.org/

### Realizar la eliminación de iniciadores en las lecturas y generar el reporte del archivo resultante:

qiime cutadapt trim-paired --i-demultiplexed-sequences demuxed_seqs.qza --p-cores 10 --p-front-f TCGTCGGCAGCGTCAGATGTGTATAAGAGACAGCCTACGGGNGGCWGCAG --p-front-r GTCTCGTGGGCTCGGAGATGTGTATAAGAGACAGGACTACHVGGGTATCTAATCC CTGCWGCCNCCCGTAGGCTGTCTCTTATACACATCTGACGCTGCCGACGA --p-minimum-length 100 --p-discard-untrimmed --o-trimmed-sequences demuxed_seqs_trimmed.qza

qiime demux summarize --i-data demuxed_seqs_trimmed.qza --o-visualization visualization/seqs_quality_trimmed.qzv

### Realizar la eliminación de ruido (denoising)utilizando DADA2:

qiime dada2 denoise-paired --i-demultiplexed-seqs demuxed_seqs_trimmed.qza --p-trunc-len-f 0 --p-trunc-len-r 0 --p-n-threads 20 --o-table table.qza --o-representative-sequences representatives.qza --o-denoising-stats denoising_stats.qza

### Generar el reporte de los archivos generados:

qiime feature-table summarize --i-table table.qza --m-sample-metadata-file metadata.txt --o-visualization visualization/table_denoised.qzv

qiime metadata tabulate --m-input-file denoising_stats.qza --o-visualization visualization/denoising_stats.qzv

qiime feature-table tabulate-seqs --i-data representatives.qza --o-visualization  visualization/representatives.qzv

### Exportar los archivos generados utilizando winscp y visualizarlo en https://view.qiime2.org/

### Realizar la asignación taxonómica utilizando la base de datos de SILVA:

qiime feature-classifier classify-sklearn --i-classifier /home/ins_user/metataxonomic/raw_data/silva-138-99-nb-classifier.qza --i-reads representatives.qza --p-n-jobs 1 --o-classification taxa.qza

### Generar el reporte de la clasificación taxonómica:

qiime metadata tabulate --m-input-file taxa.qza --o-visualization visualization/taxa.qzv

### Exportar el archivo generado utilizando winscp y visualizarlo en https://view.qiime2.org/

### Filtrar os ASVs en base a su clasificación taxonómica:

qiime taxa filter-table --i-table table.qza --i-taxonomy taxa.qza --p-include p__ --p-exclude mitochondria,chloroplast --o-filtered-table table_f.qza

qiime taxa filter-seqs --i-sequences representatives.qza --i-taxonomy taxa.qza --p-include p__ --p-exclude mitochondria,chloroplast --o-filtered-sequences representatives_f.qza

### Generar el reporte grafico de barras de la clasificación taxonómica:

qiime taxa barplot --i-table table_f.qza --i-taxonomy taxa.qza --m-metadata-file metadata.txt --o-visualization visualization/taxa_barplot.qzv

### Exportar el archivo generado utilizando winscp y visualizarlo en https://view.qiime2.org/

### Realizar el analisis filogenético de los ASVs:

qiime phylogeny align-to-tree-mafft-fasttree --i-sequences representatives_f.qza --o-alignment aligned_representative_sequences --o-masked-alignment masked_aligned_representative_sequences --o-tree unrooted_tree --o-rooted-tree rooted_tree

### Exportar los datos de abundancia y taxonomia:

qiime tools export --input-path table_f.qza --output-path exported_table

qiime tools export --input-path taxa.qza --output-path exported_taxonomy

### Reemplazar la primera linea del archivo taxonomy.tsv:

sed -i 's/Feature /#OTU/g' exported_taxonomy/taxonomy.tsv

sed -i 's/Taxon/taxonomy/g' exported_taxonomy/taxonomy.tsv

sed -i 's/Confidence/confidence/g' exported_taxonomy/taxonomy.tsv

### Generar el archivo BIOM:

biom add-metadata -i exported_table/feature-table.biom --observation-metadata-fp exported_taxonomy/taxonomy.tsv --sc-separated taxonomy -o feature-table-tax.biom

### Exportar los archivos BIOM y metadata.txt utilizando winscp.

## 4. Análisis metataxonómico utilizando datos Nanopore

```bash

cd ~/metataxonomic

mkdir nanopore

cd nanopore

conda activate metataxonomic

nano manifest.txt






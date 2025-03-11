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

conda create -n metataxonomic -c bioconda kraken2 krakentools kraken-biom krona
```

> **Comentario:** 
> - `qiime2`: Es una plataforma integral para el análisis de microbiomas, abarcando desde el control de calidad de las secuencias hasta la generación de visualizaciones y análisis estadísticos; su arquitectura modular y su amplia gama de herramientas permiten a los investigadores realizar análisis complejos de diversidad microbiana, filogenia y función, consolidándose como una herramienta indispensable en el campo de la ecología microbiana.
> - `kraken2`: Se destaca por su rapidez y precisión en la clasificación taxonómica de secuencias de ADN, permitiendo la identificación de microorganismos presentes en muestras complejas mediante la comparación con extensas bases de datos genómicas; su eficiencia lo hace ideal para el análisis de grandes conjuntos de datos de secuenciación de alto rendimiento, proporcionando información detallada sobre la composición microbiana de diversas muestras ambientales.
> - `krakentools`: Actúa como un complemento esencial para Kraken2, ofreciendo una variedad de utilidades para el procesamiento y la manipulación de los resultados obtenidos; estas herramientas facilitan la conversión de datos a formatos más manejables, la generación de informes resumidos y la extracción de información taxonómica específica, simplificando así el análisis y la interpretación de los datos de clasificación.
> - `kraken-biom`: Se especializa en la conversión de los resultados de Kraken2 al formato BIOM, un estándar fundamental en el análisis de microbiomas; al transformar los datos de abundancia taxonómica en una matriz estructurada, esta herramienta permite la integración fluida de los resultados de Kraken2 con otras plataformas de análisis, como QIIME 2, facilitando el análisis comparativo y la visualización de la diversidad microbiana.
> - `krona`: Una herramienta para visualizar la salida de Kraken en un formato jerárquico interactivo.

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

> **Comentario:** Este bloque de comandos establece la estructura inicial del directorio de trabajo para un análisis metataxonómico. Primero, cd sin argumentos asegura que el usuario se encuentre en su directorio de inicio. Luego, se crea un directorio principal llamado metataxonomic para organizar todos los datos y resultados del proyecto. Posteriormente, se ingresa a este directorio. Dentro de metataxonomic, se crea un directorio temporal llamado tmp. La variable de entorno TMPDIR se configura para apuntar a este directorio temporal, lo cual es útil para que ciertas herramientas almacenen archivos temporales durante el procesamiento. Finalmente, se crea un directorio raw_data dentro de metataxonomic, y se ingresa a este directorio. Este directorio está destinado a almacenar los datos brutos o iniciales que se utilizarán en el análisis. En resumen, este bloque de comandos prepara el entorno de trabajo al crear los directorios necesarios y configurar la variable TMPDIR, organizando así los datos para los pasos posteriores del análisis metataxonómico.

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

> **Comentario:**  Permite descargar el archivo silva-138-99-nb-classifier.qza es un modelo de aprendizaje automático pre-entrenado con scikit-learn (sklearn)que permite a QIIME 2 identificar los tipos de microorganismos presentes en muestras de ADN, utilizando como referencia la base de datos de ARN ribosómico SILVA 138; este clasificador facilita la asignación taxonómica rápida y precisa de secuencias microbianas, lo que es crucial para analizar la composición y diversidad de las comunidades microbianas en diversos entornos (https://github.com/qiime2/resources/blob/main/index.md).

```bash
wget https://genome-idx.s3.amazonaws.com/kraken/16S_Silva138_20200326.tgz

tar xzvf 16S_Silva138_20200326.tgz
```

> **Comentario:** Permite descargar una base de datos de Kraken 2 optimizada para análisis de secuencias 16S rRNA de SILVA.

## 3. Análisis metataxonómico utilizando datos Illumina

### 3.1 Crear los archivos metadata.txt y manifest.txt con las siguientes informaciones:

```bash
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
```

### 3.2 Importar las lecturas de todas las muestras en archivo demuxed_seqs.qza:

```bash
qiime tools import --type 'SampleData[PairedEndSequencesWithQuality]' --input-path manifest.txt --output-path demuxed_seqs.qza --input-format PairedEndFastqManifestPhred33V2
```

> **Comentario:** Este comando importa los datos de secuencia al formato QIIME 2. El parámetro --type especifica el tipo de datos, --input-path apunta al archivo manifest, --output-path define el nombre del archivo de salida, y --input-format indica el formato de los archivos fastq (lecturas pareadas con codificación de calidad Phred33).

### 3.3 Crear la carpeta visualización y generar el reporte de calidad del archivo demuxed_seqs.qza:

```bash
mkdir visualization

qiime demux summarize --i-data demuxed_seqs.qza --o-visualization visualization/seqs_quality.qzv
```
> **Comentario:** Genera un reporte de calidad de las secuencias demultiplexadas.

### 3.4 Visualizar el archivo creado en https://view.qiime2.org/

> **Comentario:** Este paso se refiere a la visualización de archivos generados por el software QIIME 2. QIIME 2 produce archivos de visualización con la extensión ".qzv", los cuales están diseñados para ser visualizados a través de la plataforma web view.qiime2.org.

### 3.5 Realizar la eliminación de iniciadores en las lecturas y generar el reporte del archivo resultante:

```bash
qiime cutadapt trim-paired --i-demultiplexed-sequences demuxed_seqs.qza --p-cores 2 --p-front-f TCGTCGGCAGCGTCAGATGTGTATAAGAGACAGCCTACGGGNGGCWGCAG --p-front-r GTCTCGTGGGCTCGGAGATGTGTATAAGAGACAGGACTACHVGGGTATCTAATCC --p-minimum-length 100 --p-discard-untrimmed --o-trimmed-sequences demuxed_seqs_trimmed.qza
```

> **Comentario:** Con 'cutadapt', se eliminan los adaptadores y primers de las secuencias. Los parámetros --p-front-f y --p-front-r definen las secuencias a eliminar, --p-minimum-length establece la longitud mínima de las lecturas, y --p-discard-untrimmed descarta secuencias no recortadas.

```bash
qiime demux summarize --i-data demuxed_seqs_trimmed.qza --o-visualization visualization/seqs_quality_trimmed.qzv
```

> **Comentario:** Se genera un nuevo resumen de calidad, esta vez para las secuencias que han sido recortadas, permitiendo evaluar el efecto del recorte en la calidad de los datos.

### 3.6 Realizar la eliminación de ruido (denoising) utilizando DADA2:

```bash
qiime dada2 denoise-paired --i-demultiplexed-seqs demuxed_seqs_trimmed.qza --p-trunc-len-f 0 --p-trunc-len-r 0 --p-n-threads 20 --o-table table.qza --o-representative-sequences representatives.qza --o-denoising-stats denoising_stats.qza
```

> **Comentario:** DADA2 se utiliza para el denoising de las secuencias, corrigiendo errores de secuenciación. Los parámetros --p-trunc-len-f y --p-trunc-len-r se establecen en 0 para no truncar las lecturas, --p-n-threads define el número de hilos a usar, y los parámetros --o- especifican los archivos de salida.

### 3.7 Generar el reporte de los archivos generados:

```bash
qiime feature-table summarize --i-table table.qza --m-sample-metadata-file metadata.txt --o-visualization visualization/table_denoised.qzv
```

> **Comentario:** Se genera un resumen de la tabla de características, que incluye estadísticas sobre la distribución y abundancia de ASVs.

```bash
qiime metadata tabulate --m-input-file denoising_stats.qza --o-visualization visualization/denoising_stats.qzv
```

> **Comentario:** Se tabulan los metadatos de las estadísticas de denoising, proporcionando una visión detallada del proceso de corrección de errores.

```bash
qiime feature-table tabulate-seqs --i-data representatives.qza --o-visualization  visualization/representatives.qzv
```

> **Comentario:** Se tabulan las secuencias representativas, permitiendo la visualización de las secuencias de ASVs identificadas.

### 3.8 Visualizar los archivos creados en https://view.qiime2.org/

### 3.9 Realizar la asignación taxonómica utilizando la base de datos de SILVA:

```bash
qiime feature-classifier classify-sklearn --i-classifier /home/ins_user/metataxonomic/raw_data/silva-138-99-nb-classifier.qza --i-reads representatives.qza --p-n-jobs 1 --o-classification taxa.qza
```

> **Comentario:**  Las secuencias representativas se clasifican taxonómicamente utilizando un clasificador pre-entrenado. El parámetro --i-classifier especifica el clasificador a usar, y --i-reads define el archivo de secuencias.

### 3.10 Generar el reporte de la clasificación taxonómica:

```bash
qiime metadata tabulate --m-input-file taxa.qza --o-visualization visualization/taxa.qzv
```

> **Comentario:** Se tabulan los resultados de la clasificación taxonómica, facilitando la visualización y análisis de la composición taxonómica de las muestras.

### 3.11 Visualizar el archivo creado en https://view.qiime2.org/

### 3.12 Filtrar os ASVs en base a su clasificación taxonómica:

```bash
qiime taxa filter-table --i-table table.qza --i-taxonomy taxa.qza --p-include p__ --p-exclude mitochondria,chloroplast --o-filtered-table table_f.qza
```

> **Comentario:** Se filtra la tabla de caracteristicas excluyendo los ASV clasificados como mitochondria y chloroplast.

```bash
qiime taxa filter-seqs --i-sequences representatives.qza --i-taxonomy taxa.qza --p-include p__ --p-exclude mitochondria,chloroplast --o-filtered-sequences representatives_f.qza
```

> **Comentario:** Se filtra las secuencias representativas excluyendo los ASV clasificados como mitochondria y chloroplast.

### 3.13 Generar el reporte grafico de barras de la clasificación taxonómica:

```bash
qiime taxa barplot --i-table table_f.qza --i-taxonomy taxa.qza --m-metadata-file metadata.txt --o-visualization visualization/taxa_barplot.qzv
```

> **Comentario:** Se genera un gráfico de barras de la composición taxonómica.

### 3.14 Visualizar el archivo creado en https://view.qiime2.org/

### 3.15 Realizar el analisis filogenético de los ASVs:

```bash
qiime phylogeny align-to-tree-mafft-fasttree --i-sequences representatives_f.qza --o-alignment aligned_representative_sequences --o-masked-alignment masked_aligned_representative_sequences --o-tree unrooted_tree --o-rooted-tree rooted_tree
```

> **Comentario:** Se realiza un análisis filogenético de las secuencias representativas filtradas.

### 3.16 Exportar los datos de abundancia y taxonomia:

```bash
qiime tools export --input-path table_f.qza --output-path exported_table
```

> **Comentario:** Se exporta la tabla de características filtrada.

```bash
qiime tools export --input-path taxa.qza --output-path exported_taxonomy
```

> **Comentario:** Se exporta la clasificación taxonómica.

### 3.17 Reemplazar la primera linea del archivo taxonomy.tsv:

```bash
sed -i 's/Feature /#OTU/g' exported_taxonomy/taxonomy.tsv

sed -i 's/Taxon/taxonomy/g' exported_taxonomy/taxonomy.tsv

sed -i 's/Confidence/confidence/g' exported_taxonomy/taxonomy.tsv
```

> **Comentario:** Se formatean los archivos de taxonomía exportados para compatibilidad con otras herramientas.

### 3.18 Generar el archivo BIOM:

```bash
biom add-metadata -i exported_table/feature-table.biom --observation-metadata-fp exported_taxonomy/taxonomy.tsv --sc-separated taxonomy -o feature-table-tax.biom
```

> **Comentario:** Se genera un archivo BIOM que combina la tabla de características filtrada con la información taxonómica. Este archivo es útil para análisis adicionales y visualizaciones.

### Exportar los archivos BIOM y metadata.txt para luego visualizarlos en https://www.microbiomeanalyst.ca/MicrobiomeAnalyst/upload/OtuUploadView.xhtml

> **Comentario:** Este último paso implica subir el archivo BIOM (feature-table-tax.biom) y metadata.txt a la plataforma web MicrobiomeAnalyst para su análisis y visualización interactiva. MicrobiomeAnalyst ofrece una amplia gama de herramientas para el análisis de datos de microbiomas, incluyendo análisis de diversidad, análisis de abundancia diferencial y visualizaciones interactivas. Los resultados de este paso dependerán del análisis específico realizado en MicrobiomeAnalyst.

## 4. Análisis metataxonómico utilizando datos Nanopore

### 4.1 Generar la carpeta de trabajo:

```bash
cd ~/metataxonomic

mkdir nanopore

cd nanopore

conda activate metataxonomic
```

> **Comentario:** Estos comandos inician la preparación del entorno de trabajo para el análisis metataxonómico con datos Nanopore. Primero, se desplaza al directorio principal metataxonomic. Luego, crea un nuevo directorio llamado nanopore dentro de ese directorio, donde se almacenarán todos los datos y resultados específicos de Nanopore. Finalmente, activa el entorno de conda llamado metataxonomic, asegurando que todas las dependencias necesarias estén disponibles para el análisis.

### 4.2 Generar un script para correr kraken 2 en varias muestras:

```bash
nano kraken2.sh
```

```bash
#!/bin/bash

# Directorio con los archivos fastq.gz (entrada)
input_dir="/home/ins_user/metataxonomic/raw_data/nanopore"

# Directorio para los archivos de salida
output_dir="/home/ins_user/metataxonomic/nanopore"

# Directorio de la base de datos Kraken 2
db_dir="/home/ins_user/metataxonomic/raw_data/16S_SILVA138_k2db"

# Número de hilos a usar
threads=2

# Iterar sobre todos los archivos fastq.gz en el directorio de entrada
for file in "$input_dir"/*.fastq.gz; do
  # Obtener el nombre base del archivo sin la extensión
  base_name=$(basename "$file" .fastq.gz)

  # Ejecutar Kraken 2
  kraken2 \
    -db "$db_dir" \
    --threads "$threads" \
    --use-names \
    "$file" \
    --output "$output_dir/$base_name.kraken" \
    --report "$output_dir/$base_name.report"

  echo "Kraken 2 analysis completed for $file"
done
```

> **Comentario:** Estos comandos crean un script llamado kraken2.sh que automatiza la ejecución de Kraken 2 en múltiples archivos de datos Nanopore. El script define las rutas a los directorios de entrada (archivos fastq.gz), salida, y la base de datos de Kraken 2, así como el número de hilos para el procesamiento paralelo. Luego, utiliza un bucle for para iterar a través de cada archivo fastq.gz en el directorio de entrada. Para cada archivo, extrae el nombre base, ejecuta Kraken 2 con las opciones especificadas, y guarda los resultados (archivos .kraken y .report) en el directorio de salida. Finalmente, imprime un mensaje indicando que el análisis de Kraken 2 se ha completado para cada archivo.

```bash
head -n 20 SRR25820513.report

100.00	119852	0	R	1	root
100.00	119852	0	D	3	  Bacteria
 99.95	119789	0	P	1672	    Firmicutes
 99.95	119787	30	C	1673	      Bacilli
 99.79	119596	4	O	1800	        Lactobacillales
 99.77	119575	21	F	1850	          Streptococcaceae
 99.73	119526	119526	G	1853	            Streptococcus
  0.02	28	28	G	1851	            Lactococcus
  0.01	14	0	F	1828	          Enterococcaceae
  0.01	14	14	G	1831	            Enterococcus
  0.00	2	0	F	1836	          Lactobacillaceae
  0.00	2	2	G	1837	            Lactobacillus
  0.00	1	0	F	1811	          Carnobacteriaceae
  0.00	1	1	G	1821	            Granulicatella
  0.13	158	3	O	1674	        Bacillales
  0.13	155	0	F	1680	          Bacillaceae
  0.13	153	153	G	1688	            Bacillus
  0.00	1	1	G	1690	            Caldibacillus
  0.00	1	1	G	1696	            Geobacillus
  0.00	2	0	O	45158	        Alicyclobacillales
```

> **Comentario:** Este es un ejemplo de las primeras 20 líneas del archivo de reporte de Kraken 2 para la muestra SRR25820513. Cada línea representa un taxón identificado, con información como el porcentaje de lecturas clasificadas, el número de lecturas, el rango taxonómico, el ID taxonómico y el nombre del taxón.

```bash
head -n 3 SRR25820513.kraken

C	SRR25820513.1	Streptococcus (taxid 1853)	1482	3:115 1800:7 3:33 45715:8 3:5 0:41 1853:5 3:23 1853:5 0:26 1673:5 3:2 1688:1 1673:2 0:31 1853:4 3:15 1672:4 3:9 1851:7 1800:1 3:53 1672:1 3:29 45459:5 45465:3 45589:1 0:20 1673:12 3:1 1672:5 3:80 1:6 0:45 1853:3 3:8 1853:8 0:2 1853:1 0:5 1853:8 3:19 0:11 3:3 0:5 3:3 0:1 3:5 1:4 3:77 1:3 3:3 0:10 44069:3 0:18 1853:3 0:1 1673:1 3:5 1853:5 1672:5 1853:6 1673:5 1672:5 1673:5 1672:5 1853:1 1672:29 3:90 1853:1 0:28 1853:8 3:109 1:19 3:17 1673:6 0:41 3:1 2375:2 3:118 0:1 1853:3 0:64 1850:13 3:5 1850:3 3:33
C	SRR25820513.2	Streptococcus (taxid 1853)	1482	3:22 1853:5 0:15 1673:3 1674:2 0:7 3:2 1673:5 1674:11 3:8 1673:1 3:204 45498:6 0:22 3:2 0:15 1853:1 0:27 2805:5 0:4 3:7 0:3 45544:2 0:2 46831:2 0:28 3:7 1672:5 3:17 45459:1 1672:5 0:44 1673:3 3:25 0:27 3:31 0:26 1688:5 3:11 0:31 3:18 1:3 3:10 0:1 43656:5 0:18 45342:3 45416:5 3:5 45495:4 3:30 1853:1 3:29 3723:4 0:37 1853:2 0:20 1672:5 1673:1 0:10 1853:1 0:14 1853:7 1672:1 0:34 1853:3 3:3 1853:8 3:58 0:29 3:105 1:20 3:5 0:34 3:22 1:2 3:114 1853:26 3:12 1853:1 3:3 0:25 3:5 1850:16 0:21 1688:5 3:14
C	SRR25820513.3	Streptococcus (taxid 1853)	1503	3:33 1850:3 3:5 1850:17 3:90 1853:1 3:7 1853:5 3:1 1853:15 3:67 1:2 3:1 0:27 3:33 1:19 3:23 1800:4 3:10 1853:6 3:1 1853:5 1673:4 3:89 1853:16 1673:5 1853:1 1673:5 3:30 1853:21 1800:5 1853:1 3:13 1672:11 1853:10 0:23 1853:1 0:5 1853:6 1672:5 1853:5 3:5 1673:1 0:25 3303:5 0:4 3:7 1:3 3:26 1853:2 0:4 1853:1 0:18 1688:1 1800:7 0:11 3:16 1:19 3:84 1:1 3:5 1:1 3:1 1:3 3:5 1:7 3:68 1800:1 0:5 45277:5 0:38 1672:8 3:74 1800:2 1853:9 3:22 1672:3 3:5 1853:1 3:2 1850:5 1672:2 1673:5 3:1 1673:1 3:19 45342:3 3:8 0:32 1853:5 3:14 0:6 1853:3 1672:1 0:2 1853:4 0:16 1673:1 1:2 3:64 1853:5 0:23 3723:7 3:104
```

> **Comentario:** Este es un ejemplo de las primeras 3 líneas del archivo .kraken para la muestra SRR25820513. Cada línea representa una lectura clasificada por Kraken 2, mostrando información como el estado de clasificación (C para clasificado), el ID de la lectura, el taxón identificado y su ID taxonómico, y una lista de k-mers coincidentes con la base de datos de Kraken 2.

### 4.3 Visualizar los archivos report en https://fbreitwieser.shinyapps.io/pavian/

> **Comentario:** Este paso implica subir los archivos .report generados por Kraken 2 a la aplicación web Pavian para su visualización interactiva. Pavian permite explorar la composición taxonómica de las muestras, identificar los taxones más abundantes y comparar la diversidad entre muestras. Los resultados de este paso dependerán de los datos específicos de cada archivo .report subido.

### 4.4 Visualizar los archivos report en krona:

```bash
ktUpdateTaxonomy.sh
```

> **Comentario:** Este comando actualiza la taxonomía utilizada por Krona, asegurando que los nombres y la jerarquía taxonómica estén actualizados para la visualización.

```bash
kreport2krona.py -r SRR25820513.report -o SRR25820513.krona

kreport2krona.py -r SRR25820516.report -o SRR25820516.krona

kreport2krona.py -r SRR25820522.report -o SRR25820522.krona
```

> **Comentario:** Estos comandos convierten los archivos .report de Kraken 2 a formato Krona (.krona), que es un formato jerárquico para la visualización de datos taxonómicos. Se genera un archivo .krona para cada muestra (SRR25820513, SRR25820516 y SRR25820522).

```bash
ktImportText SRR25820513.krona SRR25820516.krona SRR25820522.krona -o fluids.krona.html
```

> **Comentario:** Este comando combina los archivos .krona de las tres muestras en un único archivo HTML interactivo (fluids.krona.html). Este archivo se puede abrir en un navegador web para explorar la composición taxonómica de las muestras de forma conjunta, permitiendo la comparación entre ellas.

### 4.5 Generar el archivo de metadatos:

```bash
nano metadata.txt
```

```bash                                                           
#NAME   fluid
SRR25820626     Ascitic_fluid
SRR25820669     Ascitic_fluid
SRR25820685     Ascitic_fluid
SRR25820649     Joint_aspirate
SRR25820672     Joint_aspirate
SRR25820683     Joint_aspirate
SRR25820513     Joint_fluid
SRR25820516     Joint_fluid
SRR25820661     Joint_fluid
SRR25820532     Knee_joint_fluid
SRR25820605     Knee_joint_fluid
SRR25820617     Knee_joint_fluid
SRR25820524     Peritoneal_dialysis_fluid
SRR25820690     Peritoneal_dialysis_fluid
SRR25820710     Peritoneal_dialysis_fluid
SRR25820566     Peritoneal_fluid
SRR25820588     Peritoneal_fluid
SRR25820611     Peritoneal_fluid
SRR25820671     Pleural_aspirate
SRR25820678     Pleural_aspirate
SRR25820686     Pleural_aspirate
SRR25820522     Pleural_fluid
SRR25820570     Pleural_fluid
SRR25820644     Pleural_fluid
```

> **Comentario:** Este bloque de comandos crea un archivo de metadatos llamado metadata.txt. Este archivo asocia cada muestra (identificada por su SRR ID) con un tipo de fluido corporal. Esta información es crucial para análisis posteriores, ya que permite agrupar y comparar muestras según su origen.

### 4.6 Generar el archivo BIOM para cada base de datos y exportarlo:

```bash
kraken-biom *.report --fmt json --metadata metadata.txt -o fluid.biom
```

> **Comentario:** Este comando utiliza la herramienta kraken-biom para generar un archivo BIOM (fluid.biom) a partir de los archivos .report de Kraken 2 y el archivo de metadatos metadata.txt. El archivo BIOM es un formato estándar para representar datos de abundancia de taxones, y se utiliza comúnmente en análisis de metagenómica. La opción --fmt json especifica el formato JSON para el archivo BIOM, y la opción --metadata metadata.txt indica que se deben incluir los metadatos en el archivo BIOM.

### 4.7 Exportar el archivo BIOM y visualizarlo en https://www.microbiomeanalyst.ca/MicrobiomeAnalyst/upload/OtuUploadView.xhtml

> **Comentario:** Este último paso implica subir el archivo BIOM (fluid.biom) a la plataforma web MicrobiomeAnalyst para su análisis y visualización interactiva. MicrobiomeAnalyst ofrece una amplia gama de herramientas para el análisis de datos de microbiomas, incluyendo análisis de diversidad, análisis de abundancia diferencial y visualizaciones interactivas. Los resultados de este paso dependerán del análisis específico realizado en MicrobiomeAnalyst.

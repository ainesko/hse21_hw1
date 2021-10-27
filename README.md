# hse21_hw1
Струихина Ксения БМТ191

random seed = 427

# Обязательная часть

Рассматриваем два типа чтений:

1) Чтения типа paired-end:	oil_R1.fastq, oil_R2.fastq

2) Чтения типа mate-pairs:	oilMP_S4_L001_R1_001.fastq, oilMP_S4_L001_R2_001.fastq

Создаём ссылки в папке

```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
```

Выберем случайные чтения с параметром -s427, преобразуем их и получим отчёты для обычных и обрезанных чтений

```
seqtk sample -s427 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s427 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s427 oilMP_S4_L001_R1_001.fastq 1500000 > matep1.fastq
seqtk sample -s427 oilMP_S4_L001_R2_001.fastq 1500000 > matep2.fastq

mkdir fastqc
ls sub* matep* | xargs -tI{} fastqc -o fastqc {}

mkdir multiqc
multiqc -o multiqc fastqc

platanus_trim sub*
platanus_internal_trim matep*

mkdir fastqc_trimmed
ls sub* matep*| xargs -tI{} fastqc -o fastqc_trimmed {}

mkdir multiqc_trimmed
multiqc -o multiqc_trimmed fastqc_trimmed
```

Далее собираем контиги и скаффолды 

```
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log

time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed 2> scaffold.log
```

Уменьшаем число промежутков
```
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed 2> gapclose.log
```

Удаляем исходные файлы и подрезанные чтения
```
rm sub* matep*

rm sub*.trimmed matep*.int_trimmed
```

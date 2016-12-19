Gut microbiome of the Canadian Arctic Inuit
===========================================

Sequences, processing files, otu tables and metadata for the 16S Inuit gut project.
Data was processed using QIIME 1.9.0

##Sequencing data
Raw MiSeq sequencing data can be found in the 0_raw_reads folder.

##Joined paired reads
Paired reads were concatenated using the join_paired_ends.py script in QIIME 1.9.0

```
join_paired_ends.py -f 0_raw_reads/Undetermined_S0_L001_R1_001.fastq -r 0_raw_reads/Undetermined_S0_L001_R2_001.fastq -o 1_concatenated_reads/fastqjoin -b 0_raw_reads/Undetermined_S0_L001_I1_001.fastq
```

##Library split
Libraries were split using split_libraries_fastq.py script in QIIME 1.9.0

'''
split_libraries_fastq.py -i 1_concatenated_reads/fastqjoin.join.fastq -o 2_split_libraries -m metadata.txt -b 1_concatenated_reads/fastqjoin.join_barcodes.fastq --barcode_type hamming_8
'''

##Chimera filtering
#Chimeras were identified with the identify_chimeric_seqs.py script (using usearch and Greengenes 13_8), and removed with the filter_fasta.py script in QIIME 1.9.0

'''
identify_chimeric_seqs.py -i 2_split_libraries/seqs.fna -m usearch61 -o 3_chimera_filtering -r 97_otus.fasta

filter_fasta.py -f 2_split_libraries/seqs.fna  -o 3_chimera_filtering/seqs_chimeras_filtered.fna -s 3_chimera_filtering/non_chimeras.txt
'''

##OTU picking
#Open-reference OTU picking was performed at 97% identity using Greengenes version 13_8, with a pre-filtering step at 60% identity (also with Greengenes) using the pick_open_reference_otus.py script in QIIME 1.9.0

'''
pick_open_reference_otus.py -i 3_chimera_filtering/seqs_chimeras_filtered.fna -r 97_otus.fasta -o 4_otu_tables/otu_table.biom --prefilter_percent_id 0.6
'''

##Table clean-up
#OTUs with less than 10 observations across the OTU table were removed using the filter_otus_from_otu_table.py script in QIIME 1.9.0

'''
filter_otus_from_otu_table.py -i 4_otu_tables/otu_table.biom -o 4_otu_tables/otu_table_filtered.biom -n 10
'''

#OTU table was rarefied at a depth of 50,000 reads using the single_rarefaction script in QIIME 1.9.0

'''
single_rarefaction.py -i 4_otu_tables/otu_table_filtered.biom -d 50000 -o 4_otu_tables/otu_table_filtered_rarefied.biom
'''
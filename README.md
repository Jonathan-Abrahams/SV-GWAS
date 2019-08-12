# SV-GWAS

First we run FSM-lite on the genomes
./fsm-lite/fsm-lite -v -l fsm_files.txt -t tmp_idx  > fsm_kmers.txt

We then seperate the occorunces from the sequences using AWK
```
awk '{print $1}' fsm_kmers.txt >fsm_kmers_seq_only.txt
awk '{print $3,$4,$5,$6}' fsm_kmers.txt>fsm_kmers_occur_only.txt

```
We then split it
```
split -l 100000 fsm_kmers_occur_only.txt
```

We then turn the kmers only file into a fasta
```
awk '{ print ">"NR"\n"$0 }' < fsm_kmers_seq_only.txt>kmers_seq_only.fa

```

Map the fasta to the Reference genome
```
bowtie2 -p 8 -f -U kmers_seq_only.fa --reorder -k  10 -D 24 -R 3 -N 0 -L 5 -i S,1,0.50 -x CP017404.fa_index -S all_kmers.fa.sam
#bowtie2 -p 8 -f -U all_dupe_kmers.fa --reorder -k 100 -D 24 -R 3 -N 0 -L 5 -i S,1,0.50 -x CP017404.fa_index -S all_dupe_kmers.fa_100.sam
#bowtie2 -p 8 -f -U all_dupe_kmers.fa --reorder -D 24 -R 3 -N 0 -L 5 -i S,1,0.50 -x CP017404.fa_index -S all_dupe_kmers.fa.sam

```
Extract mapping positions and only use perfect matches

```
#grep --perl "AS:i:0\tXS:i:0\tXN:i:0\tXM:i:0\tXO:i:0\tXG:i:0\tNM:i:0\tMD:Z:9\tYT:Z:UU"
grep --perl "AS:i:0" all_kmers.fa.sam >all_kmers.fa.sam.perfect
awk '{ print $1,$4,$2}'  all_dupe_kmers.fa.sam.perfect >all_dupe_kmers.fa.sam.mapping_positions.perfect
```

Plot read occurences as a histogram
```

```

We then graph the abundence of those kmers in 10kb windows across the genome



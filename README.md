# BHPs_alphaproteobacteria

This repository contains code and data related to phylogenetic analysis for the diversity of BHPs on alphaproteobacterias.

Following you can find the commands used to generate the phylogenetic tree.

##  Tree construction

Retrieve sequences from local blast database:

```
blastdbcmd  -dbtype nucl -entry_batch acc_16S.txt -db ../databases/ncbi/NT/blast/nt -outfmt "%f" > 16S_final.fasta
```

Align sequences:

```
mafft-xinsi --thread 20 16S_final.fasta > mafft/16S-xinsi_final.alg
```

Trim:

```
java -jar /opt/biolinux/BMGE-1.12/BMGE.jar -h 0.55 -i  mafft/16S-xinsi_final.alg -t "DNA" -of bmge/16S_trimmed_final.aln
``` 
Tree construction:

```
iqtree2  --threads-max 20 -T AUTO -m MFP -pre tree/16S_final -s bmge/16S_trimmed_final.aln > tree/16S_final.log
```

##  Tree visualization

To visualize the tree, we have used iTol. You can  find txt files to label and color tree elements under the _iTol_ directory.

The default accessions on the fasta file could change according to NCBI's NT database selection (some subspecies could share the same sequence) and therefore, some accessions need to be mapped. 
The mapping  **acc_map.txt** could be generated as follows:

```
cat acc_16S.txt |\ 
        awk  'NR==FNR  {split($1,a,".");
                        h[a[1]]=$1;next} 
             $0 ~ "^>" {pr=0;for(i=1;i<=NF;i++){
                               if($i ~ ">"){
                                 acc=substr($i,2);
                                 split(acc,b,"."); 
                                 if(h[b[1]]){
                                   print h[b[1]]"\t"acc;pr=1
                                 }
                                }
                              }
                           if(pr==0){print $0}}' - 16S_final.fasta > acc_map.txt
```



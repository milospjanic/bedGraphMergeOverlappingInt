# bedGraphMergeOverlappingInt

```
#!/bin/bash

###bedGraphMergeOverlappingInt

BEDGRAPH=$(pwd)/$1
echo Proccesing file:
echo $BEDGRAPH

#merge intervals for each chromose separately, needs perl installed

for chr in {1..22} X Y 

do

export chr

(cut -f2 $BEDGRAPH; cut -f3 $BEDGRAPH) | sort -nu | perl -ne 'BEGIN{$i=0} chomp; push @F, $_; if($i++){print "chr$ENV{chr}\t$F[$i-2]\t$F[$i-1]\n"}' > b.bed

bedtools intersect -a $BEDGRAPH -b b.bed | sort -k2n -k3n -k4nr | perl -lane 'print unless $h{$F[0,1,2]}++' > $BEDGRAPH.$chr

done

#concatenate individual chromosome files

for chr in {1..22} X Y 

do

cat $BEDGRAPH.$chr >> $BEDGRAPH.merge

done

#sort merged file

sort -k1,1 -k2,2n $BEDGRAPH.merge > $BEDGRAPH.merge.sort
```

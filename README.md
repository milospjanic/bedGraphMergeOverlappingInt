# bedGraphMergeOverlappingInt

# Usage

<pre>
mpjanic@valkyr:~$ cat test.wg
chr1	1	6	2
chr1	2	7	3
chr1	3	8	6
chr12	1	7	3
chr12	2	8	4
chr12	3	9	8
chrY	1	8	2
chrY	2	9	3
chrY	3	10	6



mpjanic@valkyr:~$ chmod 755 ./bedGraphMergeOverlappingInt
mpjanic@valkyr:~$ ./bedGraphMergeOverlappingInt test.wig 
Proccesing file:
/home/mpjanic/test.wg

mpjanic@valkyr:~$ cat test.wg.merge.sort 
chr1	1	2	2
chr1	2	3	3
chr1	3	6	6
chr1	6	7	6
chr1	7	8	6
chr12	1	2	3
chr12	2	3	4
chr12	3	6	8
chr12	6	7	8
chr12	7	8	8
chr12	8	9	8
chrY	1	2	2
chrY	2	3	3
chrY	3	6	6
chrY	6	7	6
chrY	7	8	6
chrY	8	9	6
chrY	9	10	6

</pre>


# Code

```bash
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

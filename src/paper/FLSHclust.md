# FLSHclust
> Altae-Tran, Han, et al. "Uncovering the functional diversity of rare CRISPR-Cas systems with deep terascale clustering." Science 382.6673 (2023): eadi1910.

## Data
sequence data in fasta format

## Algorithm outline
1. Input the original data.
2. Initial analysis to find most likely similar pairs. 
     - e.g. we have 1000 sequence, there should have 499,500 pairs for comparisons. The work of this initial analysis is to narrow these pairs down to 6041 pairs. 
     - reproduced well.
     - more details about this step are as follows.
     - has been verifyed that the final 19 pairs come from these 6041 pairs.
3. The next analysis is based on these this reduced pairs.
4. These reduced pairs are randomly assigned to 32 partitions in PySpark.
5. Similarity calculation is performed in these partitions concurrently.
    - similarity calculate:
    ```
    # 1
    parasail.sw_trace_scan_16(x, y, 11, 1, parasail.blosum62)
    # 2
    edlib.align(x, y, mode='HW', task='locations', k=max_dist + 1) # edit distance
    ```
    - filter again based on one threshold `min_seq_id`.
    - potential pairs are narrowed down again from 6041 to 21.
    - yes, the final 19 pairs all come from these 21 pairs.
6. continue ...
    ```

    ```



     
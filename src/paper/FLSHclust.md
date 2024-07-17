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

<div class="warning">
In my opinion, in the view of discovering similar pairs, till now, it has already finished the work to discover similar pairs in a large dataset. If we summary the previous steps, we can see: for example, 

1000 seq (~500K pairs) `---step1--->` 6041 pairs `---step2--->` 21 pairs

- In `step1`, LSH algorithm is the core role. It reduces the number of comparisons to a few thousands.
- In `step2`, the common similarity calculation method is performed.

**Why the cluster work is already done at this moment?**
If we check these 21 pairs and the final 19 pairs carefully, wa can see the 21 pairs already are divided into 19 groups. And, the final 19 pairs come from each cluster, and pick the most similar pair if there are more than one pairs in each cluster. For example, if we talk about the similar sequence to Seq_173, we find two: Seq_715 and Seq_896, however, in the final output, we only pick the Seq_715 which is the most similar one.


| 19 pairs | 21 pairs |
|---------|---------|
|(173, 715)|(173,715), (173,896)|
|(889,582)|(889,582), (889,906)|

</div>


6. If we continue their work, we can see their following work is to point out the only pairs who are most close to each other in each cluster.
7. That is the reason of why we only see two similar sequence in each cluster in the final output file.
    
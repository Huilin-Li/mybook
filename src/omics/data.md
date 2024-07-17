# dataset

## ftp.ensembl.org
<https://ftp.ensembl.org/pub/current_fasta/>

```shell
# get .gz files
wget -r -np -e robots=off -A 'pep.all.fa.gz' https://ftp.ensembl.org/pub/current_fasta/ 
```
```python
import os, gzip, shutil
import glob
import numpy as np
import pandas as pd
from Bio import SeqIO

# unzip all .gz files
start_dir = "./ftp.ensembl.org/pub/current_fasta/"
gz_count = []
destination_dir = "./fa_save/"
for root, dirs, files in os.walk(start_dir):
    for gz_name in files:
        gz_file = os.path.join(root, gz_name)
        if "/pep/" in gz_file:
            # print(gz_file)
            gz_count.append(gz_file)
            out_file = destination_dir + gz_name.split(".gz")[0]
            with gzip.open(gz_file,"rb") as f_in, open(out_file,"wb") as f_out:
                shutil.copyfileobj(f_in, f_out)
```
```python
# read
with open("./fa_save/all_data.fa") as fasta_file:
    identifiers = []
    seq = []
    for seq_record in SeqIO.parse(fasta_file, 'fasta'): 
        identifiers.append(seq_record.id)
        seq.append("".join(seq_record.seq))
df_pro = pd.DataFrame()
df_pro["aa_seq"] = seq
df_pro["p_id"] = identifiers
# clear duplicates
df_pro_drop_dup = df_pro.drop_duplicates(subset='aa_seq')
```
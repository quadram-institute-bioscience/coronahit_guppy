### Customising barcode arrangement

In order to demultiplex coronaHiT data properly with Guppy, there are two files that need to be customised in the Guppy data's folder, for instance on Ubuntu `/opt/ont/guppy/data/barcoding` ; on Windows `C:\Program Files\Oxford Nanopore\ont-guppy-cpu\data` 

1. Make a copy of **barcodes_masked.fasta**  to **barcodes_coronahit_masked.fasta**.
   Replace the arrangement template starting for **BC_1st** and **BC_2nd** like below
   ```
   >CHIT_1st
   GGTGCTGNNNNNNNNNNNNNNNNNNNNNNNNTTAACCTGTCTCGTGGGCTCGG
   >CHIT_2nd
   GGTGCTGNNNNNNNNNNNNNNNNNNNNNNNNTTAACCTTCGTCGGCAGCGTC
    ```

2. Create **barcode_arrs_pcr96_coronahit.cfg** with the following configuration
    ```
    [loading_options]
   barcodes_filename = barcodes_coronahit_masked.fasta
   double_variants_frontrear = true

   [BC%02i]
   compatible_kits = coronahit
   first_index = 1
   last_index = 96
   kit = BC
   normalised_id = barcode%02i
   scoring_function = MAX
   mask1 = CHIT_1st
   mask2 = CHIT_2nd
   barcode1 = BC%02i
   barcode2 = BC%02i
    ```

In the same barcoding folder, update the file **configuration.cfg** by appending **barcode_arrs_pcr96_coronahit.cfg** to the end of the line starting with *arrangements_files*

```
# Guppy Barcoding Configuration
arrangements_files = barcode_arrs_pcr12.cfg barcode_arrs_pcr96.cfg barcode_arrs_nb12.cfg barcode_arrs_nb24.cfg barcode_arrs_nb96.cfg barcode_arrs_rbk.cfg barcode_arrs_lwb.cfg barcode_arrs_rlb.cfg barcode_arrs_rab.cfg barcode_arrs_rbk4.cfg barcode_arrs_rbk096.cfg barcode_arrs_vmk.cfg barcode_arrs_vmk2.cfg barcode_arrs_16s.cfg barcode_arrs_pcr96_coronahit.cfg
score_matrix_filename = 5x5_mismatch_matrix.txt
start_gap1 = 40
end_gap1 = 40
open_gap1 = 40
extend_gap1 = 40
start_gap2 = 40
end_gap2 = 40
open_gap2 = 160
extend_gap2 = 160
min_score = 60.0
front_window_size = 150
rear_window_size = 150
```

### Verify configuration file

If changes were correct, you should see coronahit printed at the bottom of the list with the command `guppy_barcoder --print_kits`

```
guppy_barcoder --print_kits

Available barcoding kits are:
EXP-NBD103
EXP-NBD104
EXP-NBD114
EXP-NBD196
EXP-PBC001
EXP-PBC096
OND-SQK-LP0096M
OND-SQK-LP0096S
OND-SQK-LP1152S
OND-SQK-LP9216
SQK-16S024
SQK-LWB001
SQK-PBK004
SQK-PCB109
SQK-RAB201
SQK-RAB204
SQK-RBK001
SQK-RBK004
SQK-RBK096
SQK-RLB001
SQK-RPB004
VSK-VMK001
VSK-VMK002
coronahit
```

### Demultiplex coronaHiT data

```
guppy_barcoder --barcode_kits "coronahit" -i input -s output --require_barcodes_both_ends
```
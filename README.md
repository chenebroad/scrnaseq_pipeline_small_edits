

This code is written to process all the samples listed in a sample sheet containing information on all your samples. The code can process multiple biological samples coming from a sample tracking file. Individual count matrices are made for each 10x channel by `cellranger_workflow`.

## Sample tracking file

The sample tracking file, in csv format, is a useful way to track the important information for each sample, and is needed to run this script. Each sample requires the following text fields.
- date: The date your samples are processed in yyyy_mm_dd format.
- run_pipeline: Boolean (True or False) that determines what samples are processed. Set this to True for all samples you want to processs. All other samples must be set to False. How this works in operation is that as you add your new samples, set them to run_pipeline = True and set the previously run samples to run_pipeline = False. Remember that all samples that are processed together must come from the same flow cell. The code is written to only process one flow cell!
- Channel Name: This is the sample name that is used by the experimental team to name a 10x channel.
- sampleid: This is the sample id.
- condition: Any biological or technical condition used in collecting this sample. This could be a buffer used or a flow cytometry sorting gate. If there is no special condition, label this as none.
- tissue: The tissue of origin. This column is optional.
- replicate: This is used to designate which 10x channel (up to 8 different channels on a Chromium chip) this sample was run on, and is useful when multiple 10x channels are run for the same biological sample. The channel number must be an integer but it does not matter what integers you choose, as long as different channels use different integers. I suggest 1, 2, 3... If there is only a single channel, label this as channel1.
- Lane: The lane that the sample was sequenced on within the flowcell. It can be a single lane (ex: 5), several lanes (ex: 5-6), or * (all lanes on the flow cell).
- Index: The 10x index for the sample.
- project: The name of the project you'd like to see attached to your directories
- reference: The genome reference to use when Cell Ranger `count` is creating the counts matrices. Please choose from one of references listed in Cumulus read the docs.
- chemistry: The sequencing chemistry used.
- flowcell: The flowcell id from your sequencing run.
- seq_dir: The directory of your sequencing results in GCP.
- min_umis: the min number of UMIs you'd like to use for filtering when you run cumulus pegasus.
- min_genes: the min number of genes you'd like to use for filtering when you run cumulus pegasus.
- percent_mito: the max percentage of expression coming from mito genes that you'd like to set for filtering when you run cumulus pegasus.
- focus: the genome you would like to focus on if you are are using a mixed-species genome. The analysis will be performed on alignments to this genome.
- append: the genome you'd like to append to your counts matrix. These counts will not be included in the main analysis workflow.
- calc_signature_scores: a path to a .gmt file you can use for testing gene signatures using cumulus pegasus.


## Steps
1. Create a Terra workspace https://support.terra.bio/hc/en-us/sections/360004538992-Workspaces
   - set authorization domain to klarman_cell_observatory
2. Share workspace with scrnaseq-pipeline@microbiome-xavier.iam.gserviceaccount.com and klarman_cell_observatory@firecloud.org . Make them owners.
3. Add scrnaseq-pipeline@microbiome-xavier.iam.gserviceaccount.com as a user in the billing project that owns the terra workspace
4. Import cumulus/cellranger_workflow from Broad Methods Repository (https://support.terra.bio/hc/en-us/sections/360004147011-Workflows)
   - Source: cumulus/cellranger_workflow/28
5. Create sample tracking csv file and upload it to the google cloud folder of your workspace
6. Execute pipeline 

## Executing Pipeline

Pipeline can be run on UGER or GCP. To run on UGER clone this repository and 

```commandline
bash scripts/run.sh --project-name "scp-test" \
 --gcp-bucket-basedir "gs://fc-secure-1620151c-e00c-456d-9daf-4d222e1cab18/scp-test" \
 --sample-tracking-file "gs://fc-secure-1620151c-e00c-456d-9daf-4d222e1cab18/scp-test/sample_tracking_small.csv" \
 --email "dchafamo@broadinstitute.org" \
 --workspace "'kco-tech/Gut_eQTL'" \
 --count-matrix-name "raw_feature_bc_matrix.h5" \
 --steps "MKFASTQ,COUNT,CUMULUS"
 
```



<h1 align="center">
    pblaa - Generate High-Quality, Phased Consensus Sequences from Pooled Amplicons
</h1>

### Input

The laa program needs a .subreads.bam file containing the subreads for each amplicon for which a consensus sequence is desired.  Older instruments output data in the form of bax.h5 and bas.h5 files, while newer instruments output data directly into BAM format.  To analyze older data, it must first be converted to BAM format, which may be done via SMRTLink or on the command-line with the tool bax2bam, which simply needs the name of any bax.h5 files to convert and the prefix of the output file.  Assuming your original file is named mydata.bas.h5, you can produce a file mynewbam.subreads.bam with the following command:

    bax2bam -o mynewbam mydata.1.bax.h5 mydata.2.bax.h5 mydata.3.bax.h5
    
The laagc program requires an additional file in the form of a FASTA file of sequences around which you wish to use to guide clustering.

### Running LAA

LAA can be run at the command line as follows:

    laa [OPTIONS] FILES...

An example command would be:

    laa --minLength=3000 --minSnr=3.75 myData.subreads.bam
    
The LAA program can be run with a variety of options described below.  

General Options:

|           Option           |     Example (Defaults)      |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Random Number Seed         | --rngSeed=42                | RNG seed, modulates reservoir filtering of reads.
| Generate BAM Index         | --generateBamIndex          | Generate PacBio indicies (*.pbi) for BAM files that don't have them.
| Ignore BAM Index           | --ignoreBamIndex            | Ignore PacBio indicies (*.pbi) for BAM files if they exist.
| Number of Threads          | -n,--numThreads=-1          | Number of threads to use, <= 0 means autodetection.
| Model Path                 | -M,--modelPath="model.json" | Path to a model file or directory containing model files.
| Model Specification        | -m,--modelSpec="ModelName"  | Name of chemistry or model to use, overriding default selection.

Barcode Options:

|           Option           |     Example (Defaults)         |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Barcodes                   | -b,--barcodes="barcodes.fasta" | Fasta file of the barcode sequences used, overwriting any names in the DataSet. NOTE: Only used to find barcode names.
| Minimum Barcode Score      | --minBarcodeScore=26           | Minimum average barcode score to require of subreads.
| Full Length                | --fullLength                   | Filter input reads by presence of both flanking barcodes.
| Do Barcode                 | --doBc="0--0,1--1"             | Comma-separated list of barcode pairs to analyze, either by name, such as 'lbc1--lbc1', or by index, such as '0--0'.
| Ignore Barcodes            | --ignoreBc                     | Disable barcode filtering and treat all data as one sample.

Data Filtering Options:
|           Option           |     Example (Defaults)      |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Minimum Length             | -l,--minLength=3000         | Minimum length of input reads.
| Maximum Length             | -L,--maxLength=0            | Maximum length of input reads, set 0 to disable.
| Minimum Read Score         | -s,--minReadScore=0.75      | Minimum read score of input reads.
| Minimum SNR                | --minSnr=3.75               | Minimum SNR of input reads.
| Cluster Guide File         | -g,--clusterGuide           | Path to a fasta file of sequences to use for guided clustering.
| Minimum Guide Score        | --minGuideScore=50          | Minimum score of a query alignment to the cluster guide.
| Minimum Guide Space        | --minGuideSpan=500          | Minimum span of a query alignment to the cluster guide.
| Whitelist File             | --whitelist                 | A file of ReadIds, as either Text or Fasta, to allow from the input file.

Coarse Clustering Options:

|           Option           |     Example (Defaults)      |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Maximum Reads              | -r,--maxReads=2000          | Maximum number of input reads to use.
| Maximum Clustering Reads   | -c,--maxClusteringReads=400 | Maximum number of input reads to cluster per barcode.
| Full-Length Preference     | --fullLengthPreference      | Prefer full-length subreads when clustering.
| Full-Length Clustering     | --fullLengthClustering      | Only use full-length subreads when clustering.
| Clustering Inflation       | --clusterInflation=2        | Markov Clustering inflation parameter.
| Clustering Loop Weight     | --clusterLoopWeight=0.001   | Markov Clustering loop weight parameter.
| Skip Rate                  | --skipRate=0.0              | Skip some high-scoring alignments to disperse the cluster more.
| Minimum Cluster Size       | --minClusterSize=20         | Minimum number reads supporting a cluster before it is reported.
| Do Cluster                 | --doCluster=-1              | Only analyze one specified.
| Disable Clustering         | --noClustering              | Disable the coarse clustering.

Fine Phasing Options:

|           Option           |     Example (Defaults)      |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Ignore Ends                | -i,--ignoreEnds=0           | When splitting, ignore N bases at the ends. Use to prevent excessive splitting caused by degenerate primers.
| Maximum Phasing Reads      | --maxPhasingReads=500       | Maximum number of reads to use for phasing/consensus.
| Minimum Q-Score            | --minQScore=20              | Minimum score to require of mutations  used for phasing.
| Minimum Prevalence         | --minPrevalence=0.09        | Minimum prevalence to require of mutations used for phasing.
| Minimum Split Reads        | --minSplitReads=20          | Minimum number reads favoring the minor phase required to split a haplotype.
| Minimum Split Fraction     | --minSplitFraction=0.1      | Minimum fraction of reads favoring the minor phase required to split a haplotype.
| Minimum Split Score        | --minSplitScore=500         | Global likelihood improvement required to split a haplotype.
| Minimum Z-Score            | --minZScore=-10             | Minimum ZScore to allow before adding a read to a haplotype.
| No Phasing                 | --noPhasing                 | Disable the fine phasing step.

Post-Processing Options:

|           Option           |     Example (Defaults)      |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Take N                     | --takeN=0                   | Report only the top N consensus sequences for each barcode, set <1 to disable.
| Trim Ends                  | -t,--trimEnds=0             | Trim N bases from each end of each consensus.
| Minimum Predicted Accuracy | --minPredictedAccuracy=0.95 | Minimum predicted consensus accuracy below which a consensus is called 'noise'.
| Chimera Score Threshold    | --chimeraScoreThreshold=1.0 | Minimum score to consider a sequence chimeric.
| No Chimera Filter          | --noChimeraFilter           | Deactivate the chimera filter and output all consensus.

Result Options:

|           Option           |     Example (Defaults)                                  |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Log File                   | --logFile=None                                          | Output file to write logging information to.
| Result File                | --resultFile="amplicon_analysis.fastq"                  | Output file for high-quality results.
| Junk File                  | --junkFile="amplicon_analysis_chimeras_noise.fastq"     | Output file for low-quality or chimeric results.
| Report File                | --reportFile="amplicon_analysis_summary.csv"            | Output file for summary report.
| Input Report File          | --inputReportFile="amplicon_analysis_input.csv"         | Output estimates of input PCR quality based on subread mappings.
| Locus Report File          | --locusReportFile="amplicon_analysis_per_locus.csv"     | Output estimates of sample mixture based on ZMW/subread mappings.
| White report File          | --whitelistReportFile="amplicon_analysis_whitelist.csv" | Output barcode/locus mappings for each subread seen.
| Subreads Report Prefix     | --subreadsReportPrefix="amplicon_analysis_subreads"     | Output prefix for subreads report.

Other Options:

|           Option           |     Example (Defaults)      |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Help Information           | -h,--help                   | Output this help.
| Version Information        | --version                   | Output version info.
| Verbosity                  | -v,--verbose                | Use verbose output.
| Log Level                  | --logLevel=INFO             | Set log level.

### Output

When the laa program finishes, it outputs a FASTQ file with one entry for each consensus sequence derived with a custom formated ID.

An example FASTQ result:
```
@Barcode53--53_Cluster0_Phase0_NumReads29
TCACACGCAGGTGCCTTTGCAGAAACAAAGTCAGGGTTCTTCAAGTCACAAAGGGAAGGGCAGGAACAACTCTTGCCTCTCAGTCCCACACAAGGCAGCTGTCTCACACTATAGAAAAAAATATTCATGAACAAATTCATATCCATCACAGTGAGGGGTCACACCTTAAACAGCCCATCGCATGCTCAATACATCCAATGCAAAGAAACCCCATAGCACAGCTGTGTCCACTGTTCCGCCCAACACCCAACACACATTAGGTCCTCCAAGCTCTCACCTTTACAAGCTGTGAGGGACACATCAGAGCCCTGGGCACTGTCACTGCCTGGGGTAGAACAAAAACAGAACCTGGTCAGATCCCACAGAAGATGTGGCTAGAGGAGGAATTGTGAGGTGGGTGGGCTCCCCCATGGGCTCCCAAACACAATATCCCAAGGACCTCAGGCATCAGCCTCCTTCATACTTACTTGCAGCCTGAGTGTAACTCCCTCCTTTTCTATCTGTGAGAAGAAAATGTCCTGTGAGATACCAGAAAGGAGCCAGGGCCTTAAGGTCCTAGAGGAACCTCCTAGTCTTGGACCCCAGAGAAGTTTCCAGAAATGTGTGACTGCAGACCCAGGGCGGGATCAGGAAACATGAAGAAAGCAGGTGTGGGTCCTGGACCAATAGCCCTCCTGAGGTCTGTCCTCAGGGACCTTCCCCTGTGACTTGTGACTGCTGGGATCAGGTCCCATCACCGCCGTAATCAAGGTGATAAATCTGTCCTTCATTTTAACAGGTGCTTTACAAAAGAGTAAGTGCTGGCACACAGGGCCCAGACTGGGTAGGCCCATGATTGTGGACGGTGCTTCCCAGTAATGAGACAGGGCACATTTCTAGCTGGGGCTTGGAACCCTCAGTGAGACAAGAAATCTCAGACCCCACCCTTCACCCCTTCTCCACCTGAGCTCTTCCTCCTCCACATCACGGCAGCGACCACAGCTCCAGTGATCACAGCTCCAAGGAGAACCAGGCCAGCAATGATGCCCACGATGGGGATGGTGGGCTGGGAAGACAGCTCTGGGAAAAGAGGGGAAGGTGAGGGGCCCTGACCCTGCTAAAGGTCTCCAGAGAGGCTCCTGCTTTCCCTAAGAGACATGACACCCCCATCTCCCTCCTTACCCCATCTCAGGGTGAGGGGCTTGGGCAGACCCTCATGCTGCACATGGCAGGTGTATCTCTGCTCCTCTCCAGAAGGCACCACCACAGCCGCCCACTTCTGGAAGGTTCCATCCCCTGCAGGCCTGGTCTCCACGAGCTCCGTGTCCTGGGTCTGGTCCTCCCCATCCCGCTGCCAGGTCAGTGTGATCTCCGCAGGGTAGAAGCCCAGGGCCCAGCACCTCAGGGTGGCCTCATGGTCAGAGATGGGGTGGTGGGTCATATGTGTCTTGGGGGGGTCTGACGGGAAGAGTCAGAAAATTCAGGCATTTTGCATCTGTCATGGGACACTCCACCAGCACGCATGTGGCCATCTTGAGAATGGACAGGACACCCGGGATGGGGAAGAGAGCACAGAACCCAGACACCAGCCTGGACACAGGCACCTGGGATAATCTTCTATTCCCTGAGAAGGGAACAGCGACTTCTGGTCCTGACCTGAGTGGAGGCTGAGGGACTCAGAAGTGCTGGACTCAGACCCCCACACACATTGAGTGTGAAGCAGAGAACAAGGCCTGAGAGGAAAAGTCACGGGCCCAAGGCTGCTGCCGGTGTCAAAGGGAACCACTCATCAGTATTCGAGGGATCGTCTTCCCGTCACTCCTTCAGAGATTTTATCCCTTAATTGTGTCAGAGAGCAGGGCGGAACCTCAGAGTCACTCTCTGGTACAGGATCTGGAACCCAGGAGGATTCCTCTCCCTCAGGACCAGAGGGAGGGTGATATTCTAGTGTTGGTCCCAATTGTCTCCCCTCCTTGTGGGAGGCCAGCCCGGGAGATCTACAGGCGATCAGGGAGGCGCCCCGTGGCCCCTGGTACCCGTGCGCTGCAGCGTCTCCTTCCCGTTCTCCAGGTATCTGCGGAGCCACTCCACGCACGTGCCATCCAGGTAGGCTCTCAACTGCTCCGCCTCATGGGCCGCCTCCCACTTGCGCTTGGTGATCTGAGCCGCCATGTCCGCCGCGGTCCAAGAGCGCAGGTCCTCGTTCAGGGCGATGTAATCCTTGCCGTCGTAGGCGTCCTGCCGGTACCCGCGGAGGAAGCGCCCGTCCGACCCCACGTCGCAGCCATACATTATCTGGATGGTGTGAGAACCTGGCCCCGACCCCGCGGTCAGCCCAGTCCCCCGAGCCCCGCCCAGCCCCGACCAACCCGGGGGGATTTTTGGCCTAAACTGAAAATGAAACCGGGTAAAGGCGCCTGGGCCTCTCCCGGGGCAAGGGTCTCGGGGTCCCGCGGCTTCGGGGTGGATCTCGGACCCGGAGACTGTGGGCGACCTGGCCCGTCCGTGGGGGATGAGGGGTCCTGACCTGCGCCCCCGGCCGGGGTCACTCACCGGCCTCGCTCTGGTTGTAGTAGCCGCGCAGGGTCCCCAGGTCCACTCGGTCAGTCTGTGACTGGGCCTTCACATTCCGTGTCTCCTGGTCCCAATACTCCGGCCCCTCCTGCTCTATCCACGGCGCCCGCGGCTCCATCCTCTGGCTCGCGGCGTCGCTGTCGAACCGCACGAACTGCGTGTCGTCCACGTAGCCCACGGCGATGAAGCGGGGCTCCCCGCGGCCGGGCCGGGACACGGATGTGAAGAAATACCTCATGGAGTGGGAGCCTGGGGGCGAGCAGTGGCTGAGACCTGCCCGACCCTCGTCCCGGCGCGGCTCCCCCGGTCCTGCGCCCCCGCCAGGAGGGCCCCTTGCTTCTCCCCGCAGAGGCGGTTTCCCTCCCGACCCCGCACTCACCCGCCCAGGTCTGGGTCAGGGCCAGGGCCCCCGAGAGTAGCAGGAGGAGGGTTCGGGGCGCCATGACGGCCATCCTCGGCGTCTGGGGAGAATCTGAGTCCCGGTGGGTGCGTGCGGGCTTTAGAACAGCGACCGCGACGACACTGATTGGCTTCTCTGGAAACCCGACACCCAATGGGAGTGAGAACTGGGTCCGCGTCGTGAGTATCCAGTGTGAG
+
~f}{~~~~~{~~~~~~~~~~~~i~~~p~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~=~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~[~~t~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~q~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~m~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~Z~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~|~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~w~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~?~~~~~~~~~~~~]~~~~~~~~~~~u~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~}~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~o~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~y~~~q~~~~~~~~~~~~~~~~~~~q~~~~~~~~~~~~~~~~~\~~~~~~~~~~~~F~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~t~~~~~~~~~~~~~~~l~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~w~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~}M~~~~|~~~~~~~~~~~~~~~~~~~~~~~p~~~~~~~~~~~n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~{~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~tt_|*
```

A description of each of the underscore-separate fields in the sequence id is given below:

|           Option           |     Example                 |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Barcode                    | Barcode53--53               | Pair of either barcode indices or barcode names denoting from which sample in a multiplexed dataset the consensus derives.
| Cluster                    | Cluster0                    | Index number of the "cluster" or predicted locus from current sample. NOTE: Due to high variation in the dis-similarity between alleles, not all clusters correspond to unique loci.
| Phase                      | Phase0                      | Index number of the "phase" or predicted allele from the current cluster. NOTE: Due to internal filtering, not all phases become.
| Number of Reads            | NumReads29                  | Number of independent subreads that contributed to this consensus sequence.


## LAA Summary Report

In addition to the consensus sequences, laa also outputs a number of CSV files that contain useful metadata about the consensus results, or the data supporting each result.  The most useful and important of these is the summary, by default written to "amplicon_analysis_summary.csv", that describes the nature and origin of each consensus output.

A description of each of the common separated fields is given below:

|         Field Name         |     Example                              |                                                                                                                                                                                                                                                                                                    Explanation                                                                                                                                                                                                                                                                                                     |
| -------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Barcode Name               | 53--53                                   | Pair of either barcode indices or barcode names denoting from which sample in a multiplexed dataset the result derives.
| Fasta Name                 | Barcode53--53_Cluster0_Phase0_NumReads29 | Full name, or sequence id, of the consensus sequence described by the current line in the summary CSV.
| Coarse Cluster             | 0                                        | Index number of the "cluster" or predicted locus from current sample. NOTE: Due to high variation in the dis-similarity between alleles, not all clusters correspond to unique loci.
| Phase                      | 0                                        | Index number of the "phase" or predicted allele from the current cluster. NOTE: Due to internal filtering, not all phases become.
| Total Coverage             | 29                                       | Approximate number of independent subreads that contributed to this consensus sequence.
| Sequence Length            | 3150                                     | Length of the final, polished consensus sequence for this allele
| Predicted Accuracy         | 1.0                                      | Estimated consensus accuracy, base on the combined probabilities of error of all bases in the consensus sequence
| Consensus Converged        | 1 [TRUE]                                 | Boolean flag for whether the final consensus polishing step completed with no residual uncertainty. "1" if True and "0" if False.
| Noise Sequence             | 0 [FALSE]                                | Boolean flag for whether the consensus sequence is designated as a likely non-crossover PCR artifact. "1" if True and "0" if False.
| Is Duplicate               | 0 [FALSE]                                | Boolean flag for whether the consensus sequence is a duplicate of another 
| Duplicate Of               | "N/A"                                    | Name of the consensus sequence of which this sequence is a duplicate, otherwise "N/A".
| Is Chimera                 | 0 [FALSE]                                | Boolean flag for whether the consensus sequence is flagged as a likely PCR cross-over artifact. "1" if True and "0" if False
| Chimera Score              | -1                                       | Score for the best possible PCR cross-over event found by the ChimeraLabeler, and "-1" if no hits were found.
| Parent Sequence A          | "N/A"                                    | Name of the consensus sequence that corresponds to the left-most parent of the proposed PCR cross-over, otherwise "N/A".
| Parent Sequence B          | "N/A"                                    | Name of the consensus sequence that corresponds to the right-most parent of the proposed PCR cross-over, otherwise "N/A".
| Cross-over Position        | -1                                       | Predicted position in the consensus sequence of the cross-over event, "-1" if no possible cross-overs was found.


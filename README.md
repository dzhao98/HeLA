# HeLA

srun -p compute -q batch -N 1 -n 24 --mem 500G -t 48:00:00 --pty bash

nextflow run nf-core/sarek -profile singularity --input /projects/verhaak-lab/dzhao/HeLasequence/Hela.tsv --outdir /projects/verhaak-lab/dzhao/HeLasequence/results/ -work-dir /fastscratch/dzhao --genome GRCh38

#Mutect2 
gatk Mutect2 -R /projects/verhaak-lab/GLASS-NF/references/GRCh38/Homo_sapiens_assembly38.fasta /projects/verhaak-lab/dzhao/HeLasequence/results/Preprocessing/A2/Recalibrated/A2.recal.bam -O /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A2

gatk --java-options -Xmx60g  Mutect2 \
            -R /projects/verhaak-lab/GLASS-NF/references/GRCh38/Homo_sapiens_assembly38.fasta \
            -I BAM \
            --dont-use-soft-clipped-bases true \
            --standard-min-confidence-threshold-for-calling 20 \
            -O OUTPUT \
            -bamout  bamout.bam
            
            gatk --java-options -Xmx60g Mutect2 -R /projects/verhaak-lab/GLASS-NF/references/GRCh38/Homo_sapiens_assembly38.fasta -I /projects/verhaak-lab/dzhao/HeLasequence/results/Preprocessing/A13/Recalibrated/A13.recal.bam --dont-use-soft-clipped-bases true -O /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A13/A13unfiltered.vcf -bamout /projects/verhaak-lab/dzhao/HeLasequence/Mutect/bamout13.bam
            
        #Run as tumor A2 vs normal A13
            gatk --java-options -Xmx60g Mutect2 -R /projects/verhaak-lab/GLASS-NF/references/GRCh38/Homo_sapiens_assembly38.fasta -I /projects/verhaak-lab/dzhao/HeLasequence/results/Preprocessing/A2/Recalibrated/A2.recal.bam -I /projects/verhaak-lab/dzhao/HeLasequence/results/Preprocessing/A13/Recalibrated/A13.recal.bam -normal A13 --dont-use-soft-clipped-bases true -O /projects/verhaak-lab/dzhao/HeLasequence/Mutect/compare/A2_13unfiltered.vcf -bamout /projects/verhaak-lab/dzhao/HeLasequence/Mutect/bamout213.bam
            


#Filter
gatk --java-options -Xmx60g FilterMutectCalls \
            -V /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A2/A2unfiltered.vcf \
            --stats STATS \
            -O /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A2
            
            gatk --java-options -Xmx60g FilterMutectCalls -V /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A2/A2unfiltered.vcf -R /projects/verhaak-lab/GLASS-NF/references/GRCh38/Homo_sapiens_assembly38.fasta --stats /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A2/A2unfiltered.vcf.stats   -O /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A2/A2filtered.vcf
            
            gatk --java-options -Xmx60g FilterMutectCalls -V /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A13/A13unfiltered.vcf -R /projects/verhaak-lab/GLASS-NF/references/GRCh38/Homo_sapiens_assembly38.fasta --stats /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A13/A13unfiltered.vcf.stats   -O /projects/verhaak-lab/dzhao/HeLasequence/Mutect/A13/A13filtered.vcf
            
            
            
       gatk PlotDenoisedCopyRatios --standardized-copy-ratios /projects/verhaak-lab/dzhao/HeLasequence/A2/A2.standardizedCR.tsv --denoised-copy-ratios /projects/verhaak-lab/dzhao/HeLasequence/A2/A2.denoisedCR.tsv --sequence-dictionary /projects/verhaak-lab/GLASS-NF/references/GRCh37/Homo_sapiens_assembly19.dict  --minimum-contig-length 46709983 --output /projects/verhaak-lab/dzhao/HeLasequence/A2 --output-prefix A2
     
 

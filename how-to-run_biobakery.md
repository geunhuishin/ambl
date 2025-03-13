BioBakery Run Guideline
===================

> For more information, please find [biobakery_workflows](https://github.com/biobakery/biobakery_workflows).


## 시작하기

------------------

### Whole Metagnome Shotgun (wmgx) 워크플로우 실행
#### 명령어 설명
1.  `biobakery_env` 환경을 활성화한다.
    ```sh
    conda activate biobakery_env
    ```

2. 아래 명령어를 실행한다.
    ```sh
    biobakery_workflows wmgx --input $INPUT_DIR --output $OUTPUT_DIR
    ```
   - A set of fastq (or fastq.gz) files (single-end or paired-end). 
   - The files are expected to be named `SAMPLE.fastq.gz`,`SAMPLE.R1.fastq.gz`, or `SAMPLE.R2.fastq.gz` where `SAMPLE` is the sample name or identifier corresponding to the sequences. 
   - `SAMPLE` can contain any characters except spaces or periods. The workflow will detect if paired-end files are present. By default the workflow identifies paired end reads based on file names containing ".R1" and ".R2" strings. If your paired end reads have different identifiers, use the option `--pair-identifier`. R1 to provide the identifier string for the first file in the set of pairs.
   - The workflow by default expects input files with the extension "fastq.gz". If your files are not gzipped, run with the option --input-extension fastq.

 
> `Err: ERROR: Unable to find bowtie2 index files in directory: /home/geunhui/biobakery_workflows_databases/kneaddata_db_human_genome`
> - `wget`을 이용하여 데이터베이스를 다운로드 받은 경우, 직접 .tar.gz 파일을 압축해제하여 해당 경로에 위치시킨다. 
>   ``` 
>   tar -xvzf Homo_sapiens_hg37_and_human_contamination_Bowtie2_v0.1.tar.gz
>   ```

> `AttributeError: module 're' has no attribute 'open'`
> - Because of this, I decided to run pipelines one by one.


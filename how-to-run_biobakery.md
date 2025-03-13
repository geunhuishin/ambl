# BioBakery Run Guideline

> For more information, please visit [biobakery_workflows](https://github.com/biobakery/biobakery_workflows).

---

## 시작하기

### Whole Metagenome Shotgun (WMGX) 워크플로우 실행

#### 1. `biobakery_env` 환경 활성화
```sh
conda activate biobakery_env
```

#### 2. 워크플로우 실행
```sh
biobakery_workflows wmgx --input $INPUT_DIR --output $OUTPUT_DIR
```
- 입력 파일: fastq 또는 fastq.gz 형식 (single-end 또는 paired-end)
- 파일명 형식:
  - `SAMPLE.fastq.gz`
  - `SAMPLE.R1.fastq.gz`
  - `SAMPLE.R2.fastq.gz`
- `SAMPLE` 이름에는 공백이나 마침표를 포함할 수 없음
- 기본적으로 `.R1` 및 `.R2`로 페어링을 인식하지만, 다른 형식의 경우 `--pair-identifier` 옵션 사용 가능
- gz 압축되지 않은 fastq 파일의 경우 `--input-extension fastq` 옵션 추가 필요

#### 3. 에러 해결

> **Err:** `ERROR: Unable to find bowtie2 index files in directory: /home/geunhui/biobakery_workflows_databases/kneaddata_db_human_genome`

- `wget`으로 데이터베이스를 다운로드한 경우, 직접 `.tar.gz` 파일을 압축 해제하여 해당 경로에 배치
```sh
tar -xvzf Homo_sapiens_hg37_and_human_contamination_Bowtie2_v0.1.tar.gz
```

> **Err:** `AttributeError: module 're' has no attribute 'open'`

- 해결 방법: 파이프라인을 단계별로 실행

---

## 단계별 실행 (Manual bioBakery_workflows Run) 예시

### 1. Quality Control (KneadData)
```sh
kneaddata --input1 /home/geunhui/temp-rawdata/wmgx/CSM5MCXD_R1.fastq.gz \
          --input2 /home/geunhui/temp-rawdata/wmgx/CSM5MCXD_R2.fastq.gz \
          -db /home/geunhui/biobakery_workflows_databases/kneaddata_db_human_genome \
          --output kneaddata_output
```

### 2. Taxonomic Profiling (MetaPhlAn 4.1)
```sh
metaphlan /home/geunhui/biobakery_workflows_databases/kneaddata_db_human_genome/kneaddata_output/CSM5MCXD_R1_kneaddata_paired_1.fastq \
          --bowtie2out CSM5MCXD.bz2 --nproc 5 --input_type fastq \
          -o profiled_metagenome.txt
```

#### ⚠ MetaPhlAn 설치 시 주의할 점

> **MetaPhlAn 4 데이터베이스는 이전 버전(3.1)보다 크기가 증가하여 최소 15GB RAM이 필요함**
> 
> Conda 환경 내부가 아닌 별도 폴더에 데이터베이스를 설치하는 것이 권장됨:
```sh
metaphlan --install --bowtie2db <database_folder>
```
> **사용 시 반드시 `--bowtie2db <database_folder>` 옵션으로 경로 지정 필요**

---

## MetaPhlAn 4 Database 수동 다운로드 및 설정

> `metaphlan --install`의 경우, 다운로드 중 터미널이 멈추는 현상이 발생할 수 있음. 이를 해결하기 위해 수동으로 데이터베이스를 다운로드할 수 있음.
> **데이터베이스 다운로드 이후 추가로 확인 및 가공이 필요함.** 

### 1. 데이터베이스 다운로드
```sh
axel -n 32 http://cmprod1.cibio.unitn.it/biobakery4/metaphlan_databases/mpa_vOct22_CHOCOPhlAnSGB_202403.md5
axel -n 32 http://cmprod1.cibio.unitn.it/biobakery4/metaphlan_databases/mpa_vOct22_CHOCOPhlAnSGB_202403.tar
axel -n 32 http://cmprod1.cibio.unitn.it/biobakery4/metaphlan_databases/mpa_vOct22_CHOCOPhlAnSGB_202403_marker_info.txt.bz2
axel -n 32 http://cmprod1.cibio.unitn.it/biobakery4/metaphlan_databases/mpa_vOct22_CHOCOPhlAnSGB_202403_species.txt.bz2
axel -n 32 http://cmprod1.cibio.unitn.it/biobakery4/metaphlan_databases/bowtie2_indexes/mpa_vOct22_CHOCOPhlAnSGB_202403_bt2.md5
axel -n 32 http://cmprod1.cibio.unitn.it/biobakery4/metaphlan_databases/bowtie2_indexes/mpa_vOct22_CHOCOPhlAnSGB_202403_bt2.tar
```

### 2. MD5 체크섬 확인
```sh
md5sum -c mpa_vOct22_CHOCOPhlAnSGB_202403.md5
md5sum -c mpa_vOct22_CHOCOPhlAnSGB_202403_bt2.md5
```
- 결과 예시: `mpa_vOct22_CHOCOPhlAnSGB_202403.tar: OK`

### 3. `.tar` 파일 압축 해제
```sh
tar -xvf mpa_vOct22_CHOCOPhlAnSGB_202403.tar
tar -xvf mpa_vOct22_CHOCOPhlAnSGB_202403_bt2.tar
```

### 4. `.bz2` 파일 압축 해제
```sh
bunzip2 mpa_vOct22_CHOCOPhlAnSGB_202403_marker_info.txt.bz2
bunzip2 mpa_vOct22_CHOCOPhlAnSGB_202403_species.txt.bz2
bunzip2 mpa_vOct22_CHOCOPhlAnSGB_202403_VSG.fna.bz2
bunzip2 mpa_vOct22_CHOCOPhlAnSGB_202403.fna.bz2
```

### 5. Bowtie2 인덱스 생성
```sh
bowtie2-build --threads 4 -f mpa_vOct22_CHOCOPhlAnSGB_202403.fna mpa_vOct22_CHOCOPhlAnSGB_202403
```
> 인덱스 생성에는 시간이 오래 걸릴 수 있음.

---
## Reference
- [bioBakery Workflows](https://github.com/biobakery/biobakery_workflows)
- [MetaPhlAn](https://github.com/biobakery/MetaPhlAn)
- [KneadData](https://github.com/biobakery/kneaddata)

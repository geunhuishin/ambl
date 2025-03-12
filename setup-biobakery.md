BioBakery Setup Guide using Conda
===================

> This document is for the setup of the bioBakery Workflows. I've encountered some errors during the installation process and added the solutions to them.
> For more information, please find [biobakery_workflows](https://github.com/biobakery/biobakery_workflows).


## 시작하기

------------------

## 요구사항

1.  [AnADAMA2](https://github.com/biobakery/anadama2) (installed
    automatically)
2.  Python v2.7+
3.  See individual workflows and tasks for additional software
    requirements.

## (해결된 오류 및) 설치 과정

### 소프트웨어 설치

#### Conda를 이용한 bioBakery Workflows 설치
```sh
conda create -n biobakery_env
conda install -c biobakery biobakery_workflows
```
- 위 명령어는 Kneaddata, MetaPhlAn2 등 거의 모든 워크플로우의 종속성을 포함하여 설치함.
- **단, 일부 데이터베이스는 수동으로 설치해야 함.** (*두 번째 에러 참조.)
- Conda 채널 우선순위 설정:
    ```sh
    conda config --add channels biobakery
    ```

### 데이터베이스 설치 중 발생한 오류 및 해결 방법

#### (일반적인) 데이터베이스 다운로드 방법
```sh
biobakery_workflows_databases --install $WORKFLOW
```
- 사용자의 의도에 따라서 필요한 데이터베이스를 다운로드할 수 있다.
- 그렇지만 데이터베이스 다운로드 중 터미널이 멈추는 현상이 발생할 수 있다. 이를 해결하기 위해 수동으로 데이터베이스를 다운로드할 수 있다. (*세 번째 에러 참조.)

#### 오류 및 해결 방법

1. `ModuleNotFoundError: No module named 'leveldb'`
**해결 방법:**
```sh
conda install -c conda-forge python-leveldb
```

2. `Unable to find strainphlan install`
**해결 방법:**
```sh
metaphlan --install
```
이후 `biobakery_workflows_databases --install wmgx`를 다시 실행.

3.  ```데이터베이스 다운로드 중 터미널 멈춤 현상```
    - 일부 데이터베이스 다운로드 과정에서 터미널이 멈추는 경우 발생. 이를 해결하기 위해 수동 다운로드 진행.

### 수동 데이터베이스 다운로드
#### ```wmgx``` 워크플로우를 실행하기 위해 필요한 데이터베이스 및 설치 위치:

1. **HUMAnN Utility Mapping Database**
   ```sh
   humann_databases --download utility_mapping full $INSTALL_LOCATION
   ```
   - 📂 저장 위치: `$INSTALL_LOCATION/humann/utility_mapping/`


2. **HUMAnN ChocoPhlAn (Nucleotide Reference Genomes)**
   ```sh
   humann_databases --download chocophlan full $INSTALL_LOCATION
   ```
   - 📂 저장 위치: `$INSTALL_LOCATION/humann/chocophlan/`


3. **HUMAnN UniRef90 (Protein Database)**
   ```sh
   humann_databases --download uniref uniref90_diamond $INSTALL_LOCATION
   ```
   - 📂 저장 위치: `$INSTALL_LOCATION/humann/uniref/`


4. **Kneaddata Human Genome Database**
   ```sh
   kneaddata_database --download human_genome bowtie2 $INSTALL_LOCATION
   ```
   - 📂 저장 위치: `$INSTALL_LOCATION/kneaddata/human_genome/`


5. **StrainPhlAn Markers Database** (Strain-level profiling을 위한 마커 유전자)
   ```sh
   bowtie2-inspect <strainphlan_db> > $INSTALL_LOCATION/strainphlan/all_markers.fasta
   ```
   - 📂 저장 위치: `$INSTALL_LOCATION/strainphlan/`


### Whole Metagenome & Metatranscriptome Shotgun (wmgx_wmtx)

>```wmgx_wmtx```는 ```wmgx```의 모든 데이터베이스를 포함하며, 추가적인 metatranscriptome 관련 kneaddata 데이터베이스가 필요함:

1. **Kneaddata rRNA Database** (Ribosomal RNA 제거)
   ```sh
   kneaddata_database --download ribosomal_RNA bowtie2 $INSTALL_LOCATION
   ```
   - 📂 저장 위치: `$INSTALL_LOCATION/kneaddata/ribosomal_RNA/`

2. **Kneaddata Human Transcriptome Database** (Human mRNA 제거)
   ```sh
   kneaddata_database --download human_transcriptome bowtie2 $INSTALL_LOCATION
   ```
   - 📂 저장 위치: `$INSTALL_LOCATION/kneaddata/human_transcriptome/`


### 기본 데이터베이스 저장 경로 (디폴트 설정 시)
- 기본적으로 데이터베이스는 `~/biobakery_workflows_databases/` 디렉토리에 설치됨.
- 사용자가 `--location` 옵션을 제공하면 해당 경로로 변경 가능.

**예시 경로 (디폴트 설정일 경우)**:
```
~/biobakery_workflows_databases/
├── humann/
│   ├── utility_mapping/
│   ├── chocophlan/
│   ├── uniref/
├── kneaddata/
│   ├── human_genome/
│   ├── ribosomal_RNA/   (wmgx_wmtx 추가)
│   ├── human_transcriptome/   (wmgx_wmtx 추가)
├── strainphlan/
```


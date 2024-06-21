# TFP-Bert-Sexual
BERT를 활용한 선정적, 학교 폭력 표현 분류 모델 - 전유정 


## 실행 방법 
1. git clone
2. NLP_성적, 학교폭력 대화 판별_최종 구현.ipynb 코랩에서 실행
  
## 모델 학습부터 실행하는 경우,
- **3. 데이터셋 다운로드**: 텍스트 윤리검증 데이터(https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&dataSetSn=558) 데이터셋 다운로드,
- **4. 테스트 데이터셋 다운로드**: 텍스트 윤리검증 테스트 데이터에 추가로 직접 8,600개를 증량한 데이터셋 (https://docs.google.com/spreadsheets/d/1UCrK286ScyTM0G7aUdRlhi8yEUmhOqsC/edit?usp=sharing&ouid=106613437507282921508&rtpof=true&sd=true) 다운로드,
- **4. 실행 코랩 링크 접속**: 코랩 세션 저장소에 데이터셋 업로드
  
- **5. 런타임 실행**

  
## 모델 다운로드 및 실행 

- **3. Model download Link**: https://drive.google.com/drive/folders/1d7UMQ-M65E0mKAD8Ag2eAN16k7diLfrV?usp=drive_link
  (깃허브의 대용량 파일 업로드 제한으로 구글 드라이브 링크를 통해 다운로드 해야 합니다.)
  
- **4.구글 드라이브에 모델 저장**: 구글 드라이브 '/gdrive/MyDrive/' 해당 경로에 final_stage1_model.pth, final_stage2_model.pth 두개의 모델 저장

- **5. 실행 코랩 링크 접속**: '모델 파일 로드 후 예측 함수' 타이틀 아래 셀 실행 


<br>

## Summary 
- 허깅페이스(Hugging Face)에서 제공하는 transformer 라이브러리를 활용하여 한국어 BERT로 텍스트 대화 중에서 일반 텍스트, 성적 텍스트, 학교 폭력 텍스트를를 분류했습니다. <br>

- 초기에는 선정적 대화 이진 분류로 모델을 학습 시키고자 했으나, 이진 분류만 하기에 데이터 셋의 크기가 컸기 때문에 성적 대화 뿐만 아니라 학교 폭력 대화까지 총 3가지의 대화를 클래스 별로 분류했습니다. <br>
- 학교 폭력의 경우, VIOLENCE, CRIME, ABUSE 총 3가지의 데이터셋 라벨을 '학교 폭력 데이터'로 추출하였으며, 이를 각각 훈련과 테스트 데이터에 결합시켰습니다.

- 하나의 모델에서 3개의 클래스를 학습 시키는 BERT의 다중 클래스 분류 학습을 진행했었지만, 성능이 좋지 않았습니다.
+ 성적 대화 오분류 최소화 --> **2차례의 이진 분류 학습**을 통해 선정적 대화만 1차 분류 학습 진행 후, 선정적 대화가 아닌 텍스트를 일반 대화와 학교 폭력 대화 이진 분류하는 2차 분류 학습을 진행했습니다.
- 최종적으로 선정적, 일반, 학교 폭력 세 가지 대화 유형을 분류하는 모델을 구현했습니다.

## Data Set
### 1. Data Set
이 연구는 과학기술정보통신부의 재원으로 한국지능정보사회진흥원의 지원을 받아 구축된 "텍스트 윤리 검정 데이터"을 활용하여 수행된 연구입니다. 본 연구에 활용된 데이터는 AI 허브(https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&dataSetSn=558)에서 다운로드 받으실 수 있습니다. 

AIHUB에서 제공하는 '텍스트 윤리검증 데이터'의 대화 유형 별 분류는 다음과 같습니다.

데이터셋 구조는 다음과 같습니다. 
|종류|칼럼|
|:---:|:---:|
|비윤리성 판단 정보|1|
|비윤리 유형 정보|2|
|비윤리 강도의 평균|3| 
|비윤리 강도투표자 성별 분포|4|
|텍스트|5|


### 2. Test Data Set
위 데이터 셋의 비윤리성 판단 정보(CENSURE, HATE, DISCRIMINATION, SEXUAL, ABUSE, VIOLENCE, CRIME, IMMORAL_NONE) 중 선정적 (SEXUAL), 일반 (IMMORAL_NONE), 학교폭력(ABUSE, VIOLENCE, CRIME)으로 필요한 라벨만 각색함
중복 표현, 초성, 이모티콘 등 제거하여 Okt 모델을 통한 토큰화를 통한 데이터 전처리 진행 <br>
1차 이진 분류에서 일반, 학교 폭력 문장과 선정적 문장을 분류해야하지만, 선정적 (SEXUAL) 문장이 부족하여 기존 테스트 데이터 셋에 8,600개를 추가함. 

|종류|라벨|개수|
|:---:|:---:|:---:|
|선정적|0|13,489|
|일반|1|6,700|
|학교 폭력|2|6,700|  

= **총 26,889 문장**

### 2. Train Data Set
기존 데이터셋의 라벨 개수가 불균형했기 때문에, 
선정적 (SEXUAL) 문장의 최대 개수인 40,907개에 맞추어 일반과 학교 폭력 데이터를 41,000개로 훈련 데이터셋을 구성했습니다.  
|종류|개수|
|:---:|:---:|
|선정적|40,907|
|일반|41,000|
|학교폭력|41,000|

= **총 12,2907 문장**

<br>

## System Evaluation
- 허깅페이스(Hugging Face)에서 제공하는 transformer 라이브러리를 활용하여 BERT로 3개의 대화 클래스 분류 모델 개발
- 훈련 데이터 12,2907문장 중 80%를 훈련 데이터로 사용했으며, 테스트 데이터는 26,889문장 사용
### loss, accuracy
![image](https://github.com/TwitFilterProject/TFP-Bert-Sexual/assets/109844803/2d02fccf-07d5-423c-8841-ca992ed6f166)
### f1-score
![image](https://github.com/TwitFilterProject/TFP-Bert-Sexual/assets/109844803/6a4e1af6-7ff4-468d-8d2d-45c21371daf2)

### Stage 1 Classification Report (SEXUAL (성적 문장) vs NON-SEXUAL (일반적 문장, 학교 폭력 문장))

|              | Precision | Recall | F1-Score | Support |
|--------------|-----------|--------|----------|---------|
| NON-SEXUAL   | 0.77      | 0.90   | 0.83     | 14961   |
| SEXUAL       | 0.84      | 0.66   | 0.74     | 11928   |
| **Accuracy** |           |        | **0.79** | **26889**   |
| Macro avg    | 0.80      | 0.78   | 0.78     | 26889   |
| Weighted avg | 0.80      | 0.79   | 0.79     | 26889   |

### Stage 2 Classification Report (IMMORAL_NONE (일반적 문장) vs VIOLENCE (학교 폭력 문장))

|              | Precision | Recall | F1-Score | Support |
|--------------|-----------|--------|----------|---------|
| IMMORAL_NONE | 0.85      | 0.90   | 0.88     | 6700    |
| VIOLENCE     | 0.91      | 0.88   | 0.89     | 8261    |
| **Accuracy** |           |        | **0.89** | **14961**   |
| Macro avg    | 0.88      | 0.89   | 0.88     | 14961   |
| Weighted avg | 0.89      | 0.89   | 0.89     | 14961   |



학습 손실이 점진적으로 감소하고, 학습 정확도가 점진적으로 증가했음.
- 1단계 성적 문장 분류의 성능 지표의 경우 정밀도(0.84)가 높아 성적 문장을 정확하게 예측했고, 재현율(0.66)이 낮지만, 정확도(0.79)로 중간 이상의 성능을 보임.
- 2단계 학교폭력 문장 분류의 성능 지표의 경우 정밀도와 재현율, 정확도(0.89)에서 모두 높은 성능을 보임.

<br>

## Output View
![image](https://github.com/TwitFilterProject/TFP-Bert-Sexual/assets/109844803/7b7dc253-3be5-4fc1-bbaf-e9a233b001d5)
저장한 모델을 로드하여 

<br>

<br>

## License
본 프로젝트에서는 다음과 같은 오픈 소스 라이브러리를 사용하고 있으며, 각 라이브러리의 라이센스 조건을 준수합니다:

- `pandas`: BSD-3-Clause
- `numpy`: BSD-3-Clause
- `matplotlib`: BSD-3-Clause 
- `scikit-learn`: BSD-3-Clause
- `PyTorch`: BSD-3-Clause
- `Transformers by Hugging Face`: Apache License 2.0
- `pillow (PIL)`: HPND
- `konlpy`: MIT, GPLv3

****
BSD-3-Clause
- Copyright 2024 전유정
Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS “AS IS” AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
****
HPND
Cleans

Copyright (c) 2024 전유정. All rights reserved.

Permission to use, copy, modify, and distribute this software for any
purpose with or without fee is hereby granted, provided that the above
copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR
ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF
OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
****
MIT
Copyright (C) 2024 name of 전유정
 
This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.
 
This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.
 
You should have received a copy of the GNU General Public License
along with this program. If not, see <http://www.gnu.org/licenses/>
***
Apache License 2.0

copyright 2024 전유정
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.


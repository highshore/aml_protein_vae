### VHSE embedding VAE
VHSE embedding 을 사용하는 VAE 를 업로드 하였습니다.   
VHSE embedding 은 각 아미노산을 화학적 성질에 따라서 8차원의 벡터로 표현하는 embedding 입니다.   
Embedding의 결과로 8*(protein seq length) 의 array 가 얻어집니다.   
VAE 역시 8*(protein seq length 의 array) 를 reconstruct 합니다.  
Reconstruct 된 8*(protein seq length)의 각 column vector를 가장 가까운 아미노산(cosing similarity)으로 바꾸어 주면 단백질 서열 string을 얻을 수 있습니다.   

하지만 실행해보면 코드의 성능은 매우 나쁩니다. (40 epoch 기준)
60% 정도의 sequence 일치율를 보여주는데, 앞쪽의 padding을 고려하면 실제 일치율은 절반도 못 미칠 듯 합니다.   
100 epoch training 하니 결과가 좀 나아져서, 나중에 아예 500,1000 epoch training 해보아도 좋을 것 같네요.

아 근데, 서열들이 전체적으로 너무 비슷하네요. 
Interpolate 할 만큼 서열이 다르지 않아요.  
데이터셋의 서열 다양성을 높이고 데이터셋 사이즈도 키워서 해봐야겠어요.
앞으로 많은 부분을 고쳐야겠습니다.일단 오늘은 여기까지.  

생각해볼 문제: 
1. 현재는 reconstruction loss를 8*(protein seq length)만을 가지고 계산하는데,
   numerical array가 아닌 단백질 서열 string를 가지고 계산하면 최종적인 서열을 더 잘 reconstruct 할 수 있지 않을까?
2. 위 질문에 이어서 8*(protein seq length)의 각 column vector 의 cosing similarity를 가지고 계산했는데 차라리 neural network를 사용하여 VAE 의 output 이 string 이도록 하는 것이 나은가? 
3. padding 이 너무 많으니 padding 대신 cropping 하여 train을 해볼까?
4. ADAM Optimizer  적용이 아직 안된 것 같다.
5. Reconstruct 할 때 protein 서열 중간에 padding 을 넣는 경우가 있는데 이를 방지해야한다. 


### 10월 12일 수정사항 by 김수겸 ###
1. 각자 git clone할 때 편리하게 패키지 다운받을 수 있도록 requirements.txt 생성
2. full code 코드가 돌아갈 때 상황 파악을 위해서 print statement를 곳곳에 추가
3. 기존 코드는 Blast 검색을 할 때 결과 개수에 따라 오류가 날 수 있음 => 결과가 없을 때, 하나일 때, 여러 개일 때를 대비해 케이스 바이 케이스 핸들링 추가 (코드 후반부에 영향 없음, 앞부분만 작업)
4. **다중 단백질 분석 지원**: 이제 여러 UniProt ID를 한 번에 처리 가능

### 다중 단백질 사용 방법 ###
`full_code.py`의 23-29줄에서 `uniprot_ids` 리스트를 수정하세요:

```python
uniprot_ids = [
    "A0A251P855",  # Aquaporin
    "P68871",      # Hemoglobin subunit beta
    "P01308",      # Insulin
]
```

- 단백질을 추가하려면: 리스트에 새로운 UniProt ID를 추가
- 단일 단백질만 사용하려면: 하나의 ID만 남기면 됨
- 모든 단백질의 BLAST 결과가 합쳐져서 100개의 고유한 서열을 수집함
- 중복 제거 자동 처리 (같은 accession ID는 한 번만 수집)
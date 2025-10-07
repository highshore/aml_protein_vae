### VHSE embedding VAE
VHSE embedding 을 사용하는 VAE 를 업로드 하였습니다.   
VHSE embedding 은 각 아미노산을 화학적 성질에 따라서 8차원의 벡터로 표현하는 embedding 입니다.   
Embedding의 결과로 8*(protein seq length) 의 array 가 얻어집니다.   
VAE 역시 8*(protein seq length 의 array) 를 reconstruct 합니다.  
Reconstruct 된 8*(protein seq length)의 각 column vector를 가장 가까운 아미노산(cosing similarity)으로 바꾸어 주면 단백질 서열 string을 얻을 수 있습니다.   

하지만 실행해보면 코드의 성능은 매우 나쁩니다. 
60% 정도의 sequence 일치율를 보여주는데, 앞쪽의 padding을 고려하면 실제 일치율은 절반도 못 미칠 듯 합니다.   
앞으로 많은 부분을 고쳐야겠습니다.  

생각해볼 문제: 
1. 현재는 reconstruction loss를 8*(protein seq length)만을 가지고 계산하는데,
   numerical array가 아닌 단백질 서열 string를 가지고 계산하면 최종적인 서열을 더 잘 reconstruct 할 수 있지 않을까?
2. 위 질문에 이어서 8*(protein seq length)의 각 column vector 의 cosing similarity를 가지고 계산했는데 차라리 neural network를 사용하여 VAE 의 output 이 string 이도록 하는 것이 나은가? 
3. padding 이 너무 많으니 padding 대신 cropping 하여 train을 해볼까?


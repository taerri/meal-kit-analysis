# meal-kit-analysis

## Topic
사회 구조의 변화와 시대적 트렌드가 반영되어 밀키트 제품이 발전하여 밀키트 산업이 성장함       
=> 보완관계 파악을 통해 소비자에게는 만족을 주고, 기업에게는 마케팅에 유용한 자료로 활용될 수 있을 것이며,         
새로운 트렌드나 신사업 예측
  
### 사용 데이터
BigKinds 뉴스 데이터        
  -키워드: 밀키트        
  -날짜: 2017.01.01 ~ 2021.08.08
  
  
## Research Model

### 1. 데이터 수집

#### 1-1 빅카인즈 크롤링
![image](https://user-images.githubusercontent.com/88631078/131560714-031871e3-cecb-47f7-8082-828f39c0dcb7.png)

### 2. 데이터 전처리
Mecab을 사용하여 형태소 분리

#### 2-1 명사 추출
```
from tqdm import tqdm
nouns = [] 
for x in tqdm(content):
    try:
        nouns.append(mecab.nouns(x))
    except:
        nouns.append(["None"])
```

#### 2-2 단어 교체
```python
def change(text):
    text=','.join(text)
    text_list=text.split(',')
    result=[]
    for c in text_list:
        if c=='11번':
            c=c.replace('11번','11번가')
        if c=='인공지능':
            c=c.replace('인공지능','AI')
        result.append(c)
    result=','.join(result)
    return result
```

#### 2-3 불용어 처리
```python
stopword1 = pd.read_csv("./불용어 처리.txt",header=None,sep="\t")
stopword1 = list(stopword1[0])
stopword2 = pd.read_excel("단어 삭제.xlsx")
stopword2.columns =["단어","불용어여부"]
stopword2 = stopword2[stopword2.불용어여부 == "x"]
stopword2 = list(stopword2["단어"])
stopword = list(set(stopword1 + stopword2))
stopword = [x for x in stopword if not re.compile("[0-9]+").search(str(x))]
print("불용어 개수 :",len(stopword))
```

#### 2-4 전처리 결과값
![image](https://user-images.githubusercontent.com/88631078/131561534-22cbbc54-4c63-4a96-b4e8-eebeb91106a8.png)


### 3. 토픽모델링

#### 3-1 LDA 코드
```python
dictionary = corpora.Dictionary(model_all.corpus)
corpus = [dictionary.doc2bow(text) for text in model_all.corpus]
NUM_TOPICS = 3
num_words = 30
ldamodel = gensim.models.ldamodel.LdaModel(corpus, num_topics = NUM_TOPICS, id2word=dictionary, passes=15)
topics = ldamodel.print_topics(num_words=num_words)
for topic in topics:
    print(topic)
```

#### 3-2 LDA 시각화
![KakaoTalk_20210902_011546404](https://user-images.githubusercontent.com/88631078/131706978-52794de4-b79d-47d4-afa0-5d9dbf3252bd.gif)

#### 3-3 토픽모델링 시각화 코드
```python
fig =go.Figure(go.Sunburst(
    labels=first_list + topic_1_1 + topic_2_1 + topic_3_1 + topic_4_1 + topic_5_1 + topic_6_1 + topic_7_1 + topic_8_1,
    parents=second_list + topic_1_2 + topic_2_2 + topic_3_2 + topic_4_2 + topic_5_2 + topic_6_2 + topic_7_2 + topic_8_2,
    values=value_list + topic_1_3 + topic_2_3 + topic_3_3 + topic_4_3 + topic_5_3 + topic_6_3 + topic_7_3 + topic_8_3,
    branchvalues="total"
))
fig.update_traces(textfont_size=20)


fig.update_layout(margin = dict(t=0, l=0, r=0, b=0))
fig.show()
```

#### 3-4 토픽모델링 시각화
![image](https://user-images.githubusercontent.com/88631078/131688973-6d418982-28dd-4f50-93a6-4b4e54245789.png)

### 4. 네트워크 분석

#### 4-1 n-gram 분석 코드
```python
def ngrams(lst, n):
    tlst = lst
    while True:
        a, b = tee(tlst)
        l = tuple(islice(a,n))
        if len(l) == n:
            yield l
            next(b)
            tlst = b
        else:
            break
    return tlst
```

#### 4-2 n-gram 분석 결과
![image](https://user-images.githubusercontent.com/88631078/131562509-a94066ab-005d-46b5-98ae-fa1fa5b74a06.png)

#### 4-3 페이지랭크
```python
pg=nx.pagerank(a,max_iter=100)
```

#### 4-4 페이지랭크 분석 결과
![image](https://user-images.githubusercontent.com/88631078/131562991-81d6463e-0701-4cc5-a8dd-d969b9cd068a.png)

#### 4-5 페이지랭크 시각화
![image](https://user-images.githubusercontent.com/88631078/131563193-9cdfaf73-472d-444b-880e-ce68f75f1ec5.png)

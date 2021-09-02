# meal-kit-analysis

## Topic
사회 구조의 변화와 시대적 트렌드가 반영되어 밀키트 제품이 발전하여 밀키트 산업이 성장함       
=> 보완관계 파악을 통해 소비자에게는 만족을 주고, 기업에게는 마케팅에 유용한 자료로 활용될 수 있을 것이며,         
새로운 트렌드나 신산업 예측
  
### 사용 데이터
BigKinds 뉴스 데이터        
  -키워드: 밀키트        
  -날짜: 2017.01.01 ~ 2021.08.08
  
  

## 00_빅카인즈_밀키트_크롤링

### 1. 데이터 수집

#### 1-1 빅카인즈 크롤링
- selenium 설치 (pip install selenium)
- xmltodict 설치 (pip install xmltodict)   
- 자신의 chrome 버전 확인     
- 버전에 맞는 chromedriver 다운     
https://sites.google.com/a/chromium.org/chromedriver/downloads     
- window - c:/chromedriver로 이동     
  mac -  /Users/romeoh/download로 이동     

#### 1-2 크롤링 결과 
![image](https://user-images.githubusercontent.com/88631078/131560714-031871e3-cecb-47f7-8082-828f39c0dcb7.png)

## 01_빅카인즈_밀키트_크롤링_데이터_병합



## 02-1_빅카인즈_밀키트_전처리_Kkma(본 과정은 CoLab으로 실행)

### 2. 데이터 전처리
Kkma을 사용하여 형태소 분리

- konlpy 설치 (pip install konlpy)
- customized_konlpy 설치 (pip install customized_konlpy)  


크롤링 결과 병합 후 중복되는 기사를 삭제해 5507개의 뉴스 기사 데이터를 얻음
```python
df_drop=df.drop_duplicates(['Date','Title','Content'], keep='first')
```

본문 내용이 없는 기사 확인 및 삭제    
최종적으로 5506개의 뉴스 기사 데이터를 얻음
```python
drop_row = content.dropna(axis=0)
df=pd.DataFrame([drop_row])
df= df.transpose()
```

#### 2-1 kkma 사용자 사전 추가
명사 추출 결과를 보면, 밀키트 등 여러 고유명사들이 '밀', '밀키트', '키트' 처럼 분리되어 추출    
--> 고유명사나 Kkma에 없는 단어들을 사전에 추가
```python
twitter.add_dictionary('밀키트', 'Noun')
twitter.add_dictionary('코로나19', 'Noun')
twitter.add_dictionary('간편식', 'Noun')
twitter.add_dictionary('프레시지', 'Noun')
twitter.add_dictionary('식재료', 'Noun')
```


#### 2-2 형태소 분석(명사 추출)
```python
nouns = [] 
for x in tqdm(content):
    try:
        nouns.append(kkma.nouns(x))
    except:
        nouns.append(["None"])
```

#### 2-3 정규화
의미적으로 같은 단어를 한 단어로 바꿔줌

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

#### 2-4 한글자 제거
'년', '월' 등 분석 시 불필요한 한글자 단어 제거

```python
def remove(text):
    text_list=text.split(',')
    result=[]
    for n in text_list:
        if len(n)>1:
            result.append(n)
    result=','.join(result)
    return result

corpus['ContentNoun']=corpus['ContentNoun'].map(lambda x: remove(x))
```

#### 2-5 불용어 처리
'100%', '3개월' 등 불필요한 단어의 불용어 처리

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

#### 2-6 전처리 결과값
![image](https://user-images.githubusercontent.com/88631078/131730865-76416561-1735-493d-bd98-23ed7f8f84bf.png)

#### ※ bigkinds에서 전처리 키워드를 제공하여, 이후부터는 해당 데이터(밀키트 뉴스데이터.csv)를 사용함.

## 02-2_빅카인즈_전처리 키워드_추가 전처리

#### 2-7 추가 전처리

빅카인즈에서 제공한 키워드(밀키트 뉴스데이터.csv)에 대해 연구목적에 맞게 추가적으로 전처리를 진행함


## 03-1_빅카인즈_밀키트_LDA_Topic Modeling

### 3. 토픽모델링

토픽모델링 전 주요 단어 빈도 확인

#### 3-1 LDA 토픽모델링

- gensim 설치 (pip install gensim)
- pyLDAvis 설치 (pip install pyLDAvis)

```python
dictionary = corpora.Dictionary(model_all.corpus)
corpus = [dictionary.doc2bow(text) for text in model_all.corpus]
NUM_TOPICS = 9
num_words = 30
ldamodel = gensim.models.ldamodel.LdaModel(corpus, num_topics = NUM_TOPICS, id2word=dictionary, passes=15)
topics = ldamodel.print_topics(num_words=num_words)
for topic in topics:
    print(topic)
```

#### 3-2 LDA 토픽모델링 시각화
![KakaoTalk_20210902_011546404](https://user-images.githubusercontent.com/88631078/131706978-52794de4-b79d-47d4-afa0-5d9dbf3252bd.gif)


## 03-2_빅카인즈_밀키트_LDA_Topic Modeling_고급

#### 3-3 토픽모델링 시각화 코드 및 시각화
*conda install -c plotly plotly  콘다프롬포트에 이렇게 설치 필요하다     
*주피터노트북으로 실행하면 본 화면과 다르게 보일 수 있다. 본 화면은 vscode로 실행한 결과이다.

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

![image](https://user-images.githubusercontent.com/88631078/131688973-6d418982-28dd-4f50-93a6-4b4e54245789.png)


## 04-1_빅카인즈_밀키트_네트워크 분석_N-gram

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

## 04-2_빅카인즈_밀키트_네트워크 분석_PageRank(본 과정은 CoLab으로 실행)

#### 4-3 페이지랭크
```python
pg=nx.pagerank(a,max_iter=100)
```
N-gram 분석 후 상위 250여개 데이터(n-gram_분석결과.csv)를 살펴 본 후, 분석 목적에 맞게 수동으로 데이터 수정(n-gram_분석결과_전처리.csv)
이후 수정된 데이터를 페이지랭크 분석에 활용함

#### 4-4 페이지랭크 분석 결과
![image](https://user-images.githubusercontent.com/88631078/131562991-81d6463e-0701-4cc5-a8dd-d969b9cd068a.png)

#### 4-5 페이지랭크 시각화
![image](https://user-images.githubusercontent.com/88631078/131563193-9cdfaf73-472d-444b-880e-ce68f75f1ec5.png)

페이지랭크 그래프가 랜덤으로 추출되니 마음에 드는 모양이 나올 때까지 반복 실행

## Authors

* **TaeRi Kim** - [Github](https://github.com/taerri)
* **JinSol Lim**
* **JiIn Bang**

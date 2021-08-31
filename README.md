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
```
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
```
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

#### 3-2 LDA 시각화

#### 3-3 토픽모델링 시각화
```
import plotly.graph_objects as go

first_list = [ "브랜드", "지역 및 기업 연계", "요리의 제품화", "제품의 고급화", "시장 성장", "코로나 19 이슈", "유통", "트랜드"]
second_list = [ "밀키트", "밀키트", "밀키트", "밀키트", "밀키트", "밀키트", "밀키트", "밀키트"]
value_list = [18, 16, 13, 13, 12, 12, 9, 7]

topic_1_1 = ['시장', '제품', '브렌드', '한국야쿠르트', '프레시지', '이마트', '피코크', '심플리쿡', 'CJ제일제당', '잇츠온']
topic_1_2 = ['브랜드', '브랜드', '브랜드','브랜드', '브랜드', '브랜드', '브랜드', '브랜드', '브랜드','브랜드']
topic_1_3 = [3.7, 3.5, 2, 1.6, 1.5, 1.4, 1.3, 1.2, 1, 0.8]

topic_2_1 = ['지원', '지역', '사업', '밀키트1', '소상공인', '기업', '창업', '참여', '운영']
topic_2_2 = ['지역 및 기업 연계', '지역 및 기업 연계', '지역 및 기업 연계', '지역 및 기업 연계','지역 및 기업 연계','지역 및 기업 연계','지역 및 기업 연계','지역 및 기업 연계','지역 및 기업 연계']
topic_2_3 = [3.7, 2.5, 2.1, 1.8, 1.6, 1.3, 1.2, 1.1, 0.7]

topic_3_1 = ['요리', '음식', '메뉴', '재품', '출시', '인기', '레스토랑', '호텔', '편스토랑']
topic_3_2 = ['요리의 제품화', '요리의 제품화', '요리의 제품화', '요리의 제품화', '요리의 제품화', '요리의 제품화', '요리의 제품화', '요리의 제품화', '요리의 제품화']
topic_3_3 = [3, 2.5, 2, 1.8, 1.4, 1.1, 0.6, 0.3, 0.3]

topic_4_1 = ['상품', '판매', '할인', '선물', '세트', '행사', '명절', '이벤트', '크리스마스', '산천어']
topic_4_2 = ['제품의 고급화', '제품의 고급화', '제품의 고급화', '제품의 고급화', '제품의 고급화', '제품의 고급화', '제품의 고급화', '제품의 고급화', '제품의 고급화', '제품의 고급화']
topic_4_3 = [2.5, 2, 1.7, 1.6, 1.5, 1.3, 0.9, 0.7, 0.4, 0.4]

topic_5_1 = ['식품', '투자', '사업2', '기업2', '성장', '스타트업', '온라인', '확대', '플랫폼', '오프라인', '규모']
topic_5_2 = ['시장 성장', '시장 성장', '시장 성장', '시장 성장', '시장 성장', '시장 성장', '시장 성장', '시장 성장', '시장 성장', '시장 성장', '시장 성장']
topic_5_3 = [2.1, 1.9, 1.6, 1.6, 0.9, 0.9, 0.8, 0.7, 0.6, 0.5, 0.4]

topic_6_1 = ['매출', '증가', '코로나 19', '대비', '온라인1', '소비', '주문', '판매1', '급증', '마켓컬리', '밀키트2']
topic_6_2 = ['코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈', '코로나 19 이슈']
topic_6_3 = [2.6, 2.2, 1.7, 1.5, 1.3, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2]

topic_7_1 = ['배송', '서비스', '주문1', '고객','물류','신선','새벽배송','모바일','정기']
topic_7_2 = ['유통','유통','유통','유통','유통','유통','유통','유통','유통']
topic_7_3 = [2.6, 2.5, 1.2, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2]

topic_8_1 = ['공간', '트렌드', '변화', '시대', '가전', '문화', '영상', '세대', '여행']
topic_8_2 = ['트랜드','트랜드','트랜드','트랜드','트랜드','트랜드','트랜드','트랜드','트랜드']
topic_8_3 = [1.5, 1.3, 1.2, 1, 0.7, 0.5, 0.4, 0.3, 0.1]

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

### 4. 네트워크 분석

#### 4-1 n-gram 분석 코드
```
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
```
pg=nx.pagerank(a,max_iter=100)
```

#### 4-4 페이지랭크 분석 결과
![image](https://user-images.githubusercontent.com/88631078/131562991-81d6463e-0701-4cc5-a8dd-d969b9cd068a.png)

#### 4-5 페이지랭크 시각화
![image](https://user-images.githubusercontent.com/88631078/131563193-9cdfaf73-472d-444b-880e-ce68f75f1ec5.png)

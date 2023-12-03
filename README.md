## 빅데이터 프로젝트
---
## 주제 : 대중교통 다양한 통계 시각화
### 내용
- 행정동별 대중 교통 승객수 조회 통계 (약 156개의 인천 정류장중에서 승객수가 가장 많은 top 20개만 조회)
- 버스 이용 승객들 분류(버스를 이용하는 어린이,청소년,대학생,경로우대,장애인 승객수)
- 버스에서 버스로 환승할때나 버스에서 지하철로 환승할때의 다양한 환승 이용 통계(분류방식은 위랑 동일)
- 택시를 탈때 가장 많이 타는 지역 또는 가장 많이 내리는 지역들 통계 순위 (top 20)
- 택시들이 많이 다니는 집중되는 지역 통계

---
### 진행 상황
공공데이터 시스템에 있는 api기능을 이용하여 내가 원하는 데이터를 csv에 저장한다.
(코렙을 사용해 직접 크롤링 하는 방법은 ssh오류인해 진행이 안되었기 때문에 이 방법은 아직 보류 )
* 만약 코렙으로 크롤링 하는 방법이 성공시 이쪽으로 csv데이터를 가져옴
---
### (예정)
구글 코렙을 사용하여 가져온 데이터 들을 시각화 한다.
주로 그래프를 만들어서 통계를 보여주는 시각화를 할 예정
이 모든 작업은 나중에 깃허브에 올릴 예정
시간이 남게 되면 다른 교통데이터도 알아볼예정
---

## 서론
+ 이 주제를 선택한 이유
    + 학교를 다니거나 어디 외출할때 사람들은 직접 차가 있지 않는이상 걸어가기 힘든 위치로 외출할때 택시나 버스같은 대중교통을 이용합니다. 
    + 대중 교통을 이용을 할때 항상 사람들이 많은데 어느때에 가장 사람이 많이 대중교통을 이용하는지, 어느 지역에서 사람들이 대중교통을 많이 이용하는지에 대해 궁금하여 이 주제를 선택하였습니다.

+ 데이터 셋
    + 데이터 셋은 공공데이터포털의 인천광역시 대중교통 통계조회 서비스, 인천광역시 택시 운행 통계 조회 서비스 의 open api 를 이용하였습니다.

+ 내용 (데이터는 일일 평균의 데이터를 가져옴), (colab 이용)
    + 행정동별 대중 교통 승객수 조회 통계 (약 156개의 인천 정류장중에서 승객수가 가장 많은 top 20개만 조회)
    + 대중교통 이용 승객들 분류(버스를 이용하는 어린이,청소년,대학생,경로우대,장애인 승객수)
    + 버스에서 버스로 환승할때나 버스에서 지하철로 환승할때의 다양한 환승 이용 통계(분류방식은 위랑 동일)
    + 택시를 탈때 가장 많이 타는 지역 또는 가장 많이 내리는 지역들 통계 순위 (top 20)
    + 택시들이 많이 다니는 집중되는 지역 통계

## 행정동별 대중 교통 승객수 조회 통계
행정동별 대중 교통 승객수 조회 통계 데이터를 받아와 header 부분 5개만 출력

    import pandas as pd
    df=pd.read_csv('/content/행정동별 대중 교통 승객수 조회 통계 .csv')
    df.head()

![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/a6872692-c332-4ed0-97f9-ea3665c978f2)
---
df.nlargest 를 이용하여 wholdValue(일일 평균 승객수) 열에서 가장 많은 값을 가진 20개의 행을 선택

그 20개의 데이터를 이용하여 시각화를 한다. X 는 wholeValue(일일 평균 승객수), y 는 dongName(행정동 이름)을 기준으로  가로막대 그래프로 시각화를 한다.

결과 : 부평구 부평 1동이 가장 많이 버스를 많이 이용을 했다. 두번째로는 중구 운서동

    import matplotlib.pyplot as plt
    import seaborn as sns

    # 'wholeValue' 열에서 가장 많은 값 가지는 상위 20개의 행 선택
    top_20 = df.nlargest(20, 'wholeValue')
    
    # 상위 20개의 데이터를 이용하여 시각화
    plt.figure(figsize=(12, 8))
    sns.barplot(data=top_20, x='wholeValue', y='dongName', palette='viridis')
    plt.title('Top 20 행정동별 대중 교통 데이터')
    plt.xlabel('일일 평균 승객 수')
    plt.ylabel('행정동 이름')
    plt.show()

![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/3c5797da-8919-4b1f-91ff-fbf3e3e93d16)
---

가장 사람이 많이 이용하는 부평구 부평 1동을 기준으로 가장 많이 승객이 많은  시간을 기준으로 시각화를 한다

df[df[‘dongName’] 을 이용하여 특정 행을 선택하여 그 데이터만 나오게 한다.

결과 : 가장 사람이 많이 버스를 이용하는 시간은 16시(오후 4시)두번째로는 17시(5시)와 15시(3시)가 가장 많이 이용이 됬다.

    import seaborn as sns
    time_columns = df.columns[3:27]
    
    # 특정 동 이름의 데이터 선택
    selected_dong = '부평구 부평1동'
    selected_data = df[df['dongName'] == selected_dong]
    
    # 시간에 따른 데이터 시각화
    plt.figure(figsize=(10, 8))  # 그래프의 전체 크기를 조절
    plt.barh(time_columns, selected_data[time_columns].values.flatten(), color='skyblue')
    plt.title(f'{selected_dong}')
    plt.xlabel('승객수')
    plt.ylabel('시간')

![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/a42afd83-e740-4d2a-9de2-d93b497f0963)
---
추가적으로 2번째로 사람이 많이 이용하는 중구 운서동도 시각화를 해봤다

결과 : 이 데이터에도 부평구 부평 1동처럼 16시(오후 4시)가 가장 많은 승객수를 보이고 있지만 18시(오후6시) 도 비슷하게 사람이 많은 것을 알 수 있다.

    import seaborn as sns
    time_columns = df.columns[3:27]
    
    # 특정 동 이름의 데이터 선택
    selected_dong = '중구 운서동'
    selected_data = df[df['dongName'] == selected_dong]
    
    # 시간에 따른 데이터 시각화
    plt.figure(figsize=(10, 8))  # 그래프의 전체 크기를 조절
    plt.barh(time_columns, selected_data[time_columns].values.flatten(), color='red')
    plt.title(f'{selected_dong}')
    plt.xlabel('승객수')
    plt.ylabel('시간')

![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/89f83be1-3476-4a10-983a-bca6a7603183)
---

## 대중교통 이용 승객들 분류

버스를 이용하는 승객들중 일반, 어린이, 청소년 등의 승객들의 데이터를 가져온다 

    import pandas as pd
    df=pd.read_csv('/content/다양한 환승 이용 통계.csv')
    df = df[['patternContent','normalPsgCnt','childPsgCnt','teenPsgCnt','collegePsgCnt','seniorPsgCnt','disabledPsgCnt']]
    df.columns = ['환승유형','일반승객', '어린이승객', '청소년승객', '대학생승객', '경로우대승객', '장애인승객']
    
    df.head()

![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/9e0e997b-bc5b-4652-a2d0-fc8cc843f101)
---
버스를 이용하는 각 승객 유형벌  수를 시각화한다.

데이터의 차이가 심하게 나서  값 레이블을 추가한다.

결과 : 당연하게도 일반승객이 압도적으로 높고, 그다음에 청소년승객이 높은 결과를 보여주고 있다.

    # '①[버스]'에 해당하는 데이터만 추출
    bus_data = df[df['환승유형'] == '①[버스]']
    
    # 시각화
    plt.figure(figsize=(15, 6))
    bars = plt.bar(bus_data.columns[1:], bus_data.iloc[0, 1:])
    plt.title('①[버스]의 각 승객 유형별 수')
    plt.xlabel('승객 유형')
    plt.ylabel('승객 수')
    
    # 각 막대에 값 라벨과 값 레이블 추가
    for bar, value in zip(bars, bus_data.iloc[0, 1:]):
        plt.text(bar.get_x() + bar.get_width() / 2 - 0.1, bar.get_height() + 50, str(value), ha='center', color='black', fontweight='bold')
    
    plt.show()
![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/cc62b2f9-849a-4067-82d9-02436b80ee2d)
---
이번에는 버스가 아닌  지하철의 데이터를 시각화 한다

데이터를 보면 확실히 버스보다 지하철을 이용을 하는 사람들이  많이 있다는 것을 알 수 있다.

    # '①[지하철]'에 해당하는 데이터만 추출
    subway_data = df[df['환승유형'] == '①[지하철]']
    
    # 시각화
    plt.figure(figsize=(15, 6))
    bars = plt.bar(subway_data.columns[1:], subway_data.iloc[0, 1:])
    plt.title('①[지하철]의 각 승객 유형별 수')
    plt.xlabel('승객 유형')
    plt.ylabel('승객 수')
    
    # 각 막대에 값 라벨과 값 레이블 추가
    for bar, value in zip(bars, subway_data.iloc[0, 1:]):
        plt.text(bar.get_x() + bar.get_width() / 2 - 0.1, bar.get_height() + 50, str(value), ha='center', color='black', fontweight='bold')
    
    plt.show()
![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/d6e473d9-5a34-4ba0-9cc2-604d8fad2593)
---
## 다양한 환승 이용 통계
    # '①[버스] → ②[버스]'에 해당하는 데이터만 추출
    transit_data = df[df['환승유형'] == '①[버스] → ②[버스]']
    
    # 시각화
    plt.figure(figsize=(10, 6))
    bars = plt.bar(transit_data.columns[1:], transit_data.iloc[0, 1:])
    plt.title('①[버스] → ②[버스]의 각 승객 유형별 수')
    plt.xlabel('승객 유형')
    plt.ylabel('승객 수')
    
    # 각 막대에 값 라벨과 값 레이블 추가
    for bar, value in zip(bars, transit_data.iloc[0, 1:]):
        plt.text(bar.get_x() + bar.get_width() / 2 - 0.1, bar.get_height() + 50, str(value), ha='center', color='black', fontweight='bold')
    
    plt.show()
![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/0845046e-97fc-4b3b-a0ea-b8c6c4a1811a)
---
    # '①[지하철]'에 해당하는 데이터만 추출
    subwaybus_data = df[df['환승유형'] == '①[지하철] → ②[버스]']
    
    # 시각화
    plt.figure(figsize=(15, 6))
    bars = plt.bar(subwaybus_data.columns[1:], subwaybus_data.iloc[0, 1:])
    plt.title('①[지하철] → ②[버스]의 각 승객 유형별 수')##
    plt.xlabel('승객 유형')
    plt.ylabel('승객 수')
    
    # 각 막대에 값 라벨과 값 레이블 추가
    for bar, value in zip(bars, subwaybus_data.iloc[0, 1:]):
        plt.text(bar.get_x() + bar.get_width() / 2 - 0.1, bar.get_height() + 50, str(value), ha='center', color='black', fontweight='bold')
    
    plt.show()
![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/b078748d-4956-4cd8-881c-a4e0445788d4)
---
## 택시 통계 순위 (top 20)

    import pandas as pd
    df=pd.read_csv('/content/택시를 탈때 가장 많이 타는 지역 또는 가장 많이 내리는 지역들.csv')
    df.head(20)

택시를 탈 때 가장 많이 타는 지역 또는 가장 많이 내리는 지역들이 있는 데이터를 가져온다

![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/89aa7647-6034-4af4-874b-5b0601aebfa8)
---
dongName(행정동 이름)을 기준으로 가장 택시가 많이 이동한 구역을 중심으로 시각화한다.

결과 : 남동구 구월3동 쪽에 택시가 가장 많이 돌아다닌 다는 것을 알수 있다.

    import matplotlib.pyplot as plt
    import seaborn as sns
    
    # 'rideDongName'을 기준으로 그룹화하고 'cnt' 열의 합계 계산
    grouped_df = df.groupby('rideDongName')['cnt'].sum().reset_index()
    
    # 'cnt' 열에서 가장 많은 값을 가지는 상위 20개의 행 선택
    top_20 = grouped_df.nlargest(20, 'cnt')
    top_20.head()
    
    # 'rideDongName'을 기준으로 그룹화하고 'cnt' 열의 합계 계산
    grouped_df = df.groupby('rideDongName')['cnt'].sum().reset_index()
    
    # 'cnt' 열에서 가장 많은 값을 가지는 상위 20개의 행 선택
    top_20 = grouped_df.nlargest(20, 'cnt')
    
    # 상위 20개의 데이터를 이용하여 시각화
    plt.figure(figsize=(12, 8))
    sns.barplot(data=top_20, x='cnt', y='rideDongName', palette='viridis')
    plt.title('Top 20 택시 데이터')
    plt.xlabel('일일 평균 승객 수')
    plt.ylabel('행정동 이름')
    plt.show()
![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/2017e4b7-bda5-4c94-9b06-77164f71098f)
---
## 택시들이 많이 다니는 집중되는 지역 통계

dongName(행정동 이름)을 기준으로 가장 택시가 많이 이동한 구역을 중심으로 시각화한다.

결과 : 남동구 구월3동 쪽에 택시가 가장 많이 돌아다닌 다는 것을 알수 있다.

    import matplotlib.pyplot as plt
    import seaborn as sns
    
    # 'dongName'을 기준으로 그룹화하고 'wholeCase' 열의 합계 계산
    grouped_df = df.groupby('dongName')['wholeCase'].sum().reset_index()
    
    # 'wholeCase' 열에서 가장 많은 값을 가지는 상위 20개의 행 선택
    top_20_1 = grouped_df.nlargest(20, 'wholeCase')
    # 상위 20개의 데이터를 이용하여 시각화
    import matplotlib.pyplot as plt
    import seaborn as sns
    
    # 'dongName'을 기준으로 그룹화하고 'WholeCase' 열의 합계 계산
    grouped_df = df.groupby('dongName')['wholeCase'].sum().reset_index()
    
    # 'WholeCase' 열에서 가장 많은 값을 가지는 상위 20개의 행 선택
    top_20_1 = grouped_df.nlargest(20, 'wholeCase')
    
    # 상위 20개의 데이터를 이용하여 시각화
    plt.figure(figsize=(12, 8))
    sns.barplot(data=top_20_1, x='wholeCase', y='dongName', palette='viridis')
    plt.title('Top 20 택시 데이터')
    plt.xlabel('일일 평균 택시 이동 수')
    plt.ylabel('행정동 이름')
    plt.show()
![image](https://github.com/wjddn8038/bigdataproject/assets/63216655/c6d04e8f-2b7a-4581-b13d-aa84cd68fa72)
---
## 결과
1.버스는 지역중 부평, 중구, 남동구 쪽에 버스를 타는 사람들이 몰려있다는 것을 알게 되었다.

2.가장 많이 버스를 타는 시간은 평균 15 , 16 , 18 시 쪽에 많이 탄다는 것을 알수 있다.

3.사람들은 버스보다 지하철을 훨씬 많이 이용을 한다.(주로 경로우대랑, 장애인은 버스보다 지하철을 더 선호한다.)

4.택시가 가장 많이 이용되는 미추홀구, 남동구, 부평 쪽이 택시가 가장 많이 이용이된다

5.택시가 가장 많이 있는 곳은 남동구, 중구 쪽이 가장 많이 돌아다니는 것을 알 수 있다.

## 아쉬운점

1.데이터가 일일 평균 데이터다 보니 전체 평균을 구할 수 없어 데이터가 확실하다고 볼 수가 없는 점이 아쉽다

2.원하는 데이터가 모두 있는 것이 아니라 자유롭게 데이터를 시각화 할수 없는 점이 아쉬웠다.
















  


# Data-Mining

raw csv에서 머무른 장소와, 머무른 시간을 얻기 위해 다음 두 코드를 이용합니다. 이 코드들을 이용해 2)처리용 csv를 얻습니다.

#### data_place.R: 각 건물마다 좌표를 지정해 구역을 설정한 후, 특정 구역으로 이동할 때마다 start point와 end point를 잡고 place에 미리 지정해둔 건물의 번호를 작성하는 코드입니다.

(target)표기  

1 out  
0 else(가용시간)  
1 ~ 12 : 교내 건물  
100 ~ 1200 : 해당 구역에서 갑작스럽게 20분 이상 나간 경우  
10000: 어떤 구역인지 알 수 없으나 갑작스럽게 20분 이상 나간 경우  
-1: 정문을 거쳐 나간 경우(out: 정문 거쳤는지 알아보는 함수)  

#### exception_handling.R: data_place 코드를 거친 후에 csv 파일을 보면 휴대폰 GPS 및 애플리케이션의 한계로 인해 위치가 불안정하게 움직이면서 예를 들어 실제 사용자는 1에 위치하지만 0 1 0 1 0 1 0 처럼 place가 반복되어 나타나는 경우가 발생합니다.  이런 경우 등의 다양한 예외를 처리해주기 위하여 해당 코드를 이용해  전처리를 진행해 보다 깔끔하게 시간에 따른 place 값을 얻을 수 있게 해주는 코드입니다. 세부적인 내용은 다음과 같습니다.

1. 날짜별로 분류해 처리(1/2)

(1) 100~1200, 10000 값 처리
- 해당값 이전 place와 이후 place가 모두 해당 값에 100을 나눈 것과 동일하면 그 값으로 변경
ex) 6 600 6 => 6 6 6

<우선순위: 이전place -> 이후 place> 
- 이전 place하고만 일치하면 그 값으로 변경
ex) 6 600 0 => 6 6 0

- 이후 place하고만 일치하면 그 값으로 변경
ex) 0 600 6 => 0 6 6

- 아무것도 해당되지 않으면 그대로 둠
ex) 0 600 0 이나 10000

(2) 현재 place가 0인데 이전 place와 이후 place가 동일한 경우 동일하게 변경
ex) 6 0 6 0 6 => 6 6 6 6 6

(3) 바뀐 place값을 테이블에 갱신 후, 해당 파일 dataset_replace.csv로 내보냄(현재는 주석처리 되어있음)

2. 날짜별로 분류해 처리(2/2)

(1) 연속적으로 동일한 place를 갖는 데이터를 합쳐줌
ex) 6 6 6 => 첫번째 6에서의 startpoint : startpoint/ 세번째 6에서의 endpoint: endpoint

(2) 다시 처리된 데이터를 datasetN_placeclean.csv 파일로 내보냄(현재는 주석처리 되어있음)

3. 시간차를 이용해 처리

(1) 특정 건물에 머무른 시간이 3분 이하이면 그 건물을 지나가다가 측정된 것으로 가용시간으로 처리되어야 하기 때문에 이런 경우를 모두 0으로 처리한 후, 다시 데이터를 합침

(2) 다시 처리된 데이터를 datasetN_placecleanfinal.csv 파일로 내보냄

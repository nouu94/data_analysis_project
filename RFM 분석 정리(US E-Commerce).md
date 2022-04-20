# RFM 프로젝트 분석 정리 (US E-Commerce Records 2020)

RFM 프로젝트 수행 과정을 정리한 글 입니다. 해당 프로젝트의 데이터 셋은 데이터 리안 측에서 구현해주신 solvesql의 데이터 셋 중 US E-Commerce Records 2020 데이터 셋을 바탕으로 해당 프로젝트를 구현하였습니다. 

<br>

<br>

**RFM 프로젝트를 수행하는 목적**

* 데이터 셋의 유의미한 데이터들을 찾고 그것을 바탕으로 고객들의 등급을 나누어 각 등급에 따른 Action_item을 생각해봅니다. 
* RFM Segmentation을 하는 이유는 여러가지 있겠지만 이번 RFM 프로젝트에서 제가 주력 목적은 '자사 서비스의 매출 상승'입니다. '서비스 매출 상승'이라는 목적을 주력한 RFM 분석을 시도하여 그 과정에서 어떠한 고충이 있을지 생각하고자 해당 프로젝트를 수행하였습니다.

<br>

<br>

**목차 및 프로젝트 과정**

1. RFM 분석이란? 

2. 프로젝트 데이터 셋 설명

3. EDA (탐색적 데이터 분석)

4. EDA를 통한 결론, 결론을 기반한 RFM 구간 추출

5. Action_item

6. 프로젝트 회고, KPT(Keep, Problem, Try)

<br>

<br>

<br>

---

<br>

<br>

## 1. RFM 분석이란?

[CRM(Customer Relationship Management)](https://www.google.com/search?q=CRM&oq=CRM&aqs=chrome..69i57j0i131i433i512j0i20i263i512j0i512l2j0i20i263i512j0i512j69i61.1714j0j7&sourceid=chrome&ie=UTF-8) 기법 중 하나로 고객을 R, F, M 3가지의 유형을 기준으로 각각 유형들에 가중치를 주어 사용자 그룹으로 나누어 분류하는 분석 기법입니다.

<br>

<br>

Recency :  최근 구매를 뜻하며, 고객이 제품을 최근에 구매했는가를 나타내는 지표이다. 

Frequency : 거래의 빈도를 뜻한다. 특정 기간에 얼마나 많이 제품을 구매했는가를 나타내는 지표이다. 

Monetary : 거래의 규모를 의미한다. 어떠한 고객이 특정 기간 동안 특정 가격보다 많이 구매했는가를 나타내는 지표이다. 

<br>

<br>

이러한 분석 방법은 CRM 중 사용자 행동 패턴을 기반으로 분석한다는 것, 자사의 비즈니스의 특성을 기반하여 어떠한 기준을 세워서 고객의 가치를 도출한다는 특성을 가지고 있습니다. 

<br>

<br>

<br>

---

<br>

<br>

## 2. 프로젝트 데이터 셋 설명 

 '<u>United States E-Commerce records 2020</u>' 데이터 셋의 원본의 출처는 [Kaggle](https://www.kaggle.com/datasets/ammaraahmad/us-ecommerce-record-2020) 이며, 이 데이터 셋은 2020년 미국 전자 상거래 기록을 정리한 데이터 셋 으로 다음과 같은 구조로 되어 있습니다. 해당 데이터 셋은 총 두개의 테이블로 구조로 되어 있으며, 고객이 주문 및 판매 데이터가 기록 된 records 테이블과 고객의 최근 주문 날짜, 총 주문 수, 총 지불 금액 등이 있는 customer_stats 테이블이 구성되어 있습니다. 

<br><br>

### records 테이블

![JM-15250](https://user-images.githubusercontent.com/71218142/161432656-4256ff96-9884-443f-a813-d24915035547.png)

 <br>

<details> 
    <summary>접기/펼치기</summary> 
	ship_mode : 배송 등급에 관한 컬럼이다. Standard Class, First Class 등이 있다.<br>
	customer_id : 고객의 id를 가리킨다.<br>
	segment : 고객의 타입을 나타낸다. consumer, corporate 등이 있다.<br>
	Country : 고객의 국가를 알려주는 컬럼<br>
	city : 고객의 도시를 알려주는 컬럼<br>
	state : 고객의 주를 알려주는 컬럼<br>
    postal_code : 고객의 주소 중 우편번호를 알려주는 컬럼<br>
    region : 고객이 위치한 지역을 나타냄<br>
    product_id : 제품의 아이디를 나타냄. category(3)-subcategory(2)-고유아이디 순으로 제품 아이디가 결정된다.<br>
    category : 제품의 1차 분류 카테고리를 나타냄<br>
    sub_category : 제품의 1차 분류 카테고리를 기반하여 상세 카테고리를 나타냄<br>
    product_name : 고객이 구매한 제품의 이름을 나타냄<br>
    sales : 판매가를 나타냄. 만약 의자 4개를 25달러에 샀다면 4개의 총 sales가 25달러라는 뜻이다.<br>
    quantity : 주문 수량을 나타냄<br>
    discount : 할인율을 나타냄<br>
    profit : 매출에 대비한 순 이익을 나타냄 +가 될 수도 -가 될 수도 있다.<br>

<br>

<br>

### customer_stats 테이블

![customer_stats](https://user-images.githubusercontent.com/71218142/161432685-fe506419-48d6-423e-a950-932ee36338e7.png)

<br>

<details>
    <summary>접기/펼치기</summary>
    customer_id : 고객의 id
	first_order_date : 처음 주문한 날짜 
	last_order_date : 마지막으로 주문한 날짜 
	cnt_orders :  주문횟수 (중요! records 테이블의 order_id의 DISTINCT 갯수를 기반으로 카운트 한 컬럼이다.)
	sum_sales : 해당 고객이 총 구매한 금액을 가리킨다. 

<br>

<br>

<br>

해당 데이터 셋의 각각의 테이블은 records.customer_id 속성과 customer_stats 속성으로 관계를 짓고 있습니다. 한명의 customer는 다수의 product를 살 수 있으니 1:N 관계로 나타낼 수 있습니다. 이에 따라 [draw.io](https://draw.io/)를 기반으로 IE/Crow`s Foot 모델을 나타내면 다음과 같이 정의내릴 수 있습니다.

<br>

<br>

![image](https://user-images.githubusercontent.com/71218142/163257615-719d6a2b-4ad3-4564-8b41-da0d78215810.png)



**customer_stats 입장에서 봤을 때 한명의 고객은 1 ~ N 개의 주문관련 데이터가 records에 기록될 수 있습니다.**

<br>

**records 입장에서 봤을 때 1 ~ N 개의 주문 데이터는 한명의 고객에 대한 주문 상품이 등록 될 수 있습니다. **

<br>

<br>

<br>

---

<br>

<br>



## 3. EDA (탐색적 데이터 분석)

RFM 각각의 요소에 기준을 부여하기 전 records 테이블과 customer_stats 테이블의 데이터들을 탐색적 분석을 수행하였습니다. EDA를 통해 각 테이블의 유의미한 칼럼이 어떤 것인지 분석하는 과정을 거쳐 추후 RFM에 반영하였습니다.

<br>

<br>

### 3-1. records.category를 통한 E-Commerce 사업 품목 분석

```mysql
SELECT category 
     , sub_category 
     , COUNT(*) AS selling_category_count
FROM records 
GROUP BY category 
       , sub_category 
ORDER BY selling_category_count DESC;
```

<br>

| category        | sub_category | selling_category_count |
| --------------- | ------------ | ---------------------- |
| Office Supplies | Binders      | 500                    |
| Office Supplies | Paper        | 459                    |
| Furniture       | Furnishings  | 316                    |
| Technology      | Phones       | 294                    |
| Office Supplies | Storage      | 288                    |
| Office Supplies | Art          | 282                    |
| Technology      | Accessories  | 275                    |
| Furniture       | Chairs       | 190                    |
| Office Supplies | Appliances   | 165                    |
| Office Supplies | Labels       | 114                    |
| Furniture       | Tables       | 104                    |
| Furniture       | Bookcases    | 76                     |
| Office Supplies | Envelopes    | 71                     |
| Office Supplies | Fasteners    | 64                     |
| Office Supplies | Supplies     | 59                     |
| Technology      | Machines     | 33                     |

<br>

E-Commerce 사가 어떠한 물품을 주력으로 팔고 있는지에 대한 지표를 나타냈습니다. 1차 카테고리의 칼럼 값은 총 Furniture, Office Supplies, Technology 총 3개이며, Binders, papers와 같은 저가 소비재가 가장 많이 팔리는 것으로 도출되었습니다. 하지만 그 밑으로 Furnishing과 Phones 같은 상대적으로 중, 고가형의 제품들도 많이 팔리는 것으로 나왔습니다. 즉, 자사의 판매 제품은 저가의 소비재 부터 중, 고가형의 가구와 사무실에서 일을 하기 위해 필요한 용품을 파는 E Commerce 사업을 하는 것이라는 것을 알 수 있습니다.   

<br>

<br>

<br>

<br>

### 3-2. records.segment를 그룹화 하여 한 segment 당 매출 집계

```mysql
SELECT DISTINCT r.customer_id AS customer 
     , r.segment AS segment
     , c.sum_sales AS sum_sales
FROM records r 
  JOIN customer_stats c ON 
    r.customer_id = c.customer_id
ORDER BY sum_sales DESC
LIMIT 10;
```

<br>

<br>

| customer | segment     | sum_sales |
| -------- | ----------- | --------- |
| RB-19360 | Consumer    | 14203.28  |
| TA-21385 | Home Office | 13723.5   |
| HL-15040 | Consumer    | 10522.55  |
| SV-20365 | Consumer    | 8459.936  |
| GT-14635 | Corporate   | 8167.42   |
| HW-14935 | Corporate   | 8166.354  |
| TS-21370 | Corporate   | 6702.293  |
| RW-19540 | Corporate   | 6193.446  |
| PK-19075 | Consumer    | 5979.136  |
| KF-16285 | Home Office | 5825.462  |

<br>

693개의 회원 전체의 데이터를 확인해 봤을 때 RB-19360 개인 소비자 회원이 sum_sales가 1위인 것을 볼 수 있으며, sum_sales TOP 10이 Corporate 회원이 제일 많은 것을 볼 수 있습니다.

<br>

<br>

```mysql
SELECT segment
    , ROUND(SUM(sales), 2) AS segment_group_sales 
    , COUNT(DISTINCT customer_id) AS segment_customer_count
    , ROUND(SUM(sales) / COUNT(DISTINCT customer_id), 2) AS sales_per_segment
FROM records
GROUP BY segment
ORDER BY sales_per_segment DESC;
```

<br>

| segment     | segment_group_sales | segment_customer_count | sales_per_segment |
| ----------- | ------------------- | ---------------------- | ----------------- |
| Home Office | 159462.7            | 128                    | 1245.8            |
| Corporate   | 241847.8            | 204                    | 1185.53           |
| Consumer    | 331904.7            | 361                    | 919.4             |

<br>

하지만 segment 별로 그룹화 하여 sales_per_segment(하나의 segment 회원 당 평균 매출액) 를 봤을 때 Home_group_sales가 1위였으며, 2위가 corporate, 마지막 3위가 Consumer인 결과가 도출되었습니다. Consumer 고객이 수가 많지만 한명의 Consumer보다 단체의 특성을 가진 고객이 구매가가 높은 물품을 구매한다는 것을 알 수 있습니다. ( 여기서 회원 가입을 할 때 실제로 1인 회원이 'Corporate'나 'Consumer' 라고 명시했거나, 반대로 사업체가 'Consumer'라고 명시했을거라는 변수가 있지만, 이러한 변수는 생각하지 않겠습니다. ) 

<br>

<br>

해당 지표는 유의미한 지표라고 생각합니다. 추후 RFM segmentation을 할 때 각 segment 별로 군집화 하여 Action_Item에 차별화를 주는 방식의 전략을 짜낼 수 있다고 생각합니다.

<br>

<br>

<br>

<br>

### 3-3. Frequency의 지표, records.quantity VS records.order_id (얼마나 제품을 많이 구매했는가?, 얼마나 많이 주문했는가?)

'US E-Commerce Records 2020' 데이터 셋의 records 테이블에는 제품을 구매할 때 구매하는 제품의 product_id와 order_id  속성이 있습니다. (cnt_Product, cnt_orders) 두 속성 간 어떤 관계가 있고, 두 속성 중 Frequency 지표를 어떤 속성을 기준으로 잡아야 할지 생각하는 시간을 갖기 위해 해당 속성을 위주로 집중적으로 분석하였습니다. 

<br>

<br>

결론부터 말하자면 이번 RFM 프로젝트를 하는 목적은 '자사의 E-Commerce 상품들의 매출을 올리는 것이 목적입니다. 따라서 저는 Frequency를 총 물품 구매 수에 해당하는 quantity의 합으로 결정했습니다.  

<br>

<br>

<br>

#### 시나리오 1 : 고객이 같은 날짜에 물품 배송 신청을 한번만 한다.

<br>

<br>

#####  1-1. 고객이 하나의 물품을 장바구니에 담고 주문할 때

customer_id가 'RB-19570'인 회원이 휴대폰을 장바구니에 담아 구매한다고 가정했을 때 다음과 같은 절차를 통해 제품이 구매됩니다.

**(1). 휴대폰을 구매하기 위해 장바구니에 담는다.**

![11](https://user-images.githubusercontent.com/71218142/163262660-86edb926-15d3-4a28-a27d-2cc0e1ee72d5.png)

<br>

<br>

**(2). 구매 버튼을 누른다.**

![12](https://user-images.githubusercontent.com/71218142/163262956-293ff097-4d2b-4b0b-b6df-f85a80ea8a46.png)

<br>

<br>

**(3). 구매자 정보 및 받는 사람의 정보를 입력한다.**

![13](https://user-images.githubusercontent.com/71218142/163264330-59c63569-29da-4d22-a324-f44b45076c03.png)

<br>

<br>

이러한 과정을 거쳤다면 records 테이블에는 다음과 같이 하나의 주문 기록 관련 데이터가 쌓일 것 입니다.

| order_date | order_id       | ship_mode | customer_id | ...  | quantity | ...  |
| ---------- | -------------- | --------- | ----------- | ---- | -------- | ---- |
| 2022-04-14 | CA-2017-138779 | ...       | RB-19570    | ...  | 1        | ...  |

<br>

<br>

<br>

#####  1-2. 고객이 하나의 물품을 n개 장바구니에 담고 주문할 때

<br>

똑같은 구매 방법 패턴으로 고객이 하나의 같은 물품을 n개 장바구니에 담고 주문할 때 다음과 같은 결과가 나옵니다.

| order_date | order_id       | ship_mode | customer_id | ...  | quantity | ...  |
| ---------- | -------------- | --------- | ----------- | ---- | -------- | ---- |
| 2022-04-14 | CA-2017-138779 | ...       | RB-19570    | ...  | n        | ...  |

<br>

레코드가 한개 쌓이고, quantity에 n이라고 적힐 것 입니다.

<br>

<br>

#####  1-3. 고객이 2개 물품을 각각 한개씩 장바구니에 담고 주문할 때

<br>

똑같은 구매 방법 패턴으로 고객이 2개의 물품을 각각 한개씩 장바구니에 담고 주문할 때 입니다.

| order_date | order_id       | ship_mode | customer_id | product_id      | ...  | quantity | ...  |
| ---------- | -------------- | --------- | ----------- | --------------- | ---- | -------- | ---- |
| 2022-04-14 | CA-2017-138779 | ...       | RB-19570    | TEC-PH-10003655 | ...  | 1        | ...  |
| 2022-04-14 | CA-2017-138779 | ...       | RB-19570    | FUR-FU-10003878 | ...  | 1        | ...  |

<br>

이 외에도 2개의 물품을 각각 n개, m개씩 장바구니에 담고 주문을 한다고 하면 데이터는 두 줄이 쌓일 것이고, quantity는 각각 n, m이 쌓일 것 입니다.

<br>

<br>

<br>

#### 시나리오 2 : 고객이 같은 날짜에 물품 배송 신청을 두번 이상 한다.

<br>

해당 시나리오는 다음과 같은 예시로 나타낼 수 있겠습니다. 

<br>

**(1). 휴대폰을 구매 후 배송지를 충남 천안(기숙사)으로 등록한다.** 

<br>

![1](https://user-images.githubusercontent.com/71218142/163442920-9eb59dc0-7189-4bdd-afa7-0afeb837f767.png)

<br>

<br>

**(2). 휴대폰 구매 후 이어서 가구를 구매한다. 배송지는 본가에 있는 부천시로 등록한다.**

<br>

![2](https://user-images.githubusercontent.com/71218142/163442915-848a88b3-be8e-4c56-8326-0c52eb03f9a2.png)

<br>

<br>

<br>

저는 이러한 시나리오가 있다는 EDA 수행 중  이 시나리오에 대한 레코드가 있는지 쿼리를 작성하여 확인하였습니다.

```mysql
-- self join을 통해 같은 날짜에 특정 고객이 배송 신청을 두 번 이상 했는지 확인.
SELECT r1.order_date 
    , r1.customer_id 
    , COUNT(DISTINCT r1.order_id)
FROM records r1
  JOIN records r2 ON 
    r1.order_date = r2.order_date 
    AND r1.customer_id = r2.customer_id 
WHERE r1.order_id <> r2.order_id
GROUP BY r1.order_date 
       , r1.customer_id

-- '2020-12-10' customer_id가 'BP-11185' 인 고객이 정말 하루에 2번 주문했는지 확인
SELECT * 
FROM records 
WHERE order_date = '2020-12-10' 
AND customer_id = 'BP-11185'
```

<br>

<br>

**<self join을 통해 같은 날짜에 특정 고객이 배송 신청을 두 번 이상 했는지 확인.>**

| order_date | customer_id | today_order_cnt |
| ---------- | ----------- | --------------- |
| 2020-07-03 | RD-19585    | 2               |
| 2020-08-04 | HM-14860    | 2               |
| 2020-09-11 | CB-12025    | 2               |
| 2020-12-10 | BP-11185    | 2               |
| 2020-12-17 | LC-17140    | 2               |
| 2020-12-22 | AG-10330    | 2               |

<br>

총 6명의 회원이 하루에 2번의 주문을 한 것을 확인

<br>

<br>

**<'2020-12-10' customer_id가 'BP-11185' 인 고객이 정말 하루에 2번 주문했는지 확인>**

| order_date | order_id       | ship_mode      | customer_id | segment   | country       | city      | state      | postal_code | ...  |
| ---------- | -------------- | -------------- | ----------- | --------- | ------------- | --------- | ---------- | ----------- | ---- |
| 2020-12-10 | CA-2017-167150 | Standard Class | BP-11185    | Corporate | United States | San Diego | California | 92037       | ...  |
| 2020-12-10 | CA-2017-167150 | Standard Class | BP-11185    | Corporate | United States | San Diego | California | 92037       | ...  |
| 2020-12-10 | US-2017-140907 | Second Class   | BP-11185    | Corporate | United States | Seattle   | Washington | 98103       | ...  |
| 2020-12-10 | US-2017-140907 | Second Class   | BP-11185    | Corporate | United States | Seattle   | Washington | 98103       | ...  |
| 2020-12-10 | US-2017-140907 | Second Class   | BP-11185    | Corporate | United States | Seattle   | Washington | 98103       | ...  |

<br>

<br>

<br>

<br>

시나리오 1과 시나리오 2를 총 정리하여 도출한 결론은 다음과 같습니다. 

1. customer 당 order_id 횟수는 즉 고객이 물품을 주문한 총 배송 횟수를 나타내며, 이 횟수가 클수록 자사의 물류 시스템이 크게 활성화 됩니다.
2.  고객의 제품 구매 빈도를 나타내는 것은 quantity 이며, 고객의 quantity 합은 곧 총 물품 구매 횟수를 나타냅니다.
3. 만약 이번 프로젝트가 자사의 물류 시스템 활성화 관점으로 프로젝트를 진행했다면 order_id의 횟수를 Frequency 지표로 놓았겠지만, 자사의 매출 상승에 초점을 맞추는게 근본적인 목적이므로 customer의 quantity의 합을 Frequency 지표로 정하겠습니다. 

<br>

<br>

<br>

새로운 customer_stats 테이블 

```mysql
WITH customer_sum_quantity AS ( 
  SELECT customer_id
       , segment
       , SUM(quantity) AS sum_quantity
  FROM records 
  GROUP BY customer_id
         , segment
), customer_stats AS (
    SELECT csq.customer_id
        , csq.segment
        , c.last_order_date 
        , csq.sum_quantity
        -- , c.first_order_date 
        -- , c.cnt_orders 
        , c.sum_sales
    FROM customer_sum_quantity csq
      JOIN customer_stats c ON 
        csq.customer_id = c.customer_id 
)

SELECT *
FROM customer_stats
LIMIT 10
```

<br>

<br>

| customer_id | segment     | last_order_date | sum_quantity | sum_sales |
| ----------- | ----------- | --------------- | ------------ | --------- |
| AA-10315    | Consumer    | 2020-06-29      | 5            | 374.48    |
| AA-10375    | Consumer    | 2020-12-11      | 13           | 206.732   |
| AA-10480    | Consumer    | 2020-04-15      | 3            | 15.552    |
| AA-10645    | Consumer    | 2020-11-05      | 2            | 12.96     |
| AB-10060    | Home Office | 2020-11-06      | 31           | 2936.264  |
| AB-10105    | Consumer    | 2020-11-19      | 26           | 2291.044  |
| AB-10150    | Consumer    | 2020-11-19      | 15           | 230.656   |
| AB-10165    | Consumer    | 2020-12-05      | 28           | 416.272   |
| AB-10255    | Home Office | 2020-07-17      | 14           | 738.674   |
| AB-10600    | Corporate   | 2020-11-10      | 28           | 1475.054  |

<br>

<br>

<br>

<br>

## RFM Segmentation, 분류를 위한 Scale 설정 및 도출

본격적으로 RFM Segmentation를 하기 전 각 segment 별로 RFM의 의 기준이 되는 last_order_date, sum_quantity, sum_sales 의 최대 값과 최소 값 그리고 평균을 구하는 과정을 수행하였습니다. 특히 4분위수로 나누어 segment 별로 R, F, M이 몇 분위수에 있는지 그리고 segment 별로 분포가 어떻게 되는지 확인하였습니다. 이를 바탕으로 segment 별로 나누어 RFM Segmentation을 하고 이후 segment 별로 ActionItem을 취하는 것으로 결정하였습니다. R, F, M은 내용이 길어질 것 같아 대표적으로 F에 대한 분포를 간단히 살펴보겠습니다.

<br>

<br>

Frequency에 대한 4분위 수 분포 확인

```mysql
WITH customer_sum_quantity AS ( 
  SELECT customer_id
       , segment
       , SUM(quantity) AS sum_quantity
  FROM records 
  GROUP BY customer_id
         , segment
), customer_stats AS (
    SELECT csq.customer_id
        , csq.segment
        , c.last_order_date 
        , csq.sum_quantity
        , c.sum_sales
    FROM customer_sum_quantity csq
      JOIN customer_stats c ON 
        csq.customer_id = c.customer_id 
)

-- frequency에 대한 지표를 4분위수로 출력
SELECT customer_Frequency_quartile_df.segment
     , customer_Frequency_quartile_df.Frequency_quartile
     , COUNT(customer_Frequency_quartile_df.Frequency_quartile) cnt_frequency_quartile
FROM (
SELECT customer_id 
     , segment 
     , sum_quantity 
     , PERCENT_RANK() OVER (ORDER BY sum_quantity DESC) AS Frequency_percentage
     , CASE WHEN PERCENT_RANK() OVER (ORDER BY sum_quantity DESC) <= 0.25 THEN '1사분위수'
            WHEN PERCENT_RANK() OVER (ORDER BY sum_quantity DESC) <= 0.5 THEN '2사분위수'
            WHEN PERCENT_RANK() OVER (ORDER BY sum_quantity DESC) <= 0.75 THEN '3사분위수'
            ELSE '4사분위수' END AS Frequency_quartile
FROM customer_stats
) AS customer_Frequency_quartile_df
GROUP BY customer_Frequency_quartile_df.segment
      , customer_Frequency_quartile_df.Frequency_quartile
```

<br>

<br>

| segment     | Frequency_quartile | cnt_frequency_quartile |
| ----------- | ------------------ | ---------------------- |
| Consumer    | 1사분위수          | 89                     |
| Consumer    | 2사분위수          | 85                     |
| Consumer    | 3사분위수          | 93                     |
| Consumer    | 4사분위수          | 94                     |
| Corporate   | 1사분위수          | 54                     |
| Corporate   | 2사분위수          | 55                     |
| Corporate   | 3사분위수          | 53                     |
| Corporate   | 4사분위수          | 42                     |
| Home Office | 1사분위수          | 40                     |
| Home Office | 2사분위수          | 31                     |
| Home Office | 3사분위수          | 35                     |
| Home Office | 4사분위수          | 22                     |

<br>

<br>

다음과 같은 분포가 나오는 것을 확인할 수 있습니다. 하지만 세그먼트 별로 묶어서 사분위수의 분포 수를 나타내면 다음과 같은 결과가 나옵니다. 

segment를 묶어 Frequency에 대한 4분위 수 분포 확인

| segment     | Frequency_quartile | cnt_frequency_quartile |
| ----------- | ------------------ | ---------------------- |
| Consumer    | 1사분위수          | 97                     |
| Consumer    | 2사분위수          | 91                     |
| Consumer    | 3사분위수          | 91                     |
| Consumer    | 4사분위수          | 82                     |
| Corporate   | 1사분위수          | 52                     |
| Corporate   | 2사분위수          | 57                     |
| Corporate   | 3사분위수          | 47                     |
| Corporate   | 4사분위수          | 48                     |
| Home Office | 1사분위수          | 33                     |
| Home Office | 2사분위수          | 33                     |
| Home Office | 3사분위수          | 30                     |
| Home Office | 4사분위수          | 32                     |

<br>

즉, Frequency 지표 결과 Consumer는 전체 segment와 함께 비교하여 4분위수로 나타내면 1사분위수와 2사분위수가 상대적으로 떨어진다는 것을 알 수 있습니다. 반면 Corporate나 Home Office와 같이 규모가 상대적으로 있는 고객은 전체 segment로 비교했을 때 1, 2사분위수가 높아진 것을 확인할 수 있습니다. 즉, 전체적으로 HomeOffice나 Corporate같이 규모가 단체인 고객이 Frequency 지표가 높은 것을 알 수 있습니다. 해당 지표는 개인 소비자의 성격을 띄는 Consumer가 사업 단체와 같이 규모가 있는 고객과 동일선상으로 RFM 분석을 하는 것은 무리라고 판단하였습니다. 따라서 **segment를 나누어 RFM segmentation을 하겠습니다.** 

<br>

<br>

<br>

<br>

#### Recency 

리센시












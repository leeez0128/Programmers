1. 데이터 순서대로 정렬하기 (오름차순)

```sql
SELECT * from animal_ins order by animal_id
```

2. 데이터 순서대로 정렬하기 (내림차순)

```sql
SELECT name, datetime from animal_ins order by animal_id desc
```

3. 조건문 검색

```sql
SELECT animal_id, name from animal_ins where intake_condition = 'Sick' order by animal_id

SELECT animal_id, name from animal_ins where intake_condition != 'Aged' order by animal_id
```
4. 동물 보호소에 들어온 모든 동물의 아이디와 이름, 보호 시작일을 이름 순으로 조회하는 SQL문 작성.
     단, 이름이 같은 동물 중에서는 보호를 나중에 시작한 동물을 먼저 보여줘야함.

```sql
//1. 이름 순 
//2. 보호 시작일 역순
SELECT animal_id, name, datetime from animal_ins order by name, datetime desc
```

5. 동물 보호소에 가장 먼저 들어온 동물의 이름을 조회하는 SQL 문 작성.

```sql
SELECT name from animal_ins order by datetime limit 1
```

6. 동물 보호소에 가장 최근에 들어온 동물은 언제 들어왔는지 조회하는 SQL 문 작성.

     column 명 바꾸고 싶을 때는 as '원하는 이름' or 원하는 이름

```sql
SELECT datetime as '시간' from animal_ins order by datetime desc limit 1
SELECT datetime as 시간 from animal_ins order by datetime desc limit 1
```

7. 동물 보호소에 가장 먼저 들어온 동물은 언제 들어왔는지 조회하는 SQL 문 작성.

```sql
SELECT datetime as 시간 from animal_ins order by datetime limit 1
SELECT datetime as '시간' from animal_ins order by datetime limit 1
```

8. 동물 보호소에 동물이 몇 마리 들어왔는지 조회하는 SQL 문 작성.

    column 개수 세고 싶을 때는 count(column명)

```sql
SELECT count(animal_id) as 'count' from animal_ins
```
9. 동물 보호소에 들어온 동물의 이름은 몇 개인지 조회하는 SQL 문 작성. 이름이 NULL인 경우는 집계하지 않으며 중복되는 이름은 하나로 친다.

   중복제거 distinct

```sql
SELECT count(distinct name) as 'count' from animal_ins
```

10. 동물 보호소에 들어온 동물 중 고양이와 개가 각각 몇 마리인지 조회하는 SQL문 작성.
      이때 고양이를 개보다 먼저 조회한다.

     GROUP BY : 컬럼 값이 같은 것 끼리 하나로 묶어준다
     CASE 칼럼 
         WHEN 칼럼 데이터 THEN 1
         WHEN 칼럼 데이터 THEN 1
     ELSE

```sql
SELECT animal_type, count(animal_type) as 'count' from animal_ins group by animal_type ORDER BY
CASE ANIMAL_TYPE
	WHEN 'Cat' THEN 1
	WHEN 'Dog' THEN 2
	ELSE 3
END
```

기본적으로 COUNT와 같은 집계함수(COUNT, AVG, SUM, MAX, MIN)를 써버리면
WHERE절에서 WHERE COUNT(NAME) >= 2 같은 WHERE절이 먹히지 않는다
WHERE절은 한 행마다 조건을 걸어 가져오는건데 COUNT(NAME)은 이미 묶어서 가져온 것이기 때문에 WHERE절이 통하지 않기 때문이다

그렇다면 COUNT(NAME)으로 묶어서 가져왔을 때 WHERE 절은 어떻게 추가하나?
HAVING을 사용해서 집계함수를 사용했더라도 조건을 걸어 조회할 수 있다

11. 동물 이름 중 두 번 이상 쓰인 이름과 해당 이름이 쓰인 횟수를 조회하는 SQL문 작성.
     이때 결과는 이름이 없는 동물은 집계에서 제외하며, 결과는 이름 순으로 조회한다.

```sql
SELECT name, count(name) as 'count' from animal_ins 
group by name 
having count(name) >= 2
order by name
```

** 1. 특정 칼럼으로 group by 하고 
    2. 그 각 그룹에 대한 조건은 having으로
    3. 최종 결과 테이블 상태를 order by로 정렬 **

12. 09:00부터 19:59까지, 각 시간대별로 입양이 몇 건이나 발생했는지 조회하는 SQL문 작성.
      이때 결과는 시간대 순으로 정렬한다.

```sql
SELECT HOUR(datetime) as hour, count(datetime) as count from animal_outs 
group by HOUR(datetime)
having hour >= 9 and hour <= 19
order by hour
```

13.  0시부터 23시까지, 각 시간대별로 입양이 몇 건이나 발생했는지 조회하는 SQL문 작성. 
       이때 결과는 시간대 순으로 정렬한다.

       datetime 칼럼 안의 데이터 값에 없는 Hour도 출력해야 하므로 
       변수를 선언해서 0-23까지 쭈욱 출력하고, 
       그 변수와 같은 값을 지니는 Hour를 조건 검색해서 출력한다.

```sql
set @hour = -1;
SELECT ( @hour := @hour +1 ) as hour, 
(select count(datetime) from animal_outs where hour(datetime) = @hour) as count
from animal_outs
where @hour < 23
```

14. 이름이 없는 채로 들어온 동물의 ID를 조회하는 SQL 문 작성. 단, ID는 오름차순 정렬한다.

     where 칼럼 is NULL

```sql
SELECT animal_id from animal_ins where name is NULL order by animal_id
```

15. 이름이 있는 동물의 ID를 조회하는 SQL 문 작성. 단, ID는 오름차순 정렬한다.

```sql
SELECT animal_id from animal_ins where name is not NULL order by animal_id
```

16. 동물의 생물 종, 이름, 성별 및 중성화 여부를 아이디 순으로 조회하는 SQL문 작성. 
     이때 프로그래밍을 모르는 사람들은 NULL이라는 기호를 모르기 때문에, 
     이름이 없는 동물의 이름은 "No name"으로 표시해 주세요.

     IFNULL : null 값인 데이터에 특정한 값을 주고 싶을 때

```sql
SELECT animal_type, IFNULL(name, 'No name') as name, sex_upon_intake 
from animal_ins
order by animal_id
```

### **JOIN (left, right, inner)**

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6819426a-4e3e-4433-8d8b-965b62fce199/Untitled.png]
- **두 테이블의 JOB 이 같은 것을 기준으로 LEFT OUTER JOIN을 진행합니다.**
- LEFT OUTER JOIN은 왼쪽에 오는 테이블을 기준으로, 오른쪽에 오는 테이블과 비교하여 조건에 맞는 값이 있으면 JOIN하여 가져오고, 값이 없으면 null 값을 표시합니다.
- 즉 LEFT OUTER JOIN은 이처럼 JOIN 문을 수행할 때, 왼**쪽에 있는 데이터는 무조건 가져오며, 오른쪽에 오는 테이블과 JOIN을 수행하여 조건에 맞는 데이터가 없을 시 null 로 표시**하게 됩니다.
- 위의 그림에 나온 결과표를 참고해 주세요.
    - **TABLE_ONE**에 있는 **aaa**는 **JOB 이 student** 입니다.
    - TABLE_TWO 에 JOB이 student 인 값들과 LEFT OUTER JOIN이 수행되어 모두 연결이 됩니다.
    - 하지만 **TABLE_ONE**에 **ddd**는 **JOB이 athlete** 이지만, **TABLE_TWO 에는 JOB이 athlete 인 데이터가 없습니다.**
    - 그러므로 LEFT OUTER JOIN을 수행하면, 왼쪽 테이블의 데이터인 ddd는 결과에도 무조건 표시되지만, **오른쪽 테이블에 같이 연결할 데이터가 없으므로 NULL 값을 표시**하게 됩니다.

17. 천재지변으로 인해 일부 데이터가 유실되었습니다. 
     입양을 간 기록은 있는데, 보호소에 들어온 기록이 없는 동물의 ID와 이름을 
     ID 순으로 조회하는 SQL문 작성.

     LEFT OUTER JOIN은 왼쪽에 오는 테이블을 기준으로, 오른쪽에 오는 테이블과 비교하여 
     조건에 맞는 값이 있으면 JOIN하여 가져오고, 값이 없으면 null 값을 표시

```sql
SELECT outs.animal_id, outs.name FROM animal_outs outs
LEFT OUTER JOIN animal_ins ins ON outs.animal_id = ins.animal_id
where ins.animal_id is null
order by animal_id
```

18. 보호 시작일보다 입양일이 더 빠른 동물의 아이디와 이름을 조회하는 SQL문 작성. 
      이때 결과는 보호 시작일이 빠른 순으로 조회한다.

```sql
SELECT outs.animal_id, outs.name 
from animal_ins ins, animal_outs outs
where ins.datetime > outs.datetime and ins.animal_id = outs.animal_id
order by ins.datetime
```

19. 아직 입양을 못 간 동물 중, 가장 오래 보호소에 있었던 동물 3마리의 이름과 보호 시작일을 
      조회하는 SQL문 작성. 이때 결과는 보호 시작일 순으로 조회한다.

```sql
SELECT ins.name, ins.datetime
from animal_ins ins
left outer join animal_outs outs on ins.animal_id = outs.animal_id
where outs.animal_id is null
order by ins.datetime 
limit 3
```

교집합 Join

20. 보호소에 들어올 당시에는 중성화1되지 않았지만, 보호소를 나갈 당시에는 중성화된 동물의 아이디와 생물 종, 이름을 조회하는 아이디 순으로 조회하는 SQL 문 작성.

```sql
SELECT ins.animal_id, ins.animal_type, ins.name
from animal_ins ins
join animal_outs outs on ins.animal_id = outs.animal_id
where ins.sex_upon_intake != outs.sex_upon_outcome
order by animal_id
```

21. 동물 보호소에 들어온 동물 중 이름이 Lucy, Ella, Pickle, Rogan, Sabrina, Mitty인 동물의 아이디와 이름, 성별 및 중성화 여부를 조회하는 SQL 문 작성

```sql
SELECT animal_id, name, sex_upon_intake
from animal_ins
where name = 'Lucy' or name = 'Ella' or name = 'Pickle' or name = 'Rogan' or
name = 'Sabrina' or name = 'Mitty'
order by animal_id
```

### LIKE : 데이터에서 특정 문자열 찾고 싶을 때

칼람 like '%포함문자열%'

22. 할머니가 기르던 개는 이름에 'el'이 들어간다고 합니다. 동물 보호소에 들어온 동물 이름 중, 
      이름에 "EL"이 들어가는 개의 아이디와 이름을 조회하는 SQL문 작성. 
      이때 결과는 이름 순으로 조회해주세요. 단, 이름의 대소문자는 구분하지 않습니다

```sql
SELECT animal_id, name 
from animal_ins
where animal_type = 'Dog' and name like '%el%'
order by name
```

23.  동물의 아이디와 이름, 중성화 여부를 아이디 순으로 조회하는 SQL문 작성. 
       이때 중성화가 되어있다면 'O', 아니라면 'X'라고 표시

       if(조건, 참일때, 거짓일 때)

```sql
SELECT animal_id, name,
IF(sex_upon_intake like '%neutered%' or sex_upon_intake LIKE '%spayed%','O','X') AS '중성화'
from animal_ins
order by animal_id
```

24. 입양을 간 동물 중, 
      보호 기간이 가장 길었던 동물 두 마리의 아이디와 이름을 조회하는 SQL문 작성. 
      이때 결과는 보호 기간이 긴 순으로 조회해야 합니다.

```sql
SELECT ins.animal_id, ins.name
from animal_ins ins
join animal_outs outs on ins.animal_id = outs.animal_id
order by outs.datetime - ins.datetime desc
limit 2
```

### date_format(datetime type 칼럼, '형식'

형식 
%Y(4자리 연도), %y(2자리 연도), %m(월), %d(일), %H(24시간), %h(12시간), %i(분), %s(초)

25. 모든 레코드에 대해, 각 동물의 아이디와 이름, 들어온 날짜를 조회하는 SQL문 작성. 
     이때 결과는 아이디 순으로 조회해야 합니다.

```sql
SELECT animal_id, name, date_format(datetime, '%Y-%m-%d') as '날짜' 
from animal_ins
order by animal_id
```

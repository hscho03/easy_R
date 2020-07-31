한국인의 삶의 이해 ‘복지패널데이터
================
조해슬
July 31, 2020

## 6\. 직업별 월급 차이

어떤 직업이 월급을 가장 많이 받을까? 직업별 월급을 분석한다.

### 분석 절차

### 직업 변수 검토 및 전처리하기

#### 1\. 변수 검토하기

직업을 나타낸 code\_job 변수를 살펴본다.

``` r
class(welfare$code_job)

table(welfare$code_job)
```

#### 2\. 전처리

``` r
library(readxl)
list_job <- read_excel("Koweps_Codebook.xlsx", col_names = T, sheet =2)

head(list_job)

dim(list_job)
```

``` r
welfare <- left_join(welfare, list_job, id = "code_job")
```

    ## Joining, by = "code_job"

``` r
welfare %>% 
  filter(!is.na(code_job)) %>% 
  select(code_job, job) %>% 
  head(10)
```

### 직업별 월급 차이 분석하기

#### 1\. 직업별 월급 평균표 만들기

``` r
job_income <- welfare %>% 
  filter(!is.na(job) & !is.na(income)) %>% 
  group_by(job) %>% 
  summarise(mean_income = mean(income))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
head(job_income)
```

#### 2\. 상위 10개 추출

``` r
top10 <- job_income %>% 
  arrange(desc(mean_income)) %>% 
  head(10)


top10
```

#### 3\. 그래프 만들기

앞에서 만든 표를 이용해 그래프를 만든다. 직업 이름이 길기 때문에 그래프를 기본값으로 만들면 x축이 서로 겹쳐 알아볼 수 없다.
coord\_flip()을 추가해 막대를 오른쪽으로 90도 회전한다.

출력된 표와 그래프를 보면 ‘금속 재료 공학 기술자 및 시험원’이 평균 845만 원으로 가장 많은 월급을 받고, 그 다음으로는
’의료진료 전문가’, ‘의회의원 고위공무원 및 공공단체임원’, ’보험 및 금융 관리자’의 월급이 많다는 것을 알 수 있다.

``` r
ggplot(data=top10, aes(x=reorder(job, mean_income), y=mean_income)) +geom_col()+
  coord_flip()
```

![](welfare06_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

#### 4\. 하위 10위 추출

``` r
bottom10 <- job_income %>% 
  arrange(mean_income) %>% 
  head(10)

bottom10
```

#### 5\. 그래프 만들기

``` r
ggplot(data=bottom10, aes(x=reorder(job, -mean_income),
                          y= mean_income)) +
  geom_col() +
  coord_flip()+
  ylim(0, 850)
```

![](welfare06_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

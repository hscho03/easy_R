한국인의 삶의 이해 ‘복지패널데이터’
================
조해슬
July 30, 2020

## 2\. 성별에 따른 월급 차이

데이터 분석을 통해 성별에 따라 월급 차이가 있는지 알아본다. 먼저 성별과 월급 두 변수를 검토하고 전처리한 뒤 변수 간의 관계를
분석한다.

### 분석 절차

### 성별 변수 검토 및 전처리

#### 1\. 변수 검토하기

class()로 sex 변수의 타입을 파악하고, table()로 각 범주에 몇 명이 있는지 알아본다. sex는 numeric
타입이고 1과 2로 구성된다.

``` r
class(welfare$sex)
```

    ## [1] "numeric"

``` r
table(welfare$sex)
```

    ## 
    ##    1    2 
    ## 7578 9086

#### 2\. 전처리

코드북을 보면 성별 변수의 값이 1이면 남자, 2면 여자를 의미한다는 것을 알 수 있다. 모른다고 답하거나 응답하지 않았을
경우에는 9로 코딩되어 있다. 데이터에 이상치가 있는지 검토하고, 분석에서 이상치를 제외할 수 있도록 NA를
부여한다.

sex 변수의 값은 숫자 1과 2로 되어 있다. 값의 의미를 이해하기 쉽도록 문자 “male”과 “female”로 바꾸고
table(), qplot()을 이용해 바꾼 값이 반영됐는지 출력 결과 확인한다.

``` r
welfare$sex <- ifelse(welfare$sex == 1, "male", "female")
table(welfare$sex)
```

    ## 
    ## female   male 
    ##   9086   7578

``` r
library(dplyr)
qplot(welfare$sex)
```

![](welfare02_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

### 월급 변수 검토 및 전처리

#### 1\. 변수 검토하기

income(월급) 변수를 검토하고 qplot()으로 분포를 확인한다. 성별 변수는 범주 변수이기 때문에 table()로 각
범주의 빈도를 확인하면 특징을 파악할 수 있다.

월급변수는 연속 변수이기 때문에 table()을 이용하면 너무 많은 항목이 출력된다. 연속 변수는 summary()로 요약
통계량을 확인해야 특징 파악할 수 있다.

``` r
class(welfare$income)
```

    ## [1] "numeric"

``` r
summary(welfare$income)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##     0.0   122.0   192.5   241.6   316.6  2400.0   12030

``` r
qplot(welfare$income)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 12030 rows containing non-finite values (stat_bin).

![](welfare02_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

#### 2\. 전처리

값이 0이거나 9999일 경우 결측 처리한다.

``` r
#이상치 결측 처리 
welfare$income <- ifelse(welfare$income %in% c(0,9999), NA, welfare$income)

#결측치 확인
table(is.na(welfare$income))
```

    ## 
    ## FALSE  TRUE 
    ##  4620 12044

### 성별에 따른 월급 차이 분석하기

#### 1\. 성별 월급 평균표 만들기

두 변수의 전처리 작업이 완료됐으니 변수 간 관계를 분석한다. 성별 월급 평균표를 만들어 비교한다.

``` r
sex_income <- welfare %>% 
  filter(!is.na(income)) %>% 
  group_by(sex) %>% 
  summarise(mean_income = mean(income))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

#### 2\. 그래프 만들기

앞에서 만든 성별 월급 평균표를 이용해 막대 그래프를 만든다. 남성의 월급이 여성의 두 배 가까이 될 정도로 많다는 것을 알 수
있다.

``` r
ggplot(data=sex_income, aes(x=sex, y=mean_income)) + geom_col()
```

![](welfare02_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

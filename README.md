Python Memo : 파이썬 관련 메모
===

#### R code written while studying
* R 코드 작성 중 자주 검색해보는 것들 또는 까먹지 말아야 할 것 메모했음.
* 전처리, 사용자 함수, 자주 사용하는 코드, 이론 등

*Code*
===
* rm(list=ls()) : 모든 변수 삭제
* NA(결측치)가 N/A로 character로 들어가있는 경우
    * "N/A" 문자 포함된 행 모두 제거
    ```
    for(i in 1:ncol(a)){
        a<-a[a[,i]!="N/A",] }
    ```
* 오름차순, 내림차순 sort
    * 기본
    ```
    sort(v1) # 오름차순 정렬
    sort(v1, decreasing = TRUE) # 내림차순 정렬
    ```
    * 응용1 : No using package
    ```
    data[order(data$v1),]  # v1 기준으로 data 오름차순
    data[rev(order(data$v1)),] # v1 기준으로 data 내림차순
    ```
    * 응용2 : Using package
    ```
    library(dplyr)
    arrange(data,v1) # v1 기준으로 data 오름차순
    arrange(data,desc(v1)) # v1 기준으로 data 내림차순
    ```

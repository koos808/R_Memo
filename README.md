R Scripts Memo : R Scripts 관련 메모
===

#### R code written while studying
* R 코드 작성 중 자주 검색해보는 것들 또는 까먹지 말아야 할 것들을 메모.
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

* Not in <-> %in% 의 반대
    * 1 : ``` `%not_in%` <- purrr::negate(`%in%`) ```
    * 2 : ` setdiff(D1, D0) `

* 여러개의 변수 한번에 생성하기(for 문)
    * 
    ```for(i in 1:12){
        assign(paste0('NRU',i),c()) }
    ```
* for문을 사용할 때 사용하는 여러 변수 불러오기
    * get(paste0('kk',3))

* 문자열(String) 처리
    * 예시 1
    ```
    kkk <- sapply(name, function(x){
        return(gsub(" +","_",trimws(gsub("[^[:alpha:]]", " ", x))))
        })
    ```
    * 예시 1 코드 뜻 => “ +”, “_”  공백이 한 개 이상인 것을 _로 바꾼다.
    * 정규식 표현 : ` [:alpha:] == "[^a-zA-Z]"  : 왼쪽코드는 소문자 대문자 `
    
    * ` trimws() ` : 양쪽 공백제거 함수 
    * ` grep() ` : 문자 조건에 맞춰 추출
        * ex) ``` grep(kkk[1],list1,value=T) # list1[grepl(kkk[1],list1)] ```
        * 조건에 해당하는 문자만 반환



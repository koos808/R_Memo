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
    ```
    for(i in 1:12){
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

* 결측값(NA, NAN) 처리
    * 예시 Code 1
    ```
    for(i in 1:nrow(comb)){
        abs <- set[set$MODEL ==  comb[i, 1] & set$Account == comb[i,2] & set$WH ==comb[i,3], ]
        
        if(length(abs$MAP) < 2){
            k <- rbind(k, comb[i,])
            }else if(is.na(abs$MAP[1]) == T){
                #첫행 na인값 저장
                m<- rbind(m, comb[i,])
                
                }else{
                    for(j in 2:nrow(abs)){
                        if(is.na(abs$MAP[j]) == T){
                            abs$MAP[j] <- abs$MAP[j-1]
                            } } }
                            tot<-rbind(tot,abs)
                            print(paste0(i,"/",nrow(comb))) 
                            }
    ```
    * 예시 Code 2 
        * 열 기준으로 첫 행 데이터가 결측치 인 경우 제거
        * 열 기준 NA값으로 시작하지 않기
        * 
        ```
        첫수요 발생시점까지 QTY 0 삭제
        item_li<-unique(data$ITEM)
        tot<-data.table()
  
    for(i in 1:length(item_li)){
     tmp<-data[ITEM==item_li[i]]
        for(m in 1:nrow(tmp)){
          if(tmp[1,]$QTY==0){
            tmp=tmp[-1,]
        }else{
         break
        }
     }
        tot<-rbind(tot,tmp)
        }

        ```

* GGplot 혹은 R Plot 결과 pdf로 저장하기
    * 
    ```
    pdf(paste0("C:/Users/NURI-0022/Documents/JM/NL_product_predict/",model_flag[i],".pdf"),width = 12,height = 10)
    
    for(j in 1:length(item_li)){
  
  print(melt(dat_2018 , id.vars = c("ITEM","Fcst_month") , measure.vars =  c("Fcst","ACT")) %>%
          filter(ITEM == item_li[j]) %>%
          ggplot(aes(x=Fcst_month,y=value,group=variable,colour=variable))+
          geom_line()+
          theme(axis.text.x = element_text(angle=45 , hjust = 1 , size = 10))+
          ggtitle(item_li[j]))
          
          }
    dev.off()
    ```

*함수*
===
* sprintf() : 문자열을 매개변수 width 길이로 만들고, 빈 자리는 '0'으로 채우기
    * ex) ` sprintf("%05d", var) `

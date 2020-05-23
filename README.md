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

* 캐릭터 타입의 컬럼에서 Split하여 새로운 변수로 추가하기
    * Long version 
        ```
        aka<-strsplit(data$SKU,split = ".",fixed=T)
        aka2<-data.frame(unlist(aka))
        aka2$unlist.aka.<-as.character(aka2$unlist.aka.)
        aka3<-aka2[seq(1,nrow(aka2), by = 2),]
        nl$ITEM<-aka3
        ```
    * Short version.1
        ```
        a<-unlist(strsplit(data$SKU,".",fixed = T))
        data$ITEM<-a[seq(1,length(a),by=2)]
        ```

    * Short version.2
        ` colsplit(data_with_info$SKU, "\\.", names = c("ITEM", "suffix"))[,1] `

* 문자열 대소문자, 일괄 바꾸기
    * 
    ```
    x <- "MiXeD cAsE 123"
    chartr("iXs", "why", x)
    chartr("a-cX", "D-Fw", x)
    tolower(x)
    toupper(x) 
    ```
* 한줄이면 끝나는 여러 패키지 불러오기(library many packages)
    * step1 - ` to_install <- c("arules","catboost","caTools","data.table","doParallel","xgboost" `
    * step2 - ` lapply(to_install, library, character.only = TRUE) `
* 컴퓨터/서버에 설치되어 있는 R Packages Version 간편하게 확인하기
    * 
    ```
    session <- sessionInfo()

    # Other Packages version
    otherpkg_version<-data.table()
    otherpkg_list<-names(session$otherPkgs)

    for(i in 1:length(otherpkg_list)){
    set<-cbind(otherpkg_list[i],session$otherPkgs[otherpkg_list[i]][[1]]$Version)
    otherpkg_version<-rbind(otherpkg_version,set)
    }
    colnames(otherpkg_version) = c("Packages","Version")

    # LoadedOnly Packages version

    loadpkg_version<-data.table()
    load_list<-names(session$loadedOnly)

    for(i in 1:length(load_list)){
    set<-cbind(load_list[i],session$loadedOnly[load_list[i]][[1]]$Version)
    loadpkg_version<-rbind(loadpkg_version,set)
    }

    colnames(loadpkg_version) = c("Packages","Version")

    totalpkg_version<-rbind(otherpkg_version,loadpkg_version)
    ```
* 리스트 병합 : merge
    * `all_model_merged = Reduce(function(...) merge(..., all.x = TRUE, by = c("MODEL","date")), SFA_list)`
* 문자열 중 원하는 문자로 시작하는 데이터만 추출
    * `filename[str_detect(filename,'^train')]` => filename 벡터에서 train으로 시작하는 문자만 뽑겠다. 
* write.csv/fwrite 하기 전 폴더 없으면 생성하라는 코드 : 폴더 생성 code
    * 
    ```
    if(!dir.exists(file.path(paste0(out_path,STORE,"_",DC,"_",product)))) {
        dir.create(file.path(paste0(out_path,STORE,"_",DC,"_",product)),recursive = T, mode = "0777") }
    ```
* 현재 열고 있는 Scripts path 가져오기
    * `dirname(rstudioapi::getActiveDocumentContext()$path)`
    * `library(rstudioapi) ; dirname(getActiveDocumentContext()$path)`
* 패키지 확인(check)하고 없으면 설치하기
    * 
    ```
    requiredPackages = c('lubridate','data.table','dplyr','zoo','ranger','reshape2',
                         'jsonlite','cumstats','stringi')
    for(p in requiredPackages){
        if(!require(p,character.only = TRUE)) install.packages(p)
        library(p,character.only = TRUE)
        }
    ```
*함수*
===
* sprintf() : 문자열을 매개변수 width 길이로 만들고, 빈 자리는 '0'으로 채우기
    * ex1) ` sprintf("%05d", var) `
    * ex2) ` sprintf(".5f", var) `

* assign & get
    * ex1)  
    ```
    for(i in 1:length(dc_list)){
        assign(dc_list[i],value = dcsell[DC==dc_list[i]])   }
    ```
    * ex2) ` assign('ins',readline('Insert:')) `
    * ex3) ` get(dc_list[1]) `

* data.table  - apply(), sum(), aggregate()
    * 1. apply 
        * ex) ` lapply(.SD , sum) `
    * 2. 집계함수 aggregate()
        * ex) `aggregate(. ~ VMI_ORG_CODE+Snapshot_Week, data = a1, FUN=sum) `
    * 3. JOIN
        * ex1) different key `merge(raw_data, GP, by.x ="GP_key",by.y = "Week", all.x=TRUE)`
        * ex2) double key :  `merge(set_A1, set_A2, by = c("MODEL","TIMESTAMP"), all=TRUE)`
    * 4. 정렬
        * ex) ` DT[order(cola)] `
        * 2개 이상 `total_set[order(-score,featureset)]`
    * 5. group_by
        * ex) ` DT[, , by=cola] ` 
    * 6. mutate
        * ex) ` DT[, colc := cola * colb ] `
    * 7. transmute
        * ex) ` DT[, .(colc = cola * colb)] ` 
    * 8. filter
        * ex) ` ET[cola == 3] `
    * 9. Accessing columns in data.table using a character vector of column names(열 이름으로 선택)
        * `av<-grep("Fcst1",colnames(kk),value = T)` => `kk[,av,with=FALSE]`
* SFA 계산
    * 
    ```
    result_target = result_target[,c(paste0("Fcst",1:8),paste0("GapW",1:8)),with=F]

    apply_Sum_target = apply(result_target[, c(grep("Fcst", colnames(result_target), value = T),
                                            grep("Gap", colnames(result_target), value = T)), with = FALSE], 2,
                            function(x) sum(x, na.rm = T))
    SFA_target = c()
    len=8
    for(k in 1:len) { # len 예측 주차
    SFA_target[k]  = c(1 - apply_Sum_target[k + 8] / apply_Sum_target[k])
    }

    ```

*ETC2*
===

* t 분포, z 분포 R code
    ```
    # 5(alpha)% 유의수준 = 95(1-alpha)% 신뢰수준
    # z-score
    Z<-function(mu,x,sd){
    z.score<-(x-mu)/sd
    return(z.score)
    }
    Z(172,166,6)

    # z-score tail probability
    1-pnorm(0.5,0,1)
    1-pnorm(2,0,1)
    pnorm(1,0,1)-0.5
    pnorm(1.8,0,1)-0.5
    qnorm(0.4681,0,1)

    # alpha=0.05, z alpha/2 = 1.96 ::: two-tailed test(양측검정)
    qnorm(0.05/2,0,1,lower.tail = F)
    # alpha=0.01, z alpha/2 = 2.57 ::: two-tailed test(양측검정)
    qnorm(0.01/2,0,1,lower.tail = F)

    # alpha=0.05, z alpha/2 = 1.96 ::: one-tailed test(단측검정)
    qnorm(0.05,0,1,lower.tail = F)
    # alpha=0.01, z alpha/2 = 2.57 ::: one-tailed test(단측검정)
    qnorm(0.01,0,1,lower.tail = F)


    # t 분포 : t distribution
    pt(q=1, df=1, lower.tail = TRUE)
    qt(p=0.75, df=1, lower.tail = TRUE)

    qt(p=0.05, df=10, lower.tail = F)
    qt(p=0.01, df=10, lower.tail = F)

    # 90% 신뢰구간 : alpha 0.1 ::: two-tailed test(양측검정)
    qt(p=0.1/2, df=82, lower.tail = F)
    # 95% 신뢰구간 : alpha 0.05 ::: two-tailed test(양측검정)
    qt(p=0.05/2, df=82, lower.tail = F)
    # 99% 신뢰구간 : alpha 0.01 ::: two-tailed test(양측검정)
    qt(p=0.01/2, df=82, lower.tail = F)

    # 90% 신뢰구간 : alpha 0.1 ::: one-tailed test(단측검정)
    qt(p=0.1, df=82, lower.tail = F)
    # 95% 신뢰구간 : alpha 0.05 ::: one-tailed test(단측검정)
    qt(p=0.05, df=82, lower.tail = F)
    # 99% 신뢰구간 : alpha 0.01 ::: one-tailed test(단측검정)
    qt(p=0.01, df=82, lower.tail = F)

    # F 분포 :  F-test
    # 95% 신뢰구간 : alpha 0.05 ::: one-tailed test(단측검정)
    qf(p=0.95, df1=15, df2=30, lower.tail = TRUE) # F-critical Value

    pf(q=0.5, df1=30, df2=40, lower.tail = TRUE)
    ```
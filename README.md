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


*ETC*
===
# Linux Command
* 사용 tool MobaXterm
    * `shell  __file__ ` or ` python "__file__" `
* `htop` : 서버 사용량 확인
* 커멘드로 파일 또는 폴더 지우기
    * ` rm -r-f /home/desktop/folder/file `
* `cd` : 경로 이동
* sh 파일 실행
    * ` ./train.sh 20190622 20190702 12 `
* 파일 & 폴더 복사
    * 폴더 전체를 복사 : foler_1을 foler_2로 복사
        * ` cp -R /home/desktop/folder_1 /home/desktop/folder_2 `
    * 폴더 하위 파일들을 복사 : foler_1의 하위 폴더들을 folder_2에 복사
        * ` cp -R /home/desktop/folder/file/. /home/desktop/folder_2 `
* Process 종료
    * kill -9 35221(해당 프로세스 name) or `ctrl + Shift + C`


# 리눅스 GIT Command
* git clone(복사) : cd로 원하는 폴더 이동 후 Command 입력
    * ` git clone  http://----.git(주소 복사) commit/ ` 주소의 git을 commit 폴더로 복사
* Commit
    * 1.global setting
        * ex) ( 유저네임 , email ,비밀번호)
        ` git config --global user.email "koos.gu@--.com" `
        ` git config --global user.name "koos.gu" `
    * 2.커밋 폴더로 경로 이동 `cd` 사용
        * 최상위로 이동 : `cd ..` 
    * 3.log 확인
        * `git log` or ` git log -1 `
        * `git status`
    * 4.Add
        * `git add scripts/SFA_XGboost.R scripts/Xgboost.R`
    * 5.Commit
        * `git commit` : [detail NOTE] 입력 후 커밋 내용 작성
    * 6.Push
        * `git push origin master`
    * 7.branch
        * GIT에서 branch 이후 git clone 
            * `git clone http://-----.git -b operation(branch이름) oper(생성폴더이름)/`
            * oper폴더로 이동 : `cd oper/`
            * 목록 확인 : `ls`
            * 폴더가 어떤 branch를 향하고 있는지 확인 : `git branch`
            * 전체 branch 확인 : `git branch -a`
            * add, rm, cp -R 등 작업 후 커밋, 푸쉬
                * `git add scripts/---.R scripts/--.R`
                * `git commit -a` (변경사항 전체 커밋), `git commit 변경파일이름1, 변경파일이름2,...` (부분 커밋)
                * `git push origin operation(브런치 이름)`
    * 8.ls
        * 목록보기
        * 숨김 파일 및 폴더 모두 확인 : `ls -ahl`
    * 9.파일 이름 및 위치 변경
        * `mv preprocess.R preprocess2.R/`
* 이 페이지 참고하면 좋음 : http://rogerdudler.github.io/git-guide/index.ko.html








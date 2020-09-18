###### 상품군_README


설치한 라이브러리 
install.packages("dplyr")        : %>% 와 filter 및 select함수를 사용하기 위한 라이브러리 
install.packages("reshape2")   : melt함수를 사용해서 데이터를 재구조화 시키기 위한 라이브러리 
install.packages("ggplot2")    : ggplot차트를 그리기 위한 라이브러리 
install.packages("scales")       : ggplot그래프의 y축 레이블에 콤마를 넣기 위한 라이브러리

########################################################################
###라이브러리### : 위에서 설치한 라이브러리를 불러오는 구문
library(dplyr)  		: dplyr 불러오기
library(reshape2)		: reshape2 불러오기	
library(ggplot2)		: ggplot2 불러오기
library(scales)		: scales 불러오기

########################################################################
###데이터 읽어오기###
#rm(list=ls())   		: 저장되어있는 데이터들을 삭제 
data.use <- read.csv(choose.files(),)   	: 설치되어 있는 csv파일을 data라는 데이터에 읽어오기 
	   			  파일명 : "온라인쇼핑몰_취급상품범위_상품군별거래액_20200831163311.csv"
data.use %>% head() 		: 읽어 온 data의 5행까지 보여줌 

########################################################################
##### 분기 별 온라인 쇼핑 합계액 #####
data.use.1 <- data.use %>% dplyr::select(시점,합계)   : data.use에서 `시점`과`합계`열을 선택해서 data.use.1에 저장
names(data.use.1) <- c("분기","(억)원")		      :  data.use.1의 변수명을 `분기`,`(억)원`으로 변경
data.use.1 %>% head()			      :  data.use.1의 5행까지 추출하여 보여줌 

########################################################################
###데이터 시각화###
ggp <- ggplot(data=data.use.1,aes(x=분기,y=`(억)원`,group=1)) +   :  data.use.1의 `분기`를 이용한 x축,`(억)원`을 이용한 y축을 가진 ggplot생성 
  geom_line(size=2,color="skyblue")+ 				:  크기는2, 색은 skyblue를 가진 꺽은선 그래프를 그린다.
  scale_y_continuous(labels=comma) +				:  y축 레이블에 콤마를 찍어주는 역할
  theme(axis.text=element_text(size=30),axis.title=element_text(size=25,face="bold"),axis.text.x=element_text(angle=45,hjust=1)) 	:  그래프의 레이블 글씨 크기는 30 축제목은 25와 글씨굵기를 진하게 지정하고 x축의 레이블 각도를 45도로 지정한다.
ggp 	:  위의 실행문이 저장되어 있는 ggp를 실행하는 역할



########################################################################
##### 분기 별 온라인쇼핑 상품군 거래액 #####
data.use.2 <- data.use %>% dplyr::select(시점,가전.전자.통신기기,생활용품,음식서비스,여행.및.교통서비스,음.식료품)    : data.use에서 `시점`,`가전.전자.통신기기`,`생활용품`,`음식서비스`,`여행.및.교통서비스`,`음.식료품` 열을 추출하여 data.use.2에 저장
data.use.2 %>% head()  	:  data.use.2의 5행까지 추출하여 보여준다. 
names(data.use.2) <- c("시점","가전전자통신기기","생활용품","배달음식서비스","여행및교통서비스","음식료품")     :   data.use.2의 변수명을 "시점","가전전자통신기기","생활용품","배달음식서비스","여행및교통서비스","음식료품" 로 변경해준다.

########################################################################
###데이터 재구조화###
data.melt.2 <- melt(data.use.2,id.var="시점",measure.vars=c("가전전자통신기기","생활용품","배달음식서비스","여행및교통서비스","음식료품"))	:  data.use.2의 그래프를 그리기위해  `시점`열을 기준으로 데이터를 재구조화 시킨 후 data.melt.2에 저장해준다
names(data.melt.2) <- c("분기","상품군","(억)원")	    :   재구조화시킨 data.melt.2의 변수명을 "분기","상품군","(억)원"로 변경해준다.
data.melt.2 %>% summary() 			    :  data.melt.2의 요약값을 보여준다
data.melt.2 %>% head()			    :  data.melt.2의 5행까지 추출하여 보여준다. 

########################################################################
## 데이터 전처리 ##
data.melt.a <- data.melt.2 %>% filter(상품군=='가전전자통신기기')		:  data.melt.2의 `상품군`열에 `가전전자통신기기`가 들어있는 행만 추출하여 data.melt.a에 저장한다.
data.melt.a$prepro <- data.melt.a$`(억)원`/data.melt.a[1,3]*100		:  data.melt.a의 1행3열에 해당하는 2017년1분기 거래액을 기준으로 각 분기를 나눈 값에 100을 곱한 후 data.melt.a의 prepro열을 생성하고 저장시킨다

data.melt.b <- data.melt.2 %>% filter(상품군=='생활용품')			:  data.melt.2의 `상품군`열에 `생활용품`이 들어있는 행만 추출하여 data.melt.b에 저장한다.
data.melt.b$prepro <- data.melt.b$`(억)원`/data.melt.b[1,3]*100		:  data.melt.b의 1행3열에 해당하는 2017년1분기 거래액을 기준으로 각 분기를 나눈 값에 100을 곱한 후 data.melt.b의 prepro열을 생성하고 저장시킨다

data.melt.c <- data.melt.2 %>% filter(상품군=='배달음식서비스')		:  data.melt.2의 `상품군`열에 `배달음식서비스`가 들어있는 행만 추출하여 data.melt.c에 저장한다.		
data.melt.c$prepro <- data.melt.c$`(억)원`/data.melt.c[1,3]*100		:  data.melt.c의 1행3열에 해당하는 2017년1분기 거래액을 기준으로 각 분기를 나눈 값에 100을 곱한 후 data.melt.c의 prepro열을 생성하고 저장시킨다

data.melt.d <- data.melt.2 %>% filter(상품군=='여행및교통서비스')		:  data.melt.2의 `상품군`열에 `여행및교통서비스`가 들어있는 행만 추출하여 data.melt.d에 저장한다.		
data.melt.d$prepro <- data.melt.d$`(억)원`/data.melt.d[1,3]*100		:  data.melt.d의 1행3열에 해당하는 2017년1분기 거래액을 기준으로 각 분기를 나눈 값에 100을 곱한 후 data.melt.d의 prepro열을 생성하고 저장시킨다

data.melt.e <- data.melt.2 %>% filter(상품군=='음식료품')			:  data.melt.2의 `상품군`열에 `음식료품`이 들어있는 행만 추출하여 data.melt.e에 저장한다.		
data.melt.e$prepro <- data.melt.e$`(억)원`/data.melt.e[1,3]*100		:  data.melt.e의 1행3열에 해당하는 2017년1분기 거래액을 기준으로 각 분기를 나눈 값에 100을 곱한 후 data.melt.e의 prepro열을 생성하고 저장시킨다

data.rb <- rbind(data.melt.a,data.melt.b,data.melt.c,data.melt.d,data.melt.e)	:  위의 전처리시킨 데이터 `data.melt.a`,`data.melt.b`,`data.melt.c`,`data.melt.d`,`data.melt.e`를 rbind함수를 이용해서 밑으로 병합시킨다
data.rb %>% tail()							:  data.rb의 밑에서부터 5행을 추출하여 보여준다.

########################################################################
## 데이터 시각화 ##
ggp <- ggplot(data=data.rb,aes(x=분기,y=`prepro`,group=상품군,color=상품군)) +	:  data.rb의 `분기`를 이용해 x축을,`prepro`를 이용해 y축을 그룹과 색을 `상품군`으로 나눈 ggplot을 그린다
  geom_line(size=2) + labs(x="분기", y="변화율(%)") + 				:  x축제목은 "분기",y축제목을"변화율(%)"로 하는 꺽은선 그래프를 그린다.
  scale_y_continuous(breaks=c(100,300,500,700)) +				:  y축의 레이블 범위를 100,300,500,700으로 지정한다.
  theme(axis.text=element_text(size=25),axis.title=element_text(size=25,face="bold"),axis.text.x=element_text(angle=45,hjust=1)) +	: 그래프의 레이블 크기를 25크기로, 축제목의 크기를 25와 굵기를 진하게 지정하고 x축 레이블의 각도를 45로 지정한다. 
  theme(legend.title=element_text(size=25,face="bold"),legend.text=element_text(size=20))					: 범례의 제목을 25크기와 굵기를 진하게 지정, 범례글씨는 20크기로 지정한다.
ggp 	:  위의 실행문이 저장되어 있는 ggp를 실행하는 역할		



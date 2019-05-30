# 본인의 과제명 작성

학과 | 학번 | 성명
---- | ---- | ---- 
수학과 |201411149 |주정현


## 프로젝트 개요
최근 탈 원전 정책이 키워드로 떠오르면서 원자력 발전소와 방사능에 대한 관심이
높아지고 있는 가운데 한국도 방사능과 피폭에 대해서 안일하게 생각해서는 안된다.
따라서 방사성 물질이 함유된 제품을 사용할 때 좀 더 주의를 요할 필요성이 있으며,
일반적인 물품의 방사능 검출 여부에 대하여 그 수치를 점검하는 코드를 작성 해볼 예정이다.

## 사용한 공공데이터 
[데이터보기](https://www.data.go.kr/dataset/15022083/fileData.do)

## 소스
* [링크로 소스 내용 보기](https://github.com/jycjoo815/python2019/blob/master/module1.py) 

* 코드 삽입
~~~python
#-------------------------------------------------------------------------------
# Name:        module1
# Purpose: 생활환경 방사능 측정 및 검수 
#
# Author:      주정현
#
# Created:     30-05-2019
# Copyright:   (c) CSE_USER 2019
# Licence:     <your licence>
#-------------------------------------------------------------------------------
import pandas as pd
import math as ma

df = pd.read_csv('radioactivity.csv', engine = 'python')

pddata = pd.DataFrame(df)

use = [] 
disuse1 = []
disuse2 = []
disuse3 = []

a1 = [] #검출여부에 따라 T,F가 순서대로 들어갈 배열
a2 = []
a3 = []

suitable = [] #방사능 수치를 점검한다음 disuse에서 다시 나눌 배열
unsuitable = []

category = set(pddata['식품분류기준대분류'])

while True:
    print("안전성 검토 여부 대분류 목록: ", category )
    x = input("물품의 대분류를 입력하세요 : ")
    if x not in category:
        print("입력오류!")
        break
    else:
        print(x,"에 해당하는 중분류 목록:", set(df[df['식품분류기준대분류'] == x]['식품분류기준중분류']))
        y = input("물품의 중분류를 입력하세요 : ")
    if y not in list(set(df[df['식품분류기준대분류'] == x]['식품분류기준중분류'])) :
        print("입력오류!")
        break
    else:
        print(y,"에 해당하는 소분류 목록:", set(df[df['식품분류기준중분류'] == y]['식품분류기준소분류']))
        z = input("안전성을 검토하고싶은 소분류 목록을 입력하세요 : ")
    if z not in list(set(df[df['식품분류기준중분류'] == y]['식품분류기준소분류'])):
        print("입력오류!")
        break
    else:
        print(z,"에 해당하는 요오드-131 검출여부:","\n",df[df['식품분류기준소분류'] == z]['요오드-131'])
        print(z,"에 해당하는 세슘-134 검출여부:","\n",df[df['식품분류기준소분류'] == z]['세슘-134'])
        print(z,"에 해당하는 세슘-137 검출여부:","\n",df[df['식품분류기준소분류'] == z]['세슘-137'])
        
    for i in list(df[df['식품분류기준소분류'] == z]['요오드-131']): #소분류의 검출여부 리스트에 대하여
        if i.isalpha() == True: #문자열 확인함수 문자열(불검출)일 경우 True
            A = True
            a1.append(A)
        else:
            A = False
            a1.append(A)

    for i in list(df[df['식품분류기준소분류'] == z]['세슘-134']): #소분류의 검출여부 리스트에 대하여
        if i.isalpha() == True:
            A = True
            a2.append(A)
        else:
            A = False
            a2.append(A)

    for i in list(df[df['식품분류기준소분류'] == z]['세슘-137']): #소분류의 검출여부 리스트에 대하여
        if i.isalpha() == True:
            A = True
            a3.append(A)
        else:
            A = False
            a3.append(A)
    print(a1,"\n",a2,"\n",a3)

    for i in range(len(a1)):
        if  a1[i]&a2[i]&a3[i] == True: 
            use.append(i) 
        else: 
            disuse1.append(list(df[df['식품분류기준소분류'] == z]['요오드-131'])[i])
            disuse2.append(list(df[df['식품분류기준소분류'] == z]['세슘-134'])[i])
            disuse3.append(list(df[df['식품분류기준소분류'] == z]['세슘-137'])[i]) #index 나 bool자료형이 아닌 i번째 value 자체를 넣을것

    print(disuse1)
    print(disuse2)
    print(disuse3)

    t = float(input("경과 시간을 입력하세요:"))
    T = 3600*24*365*30 #세슘137에 대하여 검사. 반감기는 고정된 값
    for i in range(len(disuse3)): #i는 물질내에 들어있는 기존 방사능 값
        i = disuse3[i]
        i = float(i)
        if i*((ma.e)**((-t*ma.log(2)/T))) > 1:
            unsuitable.append(i)
        else:
            suitable.append(i)

    print("적합:",suitable) #(order가 존재)
    print("비적합:",unsuitable)

    break
~~~

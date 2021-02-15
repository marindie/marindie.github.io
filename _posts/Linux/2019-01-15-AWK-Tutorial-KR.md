---
toc: true
title: "AWK 사용법 SQL 쿼리 로그 파싱 및 COUNT 리포트 예제 샘플"
description: "AWK 를 사용하여 로그 파일에서 SQL 문 바인딩 과 건수 체킹을 리포트 형태로 출력하는 두가지 예제에 대한 설명입니다"
categories: [Script]
tags: [Awk]
redirect_from:
  - /2019/01/15/
---

> AWK 활용한 로그 파일에서 필요한 형태로 가공하는 방법에 대하여 다루어 보았습니다.

### 배경지식 {#toc1}

```md
1. AWK 는 Text 파싱에 사용 될 수 있는 명령어로서 일반적인 GNU/Linux 환경에서 사용이 가능합니다.
2. 정규식에 대한 이해가 있어야 샘플에 대한 이해가 가능할 것 같습니다. 설명은 달겠지만, 처음 접하시는 분들에게는 어려울 수 있습니다.
3. AWK 를 사용하게 된 배경은, grep 결과의 내용을 가공하여 제가 원하는 내용으로 출력하고자 함이였습니다. 
   이와 비슷한 이유로 오신 분에게 도움이 될 것으로 생각합니다.
```

### AWK 동작 방식 {#toc2}

```md
1. AWK 는 Read, Execute, and Repeat 순환으로 동작합니다. Log 파일이 있다면, 하나의 라인을 Read 하고, AWK 로 작성한 Command 를 Execute 하고, 완료시 다음 라인을 읽어들이는 방식으로 Repeat 를 해나갑니다.
2. AWK 가 Read 하는 방식은 (file, pipe, or stdin) 등 input stream 종류들이 가능합니다.
3. Execute 는 Block 단위로 구분되어 지며, BEGIN block, Body block, END block 으로 나눌 수 있습니다.
4. Begin block syntax 는 "BEGIN {awk-commands}" 와 같은 형태이며, 최초 호출시 한번 수행됩니다. init 함수와 같은 것으로 생각하시면 편할것 같습니다.
5. Body block sytax 는 "/regex(정규식)-pattern/{awk-commands}" 와 같은 형태이며, /정규식/ 없이 {awk-commands} 만으로도 사용은 가능합니다. 이때의 의미는 필터링 없이 모든 라인에 {awk-commands} 명령어를 수행한다. 라고 보시면 될것 같습니다. 저는 일단 {} 로 정의 하고 내부에 필터링 하고자 하는 text 를 추가하였습니다.
6. End block syntax 는 "END {awk-commands}" 와 같은 형태이며, 호출한 AWK 가 최종 종료시점에 동작합니다. 로그 파일 전체를 파싱해서 어떤 특정 케이스의 건수를 리포트 형태로 출력하고자 할 때, END block 에 awk-command 로 작성하면 최종 결과를 뿌려줄 수 있습니다.
```

### AWK 활용법 {#toc3}

```bash
# log4j, logback 등의 로그를 cat 한후, sql 만추출하는 awk 실행
cat /tomcat/log/debug.log | awk -f sqllog.awk

# 운영에서 sql 쿼리가 정상적인지, 또는 sql 문만 집중해서 보면서 테스트 할때,
tail -f /tomcat/log/debug.log | awk -f sqllog.awk

# alias 를 통해서 sql 문의 결과를 특정 파일에 남김
alias sql1='cat /tomcat/log/debug.log | awk -f sqllog.awk > /tomcat/log/sql.log'
alias sql1 >> ~/.bashrc
alias tsql1='tail -f /tomcat/log/debug.log | awk -f sqllog.awk > /tomcat/log/sql.log'
alias tsql1 >> ~/.bashrc
```

### Logback 에서 sqlonly, sqltiming 등과 같은 특정 쿼리문만 추출 {#toc4}

```bash
# 소스 설명
# 주석 처리한 내용은 필요 없고 AWK 소스는 BEGIN 부터입니다.
# 대략 jdbc.sqlonly 로 시작하는 logback 로그를 대상으로 어떤 작업을 진행한다.
# getline 을 통해서 다음 줄을 계속 읽는다.
# select 1 로만 끝나는 connection 체크 쿼리는 print 하지 않고 스킵
# filterSql 등 쿼리중에서 AOP 나 쿼리가 너무 길고 자주 나타나서 소스 흐름을 읽는데 방해가 되는 녀석들 스킵
# 위 두 케이스가 아니면 출력
# 출력을 하다가 종료하는 조건은 스페이스 1칸만 있는 줄을 만나면 종료. 
# 이유는 sqlonly 가 쿼리 출력후 반복적으로 그렇게 로그를 찍기에, 이를 활용해서 SQL 문만 추출

BEGIN{
    IGNORECASE=1
}
{
    if(/jdbc.sqlonly/){
		good = "Y";
		while(getline>0){
			if(/[0-9]. select 1$/){
				while(getline>0){
					if(/^ $/){
						break;
					}					
				}
			}else if(/filterSql/){
				while(getline>0){
					if(/^ $/){
						break;
					}					
				}
			}else{
				print;
			}
			if(/^ $/){
				break;
			}
		}
		if(good == "Y"){
			
		}
    }
}
```

### SQL 쿼리문 파싱 샘플 (옜날 log4j) {#toc5}

```md
* log4j 에서 SQL 문중에 PreparedStatement 와 같은 경우, Binding 하려는 Parameter 값이 
  쿼리문에 들어가지 있지 않은 상태로 "?" 로 표시가 되고 
  그 다음 라인에 해당 값들에 대한 정보를 뿌려주는게 일반적인것 같습니다. 
* 저는 해당 쿼리에서 에러가 나거나 할때, 
  로그에서 복사한 쿼리를 그대로 복사해서 테스트 해보고 싶어서 
  AWK 를 사용해서 완성된 쿼리문을 만들어 사용하고자 AWK 를 배우게 되었습니다. 
* 이 소스는 최근에는 사용할 일이 없을것 같네요. 
* 하지만 소스의 syntax 를 이해하시면, 조건분기가 좀 많이 필요할 경우 도움이 되실 것입니다.
* replace, split 등을 활용했기에 저에게는 굉장히 도움이 많이 되는 소스입니다.
```

```bash
BEGIN{
    IGNORECASE=1
}
{
    if(/Executing Statement/){
        print "=========================================";
        gsub(/^.*?Statement: */,"");
        text = $0;
        while(getline>0){
            if(/Parameters/){
                gsub(/^.*?Parameters: \[/,"");
                gsub(/]/,"");
                param = $0;
                cnt = split($0,arr,",");
                for( i=1 ; i <= cnt ; i++){
                                        temp = "'"arr[i]"'";
                                        gsub(/ /,"",temp);
                    sub("?",temp,text);
                }
                text = text ";"
                break;
            }
        }
        print text;
        print "=========================================\n";
    }
}
```

### 소스 설명 쿼리 파싱 {#toc6}

```md
1. IGNORECASE 문은 옵션과 같은 것으로, 대소문자 구분을 무시하는것을 말합니다. 
2. if 문을 사용하여 정규식 검색을 시도합니다. 라인에 "Executing Statement" 문구가 있으면 if문 안의 명령어들이 실행이 되게 되어 있습니다.
3. gsub 명령어는 정규식을 사용하여 매칭되는 text 들에 대하여 replace 를 진행합니다. 이 때, 어떤 녀석이 INPUT 인지 모르고 사용하게 되는데요.. AWK는 $0 이라는 변수명을 기본적으로 사용하고 있고, 이 변수명에는 현재 읽어 들인 라인의 Text 내용이 기본적으로 담겨있습니다. Matching 된 녀석의 String 들을 어떤 대상으로 바꾸고자 하는 text 는 두번째 parameter string 값이 되겠습니다. (현재 예제에서는 "" 빈값이 되겠지요. 대상 matching String을 날려버리기 위함입니다.) 예제는 빈값으로 replace 합니다. Default 로 replace 한 결과는 $0 이라고 하는 변수에 들어가 있습니다. AWK 내부적으로 동작하는 방식이며, $0 이라는 syntax로 해당 라인 text 값을 조작 할 수 있도록 하고 있습니다. 정규식에 대한 설명을 간략히 하자면, 맨 앞부터 첫번째 "Statement: "라는(공백포함) Text 가 있으면 매칭이라는 의미 입니다. 맨 뒤의 별표는 한개 이상을 의미하는데 그 대상은 공백입니다. 쿼리 로그중 시간 정보와 같은 앞의 녀석을 제거하고 "Select.." 와 같은 쿼리내용만을 담게 됩니다.
4. Replace 된 결과를 text 변수에 담습니다.
5. getline 명령어는 제가 AWK 를 사용하는 주 이유로서, 강제로 다음 라인을 읽어 오도록 하는 녀석입니다. return 값이 0 이면, 파일의 끝에 도달 했다는 의미로 볼 수 있습니다. while 문을 통해서 Parameters 가 포함된 라인을 찾아서 Executing Statement 라인의 쿼리에 있는 ? 값을 Parameters 라인에 있는 실제 값으로 매핑시켜 그 결과를 출력하고자 합니다.
6. "...Parameters: ["로 시작하는 앞단을 제거하고, "]"값을 제거한 Text 만을 param 변수에 담아 놓습니다.
7. "," 를 구분자로 하여 해당 값을 split 하여 array 에 담습니다.
8. for loop 에서 각각의 ? 에 실제 값으로 대응한 결과를 text에 저장합니다.
9. 마지막으로 해당 text 결과를 출력하여 정상여부를 확인합니다.
10. 중간에 break 명령어가 있는데 매우 중요합니다. Parameter 값 Mapping 완료후에는 getline이 호출 되지 않도록 while loop를 빠져나오기 위함입니다.
11. 다음 매칭되는 Executing Statement 문이 나오기 전까지의 라인은 패스 하게 됩니다. 파일의 끝에 도달할때까지 진행됩니다.
```

### COUNT 리포트 생성 샘플 {#toc7}

```md
1. 로그 파일을 보게 되던중, 해당 라인중에서 처리에 걸리는 시간을 출력하는 로그가 있었는데, 그 건들을 처리시간 단위로 분류하여 개수를 뽑아보고 싶어서 아래의 AWK 를 작성하게 되었습니다. 아래는 해당 예제 샘플입니다.
```

```sh

BEGIN{
print "START"; 
CNT_30 = 0;
CNT_50 = 0;
CNT_100 = 0;
CNT_200 = 0;
CNT_300 = 0;
CNT_400 = 0;
CNT_500 = 0;
CNT_600 = 0;
TOTAL = 0;
}

{
        if(/tag=107/)
        {
                ++TOTAL;
                sub(/^.*etime=/,"",$0);
                time = ( $0 * 1000 );
                if( time < 30 ){
                        ++CNT_30;
                }else if( 30 <= time && time < 50 ){
                        ++CNT_50;
                }else if( 50 <= time && time < 100 ){
                        ++CNT_50;
                }else if( 100 <= time && time < 200 ){
                        ++CNT_100;
                }else if( 200 <= time && time < 300 ){
                        ++CNT_200;
                }else if( 300 <= time && time < 400 ){
                        ++CNT_300;
                }else if( 400 <= time && time < 500 ){
                        ++CNT_400;
                }else if( 500 <= time && time < 600 ){
                        ++CNT_500;
                }   
        }
}

END{
        print "END";
        printf "%-10s\t %-10s\t %-10s\t %-10s\t %-10s\t %-10s\t %-10s\t %-10s\t %-10s\n", "CNT_30", "CNT_50", "CNT_100", "CNT_200", "CNT_300", "CNT_400", "CNT_500", "CNT_600", "TOTAL";
        printf "%-10s\t %-10s\t %-10s\t %-10s\t %-10s\t %-10s\t %-10s\t %-10s\t %-10s\n", CNT_30, CNT_50, CNT_100, CNT_200, CNT_300, CNT_400, CNT_500, CNT_600, TOTAL;
}

```

### 소스 설명 COUNT 리포트 {#toc8}

```md
1. "START" 라는 String 을 출력해주고 변수들을 선언하면서 0으로 초기화 시킵니다.
2. tag=107 이 포함된 라인을 찾아서 처리 하고자 if문에 선언하였습니다.
3. 그 해당 라인에서 etime=1.0123 와 같은 처리시간이 포함된 부분을 제외하고 숫자만 남길수 있기에 etime= 까지 모든 앞단의 Text 를 잘라버린후 $0 에 그 결과를 담습니다.
4. AWK 는 String 을 숫자로 알아서 형 변환을 시킵니다. 숫자가 들어간 String 이기만 하면 해당 녀석이 정수건 소수건 알아서 처리 한다고 하네요. 그래서 저는 바로 1000을 곱하는 명령을 날리고 정상 처리하는지 확인해 보았습니다. 잘 처리됩니다.
5. 해당 연산 결과를 시간 번위 단위로 체크하여 해당 시간 안에 처리된 건이라고 표시하기 위해 COUNT 를 증가시킵니다. 30, 50, 100 등등.. 그 수치의 의미는 저에게만 의미있는 것이기에 예제에는 그다지 설명하지 않겠습니다.
6. if else if 사용 syntax 와 && 를 사용하는 AND 조건 사용법을 알수 있으며 ++CNT_400 과 같이 JAVA 형태의 Increment 가 가능하다는것 등을 아실 수 있습니다.
7. C 문법에서 사용하는 printf 를 여기서 사용이 가능합니다. 이를 사용하여 저는 COUNT 결과를 출력하였습니다. \t 는 tab 을 누른 효과를 나타내며, \n 은 Line Break 로 다음 라인으로 가는것은 대부분 아시겠지요. %-10s 는 10자리 길이의 String 값을 확보해 줍니다. 
8. 10 자리 이내의 String 들은 Left 부터 차례로 나타나며 나머지 남은 공간은
   빈 값으로 오른쪽에 채워집니다. RPADDING 이라고 생각하실 수 있겠습니다.
아래는 printf 에 대한 더 심플한 예제입니다.
```

```sh
awk '{ printf "%-10s %s\n", $1, $2 }' cust-info.txt
Amelia     555-5553
Anthony    555-3412
Becky      555-7685
Bill       555-1675
Broderick  555-0542
Camilla    555-2912
Fabius     555-1234
Julie      555-6699
Martin     555-6480
Samuel     555-3430
Jean-Paul  555-2127
```

AWK 사용법에 대해 이해하시는데에 도움이 되었으면 좋겠네요. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io

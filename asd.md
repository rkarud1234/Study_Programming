# 1. 구현된 내용
## 1.1. 정렬 필터링
### 1.1.1 제목: 정렬 필터링
### 1.1.2 내용
지역별로 필터링돼서 가져온 정보들을 가격, 면적에 따라서 정렬해준다.
### 1.1.3 적용 알고리즘
정렬 알고리즘
### 1.1.4 알고리즘 개요
정렬 알고리즘이란 이름 그대로 어떠한데이터들이주어졌을때 이를 정해진 순서대로 나열하는 알고리즘을 정렬 알고리즘이라고 한다.    
정렬 알고리즘을 구현하는 방법은 `Bubble Sort`, `Selection sort`, `Insertion Sort`, `Merge Sort`, `Heap Sort` 등의 종류가 있고 이 이외에도 정말 다양한 정렬 알고리즘이 존재한다.     
현재 서비스에서는 정렬 알고리즘의 구현을 위해 `java API`에서 제공하는 `Collections` 클래스의 `sort` 메서드를 이용하였다.    
`Collections.sort` 메서드는 **삽입 정렬**과 **합병 정렬**을 결합한 `Tim Sort`라는 정렬 방식을 사용하고 있으며 `Collections.sort`의 시간 복잡도는 평균 `O(nlog(n))`이다.  

**(1) 삽입 정렬(Insertion sort)**   
삽입 정렬은 현재 비교하고자 하는 타겟과 그 이전의 원소들과 비교하며 자리를 교환하는 정렬 방법이다.    
삽입 정렬의 전체적인 과정은 아래와 같다. (오름차순 기준)     
1. 현재 타겟이 되는 숫자와 이전 위치에 있는 원소들을 비교한다. (첫 번째 타겟은 두 번째 원소부터 시작한다.)
2. 타겟이 되는 숫자가 이전 위치에 있던 원소보다 작다면 위치를 서로 교환한다.
3. 그 다음 타겟을 찾아 위와 같은 방법으로 반복한다. 

- 삽입 정렬의 장점 및 단점     

**[장점]**     
1. 추가적인 메모리 소비가 작다.
2. 거의 정렬 된 경우 매우 효율적이다. 즉, 최선의 경우 O(N)의 시간복잡도를 갖는다.
3. 안장정렬이 가능하다.

**[단점]**
1. 역순에 가까울 수록 매우 비효율적이다. 즉, 최악의 경우 O(N2)의 시간 복잡도를 갖는다.
2. 데이터의 상태에 따라서 성능 편차가 매우 크다.   


**(2) 합병 정렬(Merge sort)**   
합병 정렬은 정렬해야 할 리스트가 주어졌을 때, 리스트의 사이즈를 1이 될 때까지 분할한 후에 합병하면서 정렬을 수행하는 알고리즘이다.    
요소를 병합하는 시점에 인접한 원소들끼리 비교하여 정렬하는 방식이다.     
합병 정렬의 과정은 아래와 같다.    
1. 주어진 리스트를 절반으로 분할하여 부분리스트로 나눈다. (Divide : 분할)
2. 해당 부분리스트의 길이가 1이 아니라면 1번 과정을 되풀이한다.
3. 인접한 부분리스트끼리 정렬하여 합친다. (Conqure : 정복)

- 합병 정렬의 장점 및 단점     
   
**[장점]**     
1. 항상 두 부분리스트로 쪼개어 들어가기 때문에 최악의 경우에도 O(NlogN) 으로 유지가 된다.
2. 안정정렬이다.

**[단점]**
1. 정렬과정에서 추가적인 보조 배열 공간을 사용하기 때문에 메모리 사용량이 많다.
2. 보조 배열에서 원본배열로 복사하는 과정은 매우 많은 시간을 소비하기 때문에 데이터가 많을경우 상대적으로 시간이 많이 소요된다.


**(3) Tim sort**   
Tim sort는 Merge sort를 기반으로 하되, 좀 더 효율적으로 run을 나누고 제각기 다른 크기를 가진 run을 최대한 효율적인 방법으로 병합하며 실생활 데이터의 특성을 이용하여 여러 가지 최적화 기법을 도입한 정렬 알고리즘이다. 
완전히 무작위인 데이터에 대해서는 속도가 빠른 편은 아니지만 일정한 패턴이 있는 일반적인 데이터에 대해서는 빠른 성능을 보여주고 안정적이며 최악의 시간 복잡도가 O(nlogn)이기에 지금까지도 많은 언어에서 표준 정렬 알고리즘으로 채택하여 사용하고 있다.      

### 1.1.5 적용 서비스: 필터링된 부동산 실거래정보를 원하는 필터로 정렬
해당 정렬 알고리즘을 원하는 지역별 및 검색어별로 필터링된 부동산 실거래정보 조회 화면에서 검색 결과를 원하는 순서대로 정렬할 수 있게 적용해보았다.    
가격 순 정렬, 면적 순 정렬, 건물의 건축년도 순으로 정렬하는 기능을 제공하며, 여러 가지 필터가 동시에 선택된 경우 우선순위는 `면적>가격>건축년도` 순으로 설정하였다.     
기본적으로 검색 결과는 이름순으로 정렬되어있다.     

**정렬 결과**       
**(1) 가격 낮은 순으로 조회한 결과**     
![image](https://user-images.githubusercontent.com/84266499/167080056-63072837-919d-4b8f-b643-2a164c2675d5.png)        

**(2) 면적 넓은 순으로 조회한 결과**     
![image](https://user-images.githubusercontent.com/84266499/167080155-a0fb3910-aacb-4129-abe5-ef8c0676753b.png)     

**(3) 건축년도 빠른 순으로 조회한 결과**     
![image](https://user-images.githubusercontent.com/84266499/167080235-0f7b957f-93d6-481a-9015-2b40decac7fc.png)     

**(4) 여러 가지 필터를 동시에 적용한 경우**      
![image](https://user-images.githubusercontent.com/84266499/167080390-fb68bc24-e8a6-42cc-904e-a1ae1181ee7c.png)       
같은 면적 내에서는 가격 낮은순으로 다시 정렬된다.       

### 1.1.6 적용 서비스 개발 개요      
정렬 필터는 `View`에서 `select`태그를 이용하여 선택할 수 있습니다.      
옵션값으로는 기본값, 오름차순, 내림차순을 설정할 수 있습니다.      

````html
가격: <select id="filter_price">
  <optgroup>
    <option>기본순</option>
    <option>가격 낮은순</option>
    <option>가격 높은순</option>
  </optgroup>
</select> 
면적: <select id="filter_area">
  <optgroup>
    <option>기본순</option>
    <option>면적 좁은순</option>
    <option>면적 넓은순</option>
  </optgroup>
</select>
건축년도: <select id="filter_builtYear">
  <optgroup>
    <option>기본순</option>
    <option>건축년도 빠른순</option>
    <option>건축년도 느린순</option>
  </optgroup>
</select>
````

해당 그룹에 변경이 일어날때마다 `ajax`로 화면을 비동기로 업데이트해줍니다.     
````javascript
    $("#filter_price, #filter_area, #filter_builtYear").on('change', function(){
    	ajaxUpdate();
    });
````      

`ajaxUpdate()` 함수에서는 필터링을 위한 데이터들을 추가로 넘겨줍니다.      
````javascript
data: {'si':$('#local_si option:selected').text(), 
      'gu':$('#local_gu option:selected').text(), 
      'dong':$('#local_dong option:selected').text(), 
      'keyword':$('#search_keyword').val(),
      'price':$('#filter_price option').index($('#filter_price option:selected')),
      'builtYear':$('#filter_builtYear option').index($('#filter_builtYear option:selected')),
      'area':$('#filter_area option').index($('#filter_area option:selected'))},
````

`Controller`에서는 파라미터를 받아서 정렬이 필요한지 판단해줍니다.     
정렬 시 `Collections.sort`를 이용했기 때문에 `Comparator`를 추가로 구현해주었습니다.     

`Comparator`의 `compare()` 메서드에서는 우선 `DB`에서 얻어온 유효한 거래정보 목록들 중 해당 기준으로 정렬을 했을 때 정렬 결과를 도출할 때 쓰일 변수들을 저장해줍니다.      
미선택시 0, 오름차순일 시 1, 내림차순일 시 2가 파라미터로 전달됩니다.    
오름차순일 경우 내림차순의 역순의 기준을 주면 되기 때문에 `(price == 1 ? 1 : -1)` 이런 값을 곱해주었습니다.       
````java
int priceDiff = (price == 1 ? 1 : -1) * (Integer.parseInt(o1.getPrice()) - Integer.parseInt(o2.getPrice()));
````

이런 식으로 리턴할 값을 미리 구해놓고 `if`문을 통해서 해당 기준으로 정렬이 필요한지 판단해줍니다.     
0일 경우 미선택(정렬 기준 없음)이기 때문에 다음과 같은 `if`조건을 설정해줍니다.
````java
if (price != 0 && area != 0)
````
그 이후에 정렬 우선순위(면적>가격>건축년도)에 따라서 분기를 설정해주고 미리 구해놓았던 반환값을 반환해주면 됩니다.        

**<전체 소스코드>**      
````java
			// 우선순위: 면적 - 가격 - 건축년도
			@Override
			public int compare(TempDealData o1, TempDealData o2) {
				int priceDiff = (price == 1 ? 1 : -1)
						* (Integer.parseInt(o1.getPrice()) - Integer.parseInt(o2.getPrice()));
				double areaDiff = (area == 1 ? 1 : -1)
						* (Double.parseDouble(o1.getArea()) - Double.parseDouble(o2.getArea()));
				int builtYearDiff = (builtYear == 1 ? 1 : -1)
						* (Integer.parseInt(o1.getBuiltYear()) - Integer.parseInt(o2.getBuiltYear()));
				if (price != 0 && area != 0) {
					if (o1.getArea().equals(o2.getArea())) {
						return priceDiff;
					} else {
						if (areaDiff > 0) {
							return 1;
						} else if (areaDiff == 0) {
							return 0;
						} else {
							return -1;
						}
					}
				} else if (price != 0) {
					return priceDiff;
				} else if (area != 0) {
					if (areaDiff > 0) {
						return 1;
					} else if (areaDiff == 0) {
						return 0;
					} else {
						return -1;
					}
				} else if (builtYear != 0) {
					return builtYearDiff;
				}
				return 0;
			}
		});
````


## 1.2. 페이징 처리
### 1.2.1 제목: 페이징 처리      
### 1.2.2 내용       
지역별로 검색된 거래정보 결과의 총 데이터 양에 따라서 페이지네이션하는 기능을 제공한다.       
### 1.2.3 적용 알고리즘     
구현 알고리즘      
### 1.2.4 알고리즘 개요      
구현 알고리즘이란 '머릿속에 있는 알고리즘을 소스코드로 바꾸는 과정'이다.      
어떤 문제를 풀든 간에 소스코드를 작성하는 과정은 필수이므로 구현 문제 유형은 모든 알고리즘의 문제 유형을 포함하는 개념이다.      
정확한 풀이 방법을 떠올리고 생각해낸 문제 풀이 방법을 우리가 원하는 프로그래밍 언어로 정확히 구현해냈을 때 비로소 정답 처리를 받을 수 있다. 이를 위해 프로그래밍 언어의 문법을 정확히 알고 있어야 하며 문제의 요구사항에 어긋나지 않는 답안 코드를 실수 없이 작성해야 한다.      
### 1.2.5 적용 서비스: 지역별로 필터링해서 가져온 데이터를 알맞게 페이징처리       
**(1) 지역 필터를 인천광역시-서구-마전동, 검색어는 지정하지 않은 경우**      
총 65개의 데이터를 불러왔으며, 총 7페이지가 만들어지고 마지막 페이지에는 5개의 거래정보만 존재합니다.      
![image](https://user-images.githubusercontent.com/84266499/167083094-84a5d086-18bd-4b85-a542-d516ed0aae0b.png)      
![image](https://user-images.githubusercontent.com/84266499/167083131-38e47ee4-0d1d-4e53-ae1a-f3e875a50009.png)      

**(2) (1)번의 결과에 '검단'이라는 검색 키워드를 추가했을 경우**      
총 19개의 데이터를 불러왔으며, 필터링된 데이터의 개수에 따라서 총 2페이지가 만들어졌습니다.       
![image](https://user-images.githubusercontent.com/84266499/167083333-d8871ae2-6caf-4b47-aae3-3fb67802887e.png)       

**(3) 총 데이터가 100개이상인 경우**   
`prev` 버튼과 `next`버튼을 이용하여 100개 단위로 페이지를 이동할 수 있습니다. (한 페이지당 10개 데이터, 10페이지 단위로 이동)       
![image](https://user-images.githubusercontent.com/84266499/167084171-ad29c7fc-91db-4b41-ad96-268d4823f049.png)


### 1.2.6 적용 서비스 개발 개요     
`View`에서 `select box`에 선택된 값에 따라서 필터링 정보를 `Controller`에게 보냅니다.      
````javascript
 $.ajax({
     url: "${root}/apt/makeDealList",
  	 data: {'si':$('#local_si option:selected').text(), 
  		    'gu':$('#local_gu option:selected').text(), 
  		    'dong':$('#local_dong option:selected').text(), 
  		    'keyword':$('#search_keyword').val(),
		    'price':$('#filter_price option').index($('#filter_price option:selected')),
		    'builtYear':$('#filter_builtYear option').index($('#filter_builtYear option:selected')),
		    'area':$('#filter_area option').index($('#filter_area option:selected'))},
	 method: 'post',
     success: function (datas) {
    	 data = datas;
    	 $('.page_cnt').text('[' + 1 + ']');
         // 페이징 제한
         max_page = datas.length;

    	 console.log('success' + datas.length);
         if (datas.length == 0) {
             // 해당 동에 아무런 거래내역이 없을 경우
             $('table tr:gt(0)').remove(); // 원래 있던 데이터들을 지우고
             let tr = $('<tr></tr>');
             tr.append('<td colspan="6" style="text-align:center;">해당 동의 거래정보 찾을 수 없음</td>');
             $('table').append(tr);
         } else {
             // 거래내역이 있을 경우 테이블과 페이징을 설정해준다.
             makePage(current_page * 10 + 1);
             makePageArea(current_page * 10 + 1, current_page * 10 + 10);
         }
     }
 });
````
`Controller`에서 해당 필터에 따라서 필터링된 부동산 실거래 내역의 리스트를 반환합니다.     
반환된 리스트를 바탕으로 페이징 처리를 해줍니다.       
````javascript
// 페이지를 클릭했을 때 해당 페이지로 이동
 $('.page-area').on('click', '.page-link', function () {
     console.log('page', $(this).text());
     makePage($(this).attr('page'));
     $('.page_cnt').text($(this).text());
 });
 
//페이지 next 버튼
 $('.next_btn').on('click', function () {
	 // 맨 끝 페이지에서는 버튼을 눌러도 페이지가 넘어가지 않도록 조건을 설정해준다. prev에서도 마찬가지
     if (current_page < max_page / 100 - 1) {
         current_page++;
         console.log(current_page);
         makePage(current_page * 10 + 1);
         makePageArea(current_page * 10 + 1, current_page * 10 + 10);
         $('.page_cnt').text('[' + (current_page * 10 + 1) + ']');
     }
 });

 // 페이지 prev 버튼
 $('.prev_btn').on('click', function () {
     if (current_page > 0) {
         current_page--;
         console.log(current_page);
         makePage(current_page * 10 + 1);
         makePageArea(current_page * 10 + 1, current_page * 10 + 10);
         $('.page_cnt').text('[' + (current_page * 10 + 1) + ']');
     }
 });

//페이징 함수
function makePageArea(start, end) {
 $('.page-area').html('');
 for (let p = start; p <= end && p < (max_page / 10) + 1; p++) {
     $('.page-area').append('<a href="#" class="page-link page-link d-inline-block text-dark" page="' + p + '">[' + p + ']</a>&nbsp;');
 }
}

//테이블에 데이터를 그리는 함수
function makePage(page) {
 $('table tr:gt(0)').remove(); // 원래 있던 데이터들을 지우고
 // 새로운 데이터를 등록
 for (let i = (page - 1) * 10; i < page * 10 && i < max_page; i++) {
     let tr = $(`<tr class="table_row"></tr>`);
     tr.append("<td>" + data[i].aptName + '</td>');
     tr.append("<td class='row_bldg_nm'>" + data[i].address + '</td>');
     tr.append('<td>' + data[i].dealDate + '</td>');
     tr.append('<td>' + data[i].price + '</td>');
     tr.append('<td>' + data[i].area + '</td>');
     tr.append('<td>' + data[i].builtYear + '</td>');
     $('table').append(tr);
 }
}
````


# 2. 추가 알고리즘 기획서
## 2.1. 암호화 알고리즘을 회원가입 적용 기획서
### 2.1.1 제목: 암호화 알고리즘을 회원가입 적용 기획서
### 2.1.2 내용
암호화 알고리즘을 회원가입 적용 기획서
### 2.1.3 적용 알고리즘
단방향 암호화 알고리즘 (Hash)
### 2.1.4 알고리즘 개요
- 해시 함수(Hash Function)
 임의의 길이의 데이터를 고정된 길이의 데이터로 변환 시켜주는 함수입니다. 입력값의 길이가 달라도 출력값은 언제나 고정된 길이로 반환되며, 동일한 값이 입력 시 동일한 출력값을 보장합니다. 이를 통하여 단방향 암호화를 수행합니다.

- 단방향 암호화
   - 해시하는 것은 원본 데이터를 문갠다라는 표현을 씁니다.
   - 절대 복호화가 불가능하여야 하며 해시값을 가지고 원본값으로 역산할 수 없어야 합니다.
   - SHA 시리즈 사용  
   - 적용 서비스 : 회원가입, 로그인
   - 적용 서비스 개발 개요
   - 웹 페이지를 만들면서 필수로 고려해야 될 요소 중 하나가 개인 정보 보안 문제이며, 이를 위해 모든 웹 서비스에는 사용자의 정보를 보호하기 위한 장치가 필요하다고 생각했습니다.
회원가입, 로그인 중 혹은 DB에 있는 개인 정보를 복호화가 불가능한 단방향 암호화를 통하여 보호하는 서비스를 고안했습니다. 
   - 장점 : 한번 암호화 한 개인 정보는 복호화가 불가능하여 암호가 유출되더라도 개인 정보의 유출까지는 막을 수 있습니다.
   - 단점 : 단방향 암호화의 특성상 비밀번호 찾기 기능을 만들 수 없습니다.

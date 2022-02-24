# Union-Find
유니온 파인드는 **분리집합**을 구현하기 위한 자료구조이다.     
- 두 원소가 같은 집합인지 구분할 때 사용    
- MST - KRUSCAL 알고리즘을 구현할 때 사용 

분리집합의 의미와 유니온 파인드를 어떻게 구현하는지 알아보도록 하자.     

## 1. 분리집합(Disjoint Set)이란?     
상호배타집합 / 분리집합 / 서로소 집합 이 세 가지 표현은 모두 같은 의미를 갖는다.     
서로 중복포함된 원소가 없는 집합들이라는 의미이다. 즉, 집합들간의 **교집합이 없음**을 의미한다.     
그렇다면 각 원소가 다른 집합에 있는지는 어떻게 구분할까?    
바로 집합의 **대표자**를 통해 구분한다.
     
각각의 집합은 집합을 식별하기 위한 대표자를 하나씩 꼭 가지고있어야 한다. 예를 들어, 학급에 반이 5개 있고, 반의 반장이 각 반을 대표한다고 해보자.   
       
<br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155528400-ed03bb81-ae3d-453a-96fb-94857e6039d1.png" width="70%" height="70%"/>
</div>
<br>

한 학교에 5개의 반이 있다고 했을 때, 두 반에 동시에 소속되어 있는 학생은 없고 각 반에는 한 명의 대표자가 있어 대표자의 이름으로 각 반을 식별할 수 있다.
즉, 이 학교의 반은 분리집합이다.         
그런데 이 학교에서는 반의 반장끼리 결투를 신청할 수 있고, 결투에서 진 반이 이긴 반으로 병합된다. 3반 반장과 5반 반장이 싸워서 5반 반장이 이겼다.
그러면 3반은 5반으로 병합될 것이고, 기존에는 3반 대표자가 물으면 아무개라고 했지만 결투 후에는 아무개의 반이 아이유의 반이 되어버린다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155529201-f1646c7d-a267-403f-bbb8-93485554c846.png" width="50%" height="50%"/>
</div>
<br>
       
유니온 파인드는 이렇게 집합의 대표자를 변경하는 방식으로 서로다른 두 집합을 병합할 수 있다.     
        
서로소 집합을 구현하기 위해서는 아래와 같은 세 가지 연산이 필요하다.
- makeSet(): 최소단위집합 생성
- union(a, b): a가 속한 집합과 b가 속한 집합을 병합. 이 연산 이후에도 서로소 집합을 유지해야한다.    
- find(a): 원소 a가 속한 집합을 반환 (1반 반장이 누구냐 물으면 홍길동을 반환)   

이 때 합치기(union)와 찾기(find)의 두 연산을 지원한다고 해서 이 자료구조를 **유니온-파인드(union-find)** 자료구조라고 부른다.
      
## 2. 유니온 파인드의 표현    
유니온 파인드는 연결 리스트나 트리를 이용해 표현할 수 있다.    

### 2.1 Linked List로 표현
어떤 원소를 속한 집합을 찾기 각각의 원소들은 link를 통해 자신의 부모 노드를 가르키는 형태이다.     
5개의 원소가 있는 상태에서 `makeSet()`을 통해 최소단위의 집합을 생성한다.     
이 때 최대 5개의 집합을 생성할 수 있고 최소단위는 1개가 되며, 각각의 원소들은 자기자신을 가르키고 있는 형태일 것이다.     
여기서 우리는 link가 자기 자신을 나타내고 있는 노드를 만났을 때 그 노드를 해당 집합의 대표자 노드로 반환하면 된다는 사실을 알 수 있다.    

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155533591-b7b0931c-c091-4812-b1f2-02e226477bcd.png" width="50%" height="50%"/>
</div>
<br>

이 상태에서 `union(a,b)`, `union(d,e)`를 해보자.    
<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155534027-09d74012-c903-41ab-87b4-77d9bb200c1f.png" width="50%" height="50%"/>
</div>
<br>

b의 link를 a로 바꾸고, e의 링크를 d로 바꾸면 된다.     
이 상태에서 find(e)를 한다면 다음과 같은 구조가 될 것이다.     
````java
// 현재 node는 e
while(!node.link.equals(node))
    node = node.link;
return node;
````
e와 a의 병합은 한 쪽의 부모 노드를 다른 쪽으로 붙여주면 된다.     
````java
Node pa = find(a); // a
Node pe = find(e); // d

pe.link = pa;
````
와 같이 표현할 수 있다.     
     
### 2.2 트리로 표현
로직 자체는 링크드리스트와 동일하다. 차이점은 링크 대신 부모자식 관계로 나타낸다는 것이다.      
초기 `makeSet()`를 하면 아래와 같은 트리가 생성될 것이다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155536683-dc542647-aece-4f4b-84ea-edf5178c9ce3.png" width="50%" height="50%"/>
</div>
<br>

`union(a,b)`, `union(d,e)`를 하면 아래와 같은 상태가 된다. (편의를 위해 알파벳이 작은 쪽으로 붙는다고 가정한다.)     

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155537348-aedd756a-41eb-4609-8122-38e72e5058a6.png" width="40%" height="40%"/>
</div>
<br>

이 상태에서 `union(b,e)`를 하면 다음과 같은 상태가 된다. 

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155537765-99dc31ed-ee7a-44f6-be04-8dd4a98b5b1d.png" width="25%" height="25%"/>
</div>
<br>

### <a name="desc"></a>
#### 유니온 파인드를 트리로 표현할 때의 문제점
만약 유니온을 다음과 같은 순서로 진행한다면....     
`union(d, e)` → `union(c,d)` → `union(b,c)`   
트리의 모양은 아래와 같아지게 되는데, 이렇게 한쪽 방향으로 자식이 몰리게 될 경우 최악의 경우에 해당 자식의 루트 노드를 찾는데 전체 노드의 수인 `O(N)`만큼 소요될 수도 있다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155538778-03e597aa-d91a-4eba-bc16-4a2c38eaf22c.png" width="35%" height="35%"/>
</div>
<br>

그렇기때문에 우리는 `find()` 연산을 진행하면서 루트 노드를 찾는 중에 해당 노드가 루트 노드를 직접 가리키도록 하는 과정을 함께 수행한다. 이를 Path compression이라고 함 ([구현](#code))      
그러면 같은 연산을 수행하더라도 결과적으로 아래와 같은 트리가 완성되어, `union()`/`find()` 연산 수행 시에 훨씬 유리한 시간으로 해결할 수 있게 된다.     

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155540254-73334f19-68b3-4a8d-9412-f2e4643ae392.png" width="30%" height="30%"/>
</div>
<br>

추가로 유니온 파인드의 시간 복잡도에 대해서는 아래에 상세하게 설명해놓은 블로그가 있으니, 링크를 첨부하겠음     
[유니온 파인드의 시간복잡도(상세)](https://www.secmem.org/blog/2021/04/19/Union-Find-Time-Complexity-Proof/)


## 3. 유니온 파인드의 구현    
유니온 파인드는 연결 리스트나 트리를 이용해 표현한다고 하였지만, 구현을 할 때에는 이를 사용할 필요가 없다.
`Tree`나 `LinkedList`를 이용하여 구현하는 것은 링크 관리도 복잡하고 메모리나 시간 효율이 좋지 않기 때문에 실제로 사용할때는 주로 위와같은 로직을 배열로 구현하여 사용한다.
각 노드의 루트 노드(집합의 번호)가 무엇인지만 저장하면 되기 때문에 1차원 배열 하나를 이용해서 간단하게 표현할 수 있다.     
`parent[i] = i번 원소가 속하는 집합의 번호`

- `makeSet()`: 각각의 원소들을 최소 단위의 분리집합으로 만들어준다.
````java 
void makeSet() {
    for(int i = 1; i <= n; i++)
        parent[i] = i;
}
````
### <a name="code"></a>
- `find(a)`: a원소가 속하는 집합의 번호를 반환한다.
````java
int find (int a) {
    if(a == parent[a]) {
        // 만약 해당 노드의 루트 노드가 자기자신이라면 반환
        return a;
    } else {
        // 아니라면 부모 노드를 타고 올라가며 루트를 만날때까지 찾아준다.
        return parent[a] = find(parent[a]);
    }
}
````
여기에서 `return find(parent[a]);`가 아닌 `return parent[a] = find(parent[a]);`를 해줌으로서 단순히 루트 노드를 타고 올라가는 것 뿐만아니라 해당 노드의 루트 노드를 갱신해주면서 찾을 수 있습니다.([설명](#desc))      
- `union(a, b)`: a원소와 b 원소가 속하는 집합을 병합한다.
````java
void union (int a, int b) {
    int ra = find(a); // a의 루트
    int rb = find(b); // b의 루트

    if(ra == rb) // a와 b가 같은 집합내에 속해있다면 종료
        return;
    p[rb] = ra; // 두 집합을 병합
}
````
`union()` 연산을 수행하면서 `return type`을 변경하는 등의 방법을 통해 두 집합이 같은 집합내에 있는지 판단할 수도 있습니다.     

<details>
<summary>boolean을 return type으로 사용하는 경우</summary>
<div markdown="1">
  
  ````java
  boolean union(int a, int b) {
      int ra = find(a); // a의 루트
      int rb = find(b); // b의 루트

      if(ra == rb) // a와 b가 같은 집합내에 속해있음 = 병합할 수 없음 
          return false;
      p[rb] = ra;
      return true; // 병합 가능
  }
  ````
  
</div>
</details>

### Union-by-rank
Path Compression을 통해서도 유니온 파인드 연산의 효율을 높일 수 있었지만, 한 가지 방법이 더 존재한다. rank를 이용하는 것.     
예를 들어서 아래와 분리집합의 연산 결과 아래와 같은 집합이 형성되었다고 하자.

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155537765-99dc31ed-ee7a-44f6-be04-8dd4a98b5b1d.png" width="25%" height="25%"/>
</div>
<br>

- c집합을 a집합으로 병합: c 하나만 a에 붙이면 됨     
- a집합을 c집합으로 병합: a집합 아래에 있는 모든 subtree를 c로 옮겨줘야 함        
 
어떤 노드에 어떤 노드를 붙이냐에 따라서 위와 같은 차이가 발생하게 된다. 그래서 `rank`를 이용할 수 있다.     
`rank = 자신의 subtree의 높이`     
위의 예시에서 `rank[a] = 2`, `rank[c] = 0`이다.     
`union()` 연산의 최적화를 위해서 rank가 낮은 집합을 rank가 높은 집합에 붙이면 된다.    
각 노드의 rank값 또한 1차원 배열을 이용하여 관리할 수 있다.

````java
// union 연산: union-by-rank 최적화 버전
void union (int a, int b) {
    int ra = find(a); // a의 루트
    int rb = find(b); // b의 루트

    if(ra == rb) // a와 b가 같은 집합내에 속해있다면 종료
        return;
        
    if(rank[ra] < rank[rb]) {
        p[ra] = rb; // ra의 높이가 더 낮다면 ra를 rb에 붙인다.
        rank[rb] += rank[ra];
    } else {
        p[rb] = ra;
        rank[ra] += rank[rb];
    }
}
````

하지만 이러한 경우 `parent` 배열과 `size` 배열 두 개를 사용하기 때문에 메모리를 두 배로 사용하게 됩니다.    
그래서 이를 개선하기 위한 방법으로 `Weighted Union Find`라는 방법이 고안되었습니다. ([참고링크](https://ssungkang.tistory.com/198))

### Weighted Union Find
`parent`배열의 값이 음수일 경우 해당 노드는 루트 노드입니다.     
`parent`배열의 값이 양수일 경우 해당 노드의 루트 노드를 가르킵니다.       
예를 들어 `parent[2] = -3`일 경우 2번 노드 밑에 두 개의 노드가 더 있다는 의미이고, `parent[3] = 5`일 경우 3번 노드의 부모 노드가 5번 노드라는 의미입니다.     

````java
int parent[MAX_SIZE];

for (int i=0; i<MAX_SIZE; i++)
  parent[i] = -1;

int find(int x){
  if (parent[x] < 0){
    return x;
  }
  else{
    int y = find(parent[x]);
    parent[x] = y;
    return y;
  }
}

void union(int x, int y){
  x = find(x);
  y = find(y);
  
  if (x == y)
    return;
  
  // parent[x], parent[y] 값은 음수이므로 값이 작은 경우가 더 높이가 큰 노드이다.
  if (parent[x] < parent[y]){
    parent[x] += parent[y];
    parent[y] = x;
  }
  else {
    parent[y] += parent[x];
    parent[x] = y;
  }
}
````

### 두 원소가 속한 트리의 전체 노드 수를 구하기
node개수를 count하기 위한 nodeCount배열 만들어서 관리      
[참고링크](https://coder-in-war.tistory.com/entry/%EA%B0%9C%EB%85%90-31-%EC%9C%A0%EB%8B%88%EC%98%A8-%ED%8C%8C%EC%9D%B8%EB%93%9CUnion-Find-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)


## 유니온 파인드 연습하기(문제)
- 유니온 파인드 기본문제: [백준1717-집합](https://www.acmicpc.net/problem/1717)      
- [분리 집합 문제 풀기](https://www.acmicpc.net/problemset?sort=ac_desc&algo=81)

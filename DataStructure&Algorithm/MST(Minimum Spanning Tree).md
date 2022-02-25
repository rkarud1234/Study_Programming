# 최소신장트리(MST, Minimum Spanning Tree)
그래프에서 고립된 정점 없이 모든 정점을 최소비용으로 연결하는 것을 스패닝 트리라고 한다.         
가중치 그래프의 스패닝 트리 중 가중치의 합이 가장 작은 트리를 찾는 문제를 최소 스패닝 트리 문제라고 한다.     
스패닝 트리의 문제를 해결하는 방법으로는 `KRUSCAL`알고리즘과 `PRIM`알고리즘이 있다.   
그렇다면 스패닝 트리의 의미는 무엇이며 MST 문제를 해결하기 위한 알고리즘은 어떻게 구현하는지 알아보도록 하자.  


## 1. 신장 트리(Spanning Tree)란?
- **신장트리(Spanning Tree)**     
   → `n`개의 정점으로 이루어진 무향 그래프에서 `n`개의 정점과 `n-1`간선으로 이루어진 부분 그래프      
   → 이 때, 스패닝 트리에 포함된 간선들은 정점들을 트리 형태로 전부 연결해야 한다.     
   > 트리 형태의 의미란 간선들이 사이클을 이루지 않는다는 의미이며, 정점들이 부모-자식 관계로 연결되어야 한다는 의미는 아닙니다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155564700-c55b8825-3417-438d-9dc8-03a96877410b.png" width="80%" height="80%"/>
</div>
<br>

  > 위의 예시에서 왼쪽의 경우 사이클이 있고, 모든 정점들이 하나로 연결되지 않았기 때문에 올바르지 않은 스패닝 트리이다.        
    
<br>    
<br>
    
- **최소 신장트리(Minimum Spanning Tree)**      
그렇다면 당연히도 가중치 그래프의 스패닝 트리들 중에서 간선들의 가중치 합이 최소인 경우를 최소 스패닝 트리라고 한다.    

## 2. 스패닝 트리 알고리즘      
### 2.0 브루트포스로 최소 스패닝 트리를 구할 수 있을까?   
그래프에 `n`개의 정점이 있을 때 그래프의 모든 정점을 연결하기 위해서는 `n-1`개의 간선이 필요할 것이다.     
스패닝 트리를 구하기 위해서 브루트포스 알고리즘을 사용해보자.     
완전 그래프로 예시를 들어보면 완전 그래프에서 간선의 개수는 `n*(n-1)/2`개이고, 이 간선들 중에 `n-1`개의 간선을 뽑는다고 하면 경우의 수는 아래와 같이 나오게 된다.    
![image](https://user-images.githubusercontent.com/84266499/155570034-ef343b6b-8581-42fc-a543-01e6c1b96fa0.png)        
이 경우에 정점이 10개밖에 존재하지 않더라도 `886163135(약 9억)`의 가지의 경우의 수가 존재한다. 그러니 스패닝 트리를 완전탐색으로 구하려는 시도는 잠시 접어두고, 앞으로 소개할 두 탐욕적인 알고리즘을 사용해주도록 하자.    


### 2.1 KRUSCAL 알고리즘     
KRUSCAL 알고리즘은 그래프의 연결 구조를 **간선 중심**으로 판별하여 MST를 찾는 알고리즘이다. 간선 중심으로 판별하기 때문에 간선 리스트를 사용한다. 이 알고리즘을 이해하기 위해서는 먼저 유니온 파인드 알고리즘에 대해서 알아야 한다. 유니온 파인드 알고리즘에 대해서 잘 모른다면 그것을 먼저 학습하도록([유니온 파인드 공부하러가기](https://github.com/rkarud1234/Study_Programming/blob/main/DataStructure%26Algorithm/UnionFind.md))      
크루스칼 알고리즘의 절차는 다음과 같다.      
1. 그래프의 간선 정보를 모두 입력받는다.
2. 입력된 간선 정보를 **가중치**가 **작은** 순서대로 정렬한다.
3. 가중치가 가장 낮은 간선부터 선택하면서 트리를 증가시킨다.
   * 만약 간선을 선택했을 때 사이클이 존재하게 될 경우 해당 간선은 PASS하고 다음 간선을 선택한다.
4. `n-1`개의 간선이 선택될 때까지, 즉 모든 정점이 한 집합을 이룰 때까지 반복한다.     

알고리즘을 이해하기 위해 아래와 같은 예시를 살펴보도록 하자.      
아래와 같은 그래프에서 간선의 정보를 가중치순으로 정렬해보면 다음과 같다.      
편의를 위해 시작 정점은 알파벳 순서가 낮은 정점으로 표시하겠다.       

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155736624-f2730de7-3af0-46fa-8ac1-a054a5ac8591.png" width="80%" height="80%"/>
</div>
<br>

`6`개의 정점을 하나로 잇기 위해 필요한 간선의 개수는 총 `5`개이기 때문에 가중치가 낮은 순서대로 `5`개의 간선을 선택하면 아래와 같은 상태가 된다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155736927-d3131d22-d79c-4417-91ff-e2b55f045cb6.png" width="80%" height="80%"/>
</div>
<br>

무조건 가중치가 낮은 순서대로 간선을 고르면 위와 같이 사이클이 생기거나 집합이 분리되는 현상이 발생할 수 있다.      
우리는 이 문제를 해결하기 위해서 **유니온 파인드(분리 집합)** 알고리즘을 사용할 수 있다.     
`n-1`개의 간선을 선택하면서, 같은 집합의 원소를 잇는 간선을 고르지 않으면 되는 것이다.        
예를 들어 위와 같은 경우 (E-F:6) 간선을 선택할 때, E와 F는 이미 같은 그룹 내에 있으므로 이 간선을 선택하는 것은 의미가 없다.     
그렇기때문에 해당 간선은 건너뛰고 다음 간선을 선택해주면, 최소 비용으로 스패닝 트리를 완성할 수 있게 된다.     

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155737900-b32f0f0f-fbc6-4a6e-868e-1096698622c1.png" width="80%" height="80%"/>
</div>
<br>

간선을 판별하는 과정을 알고리즘으로 구현하면 순서는 다음과 같을 것이다.
1. 간선을 가중치 순서대로 정렬한다.
2. 가중치가 가장 작은 간선부터 꺼내서 해당 간선이 잇는 두 정점이 같은 그룹에 속해있는지 판별한다.
    - 같은 그룹에 속해있다면 해당 간선은 건너뛴다.
    - 같은 그룹에 속해있지 않다면 해당 간선을 이어준다.
3. 2번의 과정을 n-1개의 간선이 선택될 때까지 반복한다.


#### KRUSCAL 알고리즘 구현하기
- **간선을 가중치 순서대로 정렬하기**      
우선 간선 정보를 저장하기 위해선 `시작노드`, `끝노드`, `가중치`의 세 가지 정보가 필요할 것이다. 이 정보들을 효율적으로 관리하기 위해서 `Edge`라는 이름의 클래스를 선언해주자.

````java
class Edge {
   int from, to, weight; // 시작노드, 끝노드, 가중치
   
   Node(int from, int to, int weight) { // 생성자
         this.from = from;
         this.to = to;
         this.weight = weight;
   }
}
````

클래스를 선언했다면 이 클래스를 `PriorityQueue`나 배열 등에 담아서 **가중치가 작은 순서대로** 정렬해야 할 것이다.     
레퍼런스 타입 변수를 원하는대로 정렬하는 방법은 `Comparator`나 `Comparable`이 있다. 지금은 `Node` 클래스에 `Comparable`을 구현해보도록 하겠다. ([Comparator와 Comparable](https://github.com/rkarud1234/Study_Programming/blob/main/DataStructure%26Algorithm/Comparable%EA%B3%BC%20Comparator.md))        

````java
class Edge implements Comparable<Edge> {
   // 클래스 구조는 생략
   @Override
   public int compareTo(Edge e) {
      return weight - e.weight;
   }
}
````
이제 `Edge` 배열을 만들어 정렬해주거나 `PriorityQueue`에 담아주면 정렬 과정은 끝난다!     
예제 코드는 `PriorityQueue`를 이용해 작성하도록 하겠다.

- **간선을 꺼내서 간선이 잇는 두 정점이 같은 집합에 속해있는지 판별하기**     
이제 `PriorityQueue`에 있는 간선의 정보를 하나씩 꺼내서 `n-1`개의 간선이 선택될 때 까지 반복해주면 된다.    
같은 집합에 있는지 판단하기 위해서는 두 정점의 루트 노드 정보를 저장해놓은 `int[] parent`배열을 바탕으로 유니온-파인드를 수행해주면 된다.

````java
while(n > 0 && !pq.isEmpty()) { // n:정점개수
   Edge edge = pq.poll(); // 가중치가 가장 낮은 간선부터 꺼내서
   if(union(edge.from, edge.to)) { // 같은 그룹이 아닐때만 합쳐준다
      ans += edge.weight; // MST의 가중치
      n--;
   }
}
````

여기서, `union(int a, int b)` 함수의 `return type`을 `boolean`형으로 선언했기 때문에 위와 같은 코드가 가능하다.      
````java
// union 함수의 내부구조
private static boolean union(int a, int b) {
   int pa = find(a);
   int pb = find(b);
   if (pa == pb)
      return false;
   p[pb] = pa;
   return true;
}
````

KRUSCAL 알고리즘은 이와 같이 간단히 구현할 수 있으며, **간선 중심**으로 알고리즘을 수행하기 때문에 **정점 수에 비해 간선 수가 적은 희소 그래프(Sparse Graph)** 에서 수행하기 적합한 알고리즘이다. 

### 2.2 PRIM 알고리즘     
PRIM 알고리즘은 그래프의 연결 구조를 **정점 중심**으로 판별하여 MST를 찾는 알고리즘이다. 정점 중심으로 탐색을 수행하기때문에 인접 행렬이나 인접 리스트를 사용한다.     
프림 알고리즘의 절차는 다음과 같다.    
1. 그래프의 임의의 한 점에서 탐색을 시작한다.
2. 해당 점에서 연결할 수 있는 정점들 중 가장 낮은 가중치로 연결할 수 있는 정점과 연결한다.
3. 연결한 정점에서 2번을 수행한다. (단, 이미 연결된 간선, 정점은 제외하고 판단)

PRIM 알고리즘은 말로만 설명하면 조금 와닿지 않기 때문에 그림을 같이 봐보도록 하자.      
PRIM 알고리즘을 수행할 때 시작 정점은 어디든지 상관 없다. 이번에는 임의의 정점 `D`에서 시작해보도록 하자.    
D에서 연결할 수 있는 간선들의 가중치 `2, 4, 7, 11`중 가장 작은 가중치 `2`를 갖는 간선과 연결해준다.      

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155751476-bcb3f58a-1b14-4f33-ba77-af702186881b.png" width="80%" height="80%"/>
</div>
<br>


그럼 이제 `D,F`가 그룹에 속해있고, 그룹에서 연결할 수 있는 간선인 `4,6,7,11`중 가장 작은 가중치 `4`를 갖는 간선과 연결해준다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155751696-54bf0325-dac6-4505-9c6b-fae9bf46fc88.png" width="80%" height="80%"/>
</div>
<br>

그룹: `C,D,F` / 가능한 간선: `1,6,7,11` / 최소 가중치 간선인 `1`에 연결

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155751744-a2e4c29e-8b4a-4de7-b584-17f91a31bd89.png" width="80%" height="80%"/>
</div>
<br>

그룹: `B,C,D,F` / 가능한 간선: `5,6,7,8,11` / 최소 가중치 간선인 `5`에 연결     
이 때, `E`연결되기 때문에 `E`와 연결되어 있던 다른 간선(`6`,`11`)들은 사용하지 않게 된다.   

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155751788-efeb8cb9-ac00-4d3b-8687-126f07b9f26d.png" width="80%" height="80%"/>
</div>
<br>



그룹: `B,C,D,E,F` / 가능한 간선: `7,8` / 최소 가중치 간선인 `11`에 연결       
`8`간선은 사용X

<div align="center">
  <img src="https://user-images.githubusercontent.com/84266499/155751920-2336e172-ae0d-4068-8509-82fdac4bbd20.png" width="80%" height="80%"/>
</div>
<br>

> 알고리즘 수행하는 과정을 잘 보면 가능한 간선의 가중치 중 최소인 간선을 먼저 꺼내야하기 때문에 이 또한 `Node(from, to, weight)` 타입을 저장할 수 있는 `PriorityQueue`로도 구현이 가능하다.

이렇게 해서 최소 스패닝 트리를 완성하였다.    
이 알고리즘을 구현하면 순서는 다음과 같다.      

1. 아무 정점에서 시작. 시작할 때 시작 정점의 `visit`값을 `true`로 설정한다.
2. 해당 정점과 연결 가능한 아직 방문하지 않은(`visit==false`) 정점 중, 최소 가중치 값으로 이을 수 있는 정점을 찾는다.
3. 찾은 정점과 연결해주고, 도착한 정점의 `visit`을 `true`로 변경한다.


#### PRIM 알고리즘 구현하기

**1. 배열 버전**
- **아무 정점에서 시작하기**
````java
boolean[] visit = new boolean[v + 1]; // 방문 체크 배열
int[] minEdge = new int[v + 1]; // 최소 가중치값을 저장할 배열
Arrays.fill(minEdge, Integer.MAX_VALUE); // 최솟값을 저장해야하기 때문에 충분히 큰 값으로 배열을 초기화해준다.

// visit check는 처음에 하지 않는다. 구현하는 순서를 보면 왜인지 알 수 있음
// visit[start] = true;
minEdge[start] = 0;
````

- **스패닝 트리에 포함되지 않은 원소들 중 최소비용을 갖는 원소 뽑기**
`for(i)`문을 통해서 돌리고 있지만 인덱스가 의미를 갖지는 않는다. 단지 `n`번을 반복하기 위해 사용했을 뿐 정점 순서대로 진행하지 않는다는 점을 명심하라.
````java
for (int i = 0; i < n; i++) {
   // 최솟값과 그 정점을 저장할 변수를 대충 만들어준다.
   int min = Integer.MAX_VALUE;
   int minV = 0;
   
   for (int j = 0; j < n; j++) {
      if (!visit[j] && min > minEdge[j]) { // 아직 스패닝 트리에 연결되어있지 않은 정점인데, 닿을 수 있는 거리가 현재보다 짧다.
         // 그러면 그거랑 연결해야함
         min = minEdge[j];
         minV = j;
      }
   }
   
   // 위의 for(j)문을 통해서 아직 방문한 적 없는 정점 중 최소 비용으로 닿을 수 있는 점을 찾았다.
   // 그러면 그 정점을 신장트리에 포함해준다.
   visit[minV] = true; // 방문체크 완료
   
   // 새로운 정점이 선택되었으니, 해당 정점에서 갈 수 있는 최솟값을 갱신해주자
   for (int j = 0; j < n; j++) {
      // 아직 방문하지 않았고 && 방문할 길이 있으며 && 현재 그 점에 갈 수 있는 비용보다 더 적은 비용으로 갈 수 있다면
      if (!visit[j] && adj[minV][j] != 0 && minEdge[j] > adj[minV][j])
         // 비용을 최솟값으로 갱신
         minEdge[j] = adj[minV][j];
   }
}
````
이 과정을 마치면 `minEdge` 배열에는 해당 정점을 연결할 수 있는 가중치의 최솟값들이 저장된다.     
이를 이용해 MST의 가중치를 구할 수 있다.

**2. 우선순위 큐 버전**     
위의 PRIM 알고리즘 설명에서 말했듯 최소 가중치로 갈 수 있는 정점을 먼저 연결해야 하기에 그 정점 정보를 `PriorityQueue`에 저장하기만 하면 된다!   
물론 이를 위해 우리는 `Comparable`이 구현된 `Node` 클래스가 필요할 것이다.

````java
class Node implements Comparable<Node> {
   int v, weight; // 정점번호, 가중치
   
   Node (int v, int weight) {
      this.v = v;
      this.weight = weight;
   }
   
   @Override
   public int compareTo (Node n) {
      return weight - n.weight;
   }
}
````

`Node` 클래스의 구현이 완료되었다면 최솟값 찾는 부분만 다시 구현해주면 된다.     

````java
// 시작점을 pq에 담고 시작한다.
pq.offer(new Node(start, 0));

while(!pq.isEmpty()) {
   Node node = pq.poll(); // 가장 유리한 정점이 자동으로 나온다.
   
   if (visit[node.v]) // 연결된 정점에 또 연결하지 않도록 판별해주는 작업
      continue;
   
   visit[node.v] = true; // 연결되지 않았던 정점은 연결처리해주고
   ans += node.weight; // MST의 가중치에 현재 정점의 가중치를 더해준다.
   
   // 더해진 정점에서 갈 수 있는 간선들의 정보를 업데이트해주기만 하면 끝
   for (int i = 0; i < n; i++) // 인접행렬 이용
      if ( adj[node.n][i] != 0 && !visit[i] )
         pq.offer(new Node(i, adj[node.n][i]));
      
   for (Node next: adj[node.d]) // 인접 리스트 이용
      pq.offer(next);
}
````

<br>

### 마무리
이렇게 해서 MST를 구하는 방법에 대해서 공부해보았다.     
 KRUSCAL  | PRIM 
|:-------:|:--------:|
|간선 중심|정점 중심|
|정점의 수가 간선의 수에 비해 많을 때 사용|간선의 수가 상대적으로 많을 때 사용|
|유니온 파인드를 이용해 구현|PriorityQueue 또는 배열을 이용해 구현|
|O(Elog2(E))|O(n^2)|

<br>
<br>

## 스패닝 트리 연습하기(문제)    
- [KRUSCAL 알고리즘 대표 문제: 백준1197-최소 스패닝 트리](https://www.acmicpc.net/problem/1197)      
- [PRIM 알고리즘 대표 문제: 백준 4386-별](https://www.acmicpc.net/problem/4386)      
- [스패닝 트리 관련 문제 풀러가기](https://www.acmicpc.net/problemset?sort=ac_desc&algo=49)      

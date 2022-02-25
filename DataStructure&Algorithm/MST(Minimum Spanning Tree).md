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
KRUSCAL 알고리즘은 그래프의 연결 구조를 **간선 중심**으로 판별하여 MST를 찾는 알고리즘이다. 이 알고리즘을 이해하기 위해서는 먼저 유니온 파인드 알고리즘에 대해서 알아야 한다. 유니온 파인드 알고리즘에 대해서 잘 모른다면 그것을 먼저 학습하도록([유니온 파인드 공부하러가기](https://github.com/rkarud1234/Study_Programming/blob/main/DataStructure%26Algorithm/UnionFind.md))      
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

KRUSCAL 알고리즘은 이와 같이 간단히 구현할 수 있으며, **간선 중심**으로 알고리즘을 수행하기 때문에 **정점 수에 비해 간선 수가 적은 그래프**에서 수행하기 유리한 알고리즘이다. 



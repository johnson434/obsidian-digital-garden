---
{"dg-publish":true,"permalink":"/06-full-notes/prim-algorithm/","noteIcon":""}
---

#Algorithm #Graph #kruskal 

**개념**
- 최소 신장 트리(MST)를 구하는 알고리즘
- 그리디한 방식으로 현재 우선 순위 큐에 들어있는 간선 중에 가장 가중치가 작은 간선을 계속해서 연결한다.
- 크루스칼보다 구현은 쉽다. 다익스트라 느낌이라.
**간단 알고리즘 요약**
1. 한 점을 시작점으로 잡는다.
2. 해당 점을 기준으로 주변 간선을 모두 우선순위 큐에 넣는다.
3. 우선 순위 큐에서 간선 비용이 가장 작은 간선을 구하고 연결한다.
``` python
import sys
import heapq

sys.stdin = open("input.txt", "r")

T = int(input())
# 여러개의 테스트 케이스가 주어지므로, 각각을 처리합니다.
for test_case in range(1, T + 1):
    cnt_of_v, cnt_of_e = map(int, input().split())
    graph = [[] for _ in range(cnt_of_v)]
    visited = [False] * cnt_of_v
    sum = 0
    
    for i in range(cnt_of_e):
        start, end, weight = map(int, input().split())
        graph[start - 1].append((end - 1, weight))
        graph[end - 1].append((start - 1, weight))
    
    queue = []
    current_weight, current_point = [0,0]
    heapq.heappush(queue, (current_weight, current_point))
    
    connected = 0
    while queue and connected < cnt_of_v:
        current_weight, current_point = heapq.heappop(queue)
        if visited[current_point]:
            continue
        
        sum = sum + current_weight
        connected = connected + 1
        visited[current_point] = True
        
        for next_point, next_weight in graph[current_point]:
            heapq.heappush(queue, (next_weight, next_point))

    print(f"#{test_case} {sum}")
```


# 관련 문제
https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV_mSnmKUckDFAWb&categoryId=AV_mSnmKUckDFAWb&categoryType=CODE&problemTitle=3124&orderBy=FIRST_REG_DATETIME&selectCodeLang=ALL&select-1=&pageSize=10&pageIndex=1
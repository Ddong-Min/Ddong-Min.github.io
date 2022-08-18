---
layout: post
title: "[Ddong-Min] SWEA 1767 - 프로세서 연결하기"
excerpt: "SWEA 문제풀이 - 프로세서 연결하기"

categories:
    - SW expert academy
tags:
    - [SW expert academy]

toc: true
toc_sticky: true

data: 2022-08-17
last_modified_at: 2022-08-17
---

## 프로세서 연결하기 문제
SWEA 1767번 -> <https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV4suNtaXFEDFAUf>

## 문제풀이
### 첫번째 풀이
2022-08-17

아직 vector 사용법이 익숙하지 않기 때문에 벡터를 사용하지 않고 푸는 방법을 고안해 보았습니다.

코드 메모리는 13,548kb 이고 코드 수행시간은 184ms입니다.

```cpp
#include<iostream>
#include<string.h>

int dfs(int, int , int, int map[14][14]);

using namespace std;
int cnt, dist_ans, processor_ans;
int dx[4] = { 0,1,0,-1 };
int dy[4] = { 1,0,-1,0 };
int check[200][2];

int main(int argc, char** argv)
{
	int test_case;
	int T;
	int map[14][14];

	cin >> T;
	
	for (test_case = 1; test_case <= T; ++test_case)
	{	
		int N, length;
		int cntt=0, dist = 0, processor=0;
		cnt = 0;
		dist_ans = 2147483647;
		processor_ans = 0;
		memset(map, -1, sizeof(map));
		memset(check, 0, sizeof(check));	
		cin >> N;

		for (int i = 1; i <= N; i++) {
			for (int j = 1; j <= N; j++) {
				cin >> map[i][j];
				if (map[i][j] == 1) {
					check[cnt][0] = i;
					check[cnt++][1] = j;
				}
			}
		}
		length = dfs(cntt, dist, processor, map);
		cout << "#" << test_case << ' ' << length << endl;
	}
	return 0;//정상종료시 반드시 0을 리턴해야합니다.
}

int dfs(int cntt, int dist, int processor, int map[14][14]) {
	int ddist = 0;
	
	//모든 프로세서를 다 돌아봤을 경우
	if (cntt == cnt) {
		
		//전원이 연결된 프로세서 수가 더 많다면 바꿔줌
		if (processor_ans < processor) {
			processor_ans = processor;
			return dist;
		}
		//연결된 프로세서 수가 같다면 길이가 더 짧은 걸로 변경해줌
		else if (processor_ans == processor) {
			if (dist_ans > dist) {
				return dist;
			}
			else return dist_ans;
		}
		else return dist_ans;
	 }
	
	int curr_x = check[cntt][0];
	int curr_y = check[cntt][1];

	//동서남북
	for (int i = 0; i < 4;i++) {
		ddist = 0;
		int next_x = curr_x + dx[i];
		int next_y = curr_y + dy[i];
		
		//cell만 있는 경우라면 전선이 지나가게 해줌
		while (map[next_x][next_y] == 0) {
			map[next_x][next_y] = 2;
			next_x += dx[i];
			next_y += dy[i];
			ddist++;

		}

		//끝부분에 도달하면 새로 연결된 전선의 길이를 더해주고 프로세서 개수를 늘려줌
		if (map[next_x][next_y] == -1) {
			dist += ddist;
			processor++;
			
		}

		//끝부분이 아닌 다른 프로세서나 전선에 도달한 경우 전선을 다시 지워줌
		else {
				next_x -= dx[i];
				next_y -= dy[i];
		
			while (next_x != curr_x || next_y != curr_y) {
					map[next_x][next_y] = 0;
					next_x -= dx[i];
					next_y -= dy[i];
				
			}
		}

		//최대 연결가능 프로세서 개수가 기존 최대 프로세서 개수보다 많은 경우만 실행
		if(processor+cnt-cntt > processor_ans)
			dist_ans = dfs(cntt + 1, dist, processor, map);

		//연결가능 프로세서 수가 같으면 전선 길이가 이미 더 긴경우는 제외해줌
		else if(processor + cnt - cntt == processor_ans && dist < dist_ans)
			dist_ans = dfs(cntt + 1, dist, processor, map);

		//기존에 연결됬었던 전선 제거  => 다른 전선이 연결된 경우를 보기 위해서
		if (map[next_x][next_y] == -1) {
			dist -= ddist;
			processor--;
		}
		if (next_x != curr_x || next_y != curr_y) {
			next_x -= dx[i];
			next_y -= dy[i];
		}
		while (next_x != curr_x || next_y != curr_y) {
			if (map[next_x][next_y] != 1 &&  map[next_x][next_y] != -1) {
			map[next_x][next_y] = 0;
			next_x -= dx[i];
			next_y -= dy[i];
			}
		}
	} 
 	return dist_ans;
}

```

### 풀이설명

풀이 아이디어 :

- 프로세서들을 왼쪽 위에서부터 오른쪽 아래로 이동하면서 각각 프로세서들에 대해 전선을 동서남북중 한방향으로 쭉 연결하여 전선의 총 길이가 가장 짧은 경우를 찾아야 합니다.

- 따라서 전선이 연결될 수 있는 모든 경우의 수를 알아보기 위해 DFS를 활용해서 알아보는 방법을 선택했습니다.

- 다만 모든 경우를 살펴보았을때는 1000ms가 넘는 코드 수행시간이 걸렸습니다.

```cpp
if(processor+cnt-cntt > processor_ans)
	dist_ans = dfs(cntt + 1, dist, processor, map);

else if(processor + cnt - cntt == processor_ans && dist <dist_ans)
	dist_ans = dfs(cntt + 1, dist, processor, map); 
```

- 따라서 위와 같은 조건들을 함수가 자기자신을 호출하기 전에 조건을 걸어주어서 필요없는 경우를 제외하고 찾아보도록 했습니다.
이런 조건을 추가로 걸어준 결과 184ms까지 코드 수행시간을 줄일 수 있었습니다.


cf) 코드 메모리나 코드 수행시간을 줄일 수 있는 풀이를 알아내면 추가로 작성하겠습니다.
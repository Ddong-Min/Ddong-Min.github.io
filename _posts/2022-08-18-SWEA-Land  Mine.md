---
layout: post
title : "[Ddong-Min] SWEA 1868 - 파핑파핑 지뢰찾기"
excerpt: "SWEA 문제풀이 - 파핑파핑 지뢰찾기"

categories:
    - SW expert academy
tags:
    - [SW expert academy]
toc: true
toc_sticky: true

data: 2022-08-18
last_modified_at: 2022-08-18
---

## 파핑파핑 지뢰찾기 문제
SWEA 1868번 -> <https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5LwsHaD1MDFAXc>

## 문제풀이
### 첫번째 풀이
2022-08-18

DFS를 여러번 사용하는 방식을 활용해서 문제푸는 방법을 고안해 보았습니다.

코드 메모리는 13,992kb 이고 코드 수행시간은 124ms입니다.

```cpp
#include<iostream>
#include<string.h>

using namespace std;

int N, area;
int map[302][302]; 
int land_mine[302][302];
int dx[] = { 1, 0, -1, 0, 1, 1, -1, -1};
int dy[] = { 0, 1, 0, -1, 1, -1, 1, -1 };

void dfs(int, int);

int main(int argc, char** argv)
{
	int test_case;
	int T;
	cin >> T;

	for (test_case = 1; test_case <= T; ++test_case)
2022-08-17-SWEA-Land  Mine	{
		int count;
		
		/*초기화*/
		area = 0;
		memset(map, 0, sizeof(map));
		memset(land_mine,-1, sizeof(land_mine));

		cin >> N;

		for (int i = 1; i <= N; i++) {
			string str;
			cin >> str;

			for (int j = 1; j <= N; j++) {
				map[i][j] = str[j - 1];

			}
		}
		for (int i = 1; i <= N; i++) {
			for (int j = 1; j <= N; j++) {
				count = 0;

				//지뢰가 아닌경우 주변에 지뢰가 몇개 있는지 확인
				if (map[i][j] == '.') {
					for (int k = 0; k < 8; k++) {
						int c_x = i + dx[k];
						int c_y = j + dy[k];
						
						if (map[c_x][c_y] == '*')
							count++;
					}

					//지뢰 개수 저장
					land_mine[i][j] = count ;
					}

				//지뢰일 경우 개수 저장하는 배열안에 -1 저장
				else {
					land_mine[i][j] = -1;
				}
				}
			}

		//주변에 지뢰가 하나도 없고 아직 방문하지 않았다면 해당 구역의 크기 확인
		for (int i = 1; i <= N; i++) {
			for (int j = 1; j <= N; j++) {
				if (land_mine[i][j] == 0 && map[i][j] == '.') {
				
					dfs(i, j);
					area++;
				}
			}
		}

		//주변에 지뢰가 있는 칸들만 남아있는경우 칸개수만큼 더해줌
		for (int i = 1; i <= N; i++) {
			for (int j = 1; j <= N; j++) {
				if (map[i][j] == '.') {
					area++;
				}
			}
		}
		cout << '#' << test_case << ' ' << area << endl;
	}
	return 0;//정상종료시 반드시 0을 리턴해야합니다.
}

void dfs(int x, int y) {

	//방문했다는것을 보여줌
	map[x][y] = land_mine[x][y]+'0';

	//주변 칸들을 확인해서 주변에 지뢰개수가 0인칸이 있으면 함수가 자기자신을 호출
	for (int k = 0; k < 8; k++) {
		int c_x = x + dx[k];
		int c_y = y + dy[k];

		if (map[c_x][c_y] == '*') continue;
		if (land_mine[c_x][c_y] == 0 &&  map[c_x][c_y]=='.') {
			dfs(c_x, c_y);
		}
		else {
			map[c_x][c_y] = land_mine[c_x][c_y] + '0';
		}
	}

}
```

### 풀이설명

풀이 아이디어 :

- 처음에는 "0으로 표시 될 칸들과 이와 인접한 칸들이 한 번의 클릭에 연쇄적으로 숫자가 표시된 것을 볼 수 있다." 라는 구절의 의미를 잘 이해하지 못해서 문제를 잘못접근했었습니다. 

- 해당 구절의 이미는 0으로 표시될 칸의 인접한 칸중에 0으로 표시되는 칸이 있다면 그 칸의 인접한 칸들도 숫자로 표시되는 것입니다. 하지만 처음에는 인접한 칸에 0인 칸이 있어도 그 주변칸까지 숫자로 표시될 것이라고 생각하지 못했었습니다.

- 따라서 해당문제는 외각 부근이 0이 아닌 숫자로 표시되는 영역 하나가 클릭 한번에 나타나기 때문에 여러번 dfs를 진행함으로써 구역의 개수를 찾는 방식으로 풀이를 진행했습니다.

- 또한 map배열에서 방문한 위치는 '.' 에서 '0'~'8'까지 주변 지뢰개수로 변경해 표시해줌으로써 방문했다는 정보를 전달해주었다. 처음에는 visited배열을 추가로 사용해서 방문했다는 정보를 전달해 주려 했지만 코드 메모리를 최소화 하기 위해서 위와 같은 방법을 사용했습니다.

cf) 코드 메모리나 코드 수행시간을 줄일 수 있는 풀이를 알아내면 추가로 작성하겠습니다.

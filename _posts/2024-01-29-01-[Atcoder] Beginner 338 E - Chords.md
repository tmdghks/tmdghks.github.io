---
layout: post
title: "[Atcoder] Beginner 338 E - Chords"
author: tmdghks
categories: "Problem_Solving"
tags: [알고리즘, PS, Atcoder, 스택, 수학, 기하학]
---


### [문제 링크](https://atcoder.jp/contests/abc338/tasks/abc338_e "앳코더 문제 링크")

### 분류

스택 (Stack), 수학 (Mathematics), 기하학 (Geometry)

### 일러두기

먼저, 본 풀이는 엣코더 홈페이지에 업로드되어 있는 에디토리얼을 참고하였음을 밝힌다.

### 문제 해석

임의 자연수 $N~(2 \le N \le 2 \times 10^5)$에 대하여 $1$부터 $2N$ 까지 일정한 간격으로 점이 적힌 원이 존재한다.  
이 원에는 $N$개의 현이 존재하고, 입력으로 $i$번째 현을 구성하는 두 점 $A_i,~B_i$가 주어질 때, 임의의 두 현이 교차하면 Yes, 그렇지 않으면 No를 출력하라. 단, 모든 $A_i,~B_i$의 값은 다르다.

### 이용해야 하는 수학적 성질

먼저 $\forall _{1 \le i \le N}~ A_i < B_i$ 이어도 일반성을 잃지 않으므로, 이렇다고 가정하자.  
먼저 원주를 직선으로 만든다고 생각해보자. 그렇다면 두 현이 교차하기 위한 필요충분조건은 $\exists_{1 \le i,~ j \le N} ~A_i < A_j < B_i < B_j$ 이다. 따라서 위 명제를 만족하는지만 판별하면 쉽게 이 문제를 풀 수 있다. 

### 첫 번째 시도

첫 번째 시도는 단순하게 벡터에 선분을 잇는 두 점 $a,~b~(a<b)$ 를 저장한 뒤 구간의 최대 최소를 저장하여 동적계획법을 이용하여 어떤 현의 구간이 다른 현의 구간에 포함되거나 다른 현의 구간을 포함하는지의 여부만을 판별하였다. 다음의 코드를 보자.
```C++
// Atcoder 338: E - Chords
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

struct dt {
    ll a;
    ll b;
};

using vdt = vector<dt>;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    ll n, a, b;
    cin >> n;
    vdt v;
    for (ll i = 0; i < n; i++) {
        cin >> a >> b;
        if (a > b) {
            v.push_back({b, a});
        } else {
            v.push_back({a, b});
        }
    }

    ll dp1 = v[0].a, dp2 = v[0].b;

    bool f = false;
    for (ll i = 1; i < n; i++) {
        if (dp1 <= v[i].a && dp2 >= v[i].b) {
            dp1 = v[i].a, dp2 = v[i].b;
        } else if (dp1 >= v[i].a && dp2 <= v[i].b) {
            continue;
        } else {
            f = true;
            break;
        }
    }

    if (f) {
        cout << "Yes";
    } else {
        cout << "No";
    }
}
```

하지만 이 경우 다음과 같은 반례가 존재한다.
```Input
2
1 2
3 4
```

```Output
Yes
```

그림을 그려보면, 자명하게 교차하는 지점이 없다는 사실을 알 수 없다. 다만 내가 잘못 생각한 조건에 따르면 $[1, 2] \not\subset [3, 4] \land [3, 4] \not\subset [1, 2]$이므로 교차하는 것으로 판별하여 Yes를 반환한다. 따라서 이 알고리즘은 문제 조건에 맞지 않는다.

### 정답 풀이

그러면 위와 같은 케이스를 모두 고려하는 알고리즘은 무엇일까? 스택의 First In Last Out 성질을 이용하여 풀이할 수 있다.  
먼저 원주에서의 $1$번 점부터 $2N$번 점까지 순차적으로 탐색하기 위하여 크기가 $2N$인 벡터 $v$를 선언하고 입력받은 점 $A_i,~B_i~(A_i < B_i)$에 대하여 $A_i$의 경우 $\{0, i\}$의 형태로, $B_i$의 경우 $\{1, i\}$의 형태로 저장하자.  
그리고 $1$번 점부터 $2N$번째 점까지 차례로 살피는데, $i$번째 점이 시작점일 경우, 즉 $v[i].a = 0$의 경우에는 스택에 $v[i].b$ 즉 선분의 번호를 저장한다. 다음으로 $i$번째 점이 끝점일 경우, 즉 $v[i].a = 1$의 경우에는 스택의 톱과 $v[i].b$ 즉 선분의 번호를 비교한다. 이것이 다를 경우 앞선 조건 $\exists_{1 \le i,~ j \le N} ~A_i < A_j < B_i < B_j$를 만족하는 것이므로 Yes를 출력하고 반환한다. $2N$번째 점까지 순회했을 때 이러한 점이 없다면 교차하는 현이 없다는 것이므로 No를 출력한다.
이 알고리즘의 시간 복잡도는 $\rm{O}\it(N)$임이 자명하다. 따라서 주어진 시간 2초 안에 해결할 수 있다.

### 최종 정답 코드

```C++
// Atcoder 338: E - Chords
#include <bits/stdc++.h>

using namespace std;
using ll = long long;
using vll = vector<ll>;

struct dt {
    ll a;
    ll b;
};

using vdt = vector<dt>;
bool dt_cmp(dt lhs, dt rhs) {
    return lhs.a < rhs.a;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    ll n; cin >> n;
    vdt v(2 * n);
    ll a, b;
    for (ll i = 0; i < n; i++) {
        cin >> a >> b;
        --a, --b;
        if (a > b) swap(a, b);

        v[a] = {0, i};
        v[b] = {1, i};
    }
    
    stack<ll> st;
    for (ll i = 0; i < 2 * n; i++) {
        if (v[i].a == 0) {
            st.push(v[i].b);
        } else {
            if (st.top() != v[i].b) {
                cout << "Yes";
                return 0;
            }
            st.pop();
        }
    }

    cout << "No";
}
```

### 별해

에디토리얼에서 세그먼트 트리를 이용하여 $\rm{O}\it(N\rm log \it N)$의 시간복잡도로 풀 수 있다는 것이 알려져있다. 다만 아직 내가 세그먼트 트리를 숙지하지 못하여 세그먼트 트리를 이용한 풀이는 다음 기회에 작성하고자 한다.
---
title: "Google Kick Start 2022 Round E Solution"
date: 2022-08-23
categories:
  - Solutions
tags:
  - KickStart
classes: wide
---

[Google Kick Start Round E 2022](https://codingcompetitions.withgoogle.com/kickstart/round/00000000008cb0f5){: .btn .btn--primary}

# Coloring Game
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N;
        scanf("%d", &N);
        printf("Case #%d: %d\n", kase, (N + 4) / 5);
    }
    return 0;
}
{% endhighlight %}

# Students and Mentors
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
int R[MAXN], S[MAXN];

int main() {
    S[0] = -1;
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N;
        scanf("%d", &N);
        for(int i = 1; i <= N; ++i) scanf("%d", R + i), S[i] = R[i];
        sort(S + 1, S + 1 + N);
        printf("Case #%d:", kase);
        for(int i = 1; i <= N; ++i) {
            int idx = upper_bound(S + 1, S + 1 + N, 2 * R[i]) - S;
            if(idx && S[idx - 1] == R[i]) idx--;
            printf(" %d", S[idx - 1]);
        }
        puts("");
    }
    return 0;
}
{% endhighlight %}

# Matching Palindrome
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
char P[MAXN];

inline bool check_palindrome(int l) {
    for(int i = 0; i < l / 2; ++i)
        if(P[i] != P[l - 1 - i]) return false;
    return true;
}

inline bool check_cycle(int l, int N) {
    for(int i = 0; i < N; ++i) 
        if(P[i] != P[i % l]) return false;
    return true;
}

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N;
        scanf("%d %s", &N, P);
        for(int i = 1; i <= N; ++i) {
            if(N % i) continue;
            if(check_palindrome(i) && check_cycle(i, N)) {
                P[i] = 0;
                printf("Case #%d: %s\n", kase, P);
                break;
            }
        }
    }
    return 0;
}
{% endhighlight %}

# Pizza Delivery
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef tuple<int, int, int, int> ti4;
const ll INF = 1e18;
int X[22], Y[22], C[22], K[4], MP[11][11], dx[5] = {-1, 0, 0, 1, 0}, dy[5] = {0, 1, -1, 0, 0};
char op[4][2];
ll F[11][11][21][1 << 10];
bool V[11][11][21][1 << 10];


inline ll fn(ll x, int d) {
    if(op[d][0] == '+') return x + K[d];
    if(op[d][0] == '-') return x - K[d];
    if(op[d][0] == '*') return x * K[d];
    if(op[d][0] == '/') return (ll) floor(1.0 * x / K[d]);
}

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N, P, M, Ar, Ac;
        scanf("%d %d %d %d %d", &N, &P, &M, &Ar, &Ac);

        for(int i = 1; i <= N; ++i)
            for(int j = 1; j <= N; ++j)
                for(int k = 0; k <= M; ++k)
                    for(int p = 0; p < (1 << P); ++p)
                        F[i][j][k][p] = -INF, V[i][j][k][p] = 0;

        for(int i = 1; i <= N; ++i)
            for(int j = 1; j <= N; ++j)
                MP[i][j] = -1;

        for(int i = 0; i < 4; ++i) scanf("%s %d", op + i, K + i);
        for(int i = 0; i < P; ++i) scanf("%d %d %d", X + i, Y + i, C + i), MP[X[i]][Y[i]] = i;

        queue<ti4> q;
        q.push(make_tuple(Ar, Ac, 0, 0));
        F[Ar][Ac][0][0] = 0;
        V[Ar][Ac][0][0] = 1;

        while(!q.empty()) {
            ti4 f = q.front();
            q.pop();
            int x = get<0>(f), y = get<1>(f), t = get<2>(f), p = get<3>(f);
            for(int d = 0; d <= 4; ++d) {
                int nx = x + dx[d], ny = y + dy[d], nt = t + 1, np = p;
                if(nx < 1 || nx > N || ny < 1 || ny > N) continue;
                ll nc = F[x][y][t][p];
                if(d < 4) nc = fn(nc, d);
                F[nx][ny][nt][np] = max(F[nx][ny][nt][np], nc);
                if(nt < M && !V[nx][ny][nt][np]) {
                    V[nx][ny][nt][np] = 1;
                    q.push(make_tuple(nx, ny, nt, np));
                }
                if(MP[nx][ny] != -1 && ((p >> MP[nx][ny]) & 1) == 0) {
                    np = np ^ (1 << MP[nx][ny]);
                    nc += C[MP[nx][ny]];
                    F[nx][ny][nt][np] = max(F[nx][ny][nt][np], nc);
                    if(nt < M && !V[nx][ny][nt][np]) {
                        V[nx][ny][nt][np] = 1;
                        q.push(make_tuple(nx, ny, nt, np));
                    }
                }
            }
        }

        ll ans = -INF;
        for(int i = 1; i <= N; ++i)
            for(int j = 1; j <= N; ++j)
                ans = max(ans, F[i][j][M][(1 << P) - 1]);
        
        if(ans == -INF) printf("Case #%d: IMPOSSIBLE\n", kase);
        else printf("Case #%d: %lld\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

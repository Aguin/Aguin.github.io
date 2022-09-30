---
title: "Google Kick Start 2022 Round F Solution"
date: 2022-09-30
categories:
  - Solutions
tags:
  - KickStart
classes: wide
---

[Google Kick Start Round F 2022](https://codingcompetitions.withgoogle.com/kickstart/round/00000000008cb409){: .btn .btn--primary}

# Sort the Fabrics
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1111;
int D[MAXN], U[MAXN], idx1[MAXN], idx2[MAXN];
string S[MAXN];

bool cmp1(int i, int j) {
    if(S[i] != S[j]) return S[i] < S[j];
    return U[i] < U[j];
}

bool cmp2(int i, int j) {
    if(D[i] != D[j]) return D[i] < D[j];
    return U[i] < U[j];
}

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N;
        scanf("%d", &N);
        for(int i = 1; i <= N; ++i) {
            char s[20];
            scanf("%s %d %d", s, D + i, U + i);
            S[i] = string(s);
            idx1[i] = idx2[i] = i;
        }
        sort(idx1 + 1, idx1 + 1 + N, cmp1);
        sort(idx2 + 1, idx2 + 1 + N, cmp2);
        int ans = 0;
        for(int i = 1; i <= N; ++i) ans += idx1[i] == idx2[i];
        printf("Case #%d: %d\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

# Water Container System
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1e5 + 10;
vector<int> G[MAXN];
inline void add(int u, int v) {G[u].push_back(v);}
int D[MAXN];

void dfs(int u, int fa, int dep) {
    D[dep]++;
    for(int i = 0; i < G[u].size(); ++i) {
        int v = G[u][i];
        if(v == fa) continue;
        dfs(v, u, dep + 1);
    }
}

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N, Q, u, v, x;
        scanf("%d %d", &N, &Q);
        for(int i = 1; i <= N; ++i) G[i].clear(), D[i] = 0;
        for(int i = 1; i < N; ++i) {
            scanf("%d %d", &u, &v);
            add(u, v), add(v, u);
        }
        dfs(1, 0, 1);
        for(int i = 1; i <= Q; ++i) scanf("%d", &x);
        int ans = 0;
        for(int i = 1; i <= N; ++i) {
            if(Q >= D[i]) Q -= D[i], ans += D[i];
            else break;
        }
        printf("Case #%d: %d\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

# Story of Seasons
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<ll, ll> pll;
const int MAXN = 1e5 + 10;
ll Q[MAXN], L[MAXN], V[MAXN], idx[MAXN];
bool cmp(int i, int j) {return L[i] < L[j];}


int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        ll D, N, X, ans = 0;
        scanf("%lld %lld %lld", &D, &N, &X);
        for(int i = 1; i <= N; ++i) {
            idx[i] = i;
            scanf("%lld %lld %lld", Q + i, L + i, V + i);
        }
        sort(idx + 1, idx + 1 + N, cmp);
        set<pll> val;
        for(int i = 1; i <= N; ++i) {
            while(1) {
                val.emplace(pll(V[idx[i]], idx[i]));
                if(i < N && L[idx[i + 1]] == L[idx[i]]) ++i;
                else break;
            }
            ll cur_date = max(0ll, D - L[idx[i]]);
            ll nxt_date = max(0ll, i == N ? 0 : D - L[idx[i + 1]]);
            ll r = (cur_date - nxt_date) * X;
            while(r && !val.empty()) {
                ll c = val.rbegin() -> second;
                if(Q[c] <= r) val.erase(pll(V[c], c)), ans += V[c] * Q[c], r -= Q[c];
                else Q[c] -= r, ans += V[c] * r, r = 0;
            }
        }
        printf("Case #%d: %lld\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

# Scheduling a Meeting
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
typedef pair<int, int> pii;
const int MAXN = 1e5 + 10;
int P[MAXN], L[MAXN], R[MAXN];

int mt[MAXN], sum_sm;
set<pii> sm, lg;

void init(int N, int K) {
    sum_sm = 0;
    sm.clear(), lg.clear();
    for(int i = 1; i <= N; ++i) {
        mt[i] = 0;
        if(i <= K) sm.insert(pii(0, i));
        else lg.insert(pii(0, i));
    }
}

void balance(int K) {
    while(sm.size() < K) {
        pii tmp = *lg.begin();
        lg.erase(tmp), sm.insert(tmp), sum_sm += tmp.first;
    }
    while(sm.size() > K) {
        pii tmp = *sm.rbegin();
        lg.insert(tmp), sm.erase(tmp), sum_sm -= tmp.first;
    }
}

void update(int p, int x) {
    int K = sm.size();
    if(sm.find(pii(mt[p], p)) != sm.end())
        sum_sm -= mt[p], sm.erase(pii(mt[p], p));
    else lg.erase(pii(mt[p], p));
    mt[p] += x;
    if(!sm.empty() && mt[p] < sm.rbegin()->first) sm.insert(pii(mt[p], p)), sum_sm += mt[p];
    else lg.insert(pii(mt[p], p));
    balance(K);
}


int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N, K, X, D, M;
        scanf("%d %d %d %d %d", &N, &K, &X, &D, &M);
        init(N, K);
        set<int> ts;
        map<int, vector<int>> be, ed;
        for(int i = 1; i <= M; ++i) {
            scanf("%d %d %d", P + i, L + i, R + i);
            ts.insert(L[i] - X), ts.insert(R[i]);
            be[L[i] - X].push_back(P[i]);
            ed[R[i]].push_back(P[i]);
        }
        ts.insert(0);
        int ans = M;
        for(auto t: ts) {
            for(auto p: ed[t]) update(p, -1);
            if(t >= 0 && t <= D - X) ans = min(ans, sum_sm);
            for(auto p: be[t]) update(p, 1);
        }
        printf("Case #%d: %d\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

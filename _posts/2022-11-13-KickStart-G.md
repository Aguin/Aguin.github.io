---
title: "Google Kick Start 2022 Round G Solution"
date: 2022-11-13
categories:
  - Solutions
tags:
  - KickStart
classes: wide
---

[Google Kick Start Round G 2022](https://codingcompetitions.withgoogle.com/kickstart/round/00000000008cb2e1){: .btn .btn--primary}

# Walktober
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1005;
int S[MAXN][MAXN];

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int M, N, P;
        scanf("%d %d %d", &M, &N, &P);
        for(int i = 1; i <= M; ++i)
            for(int j = 1; j <= N; ++j)
                scanf("%d", &S[i][j]);
        int ans = 0;
        for(int j = 1; j <= N; ++j) {
            int mx = S[1][j];
            for(int i = 1; i <= M; ++i) mx = max(mx, S[i][j]);
            ans += mx - S[P][j];
        }
        printf("Case #%d: %d\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

# Curling
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int Rs, Rh, N, M, X, Y;
        scanf("%d %d %d", &Rs, &Rh, &N);
        vector<int> T1, T2;
        for(int i = 1; i <= N; ++i) {
            scanf("%d %d", &X, &Y);
            int dis = X * X + Y * Y;
            T1.push_back(dis);
        }
        scanf("%d", &M);
        for(int i = 1; i <= M; ++i) {
            scanf("%d %d", &X, &Y);
            int dis = X * X + Y * Y;
            T2.push_back(dis);
        }
        sort(T1.begin(), T1.end());
        sort(T2.begin(), T2.end());
        int sw = 0, ans1 = 0, ans2 = 0;
        int mx_dis = (Rh + Rs) * (Rh + Rs);
        T1.push_back(mx_dis + 1);
        T2.push_back(mx_dis + 1);
        if(T1[0] > T2[0]) T1.swap(T2), sw = 1;
        for(int i = 0; i < T1.size(); ++i) {
            if(T1[i] < T2[0] && T1[i] <= mx_dis) ans1 += 1;
            else break;
        }
        if(sw) swap(ans1, ans2);
        printf("Case #%d: %d %d\n", kase, ans1, ans2);
    }
    return 0;
}
{% endhighlight %}

# Happy Subarrays
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int MAXN = 4e5 + 10;
int A[MAXN], S[MAXN], stk[MAXN];
ll TS[MAXN];

inline ll get_sum(int l, int r, int N) {
    ll ts = TS[r] - TS[l - 1], s = S[r] - S[l - 1];
    return ts - s * (N - r);
}

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N, top = 1;
        scanf("%d", &N);
        for(int i = 1; i <= N; ++i) {
            scanf("%d", A + i);
            S[i] = S[i - 1] + A[i];
            TS[i] = TS[i - 1] + (N + 1ll - i) * A[i];
        }
        ll ans = 0;
        stk[top] = N;
        for(int l = N; l > 0; --l) {
            while(top && S[l - 1] <= S[stk[top]]) --top;
            if(!top) ans += get_sum(l, N, N);
            else if(l < stk[top]) ans += get_sum(l, stk[top] - 1, N);
            stk[++top] = l - 1;
        }
        printf("Case #%d: %lld\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

# Cute Little Butterfly
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int, int> pii;
const int MAXN = 1e5 + 10;
const ll INF = 1e18;
int X[MAXN], Y[MAXN], C[MAXN];

class ds{
public:
    ll c[MAXN << 2];
    void init(int p, int l, int r) {
        c[p] = -INF;
        if(l < r) {
            int m = (l + r) >> 1;
            init(p << 1, l, m), init((p << 1) | 1, m + 1, r);
        }
    }
    void update(int p, int l, int r, int x, ll y) {
        if(l == r) {c[p] = max(c[p], y); return;}
        int m = (l + r) >> 1;
        if(x <= m) update(p << 1, l, m, x, y);
        else update((p << 1) | 1, m + 1, r, x, y);
        c[p] = max(c[p << 1], c[(p << 1) | 1]);
    }
    ll query(int p, int l, int r, int ql, int qr) {
        if(ql <= l && r <= qr) return c[p];
        ll ret = -INF;
        int m = (l + r) >> 1;
        if(ql <= m) ret = max(ret, query(p << 1, l, m, ql, qr));
        if(qr >= m + 1) ret = max(ret, query((p << 1) | 1, m + 1, r, ql, qr));
        return ret;
    }
};


int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N, E, mx_x = 0;
        scanf("%d %d", &N, &E);
        map<int, vector<pii>> y_mp;
        for(int i = 1; i <= N; ++i) {
            scanf("%d %d %d", X + i, Y + i, C + i);
            X[i] += 2, mx_x = max(mx_x, X[i]); // [2, mx_x]
            y_mp[Y[i]].emplace_back(pii(X[i], C[i]));
        }
        ds le, ri;
        le.init(1, 1, mx_x + 1), ri.init(1, 1, mx_x + 1);
        ri.update(1, 1, mx_x + 1, 1, 0);
        le.update(1, 1, mx_x + 1, mx_x + 1, -E);
        ll ans = 0;
        for(auto ptr = y_mp.rbegin(); ptr != y_mp.rend(); ++ptr) {
            vector<pii> pos = ptr->second;
            pos.emplace_back(pii(1, 0));
            pos.emplace_back(pii(mx_x + 1, 0));
            sort(pos.begin(), pos.end());
            int n = pos.size();
            for(int i = 0; i < n; ++i) {
                int x = pos[i].first, c = pos[i].second;
                ll cur = ri.query(1, 1, mx_x + 1, 1, x) + c;
                ans = max(cur, ans);
                ri.update(1, 1, mx_x + 1, x, cur);
            }
            for(int i = n - 1; i >= 0; --i) {
                int x = pos[i].first, c = pos[i].second;
                ll cur = le.query(1, 1, mx_x + 1, x, mx_x + 1) + c;
                ans = max(cur, ans);
                le.update(1, 1, mx_x + 1, x, cur);
            }
            le.update(1, 1, mx_x + 1, mx_x + 1, ri.query(1, 1, mx_x + 1, mx_x + 1, mx_x + 1) - E);
            ri.update(1, 1, mx_x + 1, 1, le.query(1, 1, mx_x + 1, 1, 1) - E);
        }
        printf("Case #%d: %lld\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

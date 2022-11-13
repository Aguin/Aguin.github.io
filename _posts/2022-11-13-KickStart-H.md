---
title: "Google Kick Start 2022 Round H Solution"
date: 2022-11-13
categories:
  - Solutions
tags:
  - KickStart
classes: wide
---

[Google Kick Start Round H 2022](https://codingcompetitions.withgoogle.com/kickstart/round/00000000008cb1b6){: .btn .btn--primary}

# Running in Circles
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e4 + 10;
typedef long long ll;

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        char C[10];
        ll L, N, D, pos = 0, dir = 0, cnt = 0;
        scanf("%lld %lld", &L, &N);
        for(int i = 1; i <= N; ++i) {
            scanf("%lld %s", &D, C);
            if(pos == 0) dir = C[0] == 'C' ? 1 : -1;
            ll cur_dir = C[0] == 'C' ? 1 : -1;
            ll cur_cnt = cur_dir == 1 ? (pos + D) / L : ((L - pos) % L + D) / L;
            cnt += max(0ll, cur_cnt - (dir != cur_dir));
            if(cur_cnt) dir = cur_dir;
            pos = (pos + cur_dir * D % L + L) % L;
        }
        printf("Case #%d: %lld\n", kase, cnt);
    }
    return 0;
}
{% endhighlight %}

# Magical Well Of Lilies
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 1e5 + 10;
ll f[maxn];

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int L;
        scanf("%d", &L);
        for(int i = 1; i <= L; ++i) f[i] = i;
        for(int i = 1; i <= L; ++i) {
            f[i] = min(f[i], f[i - 1] + 1);
            for(int j = i + i; j <= L; j += i) f[j] = min(f[j], f[i] + 2 + 2 * j / i);
        }
        printf("Case #%d: %lld\n", kase, f[L]);
    }
    return 0;
}
{% endhighlight %}

# Electricity
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 2e5 + 10;
vector<int> e[maxn];
int A[maxn], S[maxn], idx[maxn];
bool cmp(int i, int j) {return A[i] < A[j];}

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N;
        scanf("%d", &N);
        for(int i = 1; i <= N; ++i) 
            scanf("%d", A + i), S[i] = 1, idx[i] = i, e[i].clear();
        for(int i = 1; i < N; ++i) {
            int x, y;
            scanf("%d %d", &x, &y);
            e[x].push_back(y);
            e[y].push_back(x);
        }
        sort(idx + 1, idx + 1 + N, cmp);
        int ans = 0;
        for(int p = 1; p <= N; ++p) {
            int i = idx[p];
            ans = max(ans, S[i]);
            for(auto j: e[i]) if(A[j] > A[i]) S[j] += S[i];
        }
        printf("Case #%d: %d\n", kase, ans);
    }
    return 0;
}
{% endhighlight %}

# Level Design
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e5 + 10;
int P[maxn], vis[maxn], f[2][maxn];

int main() {
    int T;
    scanf("%d", &T);
    for(int kase = 1; kase <= T; ++kase) {
        int N;
        scanf("%d", &N);
        for(int i = 1; i <= N; ++i) scanf("%d", P + i), vis[i] = 0;
        map<int, int> mp;
        for(int i = 1; i <= N; ++i) {
            if(vis[i]) continue;
            int c = i, l = 0;
            while(!vis[c]) vis[c] = 1, c = P[c], l += 1;
            mp[l] += 1;
        }
        int o = 0;
        for(int i = 1; i <= N; ++i) f[o][i] = N + 1;
        for(auto pr: mp) {
            o ^= 1;
            int l = pr.first, n = pr.second;
            for(int i = 1; i <= N; ++i) f[o][i] = N + 1;
            f[o][l] = 0;
            for(int r = 0; r < l; ++r) {
                deque<int> dq;
                for(int i = r; i <= N; i += l) {
                    f[o][i] = min(f[o][i], f[o ^ 1][i]);
                    while(!dq.empty() && (i - dq.front()) / l > n) dq.pop_front();
                    if(!dq.empty()) f[o][i] = min(f[o][i], f[o ^ 1][dq.front()] + (i - dq.front()) / l - !dq.front());
                    while(!dq.empty() && f[o ^ 1][dq.back()] + (i - dq.back()) / l - !dq.back() >= f[o ^ 1][i]) dq.pop_back();
                    dq.push_back(i);
                }
            }
        }
        int cur_min = f[o][N];
        for(int i = N; i > 0; --i) cur_min = min(cur_min, f[o][i]), f[o][i] = min(f[o][i], cur_min + 1);
        printf("Case #%d:", kase);
        for(int i = 1; i <= N; ++i) printf(" %d", f[o][i]);
        puts("");
    }
    return 0;
}
{% endhighlight %}

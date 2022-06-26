# Unofficial Editorial for the VNOI Cup 2022 Round 2
man this contest is so orz. the people who prepared the problems are very orz. the problem statements are very pleasant to read, orz. too bad they don't have the capacity to write the editorial

link for context: https://codeforces.com/blog/entry/104144

welp as a grey i'm not exactly good at cp but i'm doing my best lol. throughout the editorial i'll be using 0 based indexing

this post is licensed under the [Creative Commons Zero v1.0](https://creativecommons.org/share-your-work/public-domain/cc0/) license. you are free to reproduce this post for any purpose

## A. Neko on a tour
to solve this problem, you can sort the array and then for each element of the array, do binary search for an element whose left endpoint is greater than the current element's right endpoint. this seems easy enough, let me just post the code lol

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    cin.tie(0)->sync_with_stdio(0);

    int n, m;
    cin >> n >> m;

    vector<tuple<int, int, int>> tours(n);
    for (auto &[l, r, _]: tours) cin >> l >> r;
    for (int i = 0; i < n; i++)
        get<2>(tours[i]) = i;

    sort(tours.begin(), tours.end());

    // NOTE: the m variable can be ignored!

    for (auto &[l, r, index]: tours)
    {
        int cursor = lower_bound(tours.begin(), tours.end(), tuple<int, int, int> { r + 1, -999, -999 }) - tours.begin();

        if (cursor == n) continue;

        cout << "YES\n";
        cout << index + 1 << " " << get<2>(tours[cursor]) + 1 << "\n";
        return 0;
    }

    cout << "NO\n";
}
```

## B. Sale
welp... this problem requires you to do dp and then use the dp code to make observations about the problem so you can write a better greedy solution.

so lemme tell you the dp solution. `dp(points, current_row)` is the maximum sum that can be achieved with `points` and the first `current_row + 1` rows. on each dp state, we do a for loop over the number of prizes on the current row, and let's call it `take`. for each `take` value, set `dp(points, current_row)` to `max(dp(points, current_row), dp(points - take, current_row - 1) + sum)`, where sum is the sum of the first `take` prizes on the current row. note:
- if `points < take`, break out of the for loop. don't update the current dp value
- if the current row is 0 (the first row), just set `dp(points, current_row)` to `sum`

this solution is enough for you to get full points for the first two subtasks. congratulations! however, if you want to fully solve the problem, you need to make more observations. now listen. to make more observations, modify the dp code so that you can trace the number of prizes taken on each row, and then log everything to the console. then generate a random test and run your program on the test. you will see a pattern

hey and here's a warning: the low constraint on m is very misleading. in fact there is no way you can exploit this

here's my dp thing
```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    cin.tie(0)->sync_with_stdio(0);

    int n, m, k;
    cin >> n >> m >> k;

    const int maxn = 300;
    const int maxm = 10;

    int A[maxn][maxm];

    for (int i = 0; i < n; i++)
    for (int j = 0; j < m; j++)
        cin >> A[i][j];

    const int maxpoints = maxn * maxm;
    long long dp[maxpoints + 1][maxn];

    for (int points = 0; points <= k; points++)
    for (int current_row = 0; current_row < n; current_row++)
    {
        long long sum = 0;
        dp[points][current_row] = 0;
        for (int take = 0; take <= m; take++)
        {
            if (take != 0) sum += A[current_row][take - 1];
            if (points < take) break;
            if (current_row == 0)
            {
                dp[points][current_row] = max(dp[points][current_row], sum);
                continue;
            }
            dp[points][current_row] = max(dp[points][current_row], dp[points - take][current_row - 1] + sum);
        }
    }

    int points = k;
    int current_row = n - 1;

    ([&]() {while (true)
    {
        long long sum = 0;
        for (int take = 0; take <= m; take++)
        {
            if (take != 0) sum += A[current_row][take - 1];
            if (points < take) continue;
            if (current_row == 0)
            {
                if (dp[points][current_row] == sum)
                {
                    cout << "Row " << current_row << ": " << take << "\n";
                    return;
                }
                continue;
            }
            if (dp[points][current_row] == dp[points - take][current_row - 1] + sum)
            {
                cout << "Row " << current_row << ": " << take << "\n";
                points -= take;
                current_row--;
                break;
            }
        }
    }})();

    cout << dp[k][n - 1] << "\n";
}
```

here is the random test data i used
```
30 10 153
64 94 205 231 469 522 647 702 781 826
17 35 37 46 100 174 212 583 650 694
4 54 230 349 383 412 472 720 841 974
51 62 117 375 396 466 562 566 659 921
5 47 89 306 368 488 592 861 893 918
38 85 141 181 287 353 455 532 839 935
162 261 432 524 607 649 650 676 939 960
90 136 280 305 327 442 648 887 937 942
24 152 159 173 201 285 390 708 793 909
24 245 271 366 378 485 575 883 892 962
216 244 512 673 692 694 736 763 782 985
68 146 473 685 696 698 853 866 938 974
145 405 497 590 602 668 722 971 975 987
20 102 220 224 224 259 559 745 841 844
6 20 73 190 281 411 550 554 708 770
16 305 392 397 547 567 674 703 721 997
481 523 563 594 847 857 874 877 878 982
24 210 267 276 473 562 583 724 736 840
77 210 239 301 482 504 539 585 681 809
11 97 298 464 497 648 705 871 929 978
124 143 205 218 252 276 346 396 462 755
83 89 284 307 480 700 782 795 811 918
4 219 369 572 620 671 755 841 863 973
134 243 294 309 489 644 750 808 831 843
306 513 527 533 567 634 717 795 801 854
32 47 157 261 283 390 476 543 801 964
15 333 421 431 591 662 743 851 919 964
95 155 254 585 681 778 827 852 857 864
97 212 316 321 371 510 727 828 872 892
93 385 443 450 596 616 699 819 944 955
```
the program outputs this
```
Row 29: 10
Row 28: 3
Row 27: 10
Row 26: 10
Row 25: 0
Row 24: 10
Row 23: 10
Row 22: 10
Row 21: 10
Row 20: 0
Row 19: 10
Row 18: 0
Row 17: 0
Row 16: 10
Row 15: 10
Row 14: 0
Row 13: 0
Row 12: 10
Row 11: 10
Row 10: 10
Row 9: 10
Row 8: 0
Row 7: 0
Row 6: 10
Row 5: 0
Row 4: 0
Row 3: 0
Row 2: 0
Row 1: 0
Row 0: 0
89721
```
so there is a pattern here. whole rows are taken. there is only one row that is not fully taken, that's row 28. so we can come up with a solution that looks like this:
- the number of rows that are fully taken is `k / m`. i'm talking about integer division here. so we take `k / m` rows with the biggest sums
- there is only one row that's not fully taken, and we need to take `k % m` prizes from it. so we just do a for loop over the remaining rows and take the row with the biggest sum for the first `k % m` prizes

so this solution does not exploit the m <= 10 constraint at all! to implement the solution, you don't even need to store the whole grid in memory. for each row just store two values: sum of first `k % m` prizes and sum of the whole row. then everything is a matter of sorting an array of pairs in descending order lol

too bad this solution doesn't necessarily lead to an optimal solution. i found this out by running the code on the random data i generated earlier. so i came up with an ad hoc modification to my scheme

the above scheme does not lead to an optimal solution because there is a chance that the row where we take the first `k % m` prizes is within the first `k / m` rows in the sorted array. so i still use the above scheme to get an initial result for the case where the partially taken row is outside of the first `k / m` rows, then after that:
- i calculate the sum of the first `k / m + 1` rows. let's call this sum `sum`
- do a for loop over the first `k / m` rows. for each row, the result where the partially taken row is the current row is `sum - whole_sum + prefix`, where `whole_sum` is the total sum of the row and `prefix` is the sum of the first `k % m` elements

here is my code
```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    cin.tie(0)->sync_with_stdio(0);

    int n, m, k;
    cin >> n >> m >> k;

    int remainder = k % m;

    vector<pair<long long, long long>> A(n);

    for (auto &[whole_sum, prefix]: A)
    {
        for (int i = 0; i < m; i++)
        {
            long long current;
            cin >> current;
            whole_sum += current;

            if (i + 1 == remainder) prefix = whole_sum;
        }
    }

    sort(A.begin(), A.end(), greater<pair<long long, long long>>());

    int take = k / m;

    long long sum = 0;
    for (int i = 0; i < take; i++) sum += A[i].first;

    long long max_remainder = 0;
    for (int i = take; i < n; i++) max_remainder = max(max_remainder, A[i].second);

    long long result = sum + max_remainder;

    // wait, this doesn't necessarily lead to an optimal solution

    if (remainder == 0)
    {
        cout << result << "\n";
        return 0;
    }

    // need to do some more handling

    sum += A[take].first;

    for (int i = 0; i < take; i++)
        result = max(result, sum - A[i].first + A[i].second);

    cout << result << "\n";
}
```

here is a proof sketch for the solution. so the solution i mentioned earlier relies on this critical assumption: there exists an optimal solution that involves taking whole `k / m` rows and the first `k % m` elements of some other row

our job here is to prove this assumption is indeed correct. good thing there's a pretty simple proof. no i didn't come up with it, i stole the proof from [a geniosity](https://codeforces.com/profile/I_love_Hoang_Yen). full credit to them

let's assume for the sake of contradiction that we have two partially taken rows, A1 and A2. remember that A1 and A2 are sorted in ascending order. let the number of prizes taken from A1 be n1, and from A2 be n2. we will demonstrate that there is a better way. consider two cases: A1[n1] <= A2[n2] and A1[n1] > A2[n2]
- first case: we can take n1 - 1 prizes from A1 and n2 + 1 prizes from A2 instead. this is a better solution. let the original sum of taken prizes of two rows be S, the new sum is S - A1[n1] + A2[n2] = S + (A2[n2] - A1[n1]). as A1[n1] <= A2[n2], we have A2[n2] - A1[n1] >= 0. therefore the new sum is greater than or equal the initial sum
- second case: we can take n1 + 1 prizes from A1 and n2 - 1 prizes from A2 instead. this is a better solution. let the original sum of taken prizes of two rows be S, the new sum is S + A1[n1] - A2[n2]. as A1[n1] > A2[n2], we have A1[n1] - A2[n2] > 0. therefore the new sum is greater than the initial sum

from these two cases, we arrive at a contradiction. therefore it is always optimal to leave at most 1 row taken partially

## C. History
if a problem can't be solved by data structures, it can be solved by a lot of data structures! apparently there's an unintended offline persistent DSU solution. here's the intended solution

the intended solution requires you to explicitly store the nodes in each connected component. this is the small to large merging technique. if you are not familiar with this technique, [please read this](https://cp-algorithms.com/data_structures/disjoint_set_union.html#storing-the-dsu-explicitly-in-a-set-list-applications-of-this-idea-when-merging-various-data-structures)

you need to maintain four data structures:
- the explicit node list in each connected component
- a normal, boring DSU
- adjacency list for partnership queries
- answers for possible innovation queries. initialize the answers to 0

so here are the things you need to do. please listen to me carefully. you might get nasty RTEs or WAs if you are not careful enough

- handling air route queries:
  - **please check if u and v are in the same connected component.** if this is true, please **ignore the query**
  - great. now set u = ancestor(u), v = ancestor(v). if size of component u is smaller, swap u and v
  - merge component v into component u. we need to update the answers for possible innovation queries while merging. here is a detailed procedure for doing so
    - iterate over every node in component v. for every node x, iterate over every node y adjacent to x in the partnership queries adjacency list. if x and y are in the same connected component, increment both answers[x] and answers[y]. then add x to the list of nodes for component u
    - set parent of v to u
    - clear component v
- handling partnership queries:
  - **please check if x and y are in the same connected component.** if this is true, just increment answers[x] and answers[y]. otherwise insert edge (x, y) to the partnership adjacency list. remember this edge is bidirectional
- handling innovation queries:
  - this one is easy. just print answers[k]

time complexity: each node is moved to another component at most O(log(n)) times. as the total number of nodes in the adjacency list is at most n, the total time complexity is O(nlogn + q)

here is my code
```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 200000;

vector<int> node_list[maxn];
int parent[maxn];
vector<int> partnership[maxn];

int main()
{
    cin.tie(0)->sync_with_stdio(0);

    int n, q;
    cin >> n >> q;

    for (int i = 0; i < n; i++) node_list[i].push_back(i);
    for (int i = 0; i < n; i++) parent[i] = i;

    auto ancestor = [&](int v) {
        auto dfs = [&](auto dfs, int v) {
            if (parent[v] == v) return v;
            return parent[v] = dfs(dfs, parent[v]);
        };

        return dfs(dfs, v);
    };

    int answer[maxn];
    fill(answer, answer + n, 0);

    while (q--)
    {
        int query_type;
        cin >> query_type;
        if (query_type == 1)
        {
            int u, v;
            cin >> u >> v;
            u--; v--;
            u = ancestor(u); v = ancestor(v);
            if (u == v) continue;
            if (node_list[u].size() < node_list[v].size()) swap(u, v);
            for (int x: node_list[v])
            {
                for (auto y: partnership[x])
                    if (ancestor(y) == u)
                    {
                        answer[x]++; answer[y]++;
                    }

                node_list[u].push_back(x);
            }

            node_list[v].clear();
            parent[v] = u;
            continue;
        }
        if (query_type == 2)
        {
            int u, v;
            cin >> u >> v;
            u--; v--;
            if (ancestor(u) == ancestor(v))
            {
                answer[u]++; answer[v]++;
                continue;
            }
            partnership[u].push_back(v);
            partnership[v].push_back(u);
            continue;
        }
        if (query_type == 3)
        {
            int k;
            cin >> k;
            k--;
            cout << answer[k] << "\n";
            continue;
        }
    }
}
```

## D. Cut the Cake
the title of this problem isn't consistent with the title of other problems... anyway... it's another data structure bash problem. have you solved [Distinct Values Queries](https://cses.fi/problemset/task/1734) on CSES? there's a lot of solutions, let me describe the most intuitive solution to me. i base my solution to this problem off of my CSES approach. let the array be A. let next[i] be the index of the next occurence of A[i], if this is the final occurrence then set next[i] to n. then to answer each [l, r] query, find the number of values in the `next` array in the range [l, r] that are greater than r. to answer these queries, you need to:
- read all queries
- categorize queries by r endpoints
- create a fenwick tree so you can do sum queries
- for all r endpoints from n - 1 down to 0
  - for all indices i where next[i] = r + 1, increment index i in the fenwick tree by 1. to do this quickly you should do some adjacency list thing
  - for all queries with the current r endpoint, the answer for each query is sum of all elements in the range [l, r] on the fenwick tree

ok so after reading the solution to the CSES problem you should have some insights into this problem. let's maintain **four** arrays:
- `left[i]`: previous occurrence of A[i]
- `right[i]`: next occurrence A[i]
- `lift_k_minus_one[i]`: next (k - 1)th occurrence of A[i]
- `lift_k[i]`: next kth occurrence of A[i]

to calculate the last two arrays, we can use binary lifting if k is big. good thing k is small, so we are spared the effort

lemme describe the things that we have to do when handling a [l, r] query:
- find all first occurrences of a value in the subarray. this is equivalent to finding all indices i in the range [l, r] where left[i] < l
- for each first occurrence, check if the next (k - 1)th occurrence is within the range [l, r] and the next kth occurrence is outside the range

we have a complex web of conditions, and we have to untangle it! good thing offline processing can take care of the first condition (left[i] < l). we have to use two pointers, but stuff can get quite hairy
- first: read all queries beforehand, then **sort all queries by left endpoint**
- second: create an insertion_order array of integer pairs. each pair stores two values: left[i] and i. **sort this array by left[i]**

maintain two cursors, one cursor for the l endpoint, and one cursor for the left[i]. we use two pointers to find two ranges:
- the first range is the range of queries in the sorted query array whose left endpoint is equal
- the second range is the range of elements in the insertion_order array. all elements in this range have to satisfy this condition: the left[i] value stored in the first element of the pair has to be less than the l endpoint

we will need two segment trees for this problem. the first segment tree, ST1 is a range max tree. the second segment tree, ST2 is a range min tree. in the code, i did everything in a single segment tree because the implementation is easier this way. initialize all elements in ST1 to negative infinity and ST2 to positive infinity

so we will need to insert every element in the second range (`insertion_order` array) into two segment trees. for each pair (left[i], i) in the range, we insert the element i into the two segment trees by setting the element at index i on ST1 to `lift_k_minus_one[i]`, and on ST2 to `lift_k[i]`. now for each query (l, r) in the first range, find range max on ST1 and range min on ST2. if the range max is greater than the r endpoint, the index of the range max is also the index of the candle whose number of occurrences in the range isn't exactly k. in the same vein, if the range min is less than or equal to r, the index of the range min is also the index of the candle whose number of occurrences in the range isn't exactly k. otherwise, the range is sparkling. it's perfectly fine

here is my code
```cpp
#include <bits/stdc++.h>
using namespace std;

// https://github.com/kth-competitive-programming/kactl/
/**
 * Author: Lucian Bicsi
 * Date: 2017-10-31
 * License: CC0
 * Source: folklore
 * Description: Zero-indexed max-tree. Bounds are inclusive to the left and exclusive to the right. Can be changed by modifying T, f and unit.
 * Time: O(\log N)
 * Status: stress-tested
 */
#pragma once

// Conditions to check: lift(k - 1) <= r, lift(k) > r.
// We need to find an element that violates either of these conditions.
// Therefore, we will do max on lift(k - 1) and min on lift(k).
// (lift(k - 1), index) is stored on the first element and (lift(k), index) is
// stored on the second element of the pair. I realize that putting everything
// in a single tree can be confusing but it is easier to code.
struct Tree {
	typedef pair<pair<int, int>, pair<int, int>> T;
	#define unit make_pair(make_pair(INT_MIN, 0), make_pair(INT_MAX, 0))
	T f(T a, T b) { return make_pair(max(a.first, b.first), min(a.second, b.second)); } // (any associative fn)
	vector<T> s; int n;
	Tree(int n = 0, T def = unit) : s(2*n, def), n(n) {}
	void update(int pos, T val) {
		for (s[pos += n] = val; pos /= 2;)
			s[pos] = f(s[pos * 2], s[pos * 2 + 1]);
	}
	T query(int b, int e) { // query [b, e)
		T ra = unit, rb = unit;
		for (b += n, e += n; b < e; b /= 2, e /= 2) {
			if (b % 2) ra = f(ra, s[b++]);
			if (e % 2) rb = f(s[--e], rb);
		}
		return f(ra, rb);
	}
};

int main()
{
    cin.tie(0)->sync_with_stdio(0);

    int n, k;
    cin >> n >> k;

    const int maxn = 1000000;

    int C[maxn];
    for (int i = 0; i < n; i++)
    {
        cin >> C[i];
        C[i]--; // for convenience's sake
    }

    // the small k constraint is just to spare us the effort of implementing
    // binary lifting!

    int left[maxn], right[maxn], lift_k_minus_one[maxn], lift_k[maxn];

    vector<int> occurrences[maxn];

    for (int i = 0; i < n; i++)
        occurrences[C[i]].push_back(i);

    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j + 1 < occurrences[i].size(); j++)
            right[occurrences[i][j]] = occurrences[i][j + 1];

        if (occurrences[i].size())
            right[occurrences[i].back()] = n;

        for (int j = 1; j < occurrences[i].size(); j++)
            left[occurrences[i][j]] = occurrences[i][j - 1];

        if (occurrences[i].size())
            left[occurrences[i].front()] = -1;
    }

    for (int i = 0; i < n; i++)
    {
        lift_k_minus_one[i] = i;

        for (int j = 1; j < k; j++)
        {
            if (lift_k_minus_one[i] == n) break;
            lift_k_minus_one[i] = right[lift_k_minus_one[i]];
        }

        if (lift_k_minus_one[i] == n) lift_k[i] = n;
        else lift_k[i] = right[lift_k_minus_one[i]];
    }

    Tree tree(n);

    int q;
    cin >> q;
    tuple<int, int, int> queries[maxn];
    for (int i = 0; i < q; i++)
    {
        cin >> get<0>(queries[i]) >> get<1>(queries[i]);
        get<0>(queries[i])--; get<1>(queries[i])--;
        get<2>(queries[i]) = i;
    }

    sort(queries, queries + q);

    // (right, index)
    pair<int, int> insertion_order[maxn];
    for (int i = 0; i < n; i++)
        insertion_order[i] = { left[i], i };

    sort(insertion_order, insertion_order + n);
    int insertion_order_cursor = 0;

    int answers[maxn];

    int cursor = 0;
    while (true)
    {
        if (cursor == q) break;

        int insertion_order_next = insertion_order_cursor;
        while (true)
        {
            if (insertion_order_next == n) break;
            if (insertion_order[insertion_order_next].first >= get<0>(queries[cursor])) break;
            int index = insertion_order[insertion_order_next].second;
            tree.update(index, make_pair(make_pair(lift_k_minus_one[index], index), make_pair(lift_k[index], index)));
            insertion_order_next++;
        }

        int next = cursor;
        while (true)
        {
            if (next == q) break;
            if (get<0>(queries[next]) != get<0>(queries[cursor])) break;

            auto result = tree.query(get<0>(queries[next]), get<1>(queries[next]) + 1);

            // lift(k - 1) <= r violation
            if (result.first.first > get<1>(queries[next]))
                answers[get<2>(queries[next])] = C[result.first.second] + 1;
            // lift(k) > r violation
            else if (result.second.first <= get<1>(queries[next]))
                answers[get<2>(queries[next])] = C[result.second.second] + 1;
            // no violation
            else answers[get<2>(queries[next])] = 0;

            next++;
        }

        cursor = next;
        insertion_order_cursor = insertion_order_next;
    }

    for (int i = 0; i < q; i++) cout << answers[i] << "\n";
}
```
## E. Thousand-knot bamboo tree
must be the hardest problem ever. orzosities claim that it's trivial tho. anyway i don't understand the solution so can't tell you. sorry to be the bearer of bad news

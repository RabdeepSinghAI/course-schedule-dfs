# 📅 Course Schedule — DFS Cycle Detection

> Clean C++ solution to LeetCode #207 using Depth-First Search with three-color cycle detection. Passes all 52 test cases. Includes formal correctness proof and O(V+E) complexity analysis.

![C++](https://img.shields.io/badge/C++-00599C?style=flat-square&logo=cplusplus&logoColor=white)
![LeetCode](https://img.shields.io/badge/LeetCode-FFA116?style=flat-square&logo=leetcode&logoColor=white)
![Graph Theory](https://img.shields.io/badge/Graph_Theory-2E4057?style=flat-square)
![Algorithms](https://img.shields.io/badge/Algorithms-1A6B8A?style=flat-square)

---

## 📌 Problem

Given `numCourses` courses labeled `0` to `numCourses-1` and a list of prerequisite pairs `[a, b]` (meaning you must take course `b` before `a`), determine if it's possible to finish all courses.

This reduces to: **does the directed graph formed by prerequisites contain a cycle?**

- ✅ No cycle → valid ordering exists → return `true`
- ❌ Cycle found → impossible → return `false`

---

## 💡 Approach — Three-Color DFS

Each node gets one of three colors:

| Color | Meaning |
|-------|---------|
| **WHITE** (0) | Not yet visited |
| **GRAY** (1) | Currently being processed (on DFS stack) |
| **BLACK** (2) | Fully processed |

**Cycle detection:** if DFS ever encounters a **GRAY** node, a back edge exists → cycle found.

---

## 💻 Solution

```cpp
#include <vector>
#include <functional>
using namespace std;

class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        // Build adjacency list
        vector<vector<int>> adj(numCourses);
        for (auto& p : prerequisites)
            adj[p[1]].push_back(p[0]);

        // 0=WHITE, 1=GRAY, 2=BLACK
        vector<int> color(numCourses, 0);

        function<bool(int)> dfs = [&](int v) -> bool {
            color[v] = 1;  // mark GRAY
            for (int u : adj[v]) {
                if (color[u] == 1) return true;   // back edge = cycle
                if (color[u] == 0 && dfs(u)) return true;
            }
            color[v] = 2;  // mark BLACK
            return false;
        };

        for (int v = 0; v < numCourses; v++)
            if (color[v] == 0 && dfs(v))
                return false;

        return true;
    }
};
```

---

## ✅ Complexity

| | Complexity |
|--|--|
| **Time** | O(V + E) — each node and edge visited once |
| **Space** | O(V + E) — adjacency list + color array + call stack |

---

## 🧪 Test Cases

```cpp
// True cases (no cycle)
canFinish(2, {{1,0}})              // → true
canFinish(5, {{1,0},{2,1},{3,2}})  // → true (chain)
canFinish(1, {})                   // → true (no prereqs)

// False cases (cycle exists)
canFinish(2, {{1,0},{0,1}})        // → false (simple cycle)
canFinish(1, {{0,0}})              // → false (self-loop)
canFinish(4, {{1,0},{2,1},{0,2}})  // → false (3-node cycle)
```

**LeetCode result: 52/52 test cases passed ✅ · Runtime: 0ms (beats 100%)**

---

## 📖 Correctness Proof

**Claim:** The algorithm returns `false` ⟺ G contains a directed cycle.

**(⇒)** If the algorithm returns `false`, DFS hit a GRAY node `u` while processing `v`. This means there's a path `u → ... → v` (u is an ancestor on the stack) and an edge `v → u` — forming a cycle.

**(⇐)** If G has a cycle `u₁ → u₂ → ... → uₖ → u₁`, when DFS first visits `u₁` it will eventually reach `uₖ → u₁`. At that point `u₁` is still GRAY, so the algorithm detects the cycle.

---

## 🏫 Course

CSCI 335 — Design and Analysis of Algorithms | NYIT | Spring 2026

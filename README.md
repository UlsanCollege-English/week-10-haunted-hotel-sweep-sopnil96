# Week 10 Coding #8: Haunted Hotel Sweep

## Summary

This assignment models a haunted hotel as a graph where each room or area is a **node** and the connections between rooms are **edges**, stored as an adjacency list (`dict[str, list[str]]`). We implement helper functions to navigate the hotel. **BFS (Breadth-First Search)** sweeps the hotel level by level — visiting all immediate neighbors before going deeper — making it useful for finding the shortest path. **DFS (Depth-First Search)** plunges as deep as possible down one corridor before backtracking, like exploring one wing fully before moving on. A `visited` set is critical in both algorithms to prevent revisiting rooms and getting stuck in cycles (infinite loops).

---

## Approach

- **`get_neighbors`**: Used `dict.get(area, [])` to safely return neighbors or an empty list if the area doesn't exist — no `KeyError` possible.
- **`has_path`**: First checked that both `start` and `target` exist in the graph. Then ran a BFS from `start`, returning `True` the moment `target` is found. Handled `start == target` as an early return.
- **`bfs_order`**: Used a `deque` as a queue (FIFO). Added `start` to both the queue and `visited` before the loop, then processed each area, enqueuing unvisited neighbors in their original order.
- **`dfs_order`**: Used a list as a stack (LIFO). Pushed neighbors in `reversed(...)` order so that when popped, they follow the original neighbor order. Checked `visited` at pop time (not push time) to handle duplicates on the stack cleanly.
- **`count_reachable_areas`**: Reused BFS logic — ran a full BFS from `start` and returned `len(visited)`, which counts every area reachable including `start` itself.
- **Preventing repeated visits**: Both BFS and DFS use a `visited: set` that tracks areas already processed, ensuring each node is handled exactly once.

---

## Complexity

### `get_neighbors`
- **Time:** O(1)
- **Space:** O(1)
- **Why:** A single dictionary lookup; no iteration.

### `has_path`
- **Time:** O(V + E) — V = areas (vertices), E = connections (edges)
- **Space:** O(V) for the `visited` set and queue
- **Why:** In the worst case, BFS visits every area and every connection once.

### `bfs_order`
- **Time:** O(V + E)
- **Space:** O(V) for the `visited` set and queue
- **Why:** Every area is enqueued and dequeued once; every edge is checked once.

### `dfs_order`
- **Time:** O(V + E)
- **Space:** O(V) for the `visited` set and stack
- **Why:** Every area is pushed/popped at most once after the visited check; every edge is examined once.

### Stretch: `count_reachable_areas`
- **Time:** O(V + E)
- **Space:** O(V)
- **Why:** It is a full BFS traversal — same cost as `bfs_order`.

---

## Edge-Case Checklist

- [x] **Empty graph** — `get_neighbors({}, "X")` returns `[]`; BFS/DFS return `[]`; `has_path` returns `False`
- [x] **Missing start area** — all functions return `[]`, `False`, or `0` safely
- [x] **Missing target area** — `has_path` returns `False` immediately
- [x] **`start == target`** — `has_path` returns `True` as long as the area exists
- [x] **Graph with a cycle** — `visited` set prevents infinite loops in all traversals
- [x] **Disconnected graph** — `has_path` returns `False`; BFS/DFS only visit the reachable component
- [x] **Area with no neighbors** — `graph.get(area, [])` returns `[]`; loop body simply doesn't execute

**Tricky edge case:** In `dfs_order`, a neighbor can be pushed onto the stack multiple times before it is visited (once per edge leading to it). Checking `if current in visited` at **pop time** (not push time) handles this correctly without extra logic.

---

## Tests Added

- `test_get_neighbors_existing` — verifies correct neighbor list returned
- `test_get_neighbors_missing` — verifies `[]` for unknown area
- `test_has_path_direct` — direct one-hop connection
- `test_has_path_indirect` — multi-hop path
- `test_has_path_no_path` — disconnected areas return `False`
- `test_has_path_missing_area` — missing start or target returns `False`
- `test_has_path_same_area` — `start == target` returns `True`
- `test_bfs_order_basic` — verifies level-by-level order
- `test_bfs_order_missing_start` — returns `[]`
- `test_dfs_order_basic` — verifies depth-first order
- `test_dfs_order_cycle` — cycle handled correctly
- `test_count_reachable_basic` — correct count in connected graph
- `test_count_reachable_missing` — returns `0`

---

## Known Limitations

No known limitations.

---

## Assistance & Sources

**AI used? Y**

AI helped with:
- Explaining why neighbors should be pushed in `reversed(...)` order for DFS with a stack
- Clarifying push-time vs. pop-time visited checking in DFS
- Reviewing edge cases (missing areas, cycles, disconnected graphs)
- Syntax reminders for `deque` and `dict.get`

**Other sources used:**
- Python `collections.deque` documentation
- Class notes on BFS and DFS traversal algorithms

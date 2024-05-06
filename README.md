# 8-puzzle
import copy
from heapq import heappush, heappop

n = 3
row = [1, 0, -1, 0]
col = [0, -1, 0, 1]

class node:
    def __init__(self, parent, mat, empty_tile_pos, cost, level):
        self.parent = parent
        self.mat = mat
        self.empty_tile_pos = empty_tile_pos
        self.cost = cost
        self.level = level

    def __lt__(self, nxt):
        return self.cost < nxt.cost

def calculateCost(mat, final) -> int:
    count = 0
    for i in range(n):
        for j in range(n):
            if mat[i][j] and mat[i][j] != final[i][j]:
                count += 1
    return count

def newNode(mat, empty_tile_pos, new_empty_tile_pos, level, parent, final) -> node:
    new_mat = copy.deepcopy(mat)
    x1, y1 = empty_tile_pos
    x2, y2 = new_empty_tile_pos
    new_mat[x1][y1], new_mat[x2][y2] = new_mat[x2][y2], new_mat[x1][y1]
    cost = calculateCost(new_mat, final)
    new_node = node(parent, new_mat, new_empty_tile_pos, cost, level)
    return new_node

def printMatrix(mat):
    for i in range(n):
        for j in range(n):
            print("%d " % (mat[i][j]), end=" ")
        print()

def isSafe(x, y):
    return 0 <= x < n and 0 <= y < n

def printPath(root):
    if root is None:
        return
    printPath(root.parent)
    printMatrix(root.mat)
    print()

def solve(initial, empty_tile_pos, final):
    visited = set()
    pq = []

    root = node(None, initial, empty_tile_pos, calculateCost(initial, final), 0)
    heappush(pq, root)

    while pq:
        current = heappop(pq)

        if current.cost == 0:
            print("Solution Path:")
            printPath(current)
            return

        visited.add(tuple(map(tuple, current.mat)))

        for i in range(4):
            new_tile_pos = [
                current.empty_tile_pos[0] + row[i],
                current.empty_tile_pos[1] + col[i],
            ]

            if isSafe(new_tile_pos[0], new_tile_pos[1]):
                new_mat = copy.deepcopy(current.mat)
                new_mat[current.empty_tile_pos[0]][current.empty_tile_pos[1]], new_mat[new_tile_pos[0]][
                    new_tile_pos[1]
                ] = new_mat[new_tile_pos[0]][new_tile_pos[1]], new_mat[current.empty_tile_pos[0]][
                    current.empty_tile_pos[1]
                ]

                if tuple(map(tuple, new_mat)) not in visited:
                    child = newNode(
                        new_mat, new_tile_pos, new_tile_pos, current.level + 1, current, final
                    )
                    heappush(pq, child)
                    visited.add(tuple(map(tuple, new_mat)))

# Define initial state, final state, and position of the empty tile
initial = [[2, 0, 3], [1, 8, 4], [7, 6, 5]]
final = [[1, 2, 3], [8, 0, 4], [7, 6, 5]]
empty_tile_pos = [0, 1]  # Empty tile initially at row 0, column 1

# Solve the puzzle
solve(initial, empty_tile_pos, final)

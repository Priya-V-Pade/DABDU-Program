Practical = 1
Write a program non-recursive and recursive program to calculate Fibonacci numbers and analyze 
their time and space complexity. 
Recurssive :
def fibonacci_recursive(n):
    if n <= 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fibonacci_recursive(n-1) + fibonacci_recursive(n-2)
num = int(input("Enter the number of terms for Fibonacci series (recursive): "))
print("Fibonacci series (recursive):")
for i in range(num):
    print(fibonacci_recursive(i), end=' ')
print()
______________________________________________________________________________________________________________________
Non Recursive Fibonacci :
def fibonacci_iterative(n):
    series = []
    a, b = 0, 1
    for _ in range(n):
        series.append(a)
        a, b = b, a + b
    return series

n = int(input("Enter the number of Fibonacci numbers to generate (iterative): "))
fib_series = fibonacci_iterative(n)
print(f"Fibonacci series (first {n} numbers) using iterative method:")
print(fib_series)
______________________________________________________________________________________________________________________
Practical = 2
Write a program to implement Huffman Encoding using a greedy strategy. 
import heapq

class Node:
    def __init__(self, freq, char, left=None, right=None):
        self.freq = freq
        self.char = char
        self.left = left
        self.right = right

    def __lt__(self, other):
        return self.freq < other.freq

def build_huffman_tree(chars, freqs):
    heap = [Node(freqs[i], chars[i]) for i in range(len(chars))]
    heapq.heapify(heap)

    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)
        merged = Node(left.freq + right.freq, '-', left, right)
        heapq.heappush(heap, merged)

    return heap[0]

def print_codes(node, code=""):
    if node.left is None and node.right is None:
        print(f"{node.char} -> {code}")
        return
    print_codes(node.left, code + "0")
    print_codes(node.right, code + "1")

def main():
    chars = list(input("Enter characters (no spaces): "))
    freqs = list(map(int, input("Enter frequencies (space-separated): ").split()))
    if len(chars) != len(freqs):
        print("Mismatch between characters and frequencies!")
        return
    root = build_huffman_tree(chars, freqs)
    print("\nHuffman Codes:")
    print_codes(root)

if __name__ == "__main__":
    main()
______________________________________________________________________________________________________________________
Practical = 3

Write a program to solve a 0-1 Knapsack problem using dynamic programming or branch and bound 
strategy.
def knapsack(weights, values, capacity):
    n = len(values)
    dp = [[0 for _ in range(capacity + 1)] for _ in range(n + 1)]

    for i in range(1, n + 1):
        for w in range(1, capacity + 1):
            if weights[i - 1] <= w:
                dp[i][w] = max(values[i - 1] + dp[i - 1][w - weights[i - 1]], dp[i - 1][w])
            else:
                dp[i][w] = dp[i - 1][w]

    return dp[n][capacity]

# Main program
values = list(map(int, input("Enter values of items (space-separated): ").split()))
weights = list(map(int, input("Enter weights of items (space-separated): ").split()))
capacity = int(input("Enter knapsack capacity: "))

max_profit = knapsack(weights, values, capacity)
print(f"\nMaximum profit that can be achieved: {max_profit}")

______________________________________________________________________________________________________________________
Practical = 4
Design n-Queens matrix having first Queen placed. Use backtracking to place remaining Queens to 
generate the final n-queen ‘s matrix. 

def is_safe(board, row, col, n):
    # Check same column
    for i in range(row):
        if board[i][col] == 'Q':
            return False

    # Check upper-left diagonal
    i, j = row - 1, col - 1
    while i >= 0 and j >= 0:
        if board[i][j] == 'Q':
            return False
        i -= 1
        j -= 1

    # Check upper-right diagonal
    i, j = row - 1, col + 1
    while i >= 0 and j < n:
        if board[i][j] == 'Q':
            return False
        i -= 1
        j += 1

    return True

def print_board(board):
    for row in board:
        print(" ".join(row))
    print()

def solve_n_queens(board, row, n):
    if row == n:
        print_board(board)
        return
    for col in range(n):
        if is_safe(board, row, col, n):
            board[row][col] = 'Q'
            solve_n_queens(board, row + 1, n)
            board[row][col] = '-'  # Backtrack

def main():
    N = 8  # You can change this to any N
    board = [['-' for _ in range(N)] for _ in range(N)]
    solve_n_queens(board, 0, N)

if __name__ == "__main__":
    main()
______________________________________________________________________________________________________________________
Practical = 5 
Write a program for analysis of quick sort by using deterministic and randomized variant 
import random, time
def quick_sort_det(arr):
    if len(arr) <= 1: return arr
    p = arr[0]
    left = [x for x in arr[1:] if x <= p]
    right = [x for x in arr[1:] if x > p]
    return quick_sort_det(left) + [p] + quick_sort_det(right)

def quick_sort_rand(arr):
    if len(arr) <= 1: return arr
    p = random.choice(arr)
    arr.remove(p)
    left = [x for x in arr if x <= p]
    right = [x for x in arr if x > p]
    return quick_sort_rand(left) + [p] + quick_sort_rand(right)

# --- Main ---
data = list(map(int, input("Enter numbers separated by space: ").split()))
print("\nOriginal list:", data)

start = time.time()
print("Sorted (Deterministic):", quick_sort_det(data.copy()))
print("Deterministic Quick Sort Time:", format(time.time() - start, ".6f"), "sec")

start = time.time()
print("Sorted (Randomized):", quick_sort_rand(data.copy()))
print("Randomized Quick Sort Time:", format(time.time() - start, ".6f"), "sec")

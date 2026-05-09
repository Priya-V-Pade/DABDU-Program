Practical=1
 Implement depth first search algorithm and Breadth First Search algorithm, Use an undirected 
graph and develop a recursive algorithm for searching all the vertices of a graph or tree data 
structure. 

from collections import deque

# Graph using dictionary
graph = {
    0: [1, 2],
    1: [0, 3, 4],
    2: [0, 5],
    3: [],
    4: [],
    5: []
}

# DFS Function
def dfs(node, visited):
    visited.add(node)
    print(node, end=" ")

    for neighbour in graph[node]:
        if neighbour not in visited:
            dfs(neighbour, visited)

# BFS Function
def bfs(start):
    visited = set()
    queue = deque([start])

    visited.add(start)

    while queue:
        node = queue.popleft()
        print(node, end=" ")

        for neighbour in graph[node]:
            if neighbour not in visited:
                visited.add(neighbour)
                queue.append(neighbour)

# Driver Code
print("DFS:")
visited = set()
dfs(0, visited)

print("\nBFS:")
bfs(0)
_____________________________________________________________________________________________________________________________________________
Practical = 2

Implement A star Algorithm for any game search problem.
import heapq

# Maze (0 = path, 1 = obstacle)
maze = [
    [0, 0, 0, 0],
    [1, 1, 0, 1],
    [0, 0, 0, 1],
    [0, 1, 0, 0]
]

start = (0, 0)
goal = (3, 3)

# Heuristic Function (Manhattan Distance)
def heuristic(a, b):
    return abs(a[0] - b[0]) + abs(a[1] - b[1])

def a_star(maze, start, goal):

    rows = len(maze)
    cols = len(maze[0])

    # Priority Queue
    open_list = []
    heapq.heappush(open_list, (0, start))

    came_from = {}
    g_cost = {start: 0}

    # Possible moves
    directions = [(0,1), (1,0), (0,-1), (-1,0)]

    while open_list:

        current = heapq.heappop(open_list)[1]

        # Goal reached
        if current == goal:

            path = []

            while current in came_from:
                path.append(current)
                current = came_from[current]

            path.append(start)
            path.reverse()

            return path

        # Explore neighbors
        for move in directions:

            next_node = (
                current[0] + move[0],
                current[1] + move[1]
            )

            r, c = next_node

            # Check valid path
            if 0 <= r < rows and 0 <= c < cols:

                if maze[r][c] == 1:
                    continue

                new_cost = g_cost[current] + 1

                if next_node not in g_cost or new_cost < g_cost[next_node]:

                    g_cost[next_node] = new_cost

                    f_cost = new_cost + heuristic(next_node, goal)

                    heapq.heappush(open_list, (f_cost, next_node))

                    came_from[next_node] = current

    return None


# Run Algorithm
path = a_star(maze, start, goal)

# Output
if path:
    print("Shortest Path Found:")
    print(path)
else:
    print("No Path Found")
_____________________________________________________________________________________________________________________________________________

Practical = 3
 Implement Greedy search algorithm for any of the following application: 
 1) Prim's algo
import heapq

class Graph:
    def __init__(self, vertices):
        self.V = vertices
        self.graph = [[] for _ in range(vertices)]

    def add_edge(self, u, v, w):
        self.graph[u].append((v, w))
        self.graph[v].append((v, w))

    def prim(self):
        visited = [False] * self.V
        min_heap = [(0, 0)]
        total_cost = 0

        while min_heap:
            weight, node = heapq.heappop(min_heap)

            if visited[node]:
                continue

            visited[node] = True
            total_cost += weight

            for neighbor, edge_weight in self.graph[node]:
                if not visited[neighbor]:
                    heapq.heappush(min_heap, (edge_weight, neighbor))

        print("Total Cost:", total_cost)


g = Graph(5)

g.add_edge(0, 1, 2)
g.add_edge(0, 3, 6)
g.add_edge(1, 2, 3)
g.add_edge(1, 3, 8)
g.add_edge(1, 4, 5)
g.add_edge(2, 4, 7)
g.add_edge(3, 4, 9)

g.prim()
_____________________________________________________________________________________________________________________________________________

Practocal = 4
Implement a solution for a Constraint Satisfaction Problem using Branch and Bound and Backtracking for n-queens problem or a graph coloring problem. 
# N-Queen Problem (Easy Version)

N = 5
board = [[0 for i in range(N)] for j in range(N)]
# Function to check if queen can be placed
def is_safe(row, col):
    # Check upper column
    for i in range(row):
        if board[i][col] == 1:
            return False
    # Check left diagonal
    i = row - 1
    j = col - 1
    while i >= 0 and j >= 0:
        if board[i][j] == 1:
            return False
        i -= 1
        j -= 1
    # Check right diagonal
    i = row - 1
    j = col + 1
    while i >= 0 and j < N:
        if board[i][j] == 1:
            return False
        i -= 1
        j += 1
    return True
# Function to solve N-Queen
def solve(row):
    # If all queens are placed
    if row == N:
        return True
    # Try every column
    for col in range(N):
        if is_safe(row, col):
            # Place queen
            board[row][col] = 1
            # Recursive call
            if solve(row + 1):
                return True
            # Backtracking
            board[row][col] = 0
    return False
# Main
solve(0)
# Print board
for row in board:
    for cell in row:
        if cell == 1:
            print("Q", end=" ")
        else:
            print(".", end=" ")
    print()
___________________________________________________________________________________________________________________________________________________________________
Practical = 5
Develop an elementary catboat for any suitable customer interaction application.

import random

class CustomerChatbot:
    def __init__(self, name):
        self.name = name

    def respond(self, message):
        message = message.lower()

        # Greeting responses
        greetings = [
            "Hello! How can I assist you today?",
            "Hi there! What can I help you with?",
            "Welcome! How may I help you?"
        ]

        # Product information
        product_info = "We offer laptops, smartphones, and accessories. Which product would you like information about?"

        # Pricing information
        pricing_info = "Our prices start from ₹10,000. Please specify the product for exact pricing."

        # Order tracking
        order_info = "Please provide your order ID to track your order."

        # Complaint response
        complaint_response = "I'm sorry for the inconvenience. Please describe your issue in detail so I can assist you."

        # Goodbye responses
        goodbyes = [
            "Thank you for visiting! Have a great day!",
            "Goodbye! Feel free to contact us again.",
            "Take care! We are always here to help."
        ]

        # Intent detection using keywords
        if any(word in message for word in ["hello", "hi", "hey"]):
            return random.choice(greetings)

        elif any(word in message for word in ["product", "item", "service"]):
            return product_info

        elif any(word in message for word in ["price", "cost", "rate"]):
            return pricing_info

        elif any(word in message for word in ["order", "track", "delivery"]):
            return order_info

        elif any(word in message for word in ["complaint", "problem", "issue"]):
            return complaint_response

        elif any(word in message for word in ["bye", "goodbye", "thank you"]):
            return random.choice(goodbyes)

        else:
            return "I'm sorry, I didn't understand your request. Could you please rephrase?"


# Create chatbot instance
chatbot = CustomerChatbot("Customer Support Bot")

print("---- Welcome to Customer Support ----")

while True:
    user_input = input("You: ")
    response = chatbot.respond(user_input)
    print(chatbot.name + ": " + response)

    if any(word in user_input.lower() for word in ["bye", "goodbye"]):
        break
___________________________________________________________________________________________________________________________________________________________________
Practical = 6
Implement any one of the following Expert System
Library management
books = []

while True:
    print("\n--- Library Management System ---")
    print("1. Add Book")
    print("2. View Books")
    print("3. Search Book")
    print("4. Delete Book")
    print("5. Exit")

    choice = input("Enter your choice: ")

    # Add Book
    if choice == "1":
        book_id = input("Enter Book ID: ")
        title = input("Enter Book Title: ")
        author = input("Enter Author Name: ")
        year = input("Enter Year: ")

        book = {
            "id": book_id,
            "title": title,
            "author": author,
            "year": year
        }

        books.append(book)
        print("Book added successfully!")

    # View Books
    elif choice == "2":

        if books == []:
            print("No books available.")

        else:
            print("\nBook List:")

            for book in books:
                print("ID:", book["id"])
                print("Title:", book["title"])
                print("Author:", book["author"])
                print("Year:", book["year"])
                print("----------------")

    # Search Book
    elif choice == "3":

        search_title = input("Enter book title to search: ")
        found = False

        for book in books:

            if book["title"].lower() == search_title.lower():
                print("\nBook Found")
                print("ID:", book["id"])
                print("Title:", book["title"])
                print("Author:", book["author"])
                print("Year:", book["year"])

                found = True

        if found == False:
            print("Book not found.")

    # Delete Book
    elif choice == "4":

        delete_id = input("Enter Book ID to delete: ")
        found = False

        for book in books:

            if book["id"] == delete_id:
                books.remove(book)
                print("Book deleted successfully!")
                found = True
                break

        if found == False:
            print("Book not found.")

    # Exit
    elif choice == "5":
        print("Thank you!")
        break

    # Invalid Choice
    else:
        print("Invalid choice. Please try again.")


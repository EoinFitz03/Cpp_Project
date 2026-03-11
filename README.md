# Cpp_Project

# First week of project
In the first lab I did some research and looked at a few A* algorithm examples to understand the main ideas (OPEN list, CLOSED list and the heuristic). After that I set up my project with a simple OOP structure with 1 header file and 2 cpp files.

I made a basic grid using `std::vector<std::string>` because it’s easy to read and print while I’m debugging. I also added a Start (`S`) and Goal (`G`) marker so I can clearly see the positions and make sure obstacles (`#`) are working properly before I start the real A* algorithm.

**Code structure so far :**
- `Astar.h` contains the class declarations.
- `Astar.cpp` contains the actual logic / functions.
- `main.cpp` just creates the object and runs the demo.

**Limitation right now:** the grid is hardcoded and there is no pathfinding logic yet.  
**Next step:** add basic validation and then begin setting up the A* data using nodes + OPEN list.
**Code used and Output**

<img width="484" height="160" alt="image" src="https://github.com/user-attachments/assets/6a80e3e6-ab96-448a-bbde-11ce8a8733dd" />
<img width="488" height="360" alt="image" src="https://github.com/user-attachments/assets/e8504cb4-bc8e-4743-985e-ce158f67a573" />


# Second week of project
This week I improved the grid setup before starting the full A* loop. I added validation to check that the Start and Goal are:
- inside the grid bounds
- not placed on a wall (`#`)

If something is wrong, the program prints a warning message and stops early. This prevents crashes and bad inputs before the search even starts.

**What I learned / issue I ran into:** grid coordinates are accessed using grid[y][x]. Without bounds checks I was getting crashes when I accidentally went outside the grid or placed the start/goal on a wall. The validation fixed this and made debugging easier with th error messages.

## Quick tests I tried
- **Test 1:** Goal at (5,5) on a 5x5 grid  
  **Expected:** “Goal is out of bounds!”  
  **Result:** PASS

- **Test 2:** Start/Goal placed on # 
  **Expected:** "Start/Goal is on a wall!" 
  **Result:** PASS

<img width="534" height="244" alt="image" src="https://github.com/user-attachments/assets/cda05577-a779-465e-bfa7-464ed1ca68fa" />
<img width="645" height="559" alt="image" src="https://github.com/user-attachments/assets/b900a5df-007e-4b17-8f34-dce532eddc49" />


## A* setup with OPEN list + g/h/f values
After validation was working, I started setting up the A* data structures. I created a Node struct and added the start node into an OPEN list std::vector<Node>. I used a vector at this stage because it is easy to print and debug. Later I can upgrade OPEN to a priority_queue for better performance.

- g = cost from start to current node, steps so far 
- h = estimated cost to the goal (heuristic)
- f = total score used by A* (`f = g + h`)

I used **Manhattan distance** for `h` because I am starting with **4-direction movement** up, down, left, right, so Manhattan matches the way the grid movement works.

I printed the start node values to confirm the scoring is correct before expanding and building the full A* loop.

**Example output and code used**
<img width="385" height="230" alt="image" src="https://github.com/user-attachments/assets/2f55a695-fecd-4ddb-9005-f3920a8ae02e" />
<img width="653" height="574" alt="image" src="https://github.com/user-attachments/assets/1f019dfe-cd8a-4e1e-9e45-059e26dd7cc0" />

**Next step:** pick the node with the lowest `f` from OPEN, move it to CLOSED, then generate neighbours 4 directions first. After that I will add parent tracking properly so I can reconstruct and print the final path.

## AI usage 
AI was used to help explain A* steps and to help me plan weekly milestones. All code was written and tested by me.

# Third week of project
This week I made the code cleaner and more like proper OOP by moving the create start node logic into its own function. My lecturer mentioned it is better to test small logic parts like this instead of having everything inside one big function, so I refactored it into `makeStartNode()`.

The idea of this function is to set up the very first node for A* correctly:
- it takes in the start (sx, sy) and goal (gx, gy)
- it sets g = 0 because the start node has no movement cost yet
- it calculates h using Manhattan distance
- it sets f = g + h
- it sets the parent values to -1 because the start node has no parent

This makes it easier to reuse later and also easier to test, because I can call this function with known start/goal values and check the output.

**Code example**
<img width="429" height="232" alt="image" src="https://github.com/user-attachments/assets/3c70bc8d-5c3a-425c-83b4-a956ae83f2f4" />

## Continuing A* setup picking q + generating neighbours
After getting the start node working, I moved on to the next A* steps from the pseudocode.

First, I added the part where A* picks the node with the lowest f value from the OPEN list, this node is called q. Once q is picked, I remove it from OPEN and move it into the CLOSED list. This basically means that node has now been visited and I am finished with it.

At the start I was a bit confused why we need both OPEN and CLOSED, but printing the sizes helped me understand it:
- OPEN is what still needs to be explored
- CLOSED is what has already been explored

After that, I added neighbour generation for q. For now I am only doing 4-direction movement (up, down, left, right) to keep it simple. Each neighbour gets checked first:
- if it is outside the grid bounds, it is skipped
- if it is a wall #, it is skipped
Neighbours are basically the next possible moves from q, and this is how A* expands the search across the grid until it eventually reaches the goal.

If it is valid, I create a new node and calculate:
- g = q.g + 1
- h = Manhattan distance from the neighbour to the goal
- f = g + h

I also set the parent of each neighbour node to q using parentX and parentY. This will matter later when I need to rebuild the final route from the goal back to the start.

With my current grid I noticed that a lot of directions get skipped because of # walls, which was a good way to confirm the obstacle checks are working properly.

**Code example**

<img width="618" height="431" alt="image" src="https://github.com/user-attachments/assets/7ee2b8d4-3359-4bdf-bf02-f396e5c4c7a2" />

# Fourth week of project
This week I moved my A* code from doing just one step into doing the actual full loop. Before this, my code could pick q once, move it to CLOSED, and add neighbours once, but it didn’t keep going. Now it keeps repeating the A* steps until it either reaches the goal or runs out of options.

## What I added
- I added a while (!open.empty()) loop so the algorithm keeps running until OPEN is empty.
- Inside the loop I pick the node with the lowest f value from OPEN this is q, remove it from OPEN, and put it into CLOSED.
- After that I check if q is the goal. If it is, I stop the loop because the goal has been found.
- If it is not the goal, I generate the neighbours of q (4-direction movement only for now), and I calculate their values:
  - g = q.g + 1
  - h = Manhattan distance to the goal
  - f = g + h
  - I also set `parentX` and `parentY` so I can rebuild the route later.

## Example Code

<img width="1302" height="672" alt="image" src="https://github.com/user-attachments/assets/b10084ba-0187-4e1d-a4a6-fcf43b09af84" /><img width="993" height="709" alt="image" src="https://github.com/user-attachments/assets/8d778212-3e81-428b-bfc9-b2d5dfc60cec" />



## Why I did it this way
The loop is needed because A* is meant to keep exploring step by step. Each time it picks the best option from OPEN lowest f and expands from it. Without the loop the algorithm would just stop after one expansion and never reach the goal.

I also added simple checks so the same node doesn’t get added again with a worse score. So if a neighbour is already in OPEN or CLOSED with a better lower g value, I skip it. This helps stop repeats and keeps the search cleaner.

## What happens if there is no path
If OPEN becomes empty and the goal is never reached, the code prints **“No path found”**. This is important because it means the algorithm can handle maps where the goal is blocked off by walls.

## Rebuilding the final route
When the goal is found, I rebuild the path by following the parents backwards from the goal node back to the start node, then reversing it. After that I print the grid again with:
- `S` for start
- `G` for goal
- `*` for the path

## Example of code running 
In this image you can see the case where a path is found. It prints the number of steps and shows the path using * from S to G.
<img width="673" height="566" alt="image" src="https://github.com/user-attachments/assets/32661d5a-075f-40f3-aeb5-d5bd033a8d5b" />

In this image you can see the case where no path is found. The goal G is blocked off by walls, so OPEN becomes empty and the program prints “No path found”.
<img width="928" height="465" alt="image" src="https://github.com/user-attachments/assets/b4015813-4398-4101-928b-bc46b9daedf9" />

## Maps (moving the grid out of Astar.cpp)
In the same week I also changed how the grid is stored. Before this, the grid was hardcoded inside Astar.cpp, which was annoying because every time I wanted to test a different map I had to go into the algorithm code and edit it.

So I made Maps.h and Maps.cpp and put the grids in there instead. Now I can just swap maps with one line in Astar.cpp (like getMap2_Path() or getMap1_NoPath()). This keeps the A* code focused on the algorithm and makes it way easier to test different cases.

I kept it as code maps instead of a `.txt` file for now because it was quicker and I still get the benefit of better structure and easier testing.

example of my 3 new maps that can be called in the Astar.cpp

<img width="764" height="678" alt="image" src="https://github.com/user-attachments/assets/cdd46841-3c84-4b55-92bf-72e4be083b60" />

## Refactor: make A* return a path (cleaner + easier testing)
This week I also refactored my code so the A* algorithm returns the final path instead of doing all the printing inside printGrid(). One of the main reasons I did this is because `Astar.cpp` was starting to get quite big, and it was getting harder to follow what part was real A* logic and what part was just output, I was finding it a bit hard to follow so I decided to implement this.

So I created a function called `findPath(...)` that takes in the grid and the start/goal coordinates, runs the A* algorithm, and returns the path as a list of coordinates. If there is no path, it returns an empty list.

After that, `printGrid()` became much simpler: it just picks the map, calls `findPath()`, and then prints the grid with the route marked using `*`.

**Why this is better:**
- It keeps the A* algorithm separate from printing which results in cleaner code.
- It makes testing easier because I can call `findPath()` in my tests and check if it returns a path or not.
- It makes the project more reusable and more like proper OOP design.

**Next step:** add more PASS/FAIL tests using `findPath()`. 

## Tests + cleaner demo output (PASS/FAIL, map name, legend, nodes expanded)
In the same week I improved the project in two ways: I added a proper set of tests (PASS/FAIL) and I also cleaned up the demo output so it’s easier to understand when the program runs.

### Adding a proper test set (PASS/FAIL)
I added a real test set in `Test.cpp` because I wanted to make sure the algorithm is actually correct and not just “looks right” when I print the grid. The tests run first and print PASS/FAIL, and after the tests finish the program runs the normal A* demo output below so I can still see the path being drawn.

Example of the code wroking 

<img width="507" height="222" alt="image" src="https://github.com/user-attachments/assets/dff3df63-50c2-4157-93d4-7a22154dde0a" />



This gave me a lot more confidence because if I change something later (like upgrading OPEN to a `priority_queue`), I can run the tests again and instantly see if I broke anything.

### Making the demo output clearer
I also cleaned up the demo output because it was starting to get messy when switching between different maps. I added a clearer demo header that prints the map name, and I added a legend so it’s obvious what each symbol means:
- `S` = start
- `G` = goal
- `*` = final path
- `#` = wall
- `.` = free space

I also added nodes expanded to the output this is basically the size of the CLOSED list. I wanted this because it gives me a simple way to see how much work the algorithm is doing, and it will be useful when comparing performance.

Honestly this helped me a lot because now the output is much easier to follow and it makes taking screenshots for the report a lot easier.

<img width="545" height="358" alt="image" src="https://github.com/user-attachments/assets/8865a353-e7e0-4178-b063-0580109e1bcd" />


**Next step:** upgrade OPEN from a vector scan to a `priority_queue` and then compare results like nodes expanded (and later time if needed).

## What cur does 
So when I added in storing the nodes expanded, I noticed the code uses a variable called cur when rebuilding the path.

When the goal is found, A* doesn’t just hand you the full route as a list straight away. What it does instead is each node remembers where it came from using parentX and parentY. Because of that, to get the final route I have to rebuild it myself.

cur basically means current node. I set cur to the goal node first, then I keep jumping back to its parent node, and each time I add that position into the path. This keeps going until the parent becomes (-1, -1), which is how I know I’ve reached the start, the start node has no parent.

<img width="501" height="321" alt="image" src="https://github.com/user-attachments/assets/65eabf88-e1a7-476e-8ea0-bfe9e2c0bdd2" />




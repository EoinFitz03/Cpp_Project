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

## Why A* Works — Understanding the Heuristic

One thing I wanted to make sure I understood properly was why A* actually finds the shortest path, not just that it does.
The key is that the heuristic h must be admissible — it must never overestimate the true cost to reach the goal. Manhattan distance satisfies this for 4-direction grid movement because the real path can only be equal to or longer than the straight-line Manhattan distance. You cannot get from point A to point B in fewer moves than |dx| + |dy| when you can only move up, down, left, or right.
If the heuristic overestimated, A* would deprioritise nodes that are actually closer to the goal and could return a suboptimal path. If h = 0 (no heuristic at all), A* degrades into Dijkstra's algorithm and expands every reachable node — correct, but much slower.
This is why heuristic choice matters. For this project, Manhattan distance is the right fit because it matches the movement model exactly.

## Week 5 – Improving the A* Implementation 

During Week 5, I focused on improving the internal implementation of my A* algorithm rather than only adding more visible output. My earlier version stored the OPEN set in a `std::vector` and manually searched through it during each iteration to find the node with the lowest `f` value. While this worked correctly for small maps, it was not the most suitable structure for this task because it required repeatedly scanning the full OPEN list.

To improve this, I refactored the algorithm to use `std::priority_queue`. This made the implementation more aligned with modern C++ and with the use of the Standard Library expected in the project brief. Since A* always needs to expand the node with the lowest estimated total cost `f = g + h`, a priority-based structure is a better fit than a normal vector.

To better demonstrate what A* is actually doing, I added a fourth map (getMap4_Large) — a 15×30 winding maze. The 5×5 maps from earlier are useful for testing correctness, but they are too small to show meaningful pathfinding behaviour. A* expands very few nodes on a 5×5 grid and the path is trivially short.

<img width="922" height="627" alt="image" src="https://github.com/user-attachments/assets/92b3629c-8f48-4a76-a11f-bb2c9f82a8cc" />

What "nodes expanded" means: Every time A* pops a node from the OPEN list and processes it, that counts as one expansion. The low number on Map4 (44 expansions for a 43-step path) shows the heuristic is working well — A* is barely exploring any dead ends. It is following the heuristic guidance closely and expanding almost only the nodes that end up on the final path.
This is the advantage of A* over a blind search like BFS. BFS would expand every reachable node in order of distance from the start, potentially visiting hundreds of cells before reaching the goal. A* focuses the search toward the goal using h, which keeps nodes expanded low.


### Why I made this change

The main reason for this change was that the OPEN set in A* is naturally a priority-based structure. In my original version, I had to manually loop through the vector to find the best node each time. That approach was functional, but not ideal from a design point of view.

By switching to `std::priority_queue`, I made the code better suited to the problem. I also used a custom comparison function so that the node with the **lowest** `f` value would be selected first, with the heuristic `h` used as a tie-breaker when two nodes had the same `f` score.

### Example of the updated code

<img width="846" height="445" alt="image" src="https://github.com/user-attachments/assets/18354bc3-9436-49f2-a0d4-3e8b8bb23d49" />


# Project Management

## Planning Approach

I did not really go into this with a detailed plan from the start if I am being honest. My approach was to use each lab session as a checkpoint and just figure out the next step at the end of each week. 
It sounds a bit loose but it actually suited what I wanted to do in this project well because each piece depended on the previous one working first.

Like there was no point thinking about path reconstruction in week one when I had not even written the A* loop yet. Each week I would get something working, make sure it was correct, and then write down what 
I wanted to do next before the following session.

## Weekly Milestones

| Week | Goal | Completed |
|------|------|-----------|
| 1 | Research A*, set up project structure, basic grid | ✅ |
| 2 | Add input validation, set up Node struct and OPEN list | ✅ |
| 3 | Refactor into makeStartNode(), add neighbour generation | ✅ |
| 4 | Full A* loop, path reconstruction, Maps.cpp, findPath() refactor, PASS/FAIL tests | ✅ |
| 5 | Making Finishing touches to my Report + making code more modern Cpp| ✅ |

## Progress Tracking

The lab sessions were basically my only real tracking method. Each one acted as a deadline  I wanted to have something new working and demonstrable each week. The "Next step" notes I left at the end of 
each week in the report also helped a lot because it meant when I sat down the following week I already knew what I was supposed to be doing instead of having to figure it out again.

I also committed to GitHub regularly which was handy because it meant 
I could go back if something broke after a refactor, which did happen.

## What I Would Do Differently

Looking back, having even a rough plan at the start would have helped. Week four especially got quite heavy inside and outisde the lab for the full A* loop, path 
reconstruction, the Maps refactor, the findPath() refactor, and the test set all ended up in the same week. If I had spread some of that out earlier it would have been a lot less stressful. Next time I would 
at least write down three or four rough milestones at the start just 
to stop everything piling up at the end.

# Reflection

## Biggest Challenge — Understanding A*

Honestly the biggest challenge was just understanding how A* actually works before I could write any of it. I knew the general idea of pathfinding going in but I did not really understand how the OPEN list, CLOSED list and the f = g + h scoring all fit together well enough to just sit down and implement it.

The way I got around it was breaking it down step by step instead of trying to write the whole thing at once. I started by just getting the start node set up and printing the g, h and f values to make sure they 
were right. Then I added picking q from OPEN, then neighbour generation, and only after all that did I try the full loop. That made debugging way easier because at each stage I knew exactly what the output should look 
like.

The moment it actually clicked for me was when I started printing the sizes of OPEN and CLOSED during the search. Seeing OPEN shrink and CLOSED grow made it make sense — OPEN is what still needs to be 
explored, CLOSED is what has already been dealt with. Once I got that the rest followed pretty naturally.

## Other Problems I Hit

One early bug that annoyed me for a while was accessing grid coordinates as `grid[x][y]` instead of `grid[y][x]`. Because the grid is stored as 
rows the outer index is y and the inner index is x, which is the opposite of what I expected. It was causing crashes that were hard to spot. Adding the bounds validation fixed it and the error messages made debugging a 
lot easier after that.

Another thing was that `Astar.cpp` got really hard to follow as the project grew. The A* logic and all the printing were mixed together and I kept getting lost in my own code. I fixed it by splitting things into 
`findPath()` for the actual algorithm and keeping the output stuff inside `printGrid()`. Both functions got shorter and it was much easier to work 
with after that.

## What I Would Do Differently

If I started the project again, I would introduce `std::priority_queue` much earlier in the development process rather than first implementing the OPEN list with a plain `std::vector`. I originally chose the vector approach because it was simpler to debug while I was still learning and testing the structure of the A* algorithm. It allowed me to inspect the contents of the OPEN list more easily and helped me confirm that the algorithm was behaving correctly in the early stages.

As my understanding improved, I replaced this with `std::priority_queue`, which is a much more suitable STL container for the OPEN set in A*. Since the algorithm always selects the node with the lowest `f` value next, a priority-based structure is the more appropriate choice. This improved the overall design of the project and made the implementation more consistent with modern C++ and the expectations of the brief.

The main thing I would do differently, therefore, is not the final decision itself, but the timing of that decision. I would move to the more suitable data structure earlier, so that the project would benefit from that stronger design choice from the beginning rather than through later refactoring.

## What Worked Well

The step by step approach worked really well for me. Because I was only 
adding one thing at a time bugs were usually in whatever I had just 
written rather than buried somewhere across the whole codebase. Adding 
the PASS/FAIL tests mid-project was probably the best decision I made 
— when I refactored `findPath()` out of `printGrid()` I just ran the 
tests straight away and could see immediately that nothing had broken. 
That made me a lot more confident about making changes later on.

## Understanding Nodes Expanded
One thing that helped my understanding a lot was tracking nodes expanded throughout the project. When I ran the algorithm on Map2 (5×5) it expanded 15 nodes to find an 8-step path. On Map4 (15×30) it expanded just 44 nodes to find a 43-step path.
That ratio — almost 1:1 between steps and nodes expanded — shows the heuristic is doing its job. A* is barely wasting effort on dead ends because Manhattan distance is guiding it toward the goal efficiently. If I set h = 0 (turning it into Dijkstra's), the nodes expanded would increase significantly because the algorithm would have no directional bias. This comparison made the purpose of the heuristic concrete for me in a way that just reading about it did not.

## Limitations & Future Work

## Current Limitations

## Maps Are Hardcoded

The maps are defined as std::vector<std::string> in Maps.cpp. To test a different layout the source code must be edited and recompiled. Loading maps from a .txt file at runtime would fix this and make the project far more flexible.
Only 4-Directional Movement
The current implementation supports movement in four directions only — up, down, left, right. A* is equally suited to 8-direction movement (adding diagonals), but this would require a different heuristic. Manhattan distance is only admissible for 4-direction grids. For 8-direction movement, the correct heuristic is Chebyshev distance:
cpph = max(abs(x - gx), abs(y - gy));
Using Manhattan distance with diagonal movement would cause it to overestimate in some cases, breaking the admissibility guarantee and potentially producing suboptimal paths.
Uniform Edge Costs
Every move costs 1, which means A* behaves similarly to BFS with a heuristic. A* is specifically designed to handle weighted graphs — for example, a swamp tile ~ could cost 3 moves while a road . costs 1. Without weighted edges the algorithm's full potential is not demonstrated.

## Future Work
The first thing I would add is weighted terrain — a ~ tile with a higher movement cost. This would require changing the neighbour cost from a fixed +1 to a lookup based on the tile type:
cppint tileCost = (grid[ny][nx] == '~') ? 3 : 1;
int newG = q.g + tileCost;
This small change would make the path avoid swamp tiles when a longer but cheaper route exists, which is exactly the kind of problem A* is built to solve.
After that, loading maps from a .txt file and adding 8-direction movement with Chebyshev distance would make the project significantly more complete.

After that, loading maps from a `.txt` file would make the whole project a lot more flexible and would get rid of the need to recompile every time I want to try a different layout.

## AI Usage
AI was used in two ways during this project. First I used it to help explain how A* works — things like why the heuristic needs to be admissible and how the OPEN and CLOSED lists interact. This helped me understand the algorithm faster than just reading about it. Second I used it to help structure and improve the report, making sure the explanations were clear. All code was written and tested by me— AI was not used to generate any of it.

# References

Amit Patel, *Introduction to A\**, Red Blob Games.
Available at: https://www.redblobgames.com/pathfinding/a-star/introduction.html

cppreference.com, *std::vector*, C++ Reference.
Available at: https://en.cppreference.com/w/cpp/container/vector

cppreference.com, *std::priority\_queue*, C++ Reference.
Available at: https://en.cppreference.com/w/cpp/container/priority_queue

Wikipedia, *A\* search algorithm*.
Available at: https://en.wikipedia.org/wiki/A*_search_algorithm

daancode, *a-star*, GitHub.
Available at: https://github.com/daancode/a-star

justinhj, *astar-algorithm-cpp*, GitHub.
Available at: https://github.com/justinhj/astar-algorithm-cpp

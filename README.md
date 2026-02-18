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

## Quick tests I tried (trial and error)
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

## AI usage (brief)
AI was used to help explain A* steps and to help me plan weekly milestones. All code was written and tested by me.

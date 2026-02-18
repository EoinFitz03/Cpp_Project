# Cpp_Project

# First week of project 
In the first lab I did a bit of research and looked through a few A* algorithm examples online to understand what it actually does like the open list, closed list and the heuristic. After that I set up my project with a simple oop structure 1 header file and 2 cpp files and made a basic grid using std::vector<std::string> so it’s easy to print and see what’s going on. I also added a Start (S) and Goal (G) point to make sure my coordinates and obstacles (#) are working properly before I start the real pathfinding. Right now the grid is still hardcoded and I haven’t added any checking or search logic yet, but that’s what I plan to do next thisisi just a general start. As you can see below is my Astar.cpp and teh code running.

<img width="484" height="160" alt="image" src="https://github.com/user-attachments/assets/6a80e3e6-ab96-448a-bbde-11ce8a8733dd" /> <img width="488" height="360" alt="image" src="https://github.com/user-attachments/assets/e8504cb4-bc8e-4743-985e-ce158f67a573" />

# Second week of project
This week I improved my grid setup a bit more before starting the full A* algorithm. I added a small validation step to check that the Start and Goal are actually valid positions. This means I check that they are inside the grid not outside the grid bounds and also that they are not placed on a wall (`#`). If something is wrong, the program prints a warning message like Start is on a wall! and then stops early, so it doesn’t crash or do something incorrect. In teh example below I have the g point set to outside the bounds and this shows up, but it also works the same way with the s. Code used to get that working is also below. 

<img width="534" height="244" alt="image" src="https://github.com/user-attachments/assets/cda05577-a779-465e-bfa7-464ed1ca68fa" />


<img width="645" height="559" alt="image" src="https://github.com/user-attachments/assets/b900a5df-007e-4b17-8f34-dce532eddc49" />

Doing this I learned that coordinates are grid[y][x], if I did not do this i kept running inot crash problems of being outosde the grid or it was hitting a # so that problem is now fixed. 

Right now I still haven’t started the full searching part of A*, but the next step is to start building the algorithm properly by creating a simple node and setting up the OPEN list with the starting node, and then expanding neighbours step by step.



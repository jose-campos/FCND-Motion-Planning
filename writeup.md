## Project: 3D Motion Planning
![Quad Image](./misc/enroute.png)

---


# Required Steps for a Passing Submission:
1. Load the 2.5D map in the colliders.csv file describing the environment.
2. Discretize the environment into a grid or graph representation.
3. Define the start and goal locations.
4. Perform a search using A* or other search algorithm.
5. Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.
6. Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the drone’s start location corresponds to [0, 0, 0, 0].
7. Write it up.
8. Congratulations!  Your Done!

## [Rubric](https://review.udacity.com/#!/rubrics/1534/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.

### Explain the Starter Code

#### backyard_flyer_solutions and motions_planning

The motion planning code builds on top of the backyard flyer solution. There are several improvements to the base code and some functional changes such as the change in how variables are set (auto() enumerators vs hardcoded numbers, all_waypoints vs waypoints and more) and more specific changes to the code in order to fit the new functionality, such as changes and additions to the states. The ```motion_planning.py``` file expands from simple box calculation to a more complex waypoints calculation which is done in the `plan_path` function, in where the colliders are loaded and a primitive path is calculated. Said calculation is based on the grid created, a set of safety settings, like altitude and safety margins, but most importantly using an algorithm to take all this information and generate the necessary waypoints. The file `planning_utils.py` is the one with the most interesting which is described below:

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`
These scripts contain a basic planning implementation that includes...

Function Name | Documentation | Use in context
--- | --- | ---
create_grid | Returns a grid representation of a 2D configuration space based on given obstacle data, drone altitude and safety distance arguments. | Takes into account all the parameters to create a grid to be used to calculate the waypoints.
class Action | - | Sets the possible actions and cost, needed in order to create the possible waypoints
valid_actions | Returns a list of valid actions given a grid and current node. | Used by the algorithm to explore all options from a given point in the grid.
a_star | - | Algorithm that uses all the functions and classes mentioned above to find a path from 2 points in the grid.

### Implementing Your Path Planning Algorithm

#### 1. Set your global home position
Here students should read the first line of the csv file, extract lat0 and lon0 as floating point values and use the self.set_home_position() method to set global home. Explain briefly how you accomplished this in your code.

**Solution:** This was done by opening the file as a text file, reading the first line and using splits and strips to clean up the string and casting the pieces as floats.

#### 2. Set your current local position
Here as long as you successfully determine your local position relative to global home you'll be all set. Explain briefly how you accomplished this in your code.

**Solution:** This was done by using global_to_local, giving it the current global position and the global home as parameters.

#### 3. Set grid start position from local position
This is another step in adding flexibility to the start location. As long as it works you're good to go!

#### 4. Set grid goal position from geodetic coords
This step is to add flexibility to the desired goal location. Should be able to choose any (lat, lon) within the map and have it rendered to a goal location on the grid.

#### 5. Modify A* to include diagonal motion (or replace A* altogether)
Minimal requirement here is to modify the code in planning_utils() to update the A* implementation to include diagonal motions on the grid that have a cost of sqrt(2), but more creative solutions are welcome. Explain the code you used to accomplish this step.

**Solution:** The actions class was updated to allow for diagonal movement with the desired cost and was used at the beginning, later on I decided to move to graphs and so I used the knowledge acquired through the lessons to use the create_grid_and_edges, and with that the updated A* function. The new A* function uses a queue to check all the nodes of the graph in a breadth first fashion, always keeping track of the path taken and the cost. This A* algorithm is incredibly expensive and more efficient ways to get this done must exist, but since it would be ran only once and it can be run by other servers/services I decided to continue with it.

#### 6. Cull waypoints 
For this step you can use a collinearity test or ray tracing method like Bresenham. The idea is simply to prune your path of unnecessary waypoints. Explain the code you used to accomplish this step.

**Solution:** Since I'm using a graph for my final solution culling the waypoints does not apply, although it would be good to find a way to minimize the visited nodes by setting a safe space around the nodes dependant on how close they are to the closes obstacle, that way I could use collinearity to remove the nodes that are slightly out of what would be a straight line from two points. In any case I included the collinearity check and the prunning function in my planning utils since it was used for my first solution.

### Execute the flight
#### 1. Does it work?
It works!

### Double check that you've met specifications for each of the [rubric](https://review.udacity.com/#!/rubrics/1534/view) points.
  
# Extra Challenges: Real World Planning

For an extra challenge, consider implementing some of the techniques described in the "Real World Planning" lesson. You could try implementing a vehicle model to take dynamic constraints into account, or implement a replanning method to invoke if you get off course or encounter unexpected obstacles.



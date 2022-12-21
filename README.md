# Futoshiki solver
In collaboration with my friend [Seeger](https://github.com/seeger22).

## Description
Futoshiki is a Japanese puzzle game that involves inequality restrictions and a sudoku-like number restriction where each row and column must contain distinct digits ranging from 1 to the length of the puzzle. 

For example, 5x5 Futoshiki puzzles require each row and column to have numbers 1-5. This means that no row nor column can contain more than one of each number. The number placements must also satisfy given inequality restrictions, for example the cell in the first row and first column must be less than the cell in the first now and second column.

Example (from Wikimedia Commons):

![image](https://user-images.githubusercontent.com/15400308/208996994-db2438bc-c7ab-494b-9423-4a762ebb31b6.png)

Our input files were given as such:  
3 0 0 0 0\
0 0 0 2 0\
0 0 0 0 3\
0 0 0 0 0\
0 0 0 0 0

0 0 0 0\
0 0 < 0\
0 0 < 0\
0 0 0 0\
0 < 0 0

0 0 0 0 v\
0 0 0 0 0\
0 0 0 0 0\
0 0 v 0 0 


The first grid is the input for the number grid itself, the second grid shows the horizontal inequalities in between each number cell, and the third grid shows the vertical inequalities in between number cells.


## Solution and Implementation
The solution to this problem involved the use of a backtracking algorithm and forward checking with the Minimum Remaining Values (MRV) heuristic and degree heuristic.  The input file would be first read to turn the grids into lists of lists. The horizontal and vertical inequality restrictions would be run through a `gen_constraints` function to grab a list of the locations of each constraint as well as the type of inequality it is. We represented these as dictionaries, where the keys would be the coordinates of each inequality in the form of a tuple, and the values would be the inequality, represented as either a 1 or a 0, as shown in our constraint dict ( `{'^' : 1,'>' : 1, '<' : 0, 'v' : 0}`). Initially, we had wanted to utilize maps that would correspond between the puzzle itself in the form of a numpy array, as well as another list containing the domains of each cell in the puzzle. However, after spending hours debugging our backtracking algorithm and running into issues regarding recursion, we eventually started from scratch with a puzzle class, as we had done with our 15-puzzle solver.

The puzzle class involves a sub-class containing the board which we can use to store state space, like the domains of each cell, the state of the puzzle board, children nodes, parent nodes, and which values that for that cell have been tried. This would allow us to create a depth-first search tree. This tree would begin with a pointer looking at the parent node, which would be the initial puzzle state containing information regarding the constraints and the initialized domains. Our backtracking algorithm would update the domains of each cell and utilize forward checking before continuing with the first target cell to try values in, which would be determined with the MRV and degree heuristics.

Once we determine the first target cell to try values in, we would create a new puzzle state as our current node's child with a deep copy of domains and our puzzle with that target cell's first value, determined by its domain, inserted. Then the pointer would point to this child. If this new child does not have any valid states after trying all the values in its domain, our pointer would then go back to its parent to try the next value for that target cell, which would be another child node. This is our depth-first search backtracking process, as all the possible values for a child fails, we would travel back up the tree to the parent node to try a different child's values. This process is done until we find a complete and valid puzzle state, meaning that it meets all the requirements of a Futoshiki problem (rows and columns have distinct digits from 1-5, and inequality requirements are met). This puzzle state would be written to an output file.

We utilized argparse to read the input file and set an output file, so a sample input in terminal would be: `python run.py --infile Input1.txt --outfile Output1.txt`

Example below:

Input:\
3 0 0 0 0\
0 0 0 2 0\
0 0 0 0 3\
0 0 0 0 0\
0 0 0 0 0

0 0 0 0\
0 0 < 0\
0 0 < 0\
0 0 0 0\
0 < 0 0

0 0 0 0 v\
0 0 0 0 0\
0 0 0 0 0\
0 0 v 0 0 

Output:

3 2 4 1 5\
5 3 1 2 4\
1 5 2 4 3\
2 4 5 3 1\
4 1 3 5 2 

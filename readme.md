# Sudoku Solver

A backtracking constraint satisfaction algorithm developed to solve the hardest possible sudokus in < 15 seconds.


### Approach to Sudoku Solver

When initially building my sudoku solver I implemented a depth first brute force attempt which simply tried inputting every number into blank cells then checking if a state was valid (using the cost function outlined below) before adding it to a list of valid states. However, I quickly realized this method was inefficient and could get stuck on branches in harder sudokus due to the lack of backtracking. In order to solve all the sudokus, I implemented a backtracking constraint search algorithm. This algorithm iterated through the sudoku until it found a 0, once a 0 had been located all possible input domains (1-9) would be tested to see if the sudoku was still valid once the value had been inputted, any sudokus that remained valid were added to a list of valid states. The valid states list acted as a stack and once all new states from one sudoku had been added the most recent states (the frontier) would be popped from the stack, this allowed backtracking. Once a correct sudoku had been found it was returned. If no correct solution was found and no valid states were left the solver returned a sudoku containing only -1. Although this method was able to solve all sudokus, it was extremely time inefficient as there was no heuristic for determining which cell to solve first.

Due to the nature of sudokus any cell is dependent on all cells in its row, column or box, because of this it is possible to implement a heuristic which greatly reduces the number of possible domains (inputs) for any given variable (cell), this was used to increase the time efficiency of the algorithm as instead of checking all possible inputs in a variable and each time checking the validity of the new state, the algorithm could instead determine what the possible inputs where by process of elimination and input those without having to check the new states validity every time. In order to further increase the time efficiency of the algorithm, I implemented a new function, sort_columns, to sort the variables of the sudoku, this function sorted the columns of the inputted sudoku into ascending order based on the number of total possible inputs for that column. The individual empty cells in each column were also sorted by the number of possible moves. This allowed the algorithm to first work on the cells with the fewest available options and gradually move on to the cells with more possibilities. This approach increased the efficiency of the algorithm to a point where it could solve all sudokus within a reasonable time frame. 

The final algorithm is a backtracking constraint satisfaction search algorithm that utilizes minimum remaining values to determine the optimal order to solve the empty variables in the sudoku.


### Functions

Below are brief outlines and uses of each function in the code:

cost(sudoku):
	This function iterates through the sudoku and checks for any clashes, i.e. the same number twice in a row, column or box. This function returns the number of clashes, i.e it would 		return 0 for a solved sudoku or a valid state unsolved sudoku. This function is used to check is an initially inputted sudoku is in a valid state.


sort_columns(sudoku):
	As mentioned above this function sorts the empty cells of an inputted sudoku so that the algorithm can work through them more efficiently, starting with the cell with the least 		valid inputs. This function returns a list of indexes of 0’s in the initial sudoku sorted by their number of possible inputs. This function is used at the start of the algorithm 		when an sudoku is first inputted.

num_possible(sudoku, row, col):
	This function is used to determine the total number of possible inputs for a given cell (but not what those inputs are). It builds a set of all values seen in the same row, column 	and box as the input cell and based on the length of this set determines how many possible inputs there are for the empty cell. This function is used when sorting the empty cells of 	the initial sudoku.

possible_values(sudoku, row, col):
	For a given cell this function outputs a list of the possible inputs that will give a valid sudoku state. It first builds a list of all impossible inputs, based on numbers already 		seen in the same row, column and box as the cell and then returns a list of possible inputs (any number not in the impossible list). This function is used when inputting new values 		to a valid, but unsolved, sudoku (as part of new_valid_states()).

new_valid_states(current_sudoku, sorted_cols):
	This function receives sudoku and a list of the sorted cells in the sudoku. It iterates through the sorted cells until it finds a 0, it then inputs all possible values of this 0 		into the current sudoku and appends the new sudokus to the new_valid list which is returned. This function is used in the solver function to build up a list of valid states (i.e. 		the frontier).

solver(single_sudoku):
	This is the main function of the algorithm and deals with outputting the answer. The first step of this function involves instantiating an empty list of valid states and appending 	the initial sudoku to that list. The empty cells of the sudoku are then sorted using sort_columns. The cost function is then used to check if the initial sudoku is in a valid state, 	if it is the main loop is called, if it isn't then a sudoku of just -1s is returned. The main loop of the algorithm pops the last sudoku from the valid_states list, checks 	to 	see if the current state is a correct solution, if it is it returns the solved sudoku, if it isn’t then it appends all the new possible states from this current state to the 	valid_states list using the new_valid_states function. The loop then checks to see if there are any valid states left to be used for the next cycle. If no valid states remain and 	the current state is not a correct sudoku then the function returns a sudoku consisting of only -1 as the sudoku is unsolvable.


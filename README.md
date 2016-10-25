# Slack-driven-Dual-Vth-Leakage-Optimization-Algorithm
TCL command to be integrated within Synopsys Design Compiler that performs a Slack-driven Dual-Vth post-synthesis optimization.  
Such a command reduces leakage power by means of dual-Vth assignment while forcing the number of quasi-critical paths below a user-defined constraint. 

## Details
The *leakage_opt* command performs a leakage power optimization on a design that is initially synthesized with LVT cells exclusively.
The first step is to check the correctness of the user-provided command options. Then, two lists are created and populated: ordered_names and ordered_wcat. The former contains the full names of all cells in the design in ascending order of worst case arrival time (wcat) of all paths a given cell is part of, while the latter contains the wcat in ascending order. ordered_names is the starting point of our algorithm, which performs to-HVT swapping starting from the cells in paths with highest slack i.e. lowest arrival time (this way it is unlikely that a path will have negative slack after cell swapping occurs).
The algorithm iteratively swaps (swap procedure) cells with their HVT equivalent in chunks of 10% of the total cells at each iteration (coarse-grained swapping) of the main while loop. After each single cell is swapped, the algorithm checks whether any of the paths in which the cell is included now have a negative slack: in this case, the cell is immediately swapped back to LVT. After the current chunk of cells has been attempted to be swapped, the following conditions are checked: 

1. if more than the user requested paths have been placed in the slack window we perform a fine-grained backtracking (backtrack procedure: it sorts HVT cells by decreasing size, it swaps back to LVT the HVT cell with lowest size and checks whether the number of paths in the window is now equal or less than the user requested paths, if not keep backtracking);

2. if the number of paths in the slack window is in the 90% to 100% of user requested paths interval, the command breaks the loop and returns correctly;

3. if all cells in the design have been attempted to be swapped and point 2 has not been satisfied, the algorithm breaks the loop as it cannot perform the optimization (unfeasible).

## Example
![Alt text](./img.png?raw=true "Example")

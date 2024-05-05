// Java code
import java.util.*;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Element {
		int row, col;
		List<Integer> candidates;

		Element(int row, int col) {
			this.row = row;
			this.col = col;
			this.candidates = new ArrayList<>();
		}
	}

// Input matrix
class Main {
	static final int N = 9;
	static int[][] grid = {
			{3, 0, 6, 5, 0, 8, 4, 0, 0},
			{5, 2, 0, 0, 0, 0, 0, 0, 0},
			{0, 8, 7, 0, 0, 0, 0, 3, 1},
			{0, 0, 3, 0, 1, 0, 0, 8, 0},
			{9, 0, 0, 8, 6, 3, 0, 0, 5},
			{0, 5, 0, 0, 9, 0, 6, 0, 0},
			{1, 3, 0, 0, 0, 0, 2, 5, 0},
			{0, 0, 0, 0, 0, 0, 0, 7, 4},
			{0, 0, 5, 2, 0, 6, 3, 0, 0}
	};

	static List<Element> graph;


// Method to check if the inserted element is safe
	static boolean isSafe(int row, int col, int num) {
		// check row
		for (int i = 0; i < N; i++) {
			if (grid[row][i] == num) {
				return false;
			}
		}

		// check column
		for (int i = 0; i < N; i++) {
			if (grid[i][col] == num) {
				return false;
			}
		}

		// check subgrid
		int subgridRow = row - row % 3;
		int subgridCol = col - col % 3;
		for (int i = subgridRow; i < subgridRow + 3; i++) {
			for (int j = subgridCol; j < subgridCol + 3; j++) {
				if (grid[i][j] == num) {
					return false;
				}
			}
		}

		return true;
	}

//initialize the graph
	static void initializeGraph() {
		graph = new ArrayList<>();
		for (int row = 0; row < N; ++row) {
			for (int col = 0; col < N; ++col) {
				if (grid[row][col] == 0) {
					Element e = new Element(row, col);
					for (int num = 1; num <= N; ++num) {
						if (isSafe(row, col, num)) {
							e.candidates.add(num);
						}
					}
					graph.add(e);
				}
			}
		}
	}


// Sort the rem map in order to start with smaller number of elements
//to be filled first. 
	static int sortByFewestCandidates(Element a, Element b) {
		return a.candidates.size() - b.candidates.size();
	}

	static boolean solveSudoku() {
		if (graph.isEmpty()) {
			return true;
		}

		Collections.sort(graph, Main::sortByFewestCandidates);
		Element e = graph.get(graph.size() - 1);
		graph.remove(graph.size() -1);
	// graph.pop_back();
	int row = e.row;
	int col = e.col;
		
	for (int i = 0; i < e.candidates.size(); ++i) {
		int num = e.candidates.get(i);
		if (isSafe(e.row, e.col, num)) {
			grid[e.row][e.col] = num;
			graph.remove(e);
			if (solveSudoku()) {
				return true;
			}
			graph.add(e);
			Collections.sort(graph, Main::sortByFewestCandidates);
			grid[e.row][e.col] = 0;
		}
	}
	return false;
}

public static void main(String[] args) {
	initializeGraph();
	solveSudoku();

	// print solved sudoku
	for (int[] row : grid) {
		for (int num : row) {
			System.out.print(num + " ");
		}
		System.out.println();
	}
}
}

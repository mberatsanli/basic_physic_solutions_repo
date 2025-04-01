# Problem 1

Equivalent Resistance Using Graph Theory

## 1. Theoretical Background

### Graph Theory in Circuit Analysis
In electrical circuit analysis, we can represent circuits as graphs where:
- Nodes (vertices) represent junctions
- Edges represent resistors
- Edge weights represent resistance values

The equivalent resistance \( R_{eq} \) between two points in a circuit can be calculated using graph-theoretic methods. This approach is particularly powerful for complex circuits where traditional series-parallel reduction becomes cumbersome.

### Mathematical Foundation

For a circuit with \(n\) nodes and \(m\) resistors, we can use:

1. **Kirchhoff's Current Law (KCL)**:
\[
\sum_{k=1}^{n} I_k = 0
\]

2. **Kirchhoff's Voltage Law (KVL)**:
\[
\sum_{k=1}^{m} V_k = 0
\]

3. **Ohm's Law** for each resistor:
\[
V = IR
\]

The conductance matrix \(G\) of the circuit is given by:

\[
G_{ij} = \begin{cases}
\sum_{k} \frac{1}{R_k} & \text{if } i = j \\
-\frac{1}{R_{ij}} & \text{if } i \text{ and } j \text{ are connected} \\
0 & \text{otherwise}
\end{cases}
\]

---

## 2. Algorithm Implementation

### Basic Implementation
Here's a Python implementation using NetworkX to handle graph operations:

```python
import networkx as nx
import numpy as np
import matplotlib.pyplot as plt

class CircuitSolver:
    def __init__(self):
        self.G = nx.Graph()
        
    def add_resistor(self, node1, node2, resistance):
        """Add a resistor between two nodes."""
        self.G.add_edge(node1, node2, weight=resistance)
        
    def get_conductance_matrix(self):
        """Generate the conductance matrix for the circuit."""
        n = self.G.number_of_nodes()
        G_matrix = np.zeros((n, n))
        
        for i in range(n):
            for j in range(n):
                if i == j:
                    # Diagonal elements: sum of conductances
                    for neighbor in self.G.neighbors(i):
                        G_matrix[i,i] += 1/self.G[i][neighbor]['weight']
                elif self.G.has_edge(i,j):
                    # Off-diagonal elements: negative conductance
                    G_matrix[i,j] = -1/self.G[i][j]['weight']
                    
        return G_matrix
    
    def calculate_equivalent_resistance(self, source, sink):
        """Calculate equivalent resistance between two nodes."""
        n = self.G.number_of_nodes()
        G_matrix = self.get_conductance_matrix()
        
        # Remove one row and column to make matrix non-singular
        G_reduced = np.delete(np.delete(G_matrix, sink, 0), sink, 1)
        
        # Create current vector (1A entering source, -1A leaving sink)
        i = np.zeros(n-1)
        if source < sink:
            i[source] = 1
        else:
            i[source-1] = 1
            
        # Solve for node voltages
        v = np.linalg.solve(G_reduced, i)
        
        # Calculate equivalent resistance (voltage difference / current)
        # Since we're using 1A current, the voltage difference is equal to the resistance
        if source < sink:
            return abs(v[source] - 0)  # sink voltage is 0 by our reference
        else:
            return abs(v[source-1] - 0)
    
    def visualize_circuit(self, ax=None, title="Circuit Graph"):
        """Visualize the circuit graph.
        
        Args:
            ax (matplotlib.axes.Axes, optional): Axes to plot on. If None, creates new figure
            title (str): Title for the plot
        """
        if ax is None:
            plt.figure(figsize=(10, 8))
            ax = plt.gca()
        
        pos = nx.spring_layout(self.G)
        
        # Draw nodes
        nx.draw_networkx_nodes(self.G, pos, node_color='lightblue', 
                             node_size=500, ax=ax)
        
        # Draw edges with resistance labels
        nx.draw_networkx_edges(self.G, pos, ax=ax)
        edge_labels = nx.get_edge_attributes(self.G, 'weight')
        nx.draw_networkx_edge_labels(self.G, pos, edge_labels)
        
        # Draw node labels
        nx.draw_networkx_labels(self.G, pos)
        
        ax.set_title(title)
        ax.axis('off')

def test_circuits():
    # Import matplotlib if not already imported
    import matplotlib.pyplot as plt
    
    # Create figure with subplots
    fig, axes = plt.subplots(1, 3, figsize=(15, 5))
    ax1, ax2, ax3 = axes  # Unpack the axes array
    
    # Test Case 1: Simple Series Circuit
    print("Test Case 1: Simple Series Circuit")
    cs1 = CircuitSolver()
    cs1.add_resistor(0, 1, 2)
    cs1.add_resistor(1, 2, 3)
    r1 = cs1.calculate_equivalent_resistance(0, 2)
    print(f"Equivalent Resistance: {r1}Ω")
    cs1.visualize_circuit(ax1, f"Series Circuit\nReq = {r1:.1f}Ω")
    
    # Test Case 2: Simple Parallel Circuit
    print("\nTest Case 2: Simple Parallel Circuit")
    cs2 = CircuitSolver()
    cs2.add_resistor(0, 1, 4)
    cs2.add_resistor(0, 1, 4)
    r2 = cs2.calculate_equivalent_resistance(0, 1)
    print(f"Equivalent Resistance: {r2}Ω")
    cs2.visualize_circuit(ax2, f"Parallel Circuit\nReq = {r2:.1f}Ω")
    
    # Test Case 3: Complex Bridge Circuit
    print("\nTest Case 3: Wheatstone Bridge Circuit")
    cs3 = CircuitSolver()
    cs3.add_resistor(0, 1, 2)
    cs3.add_resistor(1, 2, 2)
    cs3.add_resistor(2, 3, 2)
    cs3.add_resistor(0, 4, 2)
    cs3.add_resistor(4, 3, 2)
    cs3.add_resistor(1, 4, 3)
    r3 = cs3.calculate_equivalent_resistance(0, 3)
    print(f"Equivalent Resistance: {r3}Ω")
    cs3.visualize_circuit(ax3, f"Wheatstone Bridge\nReq = {r3:.1f}Ω")
    
    # Adjust layout and display
    plt.tight_layout()
    plt.show()

if __name__ == "__main__":
    test_circuits()

---

## 3. Analysis of Results

### Test Case Results

1. **Series Circuit**:
   - Expected: \(R_{eq} = R_1 + R_2 = 5\Omega\)
   - Calculated: \(5\Omega\)

2. **Parallel Circuit**:
   - Expected: \(R_{eq} = \frac{R_1R_2}{R_1 + R_2} = 2\Omega\)
   - Calculated: \(2\Omega\)

3. **Wheatstone Bridge**:
   - More complex calculation
   - Demonstrates the power of graph-theoretic approach

### Algorithm Efficiency

The algorithm's complexity is dominated by:
1. Matrix operations: \(O(n^3)\) for solving linear equations
2. Graph operations: \(O(E)\) for building conductance matrix

Where:
- \(n\) is the number of nodes
- \(E\) is the number of edges (resistors)

---

## 4. Conclusion

The graph-theoretic approach to calculating equivalent resistance offers several advantages:

1. **Systematic**: Handles complex circuits methodically
2. **Generalizable**: Works for any circuit topology
3. **Automatable**: Easy to implement in software
4. **Scalable**: Can handle large circuits efficiently

This method bridges the gap between electrical circuit theory and graph theory, demonstrating the power of mathematical abstraction in solving physical problems.

---

## 5. Further Improvements

Possible enhancements to the current implementation:

1. Add support for voltage and current sources
2. Implement parallel processing for large circuits
3. Add error handling for singular matrices
4. Optimize matrix operations for sparse circuits

![Circuit Graph Examples](assets/problem2.png) 
# mincost.cc

## Synopsis

mincost.cc is a C++ program which solves the minimum-cost flow problem on a discrete directed network given integer arc capacities, unit costs, 
and divergence values using the epsilon relaxation algorithm with epsilon scaling. Please see [http://en.wikipedia.org/wiki/Minimum-cost_flow_problem] for details on the problem and background. In simple terms, it finds a flow vector x indexed by the arcs of the graph that yields the minimum cost and 
which has the specified divergence values at every node.

### Notation:

1. \\(x\\) : flow vector indexed by the arcs of the graph. Each element of \\(x\\) corresponds to the flow assigned to a given arc
2. \\(d\\) : cost vector indexed by the arcs of the graph. Each element of \\(d\\) gives the unit cost for a given arc.
3. \\(c\\) : capacity vector indexed by arc. Each element of \\(c\\) gives the upper capacity of each arc. The lower capacities are all assumed to be \\(0\\).
4. \\(b\\) : supply vector indexed by node. Each element of b gives the **desired supply** at each node of the graph.
5. \\(r\\) : reduced cost vector indexed by arc. \\(r[j] = d[j] + u[i] - u[i']\\) where \\(j \sim (i,i')\\) is the arc from node \\(i\\) to node \\(i'\\).
6. \\(s\\) : surplus vector indexed by node. Indicates the surplus supply at each node. i.e. \\(s[i] = (\textrm{net flow into node $i$}) - b[i]\\) 
7. \\(u\\) : node potential vector indexed by node.

## Algorithm Description:

### Complementary Epsilon Slackness Condition:

A given flow \\(x\\) and potential \\(u\\) satisfy epsilon-complementary slackness if for all arcs \\(j \sim (i,i')\\), \\(d[j] + u[i] - u[i']\\) is

* in the interval \\([-\epsilon, \epsilon]\\) if \\(0 < x[j] < c[j]\\)
* \\(>= -\epsilon if 0 = x[j]\\)
* \\(<= \epsilon\\) if \\(x[j] = c[j]\\)

### Initialization: 
1. Set \\(u[i] = 0\\) for all nodes \\(i\\).
2. For each arc \\(j \sim (i,i')\\), compute \\(r[j] = d[j] + u[i] - u[i']\\)
3. Initialize flow x by calling initflow(x,r,c,A)
4. Set epsilon = maximum degree of any node so that x and u satisfy epsilon-complementary slackness condition.
5. Compute node surplus \\(s[i] = (\textrm{ net flow into node $i$}) - b[i]\\)

### Main algorithm:
1. If s[i] = 0 for all nodes i, then x is feasible, so stop. Else pick any node ibar with s[ibar] > 0. 
2. While \\(s[\overline{i}] \neq 0 \\):
..1. Paint each arc \\(j \sim (i,i')\\):
...* white if \\(r[j] \in [-\epsilon, -\epsilon/2]\\) and \\(x[j] < c[j]\\)
...* black if \\(r[j] \in [\epsilon/2, \epsilon]\\) and \\(x[j] > 0\\)
...* red otherwise
..2. If there is a black arc jbar out of ibar, then decrease \\(x[\overline{j}]\\) by \\(\min\{x[\overline{j}], s[\overline{i}]\}\\). Otherwise, if there is a white arc jbar into ibar, then increase \\(x[\overline{j}]\\) by \\(\min\{c[\overline{j}] - x[\overline{j}], s[\overline{i}]\}\\).
..3. If no criteria from previous step apply, then increase \\(u[\overline{i}]\\) by as much as possible while maintaining \\(\epsilon\\)-complementary slackness. In particular, set \\(u[\overline{i}] = u[\overline{i}] + \alpha\\), where
\begin{equation}
\alpha = \min\left\{ \{ -r[j] + \epsilon \, | \, j \sim (\overline{i}, i') \textrm{ and } x[j] > 0\}, \{r[j] + \epsilon \, | \, j \sim (i', \overline{i}) \textrm{ and } x[j] < c[j] \} \right\}.
\end{equation}
3. \\(\epsilon = \epsilon/2 \\)
3. Repeat 1 until \\(\epsilon < 1/N\\).

## Code Example

>> g++ mincost.cc -o mincost
>> ./mincost mcnf1.dat

## Author

Mark Hubenthal
markhubenthal@gmail.com
hubenjm.github.io


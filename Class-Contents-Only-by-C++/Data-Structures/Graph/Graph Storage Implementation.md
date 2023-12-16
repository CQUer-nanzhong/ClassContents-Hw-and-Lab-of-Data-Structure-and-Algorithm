# The Storage of Graph

## 图存储的两种方式

| 特点/存储法          | 邻接矩阵                               | 邻接表                                      |
|---------------------|----------------------------------------|-------------------------------------------|
| **空间效率**         | 较低，需要为每一对顶点分配空间           | 较高，只为存在的边分配空间                   |
| **存储方式**         | 二维矩阵                               | 一维数组（顶点表）+ 链表（边表）              |
| **适用场景**         | 稠密图，顶点多且边也多                 | 稀疏图，边相对顶点较少                       |
| **边的检索效率**     | 高，可以直接通过矩阵索引访问           | 低，可能需要遍历链表                          |
| **添加/删除边的效率** | 高，直接修改矩阵元素                   | 取决于链表操作，一般较低                       |
| **添加/删除顶点的效率** | 低，需要重新构造整个矩阵               | 较高，只需修改顶点表和相关链表                |
| **存储边的信息**     | 每对顶点间的关系都有存储（即使没有边） | 只存储实际存在的边                            |
| **图的类型表示**     | 无向图的邻接矩阵是对称的               | 无向图中，每条边在两个顶点的链表中各出现一次   |
| **计算顶点的度**     | 直接计算                               | 出度容易计算（遍历边表），入度计算较困难       |

### 邻接矩阵存储法（Adjacency Matrix）

邻接矩阵是表示图中顶点之间的关系的一种方法。在这种存储结构中，我们使用**一维数组**来存放顶点，用一个**二维矩阵**来表示顶点间的边。

- **数组定义**：如果图中有`n`个顶点，我们定义一个`n x n`的矩阵，矩阵的每个元素表示两个顶点之间是否有边。
- **元素表示**：矩阵中的元素`a[i][j]`表示顶点`i`到顶点`j`是否存在一条边。
  - 对于非加权图，如果`i`和`j`之间有边，`a[i][j] = 1`；如果没有边，`a[i][j] = 0`。
  - 对于加权图，`a[i][j]`可以表示`i`和`j`之间边的权重。
- **特点**：
  - 对于无向图，邻接矩阵是对称的（以主对角线）。即`a[i][j] = a[j][i]`。
  - 对于简单图，主对角线上元素`a[i][i] = 0`，即顶点到自身没有边相连。

![Adjacency Matrix 1](images/Adjacency%20Matrix%201.png)

![Adjacency Matrix 2](images/Adjacency%20Matrix%202.png)

#### 邻接矩阵存储图各项操作的时间复杂度

| 操作                   | 时间复杂度    | 解释                                                         |
|------------------------|------------|--------------------------------------------------------------|
| 检查两个顶点是否相邻（有无边连接）          | \( O(1) \) | 通过直接访问矩阵的对应元素来确定两个顶点是否相邻。                    |
| 查找给定顶点的所有邻居（计算结点的度）        | \( O(n) \) | 遍历矩阵中给定顶点对应的行或列，找到所有的邻居。                         |
| 查找度最大的结点            | \( O(n^2) \) | 需要遍历矩阵的每一行，计算所有顶点的度，然后找出度最大的顶点。              |
| 添加或删除一个顶点          | \( O(?) \) | 添加或删除顶点涉及到矩阵维度的变化，需要重新构造整个矩阵。                   |
| 添加或删除一条边            | \( O(1) \) | 直接修改两个顶点对应的矩阵元素即可添加或删除一条边。                        |

#### 加权邻接矩阵（Weighted Adjacency Matrix）

- 矩阵的行和列对应图中的顶点。
- 矩阵中的元素表示顶点之间边的权重。
  - 如果顶点`i`和`j`之间存在边，则`a[i][j]`表示这条边的权重。
  - 如果顶点`i`和`j`之间没有边，则`a[i][j]`通常设置为一个非常大的数（例如`∞`），或者在某些实现中，可能设置为一个特殊的标记值，以表示这两个顶点之间没有直接的连接。
- 对于无向加权图，邻接矩阵是对称的，即`a[i][j]`和`a[j][i]`相等，因为边是无向的，从`i`到`j`的权重与从`j`到`i`的权重是相同的。
- 对于自环（即顶点到自己的边），如果允许自环存在，它们会在主对角线上表示，即`a[i][i]`表示顶点`i`的自环的权重。其它情况下，该值一般设为\(0\)。

![Weighted Adjacency Matrix](images/Weighted%20Adjacency%20Matrix.png)

#### 邻接矩阵与加权邻接矩阵的优缺点

##### 邻接矩阵的优点

1. **快速查找边的存在性**：判断任意两个顶点之间是否存在边非常容易，只需要查看矩阵中相应的元素即可，时间复杂度为 \( O(1) \)。
2. **快速修改边的信息**：在两个顶点之间添加或删除边非常容易，直接修改矩阵对应的元素即可，操作时间复杂度也是 \( O(1) \)。
3. **易于计算顶点的度数**：对于无向图，顶点的度就是邻接矩阵中相应行（或列）非零元素的个数；对于有向图，顶点的出度是行的非零元素的个数，入度是列的非零元素的个数。

##### 邻接矩阵的缺点

1. **空间效率低**：当边的总数远远小于 \( n^2 \) 时，依然需要 \( n^2 \) 个内存单元来存储边的信息，这在稀疏图（边相对较少）中造成了巨大的空间浪费。
2. **不适合大规模稀疏图**：对于大规模的稀疏图，邻接矩阵会占用大量内存，但实际上很多内存位置是不会被使用的。

##### 加权邻接矩阵的优点

1. **直接表示边的权重**：加权邻接矩阵可以直接存储边的权重信息，这对于算法（如最短路径算法）来说是必要的。
2. **统一表示有权和无权图**：即便是非加权图，也可以通过赋予边统一的权重（如1）来使用加权邻接矩阵表示。

##### 加权邻接矩阵的缺点

1. **初始化复杂度高**：对于不存在的边，需要初始化一个较大的数或特殊值（如无穷大），这在某些实现中可能需要额外的逻辑处理。
2. **权重更新成本高**：如果图中边的权重经常变动，每次变动都需要更新矩阵，可能会导致频繁的计算和存储操作。

#### 邻接矩阵的适应情况和特殊图的存储处理

- **稠密图的存储**

  - **有向图**：如果图是稠密的，即边的数量接近顶点对的数量，那么使用邻接矩阵来存储有向图是合适的。因为大部分的顶点对之间都存在边，所以邻接矩阵中将会有**很少的空白**（或标记为无穷大的）位置，这使得空间的利用效率较高。
  - **无向图**：无向图的邻接矩阵是关于主对角线对称的。由于这种对称性，我们不必存储整个矩阵。我们只需要存储**上三角或下三角**部分（包括主对角线），这可以减少存储空间的需求。

- **稀疏图的存储**

  - 当图是稀疏的，即边的数量远少于顶点对的数量时，使用传统的邻接矩阵存储会造成大量的空间浪费，因为矩阵中的大部分位置都会是空白或标记为无连接的值。
  - 在这种情况下，一个有效的存储策略是**仅存储那些非零元素及其位置信息**。每个非零元素`a[i][j]`（表示顶点`i`和`j`之间有一条边）用一个三元组`(i, j, a[i][j])`来表示。
  - 这些三元组可以根据顶点的编号顺序进行排序，通常是先按行排序，再按列排序。这样，我们可以快速定位到任意一个顶点的所有出边或入边。
  - 这些三元组可以存储在一个顺序表（数组）或者链表中，这取决于图的具体操作需求和边的动态变化情况。

在选择邻接矩阵作为图的存储结构时，必须考虑图的稠密度和边的分布特点。**对于稠密图，完整的邻接矩阵或其上/下三角表示是合理的；对于稀疏图，仅存储非零元素及其位置是更加高效的方法**。这样的选择可以在保证操作效率的同时，尽可能地减少存储空间的浪费。

### 邻接表存储法（Adjacency List）

#### 邻接表的基本组成

邻接表是图的另一种存储方法，特别适用于**稀疏图**（即边的数量远少于顶点对数量的情况）。在邻接表中，图的表示由两部分组成：**顶点表**和**边表**。

1. **顶点表**：顶点表是一个**一维数组**，每个数组元素存储一个顶点的信息，并且包含一个指向该顶点的边表的首结点的指针（**首指针**）。这个指针实际上指向一个链表，其中包含了所有从这个顶点出发的边的信息。

2. **边表**：每个顶点的边表是一个单链表，链表中的每个节点代表从该顶点出发的一条边。在无向图中，每条边在两个相关联的顶点的边表中各出现一次；在有向图中，每条边只出现在对应的起始顶点的边表中。

![Adjacency List](images/Adjacency%20List.png)

### 邻接表的存储特点

- **空间效率**：邻接表仅存储有边的信息，不存储无边信息，这在稀疏图中大大提高了空间利用率。
- **出度的计算**：对于有向图，计算某个顶点的出度非常容易，只需要遍历该顶点的边表即可。对于无向图，遍历边表同样可以得到顶点的度。
- **入度的计算**：在普通的邻接表中，计算顶点的入度比较困难，因为需要检查图中所有顶点的边表。为了解决这个问题，有向图可以使用**逆邻接表**。
- **时间复杂度**：邻接表表示法的时间复杂度是 \( O(|V|+|E|) \)，或者当边数多于顶点数时，可以简化为 \( O(|E|) \)。

### 特殊的邻接表存储

- **逆邻接表（Inverse Adjacency List）**：逆邻接表是邻接表的变体，专门用于有向图。在逆邻接表中，每个顶点的边表存储的是指向该顶点的边，也就是说，存储的是入边。这使得计算顶点的入度变得容易。

![Inverse Adjacency List](images/Inverse%20Adjacency%20List.png)

- **动态邻接表（Dynamic Adjacency List）**：传统的邻接表使用静态数组来存储顶点表，这需要在图初始化时预估数组规模。一种改进方法是使用单链表代替静态数组来表示顶点表，每个节点代表一个顶点。这种方法在**顶点数不确定**或者**图在运行时动态变化**时更加灵活。在这种情况下，边表中存储的可能不再是顶点的数组索引，而是指向顶点的指针或引用。

  - 使用链表来表示顶点表的邻接表使得顶点的添加和删除操作非常灵活，并且对于动态变化的图结构，这种方法特别有效。然而，必须注意的是，这种方法可能会使得查找特定顶点或边的操作变得相对复杂和耗时，因为可能需要遍历链表。
  - 该条件下添加或删除边或顶点的操作方法：
    - **添加顶点**：
      - 创建一个新的顶点节点。
      - 将新顶点节点插入到顶点链表中的适当位置。
      - 如果新顶点有边连接到其他顶点，为这些边创建新的边节点，并将它们添加到新顶点的边链表中。

    - **删除顶点**：
      - 从顶点链表中移除顶点节点。
      - 遍历所有其他顶点的边链表，移除指向被删除顶点的边节点。
      - 如果被删除的顶点有自己的边链表，释放与之相关的所有边节点。

    - **添加边**：
      - 确定边的起点和终点顶点。
      - 在起点顶点的边链表中创建一个新的边节点，并设置它的指针指向终点顶点。
      - 如果是无向图，还需要在终点顶点的边链表中添加一个指向起点顶点的边节点。

    - **删除边**：
      - 确定边的起点和终点顶点。
      - 在起点顶点的边链表中找到并删除相应的边节点。
      - 如果是无向图，还需要在终点顶点的边链表中找到并删除指向起点顶点的边节点。

![Dynamic Adjacency List](images/Dynamic%20Adjacency%20List.png)

***邻接多重表与十字链表未列出***

***图的基本操作实现相关内容在“The ADT of Graph”（图的抽象数据构造）一节呈现***
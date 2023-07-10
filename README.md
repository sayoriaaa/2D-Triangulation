# Tetrahedralization

这是本人毕设中Tetrahedralization模块的实现部分，包括：

- tetgen.ipynb：调用[tetgen](https://pypi.org/project/pytetgen/)进行输入三角网格的四面体化
- delaunay_triangulation.ipynb：复现了[Computing Dirichlet tessellations](https://academic.oup.com/comjnl/article/24/2/162/338193)的三角剖分算法
- delaunay_tetrahedralization.ipynb：（TODO）复现Delaunay-based的四面体化算法

## 环境配置

```
conda activate gp
pip install pyvista
pip install tetgen

pip install jupyterlab pythreejs ipyvtklink panel
```



## 结果展示

#### 调用[tetgen](https://pypi.org/project/pytetgen/)进行输入三角网格的四面体化：

<img src="imgs\tetgen.JPG" style="zoom:50%;" />

#### 复现[Computing Dirichlet tessellations](https://academic.oup.com/comjnl/article/24/2/162/338193)的三角剖分算法：

- 动画![](imgs\triangluation.gif)

- 中间步：

<img src="imgs\tri_m.JPG" style="zoom:50%;" />

- 剖分结果：

<img src="imgs\tri_f.JPG" style="zoom:50%;" />

## 学习笔记

Tetgen是Delaunay-based方法，Delaunay三角/四面体满足外接圆/球不包含其他顶点的性质，因此能生成较为规整的网格。当输入是点时，Delaunay-based方法可以生成三角网格；当输入是三角网格时，Delaunay-based方法可以生成四面体。

构造Delaunay的方法主要有[四种](https://en.wikipedia.org/wiki/Delaunay_triangulation#Algorithms)

- Flip algorithms
- Incremental
- Divide and conquer
- Sweephull

在这里，我们实现[Watson_algorithm](https://en.wikipedia.org/wiki/Bowyer%E2%80%93Watson_algorithm)，通过以下步骤

- 创建三个点，使得构造出的三角形（super-triangle）包含所有已存在的点
- 置一个triangulation的空list，将super-triangle添加到triangulation的list中
- 遍历每个点，对于每个点$p$：
  - 设置一个badTriangles的空list
  - 遍历triangulation的list，如果存在list中的三角形，使得$p$在三角形的外接圆内，则将该三角形添加到badTriangles的list中去（这里我们使用[行列式](https://en.wikipedia.org/wiki/Delaunay_triangulation#Algorithms)计算，注意使用这个算法顶点要**保证是counterwise**的）
  - 构造点$p$对应的polygonal hole：设置一个polygon的空list，遍历badTriangles的每个三角形，对于每个三角形$A$，遍历它的每一条边，如果这条边不被badTriangles中的其他三角形所共享，则将这条边添加到polygon中
  - 将triangulation中的badTriangles删除
  - 对于点$p$和polygon中的每一条边，可以构造出一个三角形；将所有这些三角形添加到triangulation中

- 过河拆桥，将triangulation中所有包含super-triangle顶点的三角形删除



### Voronoi Diagrams and Delaunay Triangulations

记$\mathcal{P}=\{\mathbf{p}_1,\cdots,\mathbf{p}_n\}$为$\mathbb{R}^d$上的点集，我们将点集中的每个点$\mathbf{p}_i$与对应的*Voronoi region*关联：
$$
V(\mathbf{p}_i)=\{\mathbf{x}\in\mathbb{R}^d:|| \mathbf{x}-\mathbf{p}_i||\leqslant||\mathbf{x}-\mathbf{p}_i||,\forall j\neq i\}
$$
所有的这些*Voronoi region*，以及每个*Voronoi region*的面，构成了$\mathcal{P}$的*Voronoi diagram*，它可以看作$\mathbb{R}^d$上的一个分割，因为任意$\mathbb{R}^d$上的点属于至少一个*Voronoi region*。其中，所有到$\mathbf{p}_i,\mathbf{p}_j$等距的点构成的轨迹，称为bisector，所有的bisector是$\mathbb{R}^d$的仿射子空间（二维情况下就是线，三维情况下是面）。我们也可以通过bisector定义出*Voronoi cell*：通过bisector相交得到的封闭的半空间，这意味着*Voronoi cell*是凸的，因为凸集的交仍旧是凸集。



## 一些问题

个人在调用[tetgen](https://pypi.org/project/pytetgen/)进行四面体化时，发生模型略大则程序崩溃的问题



## 相关阅读

[Deep Marching Tetrahedra: a Hybrid Representation for High-Resolution 3D Shape Synthesis (nv-tlabs.github.io)](https://nv-tlabs.github.io/DMTet/)

[Tetra-NeRF: Representing Neural Radiance Fields Using Tetrahedra (jkulhanek.com)](https://jkulhanek.com/tetra-nerf/)














## Introduction
### Traditional  method :
- Translation-base :Tans Series such such as TransE,D,H,R
- using external  information such as 
	- entity types
	- textual description 
	- logic rules 

Ths  disadvantages of tradition  method: **ignore to distinguish between  concepts and instances**,which lead to drawbacks:
- **insufficient concept representation**:most of methods encode both concepts and instances as vectors ,which cannot explicitly represent the difference between concepts and instances 
- lah hahitivity of both isA relations:`instance of` and `sub class of` (generally known as isA) are two special relations in knowledge  graph .
### motivation 
**hierarchical concepts**:concepts  in people's mind iare organized hierarchically.So instances should be close to concepts that they belong to.
In TransC,concern 2are encoded as a sphere and instances as vectors in the same semantic space and relative position are employed to model the relations between concepts and instances .

### Symbols and Concepts
- Relations set $\mathcal{R}=\{r_e,r_c\}\cup\mathcal{R}_l$,where $R_{l}$  is the realtions between instances ,and $r_e$ is an `instanceOf` relation ,$r_c$ is an `subClassOf` relation.

- **InstanceOf triples set**$$\mathcal{S_{e}} =\{(i,r_{e} ,c)_{k}\}^{n_{e}}_{k=1},$$
	where $i \in \mathcal{I}, c \in C, n_{e}$ is the size of $S_{e}$

- **SubClassOf triple set** $$\mathcal{S_{c}}=\{(c_{i},r_{c},c_{j})_{k}\}^{n_{c}}_{k=1},$$
- **Relational Triple**:$$\mathcal{S_{l}}=\{h,r,t\}_{k=1}^{n_{l}}$$
	where $h,t \in \mathcal{I}$, $r \in \mathcal{R_{l}}$.

- **Concepts**:For $c \in \mathcal{C}$, we learn a sphere $s(\mathbf{p},m)$,where $\mathbf{p}$ is the sphere center and $m$ denote redius.
- **Transitivity**: 
	- `InstanceOf-subClassOf` transitivity relations can be shown as follow Equations$$(i,r_{e},c_{1})\in S_{e} \wedge (c_{1},r_{c},c_{2})\in S_{c} \rightarrow (i,r_{e},r_{2})\in S_{e}$$
	- `SubClassOf-subClassOf` transitivity can be represented:$$(c_{1},r_{c},c_{2})\in S_{c} \wedge (c_{2},r_{c},c_{3})\in S_{c}\to (c_{1},r_{c},c_{3})\in S_{c}$$
## Method
the author proposed new loss fuctions to measure the relative position  in embedding space,and then jointly the representation of concepts ,instances and relations based-on the translation model.
![[Pasted image 20240331111154.png]]
- **InstanceOf Triples Representations**:for `InstanceOf` triples $(i,r_e,s)$ ,if true ,$\mathbf{i}$ should be inside the sphere  $s$ to represent the `instanceOf`relation between them.So the loss function is defined as $$f_{e}(i,c)=\Vert \mathbf{i}-\mathbf{p}\Vert_{2}-m$$
- **SubOf Triples Representation** :for the triples $(c_{i},r_{c},c_{j})$, concepts $c_{i},c_{j}$ are encoded as sphere $s_{i}(p_{i},m_{i})$ . If $c_i$ is sub class of $c_{j}$ ,then the centers of them should be close .But there are lots of relative positone between sphere $s_{i}$ and $s_{j}$.So  the loss function is defined as$$\begin{align}
&d=\Vert p_{i}-p_{j}\Vert_{2} & (a)\\
& f_c(c_i,c_j)=||\mathbf{p}_i-\mathbf{p}_j||_2+m_i-m_{j} &\qquad(b) \\
& f_c(c_i,c_j)=||\mathbf{p}_i-\mathbf{p}_j||_2+m_i-m_{j} &\qquad(c)  \\
&f_c(c_i,c_j)=m_i-m_{j} &(d)
\end{align}$$
- 
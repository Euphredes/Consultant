

           CONSULTANT 
           A FORK IN THE ROAD 


           EUPHREDES 
           AN ALEXANDRIAN SERVANT 


           A BOOK FOR A HEIST 


           Publishing 

Licensing: Content in this text file 
           is licensed under a Creative 
           Commons Attribution 4.0 
           International license. 
Author: Nick Morris. 
Age: 33 Years Old. 
Release: Thursday, September 17th 2026. 
Location: Cherry Hill, NJ. 

           ---------- 
           Consultant 
           ---------- 

           Contents 

Introduction 
Layout 
Flow 
Evasion 
Inventory 

           Introduction 
           ------------ 

As an industrial engineer, walking the 
value stream on the groundfloor of 
thirty-six counties, I have determined 
that the information flow of media does 
not sufficiently trigger material flow 
to people, places, and things so that 
they survive and thrive. 

When I think like an industrialist, I 
see a large scale facilities planning 
problem for each village, town, and 
city; and I see a small scale 
neighboring problem for each block 
within a place. 

When I think inside of the box, I see 
that I must be a risk-averse traveler, 
and do inventory management within 
tight limits. 










           Macroscale 

VLIII. 
# Stage.mod 
#            Facilities Planning 

# this model is intended to minimize 
# the total cost of investing in a 
# set of work places, and 
# investing in a set of disposal 
# places, to supply a set of 
# people, and dispose all waste. 

# ---- define set(s) ---- 

set N;                                              # set of all nodes (ie. consumers, facilities, wastes) 
set A := N cross N;                                 # set of arcs 
set M;                                              # set of materials (ie. product, waste) 
set C;                                              # set of consumers 
set F;                                              # set of facilities 
set W;                                              # set of wastes 

# ---- define parameter(s) ---- 

param net{N,M} default 0;                           # net flow of material at a node 
param d{(i,j) in A} default 0;                      # arc distance 
param cap{(i,j) in A} default 0;                    # arc material flow capacity 
param seta{(i,j) in A} := 500 + (0.30 * d[I,j]);    # cost to use (ie. setup) an arc 
param setf{F};                                      # cost to setup a facility 
param setw{W};                                      # cost to setup a waste 
param con{F};                                       # conversion constant (ie. how much waste is produced for every unit of product) 
param c{M};                                         # material travel unit cost 

# ---- define variable(s) ---- 

var openf{F} binary;                                # a facility is/isnt setup 
var openw{W} binary;                                # a waste is/isnt setup 
var opena{(i,j) in A} binary;                       # an arc is/isnt setup 
var x{(i,j) in A, M} >= 0;                          # material flow on an arc 

# ---- define objective function(s) ---- 

minimize Cost: sum{i in F}(setf[i] * openf[i]) + sum{i in W}(setw[i] * openw[i]) + sum{(i,j) in A: i < 11 and j < 11}(seta[i,j] * opena[i,j]) + sum{(i,j) in A, k in M}(c[k] * d[i,j] * x[i,j,k]); 

# ---- define constraint(s) ---- 

s.t. Demand{i in C, k in M}: sum{(n,i) in A}(x[n,i,k]) - sum{(i,j) in A}(x[i,j,k]) = net[i,k]; 
s.t. Production{i in F, k in M}: sum{(n,i) in A}(x[n,i,k]) - sum{(i,j) in A}(x[i,j,k]) >= net[i,k] * openf[i]; 
s.t. Disposal{i in W, k in M}: sum{(n,i) in A}(x[n,i,k]) - sum{(i,j) in A}(x[i,j,k]) <= net[i,k] * openw[i]; 
s.t. Capacity{(i,j) in A}: sum{k in M}(x[i,j,k]) <= cap[i,j] * opena[i,j]; 
s.t. Conversion{i in F}: sum{(i,j) in A}(x[i,j,2]) = sum{(i,j) in A}(con[i] * x[i,j,1]); 

# ---- define assumption(s) ---- 

# there is a (500 + 0.30*model.d[i,j]) 
# usage cost from the perspective of 
# the arcs, not the facilities.. so 
# if two facilities use one arc, 
# there is one usage cost not two 
# usage costs for that particular arc. 



























           Microscale 

IL. 
# Flow.mod 
#            B-Matching Model 

# this model is intended to minimize 
# the distance between separate work 
# places such that information flow 
# actually triggers the material flow 
# that is necessary for the people 
# and things of each work place to 
# survive and sustain. 

# ---- define set(s) ----

param num;                                                                                                      # number of Dept. 
set D:=0..num;                                                                                                  # set of Dept. 

# ---- define parameter(s) ----

param l{i in D};                                                                                                # length dimension of i 
param h{i in D};                                                                                                # height dimension of i 
param b{i in D}:= 2*(l[i]+h[i]);                                                                                # perimeter of i 
param f{i in D,j in D:i<j} default 0;                                                                           # flow between Dept i & j 
param lb{i in D, j in D:i<j} default 0;                                                                         # lower bound edge contact between i & j 
param ub{i in D, j in D:i<j}:= if(i==0) then(b[j] - min(l[j],h[j])) else min(max(l[i],h[i]),max(l[j],h[j]));    # upper bound edge contact between i & j 
param w{i in D,j in D:i<j}:= if ub[i,j]>0 then f[i,j]/ub[i,j] else 0;                                           # weighted adjacency between i & j 

# ---- define variable(s) ---- 

var x{i in D, j in D:i<j} >=lb[i,j], <=ub[i,j], integer;                                                        # edge contact between i & j 

# ---- define objective function(s) ---- 

maximize Adjacency: sum{i in D, j in D:i<j}(w[i,j]*x[i,j]); 

# ---- define constraint(s) ---- 

s.t. Edge{i in D}: sum{j in D:i<j}(x[i,j]) + sum{j in D:i>j}(x[j,i]) = b[i]; 















           Risk-averse 

param n := 11;		# number of nodes per row/column
param s := 11;		# number of subtours for slitherlink 1
# param s := 3;		# number of subtours for slitherlink 2

set N := 1..(n * n);		# nodes
set A within N cross N;		# arcs
set G within N cross N cross N cross N;		# grids
set K within N cross N;		# known arcs
set SF := 1..s;		# subtour family
set S{SF} within N cross N;		# subtour members

param d{(i, j, k, l) in G};		# demand of a grid
param b{i in N} default 0;		# net flow of at a node

var x{(i, j) in A} binary;		# do or don't use an arc

minimize Arcs: sum{(i, j) in A}(x[i, j]);		# minimize arcs used
s.t. Demand{(i, j, k, l) in G}: (x[i, j] + x[j, i]) + (x[l, k] + x[k, l]) + (x[i, l] + x[l, i]) + (x[j, k] + x[k, j]) = d[i, j, k, l];		# satisfy the demand of all grids
s.t. NetFlow{j in N}: sum{(i, j) in A}(x[i, j]) - sum{(j, k) in A}(x[j, k]) = b[j];		# satisfy the netflow at all nodes
s.t. OneWay{(i, j) in A}: x[i, j] + x[j, i] <= 1;		# at most, one direction of an arc can be used 
s.t. OneIn{j in N}: sum{(i, j) in A}(x[i, j]) <= 1;		# there can only be one inbound arc to a node
s.t. OneOut{j in N}: sum{(j, k) in A}(x[j, k]) <= 1;		# there can only be one outbound arc to a node
s.t. Cuts{(i, j) in K}: x[i, j] + x[j, i] = 1;		# satisfy all known arcs
s.t. Subtours{k in SF}: sum{(i, j) in S[k]}(x[i, j] + x[j, i]) <= card(S[k]) - 1;		# break known subtours













           Inventory 

max sum{i in Items}(value[i] * count[i]) 
s.t. sum{i in Items}(weight[i] * count[i]) <= Capacity 














           ***** 
            *** 
             * 

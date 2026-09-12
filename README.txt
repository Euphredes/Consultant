

           CONSULTANT 
           A FORK IN THE ROAD 


           Publishing 

Licensing: Content in this text file 
           is licensed under a Creative 
           Commons Attribution 4.0 
           International license. 
Author: Nick Morris. 
Release: Saturday, September 12th 2026. 
Location: Philadelphia, PA. 

           ---------- 
           Consultant 
           ---------- 

           Claimer 

A thought happens when .it. wants to, 
not when I want it. The closest 
experience I'm abused by daily, 
thoughts, are a stranger. I do not 
know the who of my thinking. This 
strikes fear into my authenticity. 
I'll use chanting or humming to cope: 
to overload my mind until some 
reaction within me, that is strong 
enough, breaks thorugh my self-imposed 
monotony. That reaction is a choice 
for me to make or not. I feel like a 
gambler with a stranger, who I've 
been closest to, gambling over my 
life and death. This monster is the 
famous, owld .it. 

When a person dies, all that one goes 
to the grave with is just one's own 
stories: that is the person's bounty. 

           Contents 

Introduction 
Layout 
Flow 
Evasion 
Inventory 

           Introduction 
           ------------ 

           Review 

What is it? 
Who is it for? 
Where has it been? 
Where is it going? 
How is it done in two parts? 

           Storyline 

Opponent 
 Maslow's Pyramid 
Game 
 Heist 
Monster 
 Penal Colony 
Outward 
 Physical Adventure 
Inward 
 Obstacle Course 

           Approach 

Problem 
 Slitherlink Knapsack 
Sets 
 Destinations 
 Traps 
 Items 
 Storages 
Solver 
 Greedy 

           Responsibilities 

Mentally 
 Silence 
Physically 
 Momentum 
Spiritually 
 Focus 

           * * * 




















           Consultation 
           ------------ 

VLII. 
As an industrial engineer, walking the 
value stream on the ground-floor of 
thirty-six counties, I have determined 
that the information flow of media does 
not sufficiently trigger material flow 
to people, places, and things so that 
they survive and thrive. When I think 
like an industrialist, I see a large 
scale facilities planning problem for 
each village, town, and city; and I 
see a small scale neighboring problem 
for each block within a place. As a 
consultant, the following two 
operational models serve to test the 
strength of macroeconomics and 
microeconomics. 

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





















           ***** 
            *** 
             * 

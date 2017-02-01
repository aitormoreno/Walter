# Kinematics

Kinematics is about computation of the tool-centre-point (''TCP'') out of joint angles and vice versa. First is simple, latter is more tricky, but lets see later on.
But before starting any kinematics, it is necessary to define all coordinate systems.

<img width="400px" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image027.png"/>

The most important design decision is to let the three upper axis’ intersect in one point, the so-call wrist-center-point (WCP). This decision makes the computation of the inverse kinematic solvable without numeric approaches.

The picture shows the used coordinate systems in the default position of the bot, having all angles at 0°, starting from the base (angle0) and ending with the coordinate system of the hand (angle<sub>6</sub>). For convenience the forearm (angle<sub>1</sub>) adds +90° to the real angle in order to have the base position at 0°of the bot, although the illustrated actually is -90°. The coordinate systems have been are arranged according to the Denavit Hardenberg convention, which is:

* The angle rotates around the z-axis
* The z-axis points on the direction of the next joint
* The transformation from anglei to anglei+1 is given via 
   1. rotating around the x-axis by α
   1. translation along the x-axis by α
   1. translation along the z-axis by *d*, and
   1. rotation around the z-axis by θ

So, the Denavit Hardenberg parameters are:

| Joint      | a[°] | a[mm]            | d[mm]         |
|----------  | -----| ---------------- | ------------- |
| Hip        | -90° | 0                | d<sub>0</sub> |
| Upperarm   | 0    | a<sub>1</sub>    | 0             |
| Forearm    | -90° | 0                | 0             |
| Elbow      | 90°  | 0                | d<sub>3</sub> |
| Wrist      | -90° | 0                | 0             |
| Hand       | 0    | 0                | d<sub>5</sub> |

The general definition of a Denavit Hardenberg (DH) transformation is

<img  src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image029.png"/>

which is a homogeneous matrix with two rotations (x,z) and two translations (x,z).

Combined with the DH parameters, the following DH matrixes define the transformation from one joint to its successor:

<img  align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image030.png"/>

<img  align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image031.png"/>

<img   src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image032.png"/>

<img  align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image033.png"/>

<img  align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image034.png"/>

<img src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image035.png"/>

## Forward Kinematics

With the DH transformation matrixes at hand, computation of the bot’s pose out of the joint angles is straight forward. The matrix representing the gripper’s pose <img align="center"  src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image036.png"/> is 

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image037.png"/> 

By multiplying the transformation matrix with the origin (as homogeneous vector), we get the absolute coordinates of the tool centre point in world coordinate system (i.e. relative to the bot’s base).

<img  src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image038.png"/>

The orientation in terms of roll/nick/yaw of the tool centre point can be derived out of <img align="center"  src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image036.png"/>by taking the part representing the rotation matrix (<img align="center"  src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image040.png"/>). ([Wikipedia Roll/Nick/Yaw](https://de.wikipedia.org/wiki/Roll-Nick-Gier-Winkel#Berechnung_aus_Rotationsmatrix) )

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image041.png"/>

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image042.png"/>  
<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image043.png"/>  
<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image044.png"/>  

Due to singularities, we need to consider <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image045.png"/>
and use
	
<img align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image046.png"/>
<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image047.png"/>

instead. if <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image048.png"/> we get

<img align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image046.png"/>
<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image049.png"/>

Note: Unfortunately, the gripper’s coordinate system is not appropriate for human interaction, since the default position as illustrated in the <img align="center" src=":https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image027.png|Coordinate Systems"/> not nick/roll/yaw=(0,0,0). So, in the Trajectory Visualizer it is handy to rotate the gripper matrix such that the default position becomes . The according rotation matrix represents a rotation of -90° along x,y, and z, done by the rotation matrix

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image051.png"/>

In the following equations, this is not considered, since it is for convenience in the UI only.

## Inverse Kinematics 
Inverse kinematics denotes the computation of all joint angles out of the tool-centre-point’s position and orientation. In general it is hard to give non-numeric solution, in this case it is possible since the upper three joint angles point to one point, the so-called wrist centre point.

We know the TCP’s position and orientation in terms of roll, nick, yaw (γ,β,α).

<img align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image053.png"/>

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image054.png"/>

First, we need to compute the wrist-centre-point out the tool-centre-point. This is possible by taking the TCP and moving it back along the TCP’s orientation by the hand length. For doing so, we need the transformation matrix from the base to the last joint <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image036.png"/> 
which we can derive out of the TCP’s position and orientation.

To build the transformation matrix <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image036.png"/> we need the rotation matrix defining the orientation of the TCP. This is given by multiplying the rotation matrixes for all axis (γ,β,α) which gives <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image056.png"/>.

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image057.png"/>

Now we can denote the transformation matrix of the TCP

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image058.png"/>

From the TCP’s perspective, WCP is just translated by d<sub>5</sub>

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image059.png"/>

Furthermore, <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image060.png"/>, so we get the WCP by

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image061.png"/>

in world coordinates.

Having a top view on the robot shows how to compute the first angle:

<div align="center"><img width="600px" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image062.png"/></div>

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image064.png"/>

Actually, this angle exists in two variants: if the bot looks backwards, another valid solution is

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image065.png"/>

Thanks to the design having a wrist-centre-point where the axes of the three upper actuators intersect, the next two angles can be computed by a triangle:

<div align="center"><img align="center" width="400px" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image027.png"/></div>

Again, there are two solutions representing, one configuration corresponds with a natural pose of the elbow, solution II is a rather unhealthy position:

<div align="center"><img width="600px" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image069.png"/></div>

a and b is given by the length of the actuators a<sub>1</sub> und d<sub>3</sub>. So, cosine sentence yields the angles α and γ.

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image072.png"/>

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image073.png"/>
	
Finally, we get

<img align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image074.png"/>
<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image075.png"/>	

and – as second solution -

<img align="left" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image076.png"/>
<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image077.png"/>	
	

The upper angles *θ<sub>4</sub>*, *θ<sub>5</sub>*, *θ<sub>5</sub>* can be obtained by considering the chain of transformation matrixes. With

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image078.png"/>	

we get

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image079.png"/>	


Besides the annoying multiplication <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image080.png"/> we only need to consider the rotation part of the homogenous matrixes, translation is no more relevant.

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image081.png"/>	

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image036.png"/> - and therefore the rotation part <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image040.png"/> - is already known resp. can be obtained out of the given angles *θ<sub>0</sub>*, *θ<sub>1</sub>*, *θ<sub>2</sub>* by
	
<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image086.png"/>	

By equalizing the previous two equations we get the required angles. First angle that seems to be easily computable is *θ<sub>4</sub>*:

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image088.png"/>	

having two solutions

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image089.png"/>	

For *θ<sub>3</sub>* there is no easy matrix element, but we can combine

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image090.png"/>	
<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image091.png"/>		

to

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image092.png"/>		

which ends up in

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image093.png"/>		

again having two solutions depending on *θ<sub>4</sub>*. Same is done on *θ<sub>5</sub>*:

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image094.png"/>		


If *θ<sub>4</sub>=0*, we have an infinite number of solutions θ<sub>3</sub> and θ<sub>5</sub> (gimbal lock). In that case, we consider  <img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image095.png"/> :		

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image096.png"/>.		

Since we know the trigonometric addition theorem from school

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image097.png"/>		

we get

<img align="center" src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image098.png"/>		

We are free to choose *θ<sub>3</sub>* and take the bot’s current angle *θ<sub>3</sub>* to not move unnecessarily.

<img src="https://github.com/jochenalt/Walter/blob/master/doc/images/wiki/image101.png"/>		
 
In the end, we get eight solutions by combining the possible pose configurations of  *θ<sub>0</sub>*(forward/backward), *θ<sub>1</sub>* and *θ<sub>2</sub>*(triangle flip), and θ<sub>4</sub>(hand orientation turn).

The “right” solution is chosen by taking the one that differs the least from the current bot’s joint angles.
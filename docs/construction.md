Inverse kinematics, i.e. computation of joint angles out of the gripper’s position can be hard. If the design is too playful, nearly impossible complex. So, it is a good idea to ease the maths by having the upper three axes joining in one point. Later in the chapter Kinematics we will see that with that limitation kinematics becomes possible without having a mathematician at hand (still not easy, but feasible). 

But, lets start with the gripper. 

## Gripper

Due to space limitations, it seems to be appropriate to use a servo for the gripper. I used a standard principle where one lever is driven, in the other lever repeats the same movement by a gear wheel. The servo is hidden in a small box, it is a HerkuleX Robot Servo with 0.12 Nm.

<img width="500px" src="https://github.com/jochenalt/Walter/blob/master/docs/images/cad-gripper.png" >

## Wrist

The wrist is also designed with the same servo. A small flange connects the wrist with the two halves of the gripper housing, the hole hides the cable of the gripper servo. Worth to mention is that the bearings of the wrist have a different size, since the servo looks through the inner hole of the bigger bearing. On the other side, in the middle of the smaller bearing there is the hole for the magnet used by the magnetic encoder of the forearm. The cable of both servos (gripper and wrist) is going through the wrist underneath the servo.

<img align width="800px" src="https://github.com/jochenalt/Walter/blob/master/docs/images/cad-wrist.png" >

## Forearm

The forearm is more complex, the wrist ist driven with a belt drive and a stepper motor with an gear ratio of 1:4. The belt drive is hold tight with a spanner. At the other side of the wrist, the magnetic encoder is located. All cables are meeting in the space at the bottom of the forearm, and going down through the hole of the disk.

<img align width="800px" src="https://github.com/jochenalt/Walter/blob/master/docs/images/cad-forearm.png" >

## Elbow

The elbow consumed most time for design, it is a two stage belt-drive with a ratio of 1:7 and a stepper with 17Ncm. The flange in the middle is the connection to the forearm. It is mounted with two  bigger bearings and has a cable channel with space for a self made cable drag chain that allows to have the cables inside. This was difficult since the centre of the flange was already occupied by an magnetic encoder.

<img align width="600px" src="https://github.com/jochenalt/Walter/blob/master/docs/images/cad-elbow.png" >

## Upperarm

The upperarm contains a strong stepper with 1.9Nm and a two-staged gear with a ratio of 1:14. On the left side a magnetic encoder samples the angle of the ellbow, above the encoder the cable channel is located. The cables are going down through a hole in the middle block down to the left side of the bttom part. The ride side contains the belt to the elbow. All belts are tighened with a clamp that can be adjusted from outside.

<img align width="800px" src="https://github.com/jochenalt/Walter/blob/master/docs/images/cad-upperarm.png" >

Continue reading with [Speed Profile](https://github.com/jochenalt/Walter/wiki/Speed-Profile).

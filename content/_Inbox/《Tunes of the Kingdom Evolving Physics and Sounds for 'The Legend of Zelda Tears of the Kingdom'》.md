---
title: 《Tunes of the Kingdom Evolving Physics and Sounds for 'The Legend of Zelda Tears of the Kingdom'》
type: web-bookmark
date: 2024-04-26 14:45
publish: false
archive: 
tags:
---
原文：[GDC Vault - Tunes of the Kingdom: Evolving Physics and Sounds for 'The Legend of Zelda: Tears of the Kingdom'](https://gdcvault.com/play/1034667/Tunes-of-the-Kingdom-Evolving)

---
## Technical Director Takuhiro Dohta

Sticking Objects Together

Two themes of 荒野之息: 
- A vast ansd seamless Hyrule (but not fun enough)
- Multiplacative Gameplay (so they come out with it)
	- (It has been mentioned in GDC 2017: breaking conventions with the legend of zelda)
	- Add game objects are connected by the same world rules
	- all can interact on a rule basis eg falling boulders damage enemies
	- design eah are based on these rules

**Simply making the JM field fast DOESN'T necessarily make the game fun.**

Two themes of 王国之泪
- Expand Hyrule
	- Go upwards, downwards, even inside (I want to dig hole!  -Producer)
- Enhance Multiplacative Gameplay

How to enhance?
Combine existing objects
Freely sticking objects together
Ultrahand and fuse: even you don't know what they are use for at first, you can still come up with new way to make them useful

**sticking to things together produces something new.**

so what will happend if physics + sound?

---

## Lead Physics Programmer Takahiro Takayama

bring enhanced multiplicative gameplay to life

1. an entirely physicd-driven world
+
2. unique interactions occur without dedicated implementation

### How 1?
Physics-driven: dynamic rigid body +constraints. object have mass and moment of inertia and be controlled by velocity and acceleration
non-physics-driven: kinematic righd body. focibly moving a rigid body at a velocity calculated from the animation. it can be easily implement, but it's infinite mass and not influenced by physics-driven objects.
solution on non-physics-driven: physics-driven cogs
![[Pasted image 20240426153529.png]]
all calculation are physics-base so the problem are resolved.
they removed all the non-physics-driven object. eg. kinematic gate (it's physics-driven gate now because if you put a physics object under a kinematic controlled gate, the physic calculation will fall apart)
![[Pasted image 20240426154239.png]]

Summary: Everything being physics-driven is necessary to make multiplicative game play a reality

### How 2?
A world where anything can happen depending on player's imagination
A system that allows for unique interactions to occur without any dedicated implementation
dedicated implementation: a dedicated program for each and every interaction 
- instead of implement a vehince, implement a system that can build a vehince and provide these objects
- no dedicated "paddle boat" program

some components that trigger these interactions
- wheel ![[Pasted image 20240426160123.png]]
- chain: a series of capsule shaped rigid body, connected via ball socket constraints ![[Pasted image 20240426160204.png]]
- water: use the projected area of the direction of velocity to calculate the resistance. A easier way: simply apply velocity decay in all directions. But for raft and boat to have a more convincing field and movement to them, it's needed to changes in velocity based on the contact surface.  ![[Pasted image 20240426160326.png]] ![[Pasted image 20240426160711.png]]
- portable pod: there is no guarantee that the pod will be placed on a flat surface, meaning the dish could be spill out. The solution is to add telescopic legs to the pod ![[Pasted image 20240426165302.png]] but in the end they decided to put a joint at the bottom of the pot for stability. Due to this joint, many players use it to build vehice. ![[Pasted image 20240426165359.png]]

there is a last but not lease puzzle piece: 

### Working together as a team
team effort:
- work with artist and designer to set up the physics parameters correctly. Sometimes automatic calculations not enough; automatic calculations result in being too heavy
- Art ⇔ physics
- game design ⇔ physics
- some point out the suspention to the wheel, making wheels slight move up and down. It gives a great improvement on game play. 

Summary: working together with game designers and artists and the sound team who understanding the vision is essential in bringing this vast world to life

---

## Lead Sound Engineer Junya Osada

All about sound design. Not relative to me, pass
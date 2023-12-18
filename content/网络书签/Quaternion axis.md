#GameDev 
[Freya the stray on X: "btw - quaternion axis swapping is pretty cheap! swapping XY (xyzw order): s*( q.w - q.z ), s*( q.w + q.z ), s*( q.x - q.y ), s*(-q.x - q.y ) where s = 1/√(2) here's my code for all three swaps, and per-axis 180° rotations, which are just swizzles: https://t.co/muiUaPeVPr https://t.co/KFdVIKgjAQ" / X (twitter.com)](https://twitter.com/FreyaHolmer/status/1706416798562693498)
btw - quaternion axis swapping is pretty cheap! 
swapping XY (xyzw order): 
s*( q.w - q.z ), 
s*( q.w + q.z ), 
s*( q.x - q.y ), 
s*(-q.x - q.y ) 
where s = 1/√(2) 

here's my code for all three swaps, and per-axis 180° rotations, which are just swizzles: [https://github.com/FreyaHolmer/Mathfs/blob/master/Runtime/Extensions.cs#L184-L230](https://t.co/muiUaPeVPr)

![[Pasted image 20231107124742.png]] a diagram showing transform gizmos, and the result of swapping their YZ, ZX, and XY axes

someone in my discord asked how to do Quaternion.LookRotation(), but where the up vector is fixed, and the forward vector is only used as a loose reference, which is the opposite of how it usually works with this, you can do: Quaternion.LookRotation( up, forward ).SwapYZ()
anyway, you can also implement your own Quaternion.LookRotation that behaves that way and it'd probably be even cheaper, just thought it was a cute lil trick c:

---
title: "[Unreal Engine] Dash Mechanic | Character Movement Component In-Depth"
tags:
  - video
  - in-progress
---
![](https://www.youtube.com/watch?v=rg9id_ex_DY)
## Notes
- Using `Acceleration` instead of `ServerRPC` `@7:30`
	- In [[UCharacterMovementComponent]], you can use `Acceleration` in place of using a ServerRPC to send user input.
- Add vertical impulse to avoid friction applying immediately `@9:09`
- Override velocity instead of adding to it to give a more arcadey vibe. Adding will be more 'physics' based `@9:30`
- `Launch()` could be an alternative, but less control `@12:30`
	- `Launch()` can be called from a 'non-movement safe context', like blueprints.
	- `Launch()` will prepare a pending launch, and won't apply till the next frame.
- Using `AddImpulse()` can cause desync between client and server.


# fabric-behaviour-domain

What a body does, and how it moves. A **domain** is a packing of planes and transport layers,
and a ring forces co-location, so this is the pair that has to run together.

| plane | decides | rate |
| --- | --- | --- |
| [`fabric-taskweft-plane`](https://github.com/v-sekai-multiplayer-fabric/fabric-taskweft-plane) | **what.** An HTN plan: cross the room, sit on that bench, greet the arrival. | when the world changes, so seconds |
| [`fabric-motion-plane`](https://github.com/v-sekai-multiplayer-fabric/fabric-motion-plane) | **how.** ARDY turns each intent into a reference pose. | per body per tick |

## Why they are one domain

The planner reads the ring to know where bodies and props are, and publishes intents onto it.
The motion plane reads those intents and publishes poses. Both touch the same shared memory,
and **iceoryx2 is shared memory**, so neither can be anywhere else.

## Why they are two planes

They run three orders of magnitude apart. A plan changes in seconds and a pose changes every
tick, and planning is a search with no bounded time. Putting a search on the tick path would
make the tick as slow as the worst plan.

So the boundary between them is a rate boundary, and the ring is what makes a rate boundary
cheap: the planner publishes when it has something to say, and the motion plane reads whatever
is current without waiting for it.

## What this domain does not contain

**The physics.** An intent is not a pose and a pose is not a body. A tracker turns a reference
pose into actuator commands against gravity and contact, and it lives with the crowd plane in
the zone domain. This domain produces what a body should be doing; something else makes a body
actually do it.

## State

**Not built.** Both planes hold their decisions, and neither has a harness subtree or a ring
subscription yet. This repository holds the manifest that will run them together.

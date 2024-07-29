# Turn Formula

The player is a rectangle object that may target $4 + (2 + 2)$ different target rotation degrees by applying gradual changes in the current rotation per frame.

* NORTH = 0
* SOUTH = 180
* WEST = 360 - 90
* EAST = 90
* NORTHWEST = 360 - 45
* NORTHEAST = 45
* SOUTHWEST = 180 + 45
* SOUTHEAST = 180 - 45

Given $\text{current rotation} = 0$ and $\text{target rotation} = 180$, the rotation increments gradually by 5 (turn speed) until 180:

- Frame 1 = $\text{current rotation} = 5$
- Frame 4 = $\text{current rotation} = 20$
- Frame (last) = $\text{current rotation} = \text{target rotation} = 180$

Given $\text{current rotation} = 0$ and $\text{target rotation} = 360 - 90 = 270$, the rotation decrements gradually by 5 (turn speed) until -90 and is then converted to $360 - 90 = 270$:

- Frame 1 = $\text{current rotation} = -5$
- Frame 4 = $\text{current rotation} = -20$
- Frame (last) = $\text{current rotation} = \text{target rotation} = 360 - 90 = 270$

## Formula

$$
lock(a) = \text{if $a \ge 0$, then $a$; otherwise $360 - (-a \mod 360)$}
$$

$$
a = \text{current degrees}
$$

$$
b = \text{target degrees}
$$

$$
ab = a - b + (\text{if $a - b \gt b - a$, then 0; otherwise 360})
$$

$$
ba = b - a + (\text{if $b - a \gt a - b$, then 0; otherwise 360})
$$

$$
\text{turn speed} = \text{(your number)}
$$

$$
x = lock(a + \text{(if $ab < ba$ then $-\text{turn speed}$; otherwise $+\text{turn speed}$)})
$$

$$
\text{current rotation} = \text{if $x \gt b - (\text{turn speed})$ and $x \lt b + (\text{turn speed})$ then $b$; otherwise $x$}
$$

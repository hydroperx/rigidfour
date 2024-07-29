# Turn Formula

The player is a rectangle object that may target $4 + (2 + 2)$ different target rotation degrees by applying gradual increments in the current rotation per frame.

Follow the criteria that the angles ($\text{current rotation}, \text{target rotation}$) are always in the interval $[0, 360)$.

Given $\text{current rotation} = 0$ and $\text{target rotation} = 180$, the current rotation increments gradually by 5 until 180:

- Frame 1 = $\text{current rotation} = 5$
- Frame 4 = $\text{current rotation} = 20$
- Frame (last) = $\text{current rotation} = \text{target rotation} = 180$

Given $\text{current rotation} = 0$ and $\text{target rotation} = 360 - 90 = 270$, the current rotation decrements gradually by 5 until -90 and is then converted to $360 - 90 = 270$:

- Frame 1 = $\text{current rotation} = -5$
- Frame 4 = $\text{current rotation} = -20$
- Frame (last) = $\text{current rotation} = \text{target rotation} = 360 - 90 = 270$

Here is the lock formula to fix the degrees into $[0, 360)$:

$$
lock(a) = \text{if $a \ge 0$, then $a$; otherwise $360 - (-a \mod 360)$}
$$

For defining the $\text{current rotation}$ of that rectangle object in a frame given $a = \text{previous rotation}$ and $b = \text{target rotation}$:

$$
ab = a - b + (\text{if $a - b \gt b - a$, then 0; otherwise 360})
$$

$$
ba = b - a + (\text{if $b - a \gt a - b$, then 0; otherwise 360})
$$

Where $\text{turn speed} = 5$

If $ab < ba$ then

$$
x = lock(a - \text{turn speed})
$$

$$
\text{current rotation} = \text{if $x \lt b$, then $b$; otherwise $x$}
$$

otherwise

$$
x = lock(a + \text{turn speed})
$$

$$
\text{current rotation} = \text{if $x \gt b$, then $b$; otherwise $x$}
$$

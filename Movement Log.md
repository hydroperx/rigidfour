# Movement Log

## 29-07-2024 GDevelop

```js
const NORTH_ROTATION = 0;
const NORTHWEST_ROTATION = 315;
const NORTHEAST_ROTATION = 45;
const SOUTH_ROTATION = 180;
const SOUTHWEST_ROTATION = 225;
const SOUTHEAST_ROTATION = 135;
const WEST_ROTATION = 270;
const EAST_ROTATION = 90;
const TURN_SPEED = 5;
const MOVE_SPEED = 7;
const MAX_SPEED = 10;

const player = objects[0];
const player_vars = player.getVariables();

const movingNorth = player_vars.get("MovingNorth").getAsBoolean();
const movingSouth = player_vars.get("MovingSouth").getAsBoolean();
const movingWest = player_vars.get("MovingWest").getAsBoolean();
const movingEast = player_vars.get("MovingEast").getAsBoolean();

// Set target rotation
let b = 0;

if (movingNorth) {
    b = movingWest ? NORTHWEST_ROTATION : movingEast ? NORTHEAST_ROTATION : NORTH_ROTATION;
} else if (movingSouth) {
    b = movingWest ? SOUTHWEST_ROTATION : movingEast ? SOUTHEAST_ROTATION : SOUTH_ROTATION;
} else if (movingWest) {
    b = WEST_ROTATION;
} else if (movingEast) {
    b = EAST_ROTATION;
} else {
    b = player_vars.get("TargetRotation").getAsNumber();
}

const moving = movingNorth || movingSouth || movingWest || movingEast;

// Inertia
if (!moving) {
    if ([NORTH_ROTATION, SOUTH_ROTATION, WEST_ROTATION, EAST_ROTATION].indexOf(b) == -1  && b != 0) {
        b -= 45;
    }
}

player_vars.get("TargetRotation").setNumber(b);

let a = lock360Degrees(player.getAngle());

if (a != b) {
    // Rotate either clockwise or counterclockwise for the
    // shortest route.

    let ab = a - b; /* counterclockwise */
    let ba = b - a; /* clockwise */

    // For the lower delta, add 360.
    if (Math.min(ab, ba) == ab) {
        ab += 360;
    } else {
        ba += 360;
    }

    // Rotate
    a = lock360Degrees(a + (ab < ba ? -TURN_SPEED : +TURN_SPEED));
    a = a > b - TURN_SPEED && a < b + TURN_SPEED ? b : a;
    player.setAngle(a);
}

// Lock degrees to be between 0 and 360.
function lock360Degrees(a) {
    a = a < 0 ? 360 - (-a % 360) : a;
    return a % 360;
}

const force = player.getAverageForce();
force.setMultiplier(0.001);
force.setLength(0.001);

// Moving
if (moving) {
    const b_radians = toRadians(b);
    let vx = Math.sin(b_radians) * MOVE_SPEED;
    let vy = -Math.cos(b_radians) * MOVE_SPEED;
    player.addForce(vx, vy);
}

// Clamp speed
const forceX = force.getX();
const forceY = force.getY();
force.setX(forceX < -MAX_SPEED ? -MAX_SPEED : forceX > +MAX_SPEED ? +MAX_SPEED : forceX);
force.setY(forceY < -MAX_SPEED ? -MAX_SPEED : forceY > +MAX_SPEED ? +MAX_SPEED : forceY);

// Update force
player.updateForces();

function toRadians(a) {
    return a * Math.PI / 180;
}
```

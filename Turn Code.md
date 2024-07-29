# Turn Code

Given the car rigid body, the following GDevelop event code will turn the car either in the clockwise or counterclockwise direction based in the keyboard arrows.

- NORTH is the up arrow; SOUTH is the down arrow
- WEST is the left arrow; EAST is the right arrow

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

const player = objects[0];
const player_vars = player.getVariables();

let a = lock360Degrees(player.getAngle());
let b = player_vars.get("TargetRotation").getAsNumber();

if (a != b) {
    // Rotate either clockwise or counterclockwise for the
    // shortest route.

    let ab = a - b; /* clockwise */
    let ba = b - a; /* counterclockwise */

    // For the lower delta, add 360.
    if (Math.min(ab, ba) == ab) {
        ab += 360;
    } else {
        ba += 360;
    }

    // Rotate
    if (ab < ba) {
        // clockwise
        a += TURN_SPEED;
        a = a > b ? b : a;
    } else {
        // counterclockwise
        a -= TURN_SPEED;
        a = lock360Degrees(a);
        a = a < b ? b : a;
    }

    player.setAngle(a);
}

// Lock degrees to be between 0 and 360.
function lock360Degrees(a) {
    a = a < 0 ? 360 - (-a % 360) : a;
    return a % 360;
}

const movingNorth = player_vars.get("MovingNorth").getAsBoolean();
const movingSouth = player_vars.get("MovingSouth").getAsBoolean();
const movingWest = player_vars.get("MovingWest").getAsBoolean();
const movingEast = player_vars.get("MovingEast").getAsBoolean();

// Set target rotation
if (movingNorth) {
    player_vars.get("TargetRotation").setNumber(movingWest ? NORTHWEST_ROTATION : movingEast ? NORTHEAST_ROTATION : NORTH_ROTATION);
} else if (movingSouth) {
    player_vars.get("TargetRotation").setNumber(movingWest ? SOUTHWEST_ROTATION : movingEast ? SOUTHEAST_ROTATION : SOUTH_ROTATION);
} else if (movingWest) {
    player_vars.get("TargetRotation").setNumber(WEST_ROTATION);
} else if (movingEast) {
    player_vars.get("TargetRotation").setNumber(EAST_ROTATION);
}
```

# Movement Log

## 29-07-2024 GDevelop

https://github.com/hydroperfox/rigidfourdemogdevelop

Thought: I would like that the car would drift 360 degrees by rotating itself to point to the circunference's center.

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
const MOVE_SPEED = 300;
const MAX_SPEED = 500;
const DECEL = 100;

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

/** @type {gdjs.Physics2RuntimeBehavior} */
const physicsBehavior = player.getBehavior("Physics2");

let vx = 0, vy = 0;

// Moving
if (moving) {
    const a_radians = toRadians(a);
    vx = Math.sin(a_radians) * MOVE_SPEED;
    vy = -Math.cos(a_radians) * MOVE_SPEED;
    physicsBehavior.applyForce(vx, vy, player.getX(), player.getY());
}

vx = physicsBehavior.getLinearVelocityX();
vy = physicsBehavior.getLinearVelocityY();

// Deceleration
if (vx < -MOVE_SPEED && !movingWest) {
    vx -= vx / DECEL;
}
if (vx > +MOVE_SPEED && !movingEast) {
    vx -= vx / DECEL;
}
if (vy < -MOVE_SPEED && !movingNorth) {
    vy -= vy / DECEL;
}
if (vy > +MOVE_SPEED && !movingSouth) {
    vy -= vy / DECEL;
}

// Clamp linear velocity
vx = vx < -MAX_SPEED ? -MAX_SPEED : vx > +MAX_SPEED ? +MAX_SPEED : vx;
vy = vy < -MAX_SPEED ? -MAX_SPEED : vy > +MAX_SPEED ? +MAX_SPEED : vy;

physicsBehavior.setLinearVelocityX(vx);
physicsBehavior.setLinearVelocityY(vy);

function toRadians(a) {
    return a * Math.PI / 180;
}
```

## 29-07-2024 Construct 3

https://github.com/hydroperfox/rigidfourdemoc3

Bug: force not working correctly.

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
const MOVE_SPEED = 100;
const MAX_SPEED = 100;

/**
 * @param car {ISpriteInstance}
 */
export function move(car) {
	const movingNorth = car.instVars.MovingNorth;
	const movingSouth = car.instVars.MovingSouth;
	const movingWest = car.instVars.MovingWest;
	const movingEast = car.instVars.MovingEast;
	
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
		b = car.instVars.TargetRotation;
	}
	
	const moving = movingNorth || movingSouth || movingWest || movingEast;

	// Inertia
	if (!moving) {
    	if ([NORTH_ROTATION, SOUTH_ROTATION, WEST_ROTATION, EAST_ROTATION].indexOf(b) == -1  && b != 0) {
        	b -= 45;
    	}
	}
	
	car.instVars.TargetRotation = b;
	
	// Turn the car
	turn(car);
	
	let vx = 0, vy = 0;
	
	// Apply force
	if (moving) {
		const b_radians = toRadians(b);
		vx = Math.sin(b_radians) * MOVE_SPEED;
		vy = -Math.cos(b_radians) * MOVE_SPEED;
		car.behaviors.Physics.applyForce(vx, vy);
	}

	// Clamp speed
	vx = car.behaviors.Physics.getVelocityX();
	vy = car.behaviors.Physics.getVelocityY();
	vx = vx < -MAX_SPEED ? -MAX_SPEED : vx > +MAX_SPEED ? +MAX_SPEED : vx;
	vy = vy < -MAX_SPEED ? -MAX_SPEED : vy > +MAX_SPEED ? +MAX_SPEED : vy;

	// Deceleration
	if (vx < -MOVE_SPEED && !movingWest) {
		vx -= vx / 2;
	}
	if (vx < +MOVE_SPEED && !movingEast) {
		vx += vx / 2;
	}
	/*
	if ((vx < -MOVE_SPEED && !movingWest) || (vx > +MOVE_SPEED && !movingEast)) {
		vx -= vx / 2;
	}
	if ((vy < -MOVE_SPEED && !movingNorth) || (vy > +MOVE_SPEED && !movingSouth)) {
		vy -= vy / 2;
	}
	*/

	car.behaviors.Physics.setVelocity(vx, vy);
}

/**
 * @param car {ISpriteInstance}
 */
function turn(car) {
	let a = lockDegrees(toDegrees(car.angle));
	const b = car.instVars.TargetRotation;

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
		a = lockDegrees(a + (ab < ba ? -TURN_SPEED : +TURN_SPEED));
		a = a > b - TURN_SPEED && a < b + TURN_SPEED ? b : a;
		car.angle = toRadians(a);
	}
}

/**
 * Lock degrees to be between 0 and 360.
 */
function lockDegrees(a) {
    a = a < 0 ? 360 - (-a % 360) : a;
    return a % 360;
}

function toDegrees(a) {
    return a * 180 / Math.PI;
}

function toRadians(a) {
    return a * Math.PI / 180;
}
```

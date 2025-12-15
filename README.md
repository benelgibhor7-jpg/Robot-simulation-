import math

# ------------------ WORLD SETTINGS ------------------

GRID_SIZE = 11
OFFSET = GRID_SIZE // 2

obstacles = [
(7, 0),
(10, 1),
(-1, -1)
]
goal = (4, 2)

def is_obstacle(x, y):
    # Ensure rounded coordinates match obstacles
    return (round(x), round(y)) in obstacles

# ------------------ DRAW MAP ------------------

def draw_map(robot):
    grid = []

    # Iterate from y=5 down to y=-5
    for y in range(5, -6, -1):
        row = ""
        # Iterate from x=-5 up to x=5
        for x in range(-5, 6):
            rx = round(robot.x)
            ry = round(robot.y)

            if (x, y) == (rx, ry):
                row += " R "
            elif (x, y) in obstacles:
                row += " X "
            elif (x, y) == goal:
                row += " G "
            else:
                row += " . "
        grid.append(row)

    print("\n--- WORLD MAP ---")
    for r in grid:
        print(r)
    print("-----------------\n")

# ------------------ ROBOT CLASS ------------------

class Robot:
    # ERROR 1 FIXED: Changed 'init' to '__init__' (double underscore)
    def __init__(self):
        self.x = 0.0
        self.y = 0.0
        self.theta = 0.0
        self.speed = 0.7
        self.turbo_speed = 1.0

    def sense_forward(self, max_distance=5):
        """
        Simulate a distance sensor in front of the robot
        """
        rad = math.radians(self.theta)

        for d in range(1, max_distance + 1):
            check_x = self.x + d * math.cos(rad)
            check_y = self.y + d * math.sin(rad)

            if is_obstacle(check_x, check_y):
                return d  # obstacle detected

        return None  # no obstacle ahead

    def forward(self):
        """Move forward at normal speed"""
        rad = math.radians(self.theta)
        nx = self.x + self.speed * math.cos(rad)
        ny = self.y + self.speed * math.sin(rad)

        if is_obstacle(nx, ny):
            print("⚠️ Collision detected! Can't move forward.")
        else:
            self.x, self.y = nx, ny

    def turbo_forward(self):
        """Move forward at TURBO speed"""
        rad = math.radians(self.theta)
        nx = self.x + self.turbo_speed * math.cos(rad)
        ny = self.y + self.turbo_speed * math.sin(rad)

        if is_obstacle(nx, ny):
            print("⚠️ Collision detected! Can't turbo forward.")
        else:
            self.x, self.y = nx, ny

    def backward(self):
        """Move backward at normal speed"""
        rad = math.radians(self.theta)
        nx = self.x - self.speed * math.cos(rad)
        ny = self.y - self.speed * math.sin(rad)

        if is_obstacle(nx, ny):
            print("⚠️ Collision detected! Can't move backward.")
        else:
            self.x, self.y = nx, ny

    def left(self):
        """Turn left 30 degrees"""
        # ERROR 2 FIXED: Corrected angle update logic
        self.theta = (self.theta + 30) % 360

    def right(self):
        """Turn right 30 degrees"""
        # ERROR 2 FIXED: Corrected angle update logic
        self.theta = (self.theta - 30) % 360

    def status(self):
        """Show robot status"""
        print(f"Position: ({self.x:.2f}, {self.y:.2f})  Direction: {self.theta:.1f}°")

# ------------------ MAIN LOOP ------------------

robot = Robot()

print("Commands: f = forward | b = back | l = left | r = right | t = turbo | s = stop")

while True:
    draw_map(robot)
    robot.status()

    # Check if goal reached
    if (round(robot.x), round(robot.y)) == goal:
        print("🎉 Goal reached!")
        break

    distance = robot.sense_forward()

    if distance:
        print(f"📡 Obstacle detected {distance} units ahead")
    else:
        print("📡 Path ahead is clear")

    cmd = input("Enter command: ").lower()

    if cmd == "f":
        robot.forward()
    elif cmd == "t":
        print("🚀 TURBO MODE ACTIVATED!")
        robot.turbo_forward()
    elif cmd == "b":
        robot.backward()
    elif cmd == "l":
        robot.left()
    elif cmd == "r":
        robot.right()
    elif cmd == "s":
        print("Simulation stopped.")
        # ERROR 3 FIXED: Added 'break' to stop the loop
        break
    else:
        print("Unknown command!")

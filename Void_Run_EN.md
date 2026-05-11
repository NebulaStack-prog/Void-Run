## Part 1. Main Document.

### 1. Title and Basic Information.

• **Name:** Void Run.

• **Purpose:** Project No. 9. Product.

• **Project Phase:** Phase I.

• **Technology Stack:** Python

• **Project Status:** Fully completed.

### 2. Brief Project Description.

• The Void Run project is a parody version of the classic Pacman arcade game, developed in Python using the Pygame and tcod libraries.

• The player controls a yellow hero who collects “cookies” scattered throughout the maze, increasing the score. Ghosts of different colors move through the maze, and collisions with them reduce health. Upon reaching 50 points, gates open, granting access to a new section of the maze in the center. The objective of the game is to collect all 318 cookies and win.

### 3. Clear Project Goals.

• Create a parody version of the classic Pacman game with a unique maze.

• Implement a complete hero movement system with input buffering and wall collisions.

• Implement ghost movement using the A* pathfinding algorithm in a two-dimensional array (tcod library).

• Introduce a hero health system (3 lives) and an invulnerability mode after collisions.

• Implement a gate-opening mechanism upon reaching a certain score.

• Ensure correct game completion in both victory conditions (collecting all cookies) and defeat conditions (losing all lives).

### 4. Project Components.

• Hero movement with direction buffering (input is stored in advance)

• Hero collisions with walls (passing through is impossible)

• Passage through the left and right edges of the screen (teleportation)

• Cookie collection system with score increase

• Three hero lives (displayed as HP)

• Invulnerability mode after taking damage (3 seconds)

• 4 ghosts of different colors moving through the maze

• Ghost AI based on the A* algorithm (shortest path search)

• Random route generation for ghosts

• Gate opening mechanism at 50 points

• Display of current score and health on the screen

• Victory screen after collecting all 318 cookies

• Defeat screen after losing all lives

• Maze size: 28×31 cells (28 columns, 31 rows)

• Conversion of an ASCII map into a game maze (symbol X – wall, G – ghost spawn point, space – walkable area)

### 5. User Guide.

• Before using the game, ensure that all required libraries are installed: pygame, numpy, tcod.

• After launching the game, a maze window will appear. The yellow hero is located in the upper-left corner, while the ghosts are at the bottom of the map.

• Controls are performed using the arrow keys: **UP, DOWN, LEFT, RIGHT**. The hero moves in the selected direction until hitting a wall.

• The player’s task is to collect all yellow dots (cookies) scattered throughout the maze. Each cookie grants 1 point.

• Upon colliding with a ghost, the player loses one unit of health (HP) and becomes temporarily invulnerable (ghosts pass through without causing damage).

• When the player reaches 50 points, the gates in the central part of the maze open, allowing access to the previously closed area.

• To achieve complete victory, all 318 cookies in the maze must be collected. After that, the “YOU WIN!” screen appears.

• If health drops to 0, the game ends with the “GAME OVER” screen.

• Important note: the hero can pass through the left and right boundaries of the screen, appearing on the opposite side. Passing through the top and bottom boundaries is impossible.

## Part 2. Technical Document.

### 1. Development Goals.

• Create a complete parody version of Pacman.

• Implement a pathfinding system for ghosts using the tcod library.

• Practice object-oriented programming skills: class hierarchy **GameObject → MovableObject → Hero / Ghost.**

• Develop a flexible level-loading system from an ASCII scheme with automatic recognition of walls, cookies, and ghost spawn points.

• Implement a collision system based on rectangles (pygame.Rect) and correct handling of collisions with multiple objects simultaneously.

• Ensure smooth controls with input buffering for a more responsive gameplay system.

### 2. Technologies Used.

• **Programming Languages:** Python.

• **Libraries:** pygame (graphics, input, rendering), numpy (array handling for pathfinding), tcod (A* pathfinding algorithm), random (random routes), enum (movement directions), sys (system operations).

### 3. Project Architecture.

• The project uses an object-oriented architecture with clear separation of responsibilities.

• **GameRenderer** – the main game controller: manages the game loop, rendering, event handling, stores all game objects (walls, cookies, hero, ghosts), score, and health.

• **GameObject** – the base class for all screen objects: contains position, size, color, shape (circle/rectangle), and the draw() rendering method.

• **MovableObject** – inherits from GameObject and adds movement logic: direction buffer, wall collision checks, automatic movement.

• **Hero** – inherits from MovableObject and implements hero-specific logic: cookie collection, ghost collisions, invulnerability mode, teleportation through screen edges, rendering with an open mouth depending on movement direction.

• **Ghost** – inherits from MovableObject and implements ghost logic: movement along a precomputed path using A*, requesting a new random route upon reaching the destination.

• **Pathfinder** – a wrapper class over tcod.path.AStar, providing the get_path() method for shortest path searching between two points in the maze.

• **PacmanGameController** – level loader: stores the ASCII maze scheme, converts it into a numpy array (0 – wall, 1 – path), creates lists of cookie positions, reachable cells, and ghost spawn points.

• **Cookie** – inherits from GameObject and represents a small yellow collectible dot (circle).

• **Wall** – inherits from GameObject and represents a wall (blue rectangle) that cannot be passed through.

• **Direction** – an enumeration (Enum) of possible movement directions: **LEFT, UP, RIGHT, DOWN, NONE.**

### 4. Project Structure.

• The project consists of one main file: Void_Run.py.

• The following are sequentially defined in this file:

• Imports (pygame, numpy, tcod, random, enum)

• Direction class (Enum)

• Helper functions translate_screen_to_maze() and translate_maze_to_screen()

• Base class GameObject

• Wall class (inherits GameObject)

• GameRenderer class (main controller)

• MovableObject class (inherits GameObject)

• Hero class (inherits MovableObject)

• Ghost class (inherits MovableObject)

• Cookie class (inherits GameObject)

• Pathfinder class

• PacmanGameController class (level loader)

• Entry point (if name == "main")

### 5. Key System Components.

• **GameRenderer** – the central component managing everything. Stores lists _game_objects, _walls, _cookies, and a reference to _hero. Contains the main game loop tick(in_fps), which runs while _done == False. Each frame calls tick() and draw() for all objects, updates score and HP, and checks gate-opening and victory conditions.

• **Collision System** – each object has a get_shape() method returning pygame.Rect. Collision is checked using colliderect(). The hero checks wall collisions before every movement and checks cookies/ghosts after movement.

• **Input Buffering** – Hero contains current_direction (current direction) and direction_buffer (requested direction). If the buffered direction does not lead into a wall, it becomes the current direction. This allows the player to press a key in advance, and the hero will turn as soon as possible.

• **Ghost Pathfinding** – Ghost does not have its own “mind.” Instead, it receives from PacmanGameController a list of route points (screen coordinates) and moves toward them, recalculating direction at each step. Upon reaching the target or if the next target is unreachable, the ghost requests a new random route.

• **A*** **Algorithm** – implemented using tcod.path.AStar. A cost array is provided as input (bool: True – passable, False – wall). The get_path(from_x, from_y, to_x, to_y) method returns a list of shortest-path coordinates.

• **Level Loading** – ASCII scheme of size 31 rows × 28 characters. Symbol “X” – wall, “G” – ghost spawn point, space – passable area with a cookie, “P” – hero start position. All walls and cookies are automatically created, and ghost spawn points are stored.

• **Gate Opening** – in GameRenderer, at the start of the game, certain walls (y==12, x==13 and x==14) are stored in _gate_walls. When the score reaches >= 50, these walls are removed from _game_objects and _walls, and the _gate_opened flag becomes True.

• **Health System** – _hp = 3. Upon collision with a ghost (if not invincible), damage_player() is called, reducing HP. When _hp <= 0 – show_game_over(). After taking damage, invincible = True for 180 frames (approximately 3 seconds at 60 FPS, or 1.5 seconds at 120 FPS).

• **Teleportation** – in Hero.tick(), if x < 0, then x = screen width; if x > screen width, then x = 0. This creates a tunnel effect from the left edge to the right.

### 6. User Interface Implementation.

• The interface is built entirely with Pygame without the use of visual editors.

• The game window size is calculated dynamically: width = number of maze columns (28) × cell size (20) = 560 pixels; height = number of maze rows (31) × 20 = 620 pixels.

• The maze is rendered using walls (blue rectangles) on a black background.

• Cookies are small yellow circles with a diameter of 4 pixels, centered in the cell.

• The hero is a yellow circle with a triangular cut-out mouth. The mouth rotates depending on the current movement direction.

• Ghosts are squares (or circles, depending on is_circle) of different colors: pink-purple, red, cyan, orange.

• The score text is displayed at the top of the screen: “Score: X / 318” in white, Arial 24 font.

• The health text is displayed at the bottom-left corner: “HP: X” in white, Arial 24 font.

• Victory screen – green “YOU WIN!” text in large font (72) centered on the screen, displayed for 3 seconds.

• Defeat screen – red “GAME OVER” text in large font (72) centered on the screen, displayed for 3 seconds.

### 7. Development Process.

• First, a basic game loop in Pygame and the GameObject class for rendering primitives were created.

• Then the MovableObject class with basic movement logic and wall collision checks was developed.

• After that, the Hero class was implemented with keyboard controls, input buffering, and object collection.

• Next, the ASCII maze scheme and the PacmanGameController class for parsing it were created.

• The Pathfinder class was implemented using the tcod library for pathfinding.

• Then the Ghost class was implemented with movement logic based on route points and requests for new paths.

• The health system, gate opening, and victory/defeat screens were added last.

• The entire process was accompanied by continuous testing of collisions, ghost movement correctness, and difficulty balancing.

### 8. Main Challenges and Solutions.

• **(1) Challenge:** ghosts could not find paths to random points in the maze because screen coordinates did not match array coordinates.

**Solution:** two coordinate translation functions were created: translate_screen_to_maze() converts screen pixel coordinates into array indices; translate_maze_to_screen() performs the reverse conversion. All pathfinding operations are executed in “maze” coordinates and then translated back to screen coordinates.

• **(2) Challenge:** the hero could become “stuck” in walls during rapid key presses because movement was applied after collision checks.

**Solution:** the Hero class includes a variable last_non_colliding_position, which stores the last valid position. If the hero ends up inside a wall after movement, the hero returns to last_non_colliding_position.

• **(3) Challenge:** cookies could be collected multiple times because removal from the _game_objects list was not synchronized with the _cookies list.

**Solution:** when collecting a cookie, the system checks that the object exists in _game_objects, and removal is performed from the main list. The _cookies list remains unchanged until the next iteration, but collision checks use _cookies while removal occurs from _game_objects.

• **(4) Challenge:** ghosts sometimes “froze” in place when the next target in the path was unreachable.

**Solution:** in Ghost.calculate_direction_to_next_target(), a check was added: if next_target is None, the ghost immediately requests a new random path. Additionally, if the difference in both X and Y is non-zero simultaneously (diagonal movement is impossible), a new path is requested.

### 9. Current Project Limitations.

• All code is contained in a single file, making navigation and maintenance more difficult.

• Some mechanics from the original Pacman are missing.

• Ghost behavior is fully random; there are no different modes (chase, scatter, patrol).

• There is no sound design (background music or sound effects).

• The game does not save the best score between sessions.

• At high frame rates (120 FPS), some timed intervals (invulnerability) become shorter than intended.

• The interface is only in English; language switching is not supported.

### 10. Possible Improvements and Development Plans.

• Split the code into multiple modules: entities.py, renderer.py, pathfinder.py, level_loader.py for easier maintenance.

• Add large collectible power pellets that make ghosts vulnerable and edible after being consumed.

• Implement different AI modes for ghosts: one chases the hero, another patrols, and a third attempts to intercept from the front.

• Add sound effects: cookie collection, ghost collision, gate opening, victory, defeat.

• Implement a leaderboard system with JSON file saving.

• Add difficulty selection affecting ghost speed.

• Translate the game into Russian with language switching support.

• Add a blinking animation during invulnerability (the hero changes color or transparency).

• Add gamepad (joystick) support for controls.

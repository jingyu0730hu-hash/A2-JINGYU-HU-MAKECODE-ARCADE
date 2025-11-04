# A2-JINGYU-HU-MAKECODE-ARCADE

# Start from below

@namespace
# Define custom sprite categories  
class SpriteKind:
    Decoration = SpriteKind.create()  # Static coral background
    FISH = SpriteKind.create()        # Sharks or enemy fish
    FISH1 = SpriteKind.create()       # Background small fish (type 1)
    FISH2 = SpriteKind.create()       # Background small fish (type 2)

# PLAYER CONTROL & ANIMATION

def on_left_pressed():
    # When player moves left, play a "swim" animation sequence
    animation.run_image_animation(mySprite,
        assets.animation("swim right"),
        200,
        True)
controller.left.on_event(ControllerButtonEvent.PRESSED, on_left_pressed)

#  GAME RULES & END CONDITION 

def on_countdown_end():
    # End the game and display "GAME OVER" when timer finishes
    game.game_over(False)
    game.set_game_over_message(False, "GAME OVER!")
info.on_countdown_end(on_countdown_end)

# COLLISION HANDLERS

def on_on_overlap(sprite3, otherSprite3):
    # Player hits enemy (e.g., shark): lose 1 life, play bubbles effect
    otherSprite3.destroy(effects.bubbles, 80)
    info.change_life_by(-1)
sprites.on_overlap(SpriteKind.player, SpriteKind.enemy, on_on_overlap)

def on_on_overlap2(sprite2, otherSprite2):
    # Player collects coral: gain 1 point, play smile effect
    otherSprite2.destroy(effects.smiles, 100)
    info.change_score_by(1)
sprites.on_overlap(SpriteKind.player, SpriteKind.food, on_on_overlap2)

def on_on_overlap3(sprite, otherSprite):
    # Player touches shark/fish: lose 1 point, show fire effect
    otherSprite.destroy(effects.fire, 80)
    info.change_score_by(-1)
sprites.on_overlap(SpriteKind.player, SpriteKind.FISH, on_on_overlap3)

#  VARIABLE INITIALIZATION  

# Declare global sprite variables used later in spawning and setup
myFood = Myenemy = Myfish = myDecor = mySprite3 = mySprite2 = mySprite = None

#  SCENE & GAME INTRODUCTION 

scene.set_background_color(9)  # Set ocean blue background
game.splash("WELCOME TO OCEAN WARRIOR")
game.splash("PRESS A TO START")
game.show_long_text(
    "TIPS: DON'T GET EATEN BY THE SHARKS, YOU HAVE THREE LIVES. GAME OVER IF USED ALL.",
    DialogLayout.FULL)

# DIVER/ PLAYER SETUP

# Create main diver sprite controlled by the player
mySprite = sprites.create(img("""
        . . . . . . . . . . . . . . . .
        . . . . . f f f f f f . . . . .
        . . . f f e e e e f 2 f . . . .
        . . f f e e e e f 2 2 2 f . . .
        . . f e e e f f e e e e f . . .
        . . f f f f e e 2 2 2 2 e f . .
        . . f e 2 2 2 f f f f e 2 f . .
        . f f f f f f f e e e f f f . .
        . f f e 4 4 e b f 4 4 e e f . .
        . f e e 4 d 4 1 f d d e f f . .
        . . f e e e 4 d d d d f d d f .
        . . . . f e e 4 e e e f b b f .
        . . . . f 2 2 2 4 d d e b b f .
        . . . f f 4 4 4 e d d e b f . .
        . . . f f f f f f e e f f . . .
        . . . . f f . . . f f f . . . .
        """), SpriteKind.player)

info.set_life(3)                # Player starts with 3 lives
controller.move_sprite(mySprite)  # Enable arrow-key movement
mySprite.set_stay_in_screen(True) # Keep diver inside the screen
info.start_countdown(30)        # Set 30-second game timer
game.splash("")                 # Clear splash before play

# BACKGROUND FISH MOVEMENT

# Create small decorative fish type 1
for index in range(2):
    mySprite2 = sprites.create(img("..."), SpriteKind.FISH1)
    mySprite2.vx = 10                     # Move horizontally
    mySprite2.set_bounce_on_wall(True)    # Bounce when touching wall
    mySprite2.y = randint(10, 80)         # Random Y position

# Create small decorative fish type 2 with faster speed
for index2 in range(2):
    mySprite3 = sprites.create(img("..."), SpriteKind.FISH2)
    mySprite3.vx = 20
    mySprite3.set_bounce_on_wall(True)
    mySprite3.y = randint(10, 80)

# Apply continuous swimming animation to the player
animation.run_image_animation(mySprite, assets.animation("swim right"), 400, True)

# CORAL DECORATION

# Generate decorative coral reef sprites along the bottom
for index3 in range(8):
    myDecor = sprites.create(img("..."), SpriteKind.Decoration)
    myDecor.set_position(randint(0, 180), randint(97, 109))

# OBJECT SPAWN LOGIC

def on_update_interval():
    # Spawn swimming fish (type FISH) moving across screen
    global Myfish
    Myfish = sprites.create(assets.image("food"), SpriteKind.FISH)
    Myfish.set_position(scene.screen_width(), randint(5, 80))
    Myfish.vx = -75
game.on_update_interval(5000, on_update_interval)

def on_update_interval2():
    # Spawn sharks (enemies) that reduce player’s life or score
    global Myenemy
    Myenemy = sprites.create(assets.image("food"), SpriteKind.enemy)
    Myenemy.set_position(scene.screen_width(), randint(5, 80))
    Myenemy.vx = -75
game.on_update_interval(5000, on_update_interval2)

def on_update_interval3():
    # Spawn collectible bleached corals that give points
    global myFood
    myFood = sprites.create(img("..."), SpriteKind.food)
    myFood.set_position(scene.screen_width(), randint(5, 115))
    myFood.vx = -75
game.on_update_interval(1800, on_update_interval3)

# THE END

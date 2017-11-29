# Game Programming Adventures - BOX AVOIDER
A crash course introduction to game programming by Richard Marks.

> Last Updated November 25, 2017

## Creating The Box Avoider Project

### GP Course Admin Tools - The Template Code

All projects in this course will be started from the template code. In order to obtain the template code, you will need the admin tools developed for generating a course project.

First clone the tools repository

+ `git clone https://github.com/RichardMarks/gp-course-admin-tools.git`

Now use the `create-project` admin tool to create the `box-avoider` project.

+ `node tools/create-project.js box-avoider`

> If for whatever reason you are unable to run the admin tools on your machine (not tested in Windows for example), then you will need to do the following steps manually.

> + create a directory for the project named `box-avoider`
> + copy the `template.html` file from the `src` directory into the `box-avoider` directory
> + rename the file copied to `index.html`
> + copy the `template.css` file from the `src` directory into the `box-avoider` directory
> + rename the copied file `style.css`
> + copy the `template.js` file from the `src` directory into the `box-avoider` directory
> + rename the copied file `main.js`
> + open the `index.html` file with your code editor and replace `{{projectName}}` with `box avoider`
> + replace `{{screenWidth}}` with `640`
> + replace `{{screenHeight}}` with `400`
> + replace `{{authorName}}` with `your name`
> + open the `main.js` file with your code editor
> + replace `{{screenWidth}}` with `640`
> + replace `{{screenHeight}}` with `400`
> 
> Now you are ready to continue.

### Template Code Examined

First let's take a quick look at `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>box-avoider</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <div class='game'>
      <h1 class='game-title-text'>box-avoider</h1>
      <canvas
        width='640'
        height='400'
        class='game-canvas'></canvas>
      <h3 class='game-credits-text'>by Richard Marks</h3>
    </div>
    <script src="main.js"></script>
  </body>
</html>
```

This file is what will be loaded by the browser to present your game.
We can see looking at this file, that we are depending on two other files `style.css` (makes our presentation of the game pretty) and `main.js` (our game code)

Really this is some very basic HTML5 that shows header text, footer text, and a canvas element where our game will be rendered.

You don't need all of this for your own games, but for my course projects to be consistent, I decided to fancy the presentation up a little.

The bare bones you could use is shown below for your own convenience.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>box-avoider</title>
  </head>
  <body>
    <canvas
      width='640'
      height='400'
      class='game-canvas'></canvas>
    <script src="main.js"></script>
  </body>
</html>
```

Next we will look at the `style.css` file.

```css
@import url('https://fonts.googleapis.com/css?family=Kelly+Slab');

* {
  box-sizing: border-box;
  font-family: 'Kelly Slab', cursive;
  image-rendering: pixelated;
}

body {
  margin: 0;
  padding: 0;
  height: 100vh;
  background: #303030;
  color: lime;
  image-rendering: pixelated;
  display: flex;
  justify-content: center;
  align-items: center;
}

.game {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  box-shadow: 0 0 16px black;
  border-radius: 16px;
  padding: 32px;
}

.game-title-text {
  margin: 0;
}

.game-canvas {

}

.game-credits-text {
  margin: 0;
}
```

I have a few flexbox containers to center things on the page and minimal styling for the header and footer text.

The only real thing to note here is the use of the `Kelly Slab` font from Google Fonts. Feel free to change this to any other google font you like.

Let's take a look at the `main.js` file now.

First a couple constants to define the width and height of our game screen

```js
const SCREEN_WIDTH = 640
const SCREEN_HEIGHT = 400
```

Next we have the `game` object. This is where the game code will exist. There are 3 methods of importance on the object.

The `init` method is called before the main game loop starts. This is where you should construct any variables your game needs, like score, player, etc...

The `update` method is called every time the main game loop executes. This is roughly 60 times a second. The `deltaTime` argument to the method is a numerical value of the amount of time that has elapsed since the last update. This is used to obtain a smooth movement of objects no matter the framerate of your game.

The `render` method is where your game is rendered to the [HTMLCanvasElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement). The `renderingContext` argument to the method is a reference to the [CanvasRenderingContext2D](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D) which provides the api for drawing things on the canvas.

```js
const game = {
  canvas: undefined,

  init () {

  },

  update (deltaTime) {

  },

  render (renderingContext) {
    const ctx = renderingContext

    ctx.clearRect(0, 0, SCREEN_WIDTH, SCREEN_HEIGHT)

    ctx.fillText('This is my Game', SCREEN_WIDTH * 0.5, SCREEN_HEIGHT * 0.5)

    ctx.strokeRect(0, 0, SCREEN_WIDTH, SCREEN_HEIGHT)
  }
}
```

Finally we have the boot function. This gets called when the [DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded) event is fired by the browser.

A reference to the canvas is defined, a reference to the rendering context is created, the text rendering is configured to use our custom font, centered on the X and Y and our rendering will all be lime colored by default.

Next we call the `game.init()` method, then construct the main game loop where we calculate the elapsed timing, update the game and render the game. We use [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) to repeated call the main game loop after it finishes being called.

We must call the main game loop once manually to kick things off.

```js
const boot = () => {
  console.clear()
  game.canvas = document.querySelector('.game-canvas')
  const renderingContext = game.canvas.getContext('2d')
  renderingContext.textAlign = 'center'
  renderingContext.textBaseline = 'middle'
  renderingContext.font = '16px "Kelly Slab"'
  renderingContext.fillStyle = 'lime'
  renderingContext.strokeStyle = 'lime'

  game.init && game.init()

  let lastTime = Date.now()
  const mainLoop = elapsedTime => {
    const currentTime = Date.now()
    const deltaTime = (currentTime - lastTime) * 0.001
    lastTime = currentTime
    game.update && game.update(deltaTime)
    game.render && game.render(renderingContext)
    window.requestAnimationFrame(mainLoop)
  }

  mainLoop(0)
}
```

Lastly we hook up the event to call our boot function and we are done.

```js
document.addEventListener('DOMContentLoaded', boot, false)
```

## Serving

You need a web server to run your game. There are tons of options, but for this course, we will use the simple HTTP Server from the `npm` package `http-server`.

Install the server on your machine globally

+ `npm install -g http-server`

Run the server in the project's directory

+ `http-server -p 8080 path/to/project/directory`

> Note: I have not tried this on Windows, so I do not know if it will work or not. If it does not, look for a free windows web server solution.

+ Open your web browser to [http://localhost:8080](http://localhost:8080)

You should see the template code "game". If you don't see anything, or have any errors, try following the steps above, or reach out to me at [ccpsceo@gmail.com]()

## Where should we begin?

### Keyboard Controls

The first task we are going to tackle is adding the ability to check the state of the keyboard controls we will use in the game.

> Every key on your keyboard has a value that is associated with it called a `keyCode`

We are going to control our player with the arrow keys, so we will define a few **const**ants to more easily refer to the keys.

```js
const KEY_UP = 38
const KEY_DOWN = 40
const KEY_LEFT = 37
const KEY_RIGHT = 39
```
> TIP: The way that I obtained the values to use was by running the following piece of code and writing down what was output to the browser's devtools console when I pressed a key on my keyboard.

> ```js
> window.addEventListener('keyup', event => {
>   event.preventDefault()
>   console.log(event.keyCode)
> }, true)
> ```

In the `game.init` method, we are going to define a new object called `input` which will hold our input device handling code.

```js
game.input = {
  keys: {}
}
```

Well that was a bit easy, wasn't it?

But that's not everything. We need to actually handle the keyboard input events [keydown](https://developer.mozilla.org/en-US/docs/Web/Events/keydown) and [keyup](https://developer.mozilla.org/en-US/docs/Web/Events/keyup) and we do that next. This code is still within the `game.init` method.

When a key on the keyboard is pressed down, we set a `true` Boolean value in our input object's `keys` object.

```js
window.addEventListener('keydown', event => {
  game.input.keys[event.keyCode] = true
}, true)
```

When a key is released, we remove the Boolean that we had set when it was pressed down.

```js
window.addEventListener('keyup', event => {
  delete game.input.keys[event.keyCode]
}, true)
```

This is all that we need at this point. We will add more functionality later as the project grows. For now, let us move on to adding our little player to the screen.

### Adding the Player

We need to define some *rules* for our player object.

+ Represented visually by a lime green square box primitive shape.
+ Will logically take up 32x32 units of game world space.

We will create a player object that will have it's own update and render methods that will be called by the game methods respectively.

We need to track the position and size of the player, we will do this using `x` and `y` for the position, `width` and `height` for the size. We also need to keep half the size as well to avoid multiple divisions since we will treat the center of the player's shape as it's position.
`halfWidth` and `halfHeight` will be used to find the upper-left corner of the player relative to it's centralized position.

Here is the initial player object construction.

```js
game.player = {
  x: 16,
  y: 16,
  width: 32,
  height: 32,
  halfWidth: 16,
  halfHeight: 16,
  
  update (deltaTime) {
  },
  
  render (renderingContext) {
  }
}
```

Let us now write the code that will render our player to the screen. This should go in the `render` method of the player object.

```js
// save the current context transformation
renderingContext.save()

// translate the context coordinate system to place the player `x` and `y` at the origin of `0, 0`
renderingContext.translate(game.player.x, game.player.y)

// draw a filled rectangle with the upper-left corner offset
// to the left and above the origin by half the player's size
// and is the player's full size. This renders the rectangle
// such that the center is at the origin.
renderingContext.fillRect(
  -game.player.halfWidth,
  -game.player.halfHeight,
  game.player.width,
  game.player.height
)

// restore the previous conterxt transformation
renderingContext.restore()
```

Now we need to tell the game about our player object so we can see it rendered on the screen.

This code replaces the `game.render` method from the template code.

```js
render (renderingContext) {
  renderingContext.clearRect(0, 0, SCREEN_WIDTH, SCREEN_HEIGHT)
  game.player.render(renderingContext)
}
```

If you serve up the game in your browser, you should now see the player in all his glory rendered in the top left corner of the screen. He doesn't do anything, so let's take care of that now, and implement his controls.

### Moving the Player

Let's add some new rules to our player object.

+ Will move at roughly 150 pixels per second.
+ Will be controlled using the arrow keys.
+ Will stop moving at the edges of the screen.

We will track the speed at which the player moves with the `speed` property. And the velocity of the player with `vx` and `vy`.

Let's see the full player object and add our new property.

```js
game.player = {
  x: 16,
  y: 16,
  width: 32,
  height: 32,
  halfWidth: 16,
  halfHeight: 16,
  speed: 150, // we added this new property to track speed
  vx: 0, // we added this new property to track the X velocity
  vy: 0, // we added this new property to track the Y velocity
  
  update (deltaTime) {
  },
  
  render (renderingContext) {
    renderingContext.save()
    renderingContext.translate(game.player.x, game.player.y)
    renderingContext.fillRect(
      -game.player.halfWidth,
      -game.player.halfHeight,
      game.player.width,
      game.player.height
    )
    renderingContext.restore()
  }
}
```

The code we will now write should go in the `game.player.update` method.

```js
// reset the velocity of the player
game.player.vx = 0
game.player.vy = 0

// if the up arrow key is being pressed, use an upwards velocity
// otherwise if the down arrow is being pressed, use a downwards velocity
if (game.input.keys[KEY_UP]) {
  game.player.vy = -1
} else if (game.input.keys[KEY_DOWN]) {
  game.player.vy = 1
}

// if the left arrow is being pressed, use a velocity to the left
// otherwise if the right arrow is being pressed, use a velocity to the right
if (game.input.keys[KEY_LEFT]) {
  game.player.vx = -1
} else if (game.input.keys[KEY_RIGHT]) {
  game.player.vx = 1
}
```

Now that we can detect if the arrow keys are being pressed, we need to apply the velocity of the player to it's position, which causes the effect of movement.

```js
if (game.player.vx !== 0 || game.player.vy  !== 0) {
  // get the length of the player's velocity vector
  // distance formula: d = √ x² + y²
  const x2 = game.player.vx * game.player.vx
  const y2 = game.player.vy * game.player.vy
  const magnitude = Math.sqrt(x2 + y2)

  // if there is a length (not zero) then normalize the vector
  // scale the normalized vector by the player's speed
  // and scale by the deltaTime to obtain the amount of units
  // that the player needs to move this update
  if (magnitude) {
    // normalize the velocity vector
    const vx = game.player.vx / magnitude
    const vy = game.player.vy / magnitude

    // scale by the player's speed
    const moveX = game.player.speed * vx
    const moveY = game.player.speed * vy

    // scale by the deltaTime and apply to the position
    game.player.x += moveX * deltaTime
    game.player.y += moveY * deltaTime
  }
}
```

If we plug this into the game by calling the `update` method of the player from the `update` method of the game, like this:

```js
update (deltaTime) {
  game.player.update(deltaTime)
}
```

And if you serve up your game, you should be able to move the player around the screen. However you can move outside of the bounds of the screen, and your player gets *lost* somewhere in the game world. Let us now address clamping the player's position.

After the last piece of code we wrote in the `game.player.update` method, we need to check if the player moves off the screen, if so, move it back.

```js
// if the left edge of the player goes off the left edge of the screen
// then move the player so it's left edge is equal to the left edge of the screen
// otherwise if the right edge of the player goes off the right edge of the screen
// then move the player so it's right edge is equal to the right edge of the screen
if (game.player.x - game.player.halfWidth < 0) {
  game.player.x = game.player.halfWidth
} else if (game.player.x + game.player.halfWidth > SCREEN_WIDTH) {
  game.player.x = SCREEN_WIDTH - game.player.halfWidth
}

// if the top edge of the player goes off the top edge of the screen
// then move the player so it's top edge is equal to the top edge of the screen
// otherwise if the bottom edge of the player goes off the bottom edge of the screen
// then move the player so it's bottom edge is equal to the bottom edge of the screen
if (game.player.y - game.player.halfHeight < 0) {
  game.player.y = game.player.halfHeight
} else if (game.player.y + game.player.halfHeight > SCREEN_HEIGHT) {
  game.player.y = SCREEN_HEIGHT - game.player.halfHeight
}
```

And that wraps up the player for the time being. We will come back to the update method of the player later when we implement other features.

### Adding the score timer

We gain score points as time elapses in the game. We will track these points with the `score` property on our `game` object.
We will also create an entity that is responsible for updating the score based on time and rendering the score to the screen called `scoreTimer` also placed on the `game` object.

In the `game.init` method, we add the following code.

```js
game.score = 0

game.scoreTimer = {
  time: 0,
  
  update (deltaTime) {
    // add the elapsed time this update to the accumulated time
    game.scoreTimer.time += deltaTime
    // when the accumulated time reaches a second, reset the time and increment the score
    if (game.scoreTimer.time >= 1.0) {
      game.scoreTimer.time -= 1.0
      game.score += 1
    }
  },
  
  render (renderingContext) {
    // render the score at the top center of the screen
    renderingContext.fillText(`SCORE: ${game.score}`, SCREEN_WIDTH * 0.5, 16)
  }
}
```

You need to plug this new object into the game now in the `update` and `render` methods of the `game` object, just as we did with the player. Hopefully you are seeing a pattern by now.

```js
update (deltaTime) {
  game.player.update(deltaTime)
  game.scoreTimer.update(deltaTime)
},

render (renderingContext) {
  game.player.render(renderingContext)
  game.scoreTimer.render(renderingContext)
}
```

Now if you serve up your game in the browser, you will have a score counter that continuously grows as time goes on. It's officially a game. It's done. Ship it.

...Well no, not yet. It's a bit boring right now. We have nothing to do but move around the screen.

Let's fix this and add an evil red enemy box to our game!

### Adding the first enemy

We are going to do the same thing as we have been doing, create an object, write some update and render code, plug it into the game. I will not repeat saying where the code should go after this point, it should be pretty obvious if you have been paying attention. Here is a reference for you use if you forget.

> ----
> #### Q. Where do I put my code?!
> #### A.
> 
> + Write new object constructions in the `init` method of the `game` > object
> + Write an `update` method for any active entities.
> + Write a `render` method for any visible entities.
> + Call the `update` method for any entity you need to update from the `update` method of the `game` object.
> + Call the `render` method for any entity you need to render from the `render` method of the `game` object.

> ----

We will place all the enemy boxes inside an array property named `boxes` on the `game` object.

```js
game.boxes = []
```

We are going to create two helper methods `updateBoxes` and `renderBoxes    ` that will iterate through the `boxes` array and call the `update` and `render` methods of each box.

```js
game.updateBoxes = deltaTime => {
 game.boxes.forEach(box => box.update(deltaTime))
}

game.renderBoxes = renderingContext => {
  renderingContext.save()
  renderingContext.fillStyle = 'crimson'
  game.boxes.forEach(box => box.render(renderingContext))
  renderingContext.restore()
}
```

Call these new methods from the `update` and `render` methods of the `game` object to update and render the boxes respectively.

Nothing will change if you serve the game right now, because we have not  put any boxes into the `boxes` array! Let's add our first enemy box!

```js
const box = {}
game.boxes.push(box)
```

This is fine for just one, but if we want multiple enemies, we shouldn't repeat our code for each enemy... What to do you ask?

We will create a new method named `spawnBox` that will create a new box object that is all setup for us, and will call the new method to populate the `boxes` array with the return value of the `spawnBox` calls.

```js
game.spawnBox = ({
  x,
  y,
  vx,
  vy,
  width,
  height
}) => {
const box = {
  x,
  y,
  vx,
  vy,
  width,
  height,
  get halfWidth () { return box.width / 2 },
  get halfHeight () { return box.height / 2 },
  
  update (deltaTime) {
    box.y += box.vy * deltaTime
    box.x += box.vx * deltaTime
  },
  
  render (renderingContext) {
    renderingContext.translate(box.x, box.y)
    renderingContext.fillRect(
      -box.halfWidth,
      -box.halfHeight,
      box.width,
      box.height
    )
    renderingContext.translate(-box.x, -box.y)
  }
}

return box
}
```

Next we call `spawnBox` inside our `boxes` array

```js
game.boxes = [
  game.spawnBox({
    x: SCREEN_WIDTH * 0.25,
    y: SCREEN_HEIGHT * 0.25,
    vy: 60,
    vx: 60,
    width: 48,
    height: 48,
  })
]
```

Now you should see a red box moving on the screen slowly towards the bottom right. It will go offscreen when it reaches the edge, because we didn't tell it not to.

We will address this later when we look at AI, for now, we can write some code to detect when the player hits the enemy, we can reset the score to zero.

### Collision Detection

We will add a new method to the player object named `checkCollisionAgainstBoxes` that will iterate through the `boxes` array and check rectangle vs rectangle intersection to determine if the player has hit (collided with) another box.

```js
checkCollisionAgainstBoxes () {
  const playerLeft = game.player.x - game.player.halfWidth
  const playerTop = game.player.y - game.player.halfHeight
  const playerRight = game.player.x + game.player.halfWidth
  const playerBottom = game.player.y + game.player.halfHeight

  const boxCount = game.boxes.length
  let collision = false

  for (let i = 0; i < boxCount; i += 1) {
    const box = game.boxes[i]

    const boxLeft = box.x - box.halfWidth
    const boxTop = box.y - box.halfHeight
    const boxRight = box.x + box.halfWidth
    const boxBottom = box.y + box.halfHeight

    if (!(
      (playerBottom < boxTop) ||
      (playerTop > boxBottom) ||
      (playerLeft > boxRight) ||
      (playerRight < boxLeft)
    )) {
      collision = true
      break
    }
  }

  return collision
}
```

We will now modify the `update` method of the player object to check for collisions by calling the new method we just created. This code should come first in the method before the velocity vector is reset to zero.

```js
const collision = game.player.checkCollisionAgainstBoxes()

if (collision) {
  game.score = 0
  return
}
```

Now if you serve the game up and move your player into the path of the enemy box, and watch your score at the top, it will reset to zero when the two boxes collide.

We are getting closer to having a finished game now!

There are a lot of other features in the example project that I will not cover in such detail here, as the code is readily available and you should be able to figure it out once you've completed this course.

### Game States

Our next task will be splitting the game into 3 distinct *game states* each of which will be responsible for updating and rendering the respective objects for that state.

At the beginning of the code before the game object and before the key code constants, we will add some constants for our game states.

```js
const GS_TITLE = 'title'
const GS_PLAY = 'play'
const GS_GAMEOVER = 'gameover'
```

We are going to need to use the `spacebar` key and `escape` key, so let us add a few new key code constants.

```js
const KEY_SPACE = 32
const KEY_ESCAPE = 27
```

We will want to test keyboard input as a toggle state for these keys, so let's add another property to `game.input` called `keysPressed`

```js
game.input = {
  keys: {},
  keysPressed: {}
}
```

Our `game` object is going to need to track the current game state, so we will add the `state` property and initialize to the title state.

```js
game.state = GS_TITLE
```

Now things are going to get a little tricky. We will use a few if/else if blocks in the `update` and `render` methods of the game object to specify what to update and render for what game state.

> This is not the *best* way to approach the problem of multiple game states, but it's one of the easiest and so that is why I have chosen this architecture for the code.
> 
> A better solution would be to create a new object per state, map the state objects to string names, and use a lookup into the known states with the current state string as the key, and then call the methods of the state object... something along the lines of this:
> 
> ```js
> const titleState = {
>   update (deltaTime) {
>     // ...
>   },
>   render (renderingContext) {
>     // ...
>   }
> }
> // ...
> game.states = {
>   [GS_TITLE]: titleState,
>   [GS_PLAY]: playState,
>   // ...
> }
> 
> // ...
> update (deltaTime) {
>   const state = game.states[game.state]
>   state && state.update && state.update(deltaTime)
> }
> 
> render (renderingContext) {
>   const state = game.states[game.state]
>   state && state.render && state.render(renderingContext)
> }
> ```

#### Implementing The Title Game State

```js
update (deltaTime) {
  if (game.state == GS_TITLE) {
    // update the title state
  }
  // ... existing code from update omitted ...
}
```

For our title, we will render some text and just wait for the user to press the spacebar which will change the current game state from the title state to the play state, starting the game.

```js
render (renderingContext) {
  renderingContext.clearRect(0, 0, SCREEN_WIDTH, SCREEN_HEIGHT)
  
  if (game.state === GS_TITLE) {
    renderingContext.fillText(
      'Press Space to Play',
      SCREEN_WIDTH * 0.5,
      SCREEN_HEIGHT * 0.5
    )
  }
}
```

And the code that goes into the `update the title state` block from earlier looks like this.

```js
if (game.input.keys[KEY_SPACE] && !game.input.keysPressed[KEY_SPACE]) {
  game.input.keysPressed[KEY_SPACE] = true
} else if (!game.input.keys[KEY_SPACE] && game.input.keysPressed[KEY_SPACE]) {
  game.input.keysPressed[KEY_SPACE] = false
  game.score = 0
  game.state = GS_PLAY
}
```

> We are going to be really repetitive when testing input for the sake of simplicity. In the real world, you should abstract this stuff into a method and use that where you need to.

Okay so now the code that runs when you are staring at the title is just a check to see if the spacebar was pressed and then released. When it is  released, the game score will reset to zero and the game state will change to the play state.

#### Implementing The Play Game State

```js
update (deltaTime) {
  if (game.state == GS_TITLE) {
    // update the title state
    // ... existing code from update omitted ...
  } else if (game.state == GS_PLAY) {
    // update the play state
  }
}
```

We already have the code written for the play state, it is what was there before we added the title state. Move the code into the else if block in both the render and update methods.

#### Implementing The Gameover Game State

```js
update (deltaTime) {
  if (game.state == GS_TITLE) {
    // update the title state
    // ... existing code from update omitted ...
  } else if (game.state == GS_PLAY) {
    // update the play state
    // ... existing code from update omitted ...
  } else if (game.state == GS_GAMEOVER) {
    // update the gameover state
  }
}
```

For the gameover state, we will render the same elements as the play state, a translucent black fill over the entire screen to *dim* the screen, and finally the game over text and instructional text. The game will wait for the spacebar key to be pressed same as the title, only the state will change from the gameover state to the title state.

Render code for the gameover state.

```js
// render game elements
game.scoreTimer.render(renderingContext)
game.drawBoxes(renderingContext)
game.player.render(renderingContext)

// dim the screen
renderingContext.save()
renderingContext.globalAlpha = 0.5
renderingContext.fillStyle = 'black'
renderingContext.fillRect(0, 0, SCREEN_WIDTH, SCREEN_HEIGHT)
renderingContext.restore()

// write the text
const oldFont = renderingContext.font
renderingContext.fillStyle = 'red'
renderingContext.font = '48px "Kelly Slab"'
renderingContext.fillText(
  'GAME OVER',
  SCREEN_WIDTH * 0.5,
  SCREEN_HEIGHT * 0.5
)
renderingContext.font = oldFont
renderingContext.fillStyle = 'lime'
renderingContext.fillText(
  'Press Space to Restart',
  SCREEN_WIDTH * 0.5,
  SCREEN_HEIGHT * 0.65
)
```

And the update code is as follows.

```js
if (game.input.keys[KEY_SPACE] && !game.input.keysPressed[KEY_SPACE]) {
  game.input.keysPressed[KEY_SPACE] = true
} else if (!game.input.keys[KEY_SPACE] && game.input.keysPressed[KEY_SPACE]) {
  game.input.keysPressed[KEY_SPACE] = false
  game.state = GS_TITLE
}
```

### Wrapping things up

In our player update method we check for collisions against the boxes, and if there is a collision, we reset the score to zero. Instead of resetting to zero, just change the game state to gameover and we have a finished playable simple game.

```js
if (collision) {
  game.state = GS_GAMEOVER
}
```

There are many things that we have not covered here that exist in the example project, but you should have no problem understanding them by now.

+ boost speed when shift is held down
+ speed boost gauge
+ limiting speed boost usage and adding a *cooldown*
+ adding multiple enemies
+ multiple enemy AI types
+ pausing the play state and resuming the play state
+ quitting from the paused play state to the title state
+ keeping track of a highscore in local storage
+ displaying the highscore in the title state

You can check out all of the code from [https://github.com/RichardMarks/gp-course-example-game-box-avoider](https://github.com/RichardMarks/gp-course-example-game-box-avoider)

## That's All Folks...
I sincerely hope that you enjoyed this *little* crash course introduction into game programming adventures as much as I did writing it. 😜

To be notified when the Game Programming Adventures full course is available, email [ccpsceo@gmail.com]() with the subject `GPA2D` and the body `SUBSCRIBE` and you will get emails from me, Richard Marks when the course is ready for primetime.

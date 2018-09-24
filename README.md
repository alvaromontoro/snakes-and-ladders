# Snakes and Ladders

Snakes and Ladders is an ancient Indian board game regarded today as a worldwide classic. A number of 'ladders' and 'snakes' are pictured on the board, each connecting two specific board squares. The object of the game is to navigate one's game piece, according to die rolls, from the start (bottom square) to the finish (top square), helped or hindered by ladders and snakes respectively.<sup>[1](https://en.wikipedia.org/wiki/Snakes_and_Ladders)</sup>

This is a multiplayer version (1-4 players) developed using HTML and CSS **without any JavaScript**. It is based on the idea that many classic board games are actually Finite-State Machines (FSM), so they can be recreated simulating the states of the automaton with radio buttons, labels, and some CSS styling... without any other scripting/imperative language.

## How it works

### Simulation of states

In theory, the number of states/radio buttons for this game could be up to 100<sup>4</sup> = 100,000,000, just to represent the different positions of the 4 game pieces on the board. And we would need to multiply that number by 4 so we could also track the player's turn. Making it a 400,000,000-state automaton.
  
That would be an insane number of radio buttons to maintain and render by the browser, so we only keep track of 100 positions for each player (400 radio buttons) and an additional set of 4 radio buttons to keep track of the player's turn. This reduction helps ease the rendering of the page, but it brings an usability problem: we need the "Next Player" button to jump from one player to the next one.

Each of the 400 position radio buttons will have label associated to it that will be used as part of the dice (see below) to move from one state/position into another.

### Game Logic

There are many parts moving, but the logic in itself is simple: the labels for a player's next six available positions are shown (in the form of a dice), the user clicks on one of them activating the radio button associated to it, and that triggers a series of events: the player's piece moves on the board, a sound plays, the next six available position `label`s are shown...

The trick for doing this is using the general sibling selector (`~`) and the `:checked` pseudo class. The radio buttons will be at a global level, and the board, pieces, sounds, etc. will be in different containers. To activate the action of a radio button, we will need to verify that it has been activated (with `:checked`), and then proceed to select the element that will go through a change.

For example, when the radio button por position 18 is activated, we can select the player's piece by doing something like this in CSS (just for demo, not the actual code from the game):

    #radio-18:checked ~ #board #player1-piece {
       ...
    }
    
And inside that CSS rule, we would add the coordinates of the 18<sup>th</sup> tile using `top` and `left`. Easily animating the move by having `transition` set up.

### Dice

The dice is an interesting part of the game: how to develop a random number generator using HTML and CSS without JavaScript? The answer is you can't. There is no real way to do it, because both HTML and CSS are languages that describe the content of the page, but none of them have the capability to generate random content. So a random dice is out of the question.

...but you can develop a pseudo-random one!

By having a set of stacked `label` elements that change their position on top every few milliseconds, you can create a random effect... it is not really random, but it looks like it, and that's more than enough in this case, as humans would need really good time-control in order to take advantage of that pseudo-randomness.

This was a bit more challenging to develop than expected (in fact, it required some [help from our friends at StackOverflow](https://stackoverflow.com/q/51449737/3695983)) because the click effect (on the `label`) is only effective if the active element when the mouse button goes down is the same as when the mouse button goes up. And as the `label`s are part of an animation, sometimes the mouse button up moment happened after the `label` was no longer the active one. A workaround for this (as explained on the linked question) is to use CSS pseudo-elements and different positioning.

### Sound

> Notice: The sound **only works on Chrome**. On other browsers, the sounds may play once when the page is loaded (and then never play again), or not play at all.

Instead of using an `audio` tag for the sound, we used `embed`. The `embed` element can be used to embed/import external content to our page (in this case, an MP3 file). This `embed` element is contained in a hidden section that is only made visible for a fraction of a second when an action that triggers the sound happens. 

    <div id="fx-sound">
      <embed src="/path/to/sound.mp3" />
    </div>

According to the [definition of the `embed` element in the living HTML standard](https://html.spec.whatwg.org/multipage/iframe-embed-object.html#the-embed-element): "_Whenever an `embed` element that was not potentially active becomes potentially active [...] the user agent must queue a task using the embed task source to run the embed element setup steps for that element_". So by making the `embed` container visible for a little time, we are actually triggering the rendering of the `embed` element, and the sound should play.

So, at least in theory, Chrome's behavior should actually be the expected one in this case.

## Potential improvements

Apart from visual changes to make it look nicer, there are other things that could be added/changed to improve the game usability without impacting the general dynamics of the game (and still without using any JavaScript):

- Have the max number of players as a variable (in Pug and SCSS). Right now 4 is used as a "magic number" to generate the radio buttons and elements on the page, if it was saved as a variable, the game could be done even more generic and add more players easily (just by updating the value of the variable).
- Have more combinations of colors. Right now there are only 4 possibilities to pick from, but potentially there could be all of them, either by directly listing them or by changing the way it is designed so each user picks the color they want.
- Add international versions. This would be a simple change that would put the instructions and text copies in different languages. One radio button by language would be required, and then the text could be specified by hiding/showing repeated elements, or by having the texts in pseudo-elements.

## Other versions

Current and older versions playable on CodePen: 

- [Current 1-4 player version](https://codepen.io/alvaromontoro/details/gjWPNW/)
- [Current 1-4 player version (with animated dice)](https://codepen.io/alvaromontoro/pen/OwwQMm/)
- [Simplified two-player version](https://codepen.io/alvaromontoro/details/zLNdZQ/)
- [One-player version](https://codepen.io/alvaromontoro/pen/ejzJBJ/)

## Bibliography and Reference

- [Wikipedia's article on Snakes and Ladders](https://en.wikipedia.org/wiki/Snakes_and_Ladders)
- [Wikipedia's article on Finite State Machines](https://en.wikipedia.org/wiki/Finite-state_machine)
- [HTML Living Standard: The `embed` element](https://html.spec.whatwg.org/multipage/iframe-embed-object.html#the-embed-element)
- [MDN's article on `label`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)
- [MDN's article on CSS Selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors)
- [MDN's article on General Sibling Selectors (`~`)](https://developer.mozilla.org/en-US/docs/Web/CSS/General_sibling_selectors)

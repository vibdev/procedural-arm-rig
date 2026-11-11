# Procedural rigged arm: extras

A fork of [apekshik/procedural-arm-rig](https://github.com/apekshik/procedural-arm-rig) that gives the arm some new things to do: write, draw, flick a paper football, play rock paper scissors and make shadow puppets. The original motions (Reach & grab through Rest) are still in the code and drive the showcase recorder, but the bar on this page only shows the additions. Go to the original to play with those.

A human arm and hand built entirely in code with three.js: the mesh, the skeleton, the skin weights and the motion. There are no model files and no animation clips. Everything lives in one HTML file.

![Skin on top, skeleton below](preview.jpg)

## Run it

Live demo of the original: https://procedural-arm-rig.vercel.app

For this fork, open `index.html` in a browser. It loads three.js and the lighting environment from a CDN, so it needs a connection. It takes a few seconds to start, because the hand's skin is generated at load.

- **Modes** (bottom buttons, or keys 1–5 outside Write): Write, Draw, Paper football, Rock paper scissors, Shadow puppets.
- **Write**: type and the hand writes it out with a pen. Enter starts a new line, Backspace takes back what has not been written yet, Esc takes a fresh sheet, and a click on the paper moves where the writing continues. Every key is text in this mode, so use the buttons to leave it.
- **Draw**: move the mouse or trackpad over the paper and the pen follows, hovering; press and drag to draw. Esc takes a fresh sheet. **Ask your Claude for a picture** puts a small note beside the page with the request written on it, and turns the button into a box. Click the C in the note's corner to copy it: what lands on the clipboard is the request plus the rules for the stroke format. Click the note to select it, then type, and the hand rewrites the request. Paste the reply into the box and the hand draws it. Once there is a picture on the sheet (pasted, or drawn by you), **Ask it to add more** does the same with a prompt that lists the strokes already there, and anything written in Write mode as words with their positions; that reply is drawn on top without clearing. **Watch Claude's cat** plays a picture that ships with the page. **Spirograph** lays a toothed ring on the page, rolls a wheel round inside it with the pen through one of its holes, and draws the pattern that pairing gives; each press picks a different wheel and ink and draws over the last.
- **Paper football**: point at the desk to aim, hold to wind up the finger, let go to flick the paper triangle at the goal. Left alone, the hand takes its own shots, and misses some.
- **Rock paper scissors**: pick with the buttons or keys R, P, S. The hand pumps its fist three times and throws a shape it picked at random.
- **Shadow puppets**: a lamp throws the hand's shadow on a wall. Pick Dog, Swan or Rabbit, or let them take turns.
- **Views** (top right): Skin, X-ray (bones inside translucent skin), Weights.
- Drag to orbit, scroll to zoom, Space to pause (outside Write).

## What is in it

**Skeleton.** 38 bones: shoulder, upper arm, forearm, hand, and three per finger and thumb. The forearm twist is spread over three bones. The elbow and every finger joint have a helper bone that turns half the bend, so joints keep their volume instead of pinching.

**Skin.** The arm is a tube with painted weights. On the inside of the elbow the two segments stay nearly rigid and fold along a crease; on the outside the blend is wide. The hand is one seamless surface: palm, fingers and thumb are described as distance fields, blended where they meet, and meshed. The biceps and forearm swell as the elbow bends and the hand tenses.

**Arm motion.** The shoulder and elbow are solved analytically from where the hand needs to be. The forearm rolls to face the palm the right way, within anatomical limits. Every channel runs through a damped spring, and the wrist and fingers trail behind the hand's movement.

**Hand contact.** Each finger bone is a capsule tested against the palm, the other fingers, the ball and the table. A finger keeps last frame's pose and moves a limited amount per frame; each joint closes, knuckle first, until it touches. Blocked joints wait while the rest keep closing, which is what wraps a grip around an object. Joints have human ranges, and if a finger runs out of range against the table the whole hand rises.

**Hand behaviour.** Poses and the order of movement were tuned against slow-motion reference footage of a real hand:

- Closing a fist is two stages: the knuckles fold and the fingertips gather, then the fingers roll shut.
- The thumb closes a beat behind the fingers and lands as the index finger finishes.
- Fingers share tendons, so moving one drags its neighbours part of the way.
- A relaxed hand curls more toward the little finger.

**Pen and paper.** The pen is fixed in the hand's frame and the fingers close onto it by contact, the same way they wrap the ball; the ring and little finger stay curled and settle from the knuckle until they rest on the desk. The grip itself (finger targets and where the pen sits) was fitted numerically so that thumb, index and middle finger each touch the barrel and surround it. Writing uses a single-line font defined in the file as a few points per stroke. A target point runs along each stroke, slower through tight curves and nearly stopping at corners, and hops through the air between strokes. The arm is solved so the pen tip lands exactly on that point, the wrist takes the quick part of the motion, and the ink is drawn on the paper's texture where the tip is. In Draw the same point chases the pointer instead. The arm is a left arm, so the writer is left-handed and the camera moves behind the shoulder to read along.

**Paper football.** The index finger is cocked behind the thumb and released; the triangle is launched when the fingertip reaches it, with a speed set by how long the finger was wound up. From there it is a small rigid body: gravity, tumbling, bounces off the desk, the floor and the goal's posts, and a check of where it crosses the plane of the goal.

**Scripted pictures.** A picture is plain text, one pen stroke per line: `x,y x,y x,y ...` in millimetres on the 210 x 148 page, origin at the bottom-left. A smooth curve is fitted through the points, `!` after a point makes a sharp corner, and a stroke that ends on its first point is closed. Lines that are not strokes are ignored, so a whole chat reply can be pasted in. The strokes go through the same planner as handwriting.

**Spirograph.** Patterns come from tooth counts, as on the toy: a wheel of n teeth in a ring of N returns to its start after n/gcd(N, n) trips round and leaves N/gcd(N, n) points. The ring and wheel are drawn as clear plastic and the wheel is placed from where the pen is along the curve, so the pen stays in its hole.

**Shadow puppets.** A spotlight to the arm's side casts onto a wall on the other side while the room lights dim, so each pose is designed for its outline along the beam: the hand on edge for the dog, the forearm as the swan's neck, a fist with two fingers up for the rabbit.

## Recording the showcase video

The page can render a scripted two-panel video frame by frame, so it never drops frames.

```bash
node record-server.js
```

Open `http://localhost:5187/`, then run this in the browser console:

```js
await rig.direct()
```

Frames are written to `frames/`. Encode them with ffmpeg:

```bash
ffmpeg -framerate 60 -i frames/f_%05d.jpg -c:v libx264 -crf 18 -pix_fmt yuv420p showcase.mp4
```

The shot list, camera moves and ball path are in the `direct()` function in `index.html`.

## Known limits

- Only the fingers collide. The forearm and palm can pass through the table or the torso.
- Neighbouring fingers do not collide with each other.
- There is a faint seam at the wrist where the arm skin meets the hand skin.
- The wood and the skin are generated in code and are stylised, not photographic.
- The pen appears in the hand instead of being picked up, and the hand covers part of the line it has just written, as a left hand does.
- Write needs a physical keyboard; on a touch screen only Draw takes input.

# Procedural rigged arm: extras

A fork of [apekshik/procedural-arm-rig](https://github.com/apekshik/procedural-arm-rig) that gives the arm some new things to do: write, draw, flick a paper football, play rock paper scissors, make shadow puppets, toss a ball, juggle two in one hand, and pass one, two or three between two hands. For that it has grown a second arm. The original motions (Reach & grab through Rest) are still in the code and drive the showcase recorder, but the bar on this page only shows the additions. Go to the original to play with those.

A human arm and hand built entirely in code with three.js: the mesh, the skeleton, the skin weights and the motion. There are no model files and no animation clips. Everything lives in one HTML file.

![Skin on top, skeleton below](preview.jpg)

## Run it

Live demo of the original: https://procedural-arm-rig.vercel.app

For this fork, open `index.html` in a browser. It loads three.js and the lighting environment from a CDN, so it needs a connection. It takes a few seconds to start, because the hand's skin is generated at load.

- **Modes** (bottom buttons, or keys 1–6 outside Write): Write, Draw, Paper football, Rock paper scissors, Shadow puppets, Toss.
- **Write**: type and the hand writes it out with a pen. Enter starts a new line, Backspace takes back what has not been written yet, Esc takes a fresh sheet, and a click on the paper moves where the writing continues. Every key is text in this mode, so use the buttons to leave it.
- **Draw**: move the mouse or trackpad over the paper and the pen follows, hovering; press and drag to draw. Esc takes a fresh sheet. **Ask your Claude for a picture** puts a small note beside the page with the request written on it, and turns the button into a box. Click the C in the note's corner to copy it: what lands on the clipboard is the request plus the rules for the stroke format. Click the note to select it, then type, and the hand rewrites the request. Paste the reply into the box and the hand draws it. Once there is a picture on the sheet (pasted, or drawn by you), **Ask it to add more** does the same with a prompt that lists the strokes already there, and anything written in Write mode as words with their positions; that reply is drawn on top without clearing. **Watch Claude's cat** plays a picture that ships with the page. **Spirograph** lays a toothed ring on the page, rolls a wheel round inside it with the pen through one of its holes, and draws the pattern that pairing gives; each press picks a different wheel and ink and draws over the last.
- **Paper football**: point at the desk to aim, hold to wind up the finger, let go to flick the paper triangle at the goal. Left alone, the hand takes its own shots, and misses some.
- **Rock paper scissors**: pick with the buttons or keys R, P, S. The hand pumps its fist three times and throws a shape it picked at random.
- **Shadow puppets**: a lamp throws the hand's shadow on a wall. Pick Dog, Swan or Rabbit, or let them take turns.
- **Toss**: **Toss one** throws a ball up and catches it; **Just hold** holds it. **Columns** and **Circles** hand in a second ball and the hand keeps both going, two in one hand; you can change from one pattern to the other without the hand stopping. **Pass one**, **Pass two** and **Cascade** use both hands: one ball thrown from hand to hand, then two, then all three in a cascade. The line above the buttons counts the catches in a row. Drag a ball to take it away, then drop it, lob it at the hand, press it into the palm, or leave it on the desk. Break the pattern and the hand gets hold of one ball and starts again.
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

**Balls that react.** In Toss nothing carries a ball. It has a velocity and a spin, and gravity pulls on it. The finger capsules, the palm, the forearm, the desk, the floor and the other ball push it out of themselves with friction, so it rolls or slides. Every push the arm gives a ball comes back on the arm as a kick to its springs, so the hand sags under the weight, dips on a catch, and can be pushed down with the ball. A throw is the palm rising on a planned path and stopping short: the ball leaves by itself when the hand slows down faster than gravity slows the ball. A catch is the hand working out where the ball will come down and being there, open; the fingers close when it lands. Each frame the ball moves before the fingers are resolved, so a moving hand takes the ball with it, and the fingers only give way to what the ball could not get out of the way of.

**Juggling.** Two in one hand, in either of two patterns. In columns each ball goes up and comes down in a lane of its own, and the hand crosses between the lanes while it is empty. In circles every ball is caught on the outside, carried across to the inside, and thrown from there, out and up, to come down where it was caught; the throws are higher, to leave time for the carry. Either way the hand only ever holds one ball, and its path is planned from each event to the next. From a catch it coasts down with the ball, drives up to the place and speed of the throw, and lets go just early enough to reach the next catch. From a throw it goes to where the other ball will land, arriving already moving down with it. A few things make it hold together:

- The balls are beanbags. On skin they grip or slide but do not roll. A ball that rolls in the palm takes up only two sevenths of the hand's sideways motion, drifts between catch and throw, and leaves crooked; this was the difference between runs of ten catches and runs that do not end.
- The palm is dead level. The single ball is held on a palm tipped toward the thumb so that it nestles there, but that tilt kicks every fast catch sideways.
- The arm is where a juggler's is: the elbow hangs at the side, the forearm points forward and stays near level, and the hand works in front of the shoulder. Palm up with the forearm forward is the forearm turned out as far as it goes, so in this mode it is allowed the full quarter turn, and the patterns are kept to the strip in front of the shoulder where that is enough. The desk slides aside to make room: with it there the hand had to work at chest height to clear it, and the elbow had to come forward and across to keep the palm level up there.
- The hand does not drop away from a ball it has caught, and does not pull it sideways harder than its grip on the palm allows, which is its weight plus the push under it.
- The arm is told how its target is really moving, speed and push, read off the target frame to frame, so it follows the plan instead of trailing it. That includes the hand turning about the wrist as the arm swings.
- The hand measures each throw as the ball leaves and corrects the next one: how high, and which way it leaned. One lean is known in advance: carrying a ball across swings the forearm, the swing is still in the hand as it throws, and the throw is aimed to allow for it in proportion to the carry.
- The balls are kept from colliding. In columns a ball caught off its lane is thrown from the hand's own side of the other ball's path. In circles the inside is never nearer than a set distance to where the other ball is coming down, and the circle is turned a little front to back, which adds room as they pass.
- A change of pattern is taken up at the next catch, and a ball already in the air from the old pattern is treated as what it is: something coming down at a known place, to be kept clear of.
- The tossing hand is simulated in steps of one fixed size, however unevenly the frames arrive. A throw's speed is set in its last few steps, so steps of varying length make throws of varying height.

**Two arms.** There is one arm's worth of code and state. The right arm is the same code run a second time each frame, in a world mirrored left to right about the middle of the torso: its state is put in place, the frame is worked out as if for a left arm, and the state is put aside again. Its bones are a copy of the left arm's, drawn under a mirror, and the balls are mirrored while it deals with them. So the right arm is exactly the left arm's mirror image, and everything tuned for one holds for the other. Outside Toss it hangs at rest.

**Passing.** Passing is a three-ball cascade with one, two or all three of its balls, which is also how a person learns it. The hands take turns on a steady beat. Each catches on its outside, carries the ball in toward the middle, and throws it up and across to come down on the other hand's outside three beats later, by which time that hand has thrown what it held. With fewer than three balls the same beats go by empty. Each hand runs the one-hand planner in its own mirrored world. The place a hand throws from is far enough inside the place it catches that the ball going up and the one coming down move apart from the moment of the throw. With three balls the third is let go in the air above the left hand's catching place, timed to land when a thrown one would.

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

- Only the fingers collide. The forearm and palm can pass through the table or the torso, and an arm hanging at the side overlaps the torso's outline.
- Neighbouring fingers do not collide with each other.
- There is a faint seam at the wrist where the arm skin meets the hand skin.
- The wood and the skin are generated in code and are stylised, not photographic.
- The pen appears in the hand instead of being picked up, and the hand covers part of the line it has just written, as a left hand does.
- A tossed ball is only held by what is under and around it. The fingers stop where they touch and do not squeeze, so there is no grip that holds against gravity with the palm turned down, and the hand cannot pick a ball up: a dropped ball fades out and is handed back in the air.
- Juggling starts with the second ball let go in the air above its lane, because the hand cannot hold two.
- Write needs a physical keyboard; on a touch screen only Draw takes input.

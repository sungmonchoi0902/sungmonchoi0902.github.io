#Final Project Work Log

##My TItle Of Final 'Meet Myself Through Shout'

“Meet Myself Through Shout” is an interactive artwork about letting out your feelings by using your voice. In today’s world, many people keep their stress inside and hide their true emotions. This project turns that hidden feeling into sound and visuals. When users shout into the microphone, the screen slowly changes from a blurry image to a clear one, showing their real self. It gives people a digital space to express emotions freely and feel relief through sound, mixing art, technology, and emotion.
 
#Process Of My Fianl

##Project1: Applying mosaic effects based on microphone input levels(Good)

###In this process, I will create a mosaic effect and make it disappear as the microphone input gets louder.

I made this using p5.js, and I checked the reference site to create a camera, but it was hard to find, so I asked ChatGPT. 

First, I declared a variable called cam and created a canvas of an appropriate size. Then, to receive the webcam stream, I used the createCapture(VIDEO) function and matched the video input size to the canvas size. I used cam.hide() because createCapture(VIDEO) automatically displays a default video element in the browser, and if that appears along with the video drawn on the canvas, the screen would show two overlapping videos, making the UI messy. By using hide(), I hid the default browser video and displayed only the one drawn on the canvas. After that, the draw() function ran every frame, and background(0) was used to repaint the canvas black each frame so that no afterimages remained. Finally, I used image(cam, 0, 0, width, height) to display the webcam video across the entire canvas.

![camcall](img/camcall.png)

However, the generated camera acted like a mirror, my image didn’t move naturally and it moved in the opposite direction of my actual movement. So I used push and pop codes.
 
`
let cam;
function setup() {
  createCanvas(640, 480);
  cam = createCapture(VIDEO);
  cam.size(640, 480);
  cam.hide();
}
function draw() {
  background(0);
  push(); 
  scale(-1, 1);
  pop();
}
`

But it didn't work. So I asked Chat gpt, and I realized that I forgot 'traslate' and 'image'. 

When I flipped the screen horizontally using scale(-1, 1), the video was mirrored off to the left and became invisible. To fix this, I used translate to shift the entire canvas 640 pixels to the right so the flipped video would appear back inside the visible area. Also, the webcam video didn’t show up because I forgot to include the image() function, which was the main cause of the issue.

![mirrorcam](img/mirrorcam.png)

And I could learn that I should add 'image' code to designate the object. Finally it worked!

![mirrorsolve](img/mirrorsolve.png)

I looked up several references to implement a mosaic effect, but I still couldn’t figure it out, so I asked ChatGPT.

![mosaic](img/mosaic.png)

First, I set blockSize = 20 to define the block size used for the mosaic effect. I also used pixelDensity(1);, which seems to be related to how pixels are handled on the canvas, but I didn’t fully understand this part. Then I adjusted the resolution with cam.size(320, 240) to make the mosaic look smoother. The function cam.loadPixels(); loads the pixel data from the camera, and without it, the pixel information doesn’t update, causing the screen to appear completely black. The line if (cam.pixels.length === 0) return; is used to skip the code temporarily when the camera isn’t ready yet. Here, return means “don’t execute the code below this line and just end this frame.”

The loops 
`
for (let y = 0; y < cam.height; y += blockSize) {
for (let x = 0; x < cam.width; x += blockSize) {
`
iterate through the entire video, skipping by blockSize each time. The line let i = (y * cam.width + x) * 4; calculates the position in the pixel array, but I didn’t fully understand how this calculation works. I do know that i, i+1, and i+2 correspond to the R, G, and B values, but I’m not exactly sure why the array is structured this way.

The reason the mosaic appears transparent is not because it is actually transparent, but because the “clarity” of the image is reduced. When blockSize becomes larger, multiple small pixels are replaced by one big color block. This removes the detailed features of the face and leaves only large patches of color, making the image blurry and faint. That’s why it looks almost transparent.

![mosaicsuc](img/mosaicsuc.png)

To input the microphone signal, I used code I found in the p5.js reference.

![micreference](img/micreference.png)

`
mic = new p5.AudioIn();
mic.start();
`

I wanted the mosaic to clear up as the microphone signal increased, and for it to disappear completely once it reached a certain volume. However, I couldn’t find any reference that explained how to do this, so I asked ChatGPT.

![dismosaic](img/dismosaic.png)

This was very difficult to understand.

First, I used smoothVol = lerp(smoothVol, vol, 0.1); to smooth out the microphone input. Without this, the mic values would jump abruptly, causing the screen to shake constantly. The map() function converts a value from one range to another while keeping its proportional relationship. Here, I used it to convert the microphone volume into the mosaic block size. I set the minimum volume to 0 and the maximum to 0.03, but 0.03 is actually a very small sound level, so I plan to adjust it to a higher value later. When the volume is low, I set the mosaic size to 60 so the screen appears blocky. As the volume increases, the mosaic size decreases to as small as 1, making the video gradually clearer. The final parameter, true, prevents the value from going outside the specified range. This keeps the behavior stable so the block size doesn’t jump unexpectedly even when the sound is too loud or too quiet.

###Through this process, I realized how the mosaic effect is generated. And I think I’ll be able to create interactive art on my own from now on.

##Project 2: Generating background music(Better)

###In this process, I will create a mosaic effect and make it disappear as the microphone input gets louder.

First, I searched the reference site to see how sound is generated. I will use only three sine waves that are comfortable to listen to, similar to a drone sound.

![soundref](img/soundref.png)

What I realized from looking at this reference is that I need to set the waveform first, then the frequency, and finally the output level. And then I tried it exactly as the reference showed. I named the background music oscillator ‘bgOsc.’ and I used the following code and played the frequency corresponding to C3, and it worked properly.

`
let bgOsc
bgOsc = new p5.Oscillator('sine');
bgOsc.freq(130.81);
bgOsc.amp(0.2)
`

I added additional frequencies and created the following code to make it easy to understand. 

`
let cam;
let mic;
let smoothVol = 0;
let bgOsc1, bgOsc2, bgOsc3;
function setup() {
  createCanvas(640, 480);
  pixelDensity(1);
  cam = createCapture(VIDEO);
  cam.size(320, 240);
  cam.hide();
  mic = new p5.AudioIn();
  mic.start();
  noStroke();
  rectMode(CORNER);
  frameRate(30);
  // bgOsc only
  bgOsc1 = new p5.Oscillator('sine');
  bgOsc2 = new p5.Oscillator('sine');
  bgOsc3 = new p5.Oscillator('sine');
  bgOsc1.freq(130.81);
  bgOsc2.freq(293.66);
  bgOsc3.freq(493.88);
  bgOsc1.amp(0.03);
  bgOsc2.amp(0.025);
  bgOsc3.amp(0.02);
  bgOsc1.start();
  bgOsc2.start();
  bgOsc3.start();
}
function draw() {
  background(0);
  let vol = mic.getLevel();
  smoothVol = lerp(smoothVol, vol, 0.1);
  // sound → mosaic decreasing
  let mosaicSize = int(map(smoothVol, 0, 0.3, 60, 1, true));
  cam.loadPixels();
  if (cam.pixels.length === 0) return;
  push();
  translate(width, 0);
  scale(-1, 1);
  if (mosaicSize > 2) {
    // Mosaic mode
    for (let y = 0; y < cam.height; y += mosaicSize) {
      for (let x = 0; x < cam.width; x += mosaicSize) {
        let i = (y * cam.width + x) * 4;
        let r = cam.pixels[i];
        let g = cam.pixels[i + 1];
        let b = cam.pixels[i + 2];
        fill(r, g, b);
        rect(x * 2, y * 2, mosaicSize * 2, mosaicSize * 2);
      }
    }
  } else {
    // Clear mode
    image(cam, 0, 0, width, height);
  }
  pop();
}
`

To make a beeping sound when the mosaic disappears, I first added a beep oscillator to the code.

`
let beepOsc;
beepOsc = new p5.Oscillator('sine');
beepOsc.freq(880);
beepOsc.amp(0.2);
beepOsc.start();
`

To make the beep sound play when the mosaic disappears, I tried using the true/false functionality of the if statement that we learned in class. First, for convenience in handling variable states, I set the state in which bgOsc is playing to ‘bgOn’ and set it to true, and the state in which beepOsc is playing to ‘beepOn’ and set it to false.

`
let bgOn = true;
let beepOn = false;
`

And I tried the following, but it didn’t work.

`
    if (beepOn) {
      beepOn = false;
    }
    if (!bgOn) {
      bgOn = true;
    }
  } else {
    image(cam, 0, 0, width, height);
    if (!beepOn) {
      beepOn = true;
    }
    if (bgOn) {
      bgOn = false;
    }
  }
`

I asked ChatGPT why it wasn’t working.

![tf](img/tf.png)

I realized that I had forgotten to set the amplitude output. After assigning an appropriate value, it worked properly.

In this part, I added a feature that turns the beep oscillator and the background oscillators on and off depending on the situation. First, when beepOn is true inside the if statement, the code beepOsc.amp(0, 0.3); slowly lowers the beep sound’s volume down to 0 over 0.3 seconds, effectively turning it off. Then, by setting beepOn = false;, I marked that the beep sound is now in the “off” state.

Next, when bgOn is false, the three background oscillators gradually increase their amp values to 0.03, 0.025, and 0.02 over 0.5 seconds. This creates a state where, when the mosaic effect is fully cleared, the background sound turns on while the beep sound stays off.

On the other hand, the else section runs when the mosaic effect appears again. The line image code displays the actual webcam video, and if beepOn is false, the code beepOsc.amp(0.2, 0.2); raises the beep sound’s volume to 0.2 over 0.2 seconds, making the beep sound come back. Then beepOn = true; marks that the beep sound is now turned on.

Finally, when bgOn is true, all three background oscillators gradually reduce their volume to 0 over 0.5 seconds, turning the background sound off, and bgOn = false; updates that state. In other words, when the mosaic appears again, the beep sound turns on, and the background sound turns off, completing the intended behavior.

`
if (beepOn) {
      beepOsc.amp(0,0.3);
      beepOn = false;
    }
    if (!bgOn) {
      bgOsc1.amp(0.03, 0.5);
      bgOsc2.amp(0.025, 0.5);
      bgOsc3.amp(0.02, 0.5);
      bgOn = true;
    }
  } else {
    image(cam, 0, 0, width, height);
    if (!beepOn) {
      beepOsc.amp(0.2, 0.2);
      beepOn = true;
    }
    if (bgOn) {
      bgOsc1.amp(0, 0.5);
      bgOsc2.amp(0, 0.5);
      bgOsc3.amp(0, 0.5);
      bgOn = false;
    }
  }
`

###Through this process, I learned to use the if-statement more flexibly and gained an understanding of how sound generation works.

##Project3: Meet myself longer(Best)

###In this process, I will keep the mosaic fully cleared for about three seconds.

I had no idea how to keep the mosaic in its cleared state at first, so I searched for the code ‘stop’ on the reference site.

![stop](img/stop.png)

However, the stop function was only for stopping audio and couldn’t be used to freeze the video. So I asked ChatGPT.

![hts](img/hts.png)

And I used this code as it said. 

This code is designed to keep the mosaic completely removed for three seconds once it disappears. First, I created a variable called cleanMode, which stores whether the mosaic is currently in a fully cleared state. I also used cleanUntil to record the time until which this clean mode should be maintained.

If millis() is still less than cleanUntil, meaning the set time hasn’t passed yet, the code keeps cleanMode set to true. This means “don’t bring the mosaic back yet—three seconds haven’t passed, so keep the screen clean.” On the other hand, if millis() is greater than cleanUntil, then cleanMode only becomes true when mosaicSize is 2 or below, which represents the moment when the mosaic blocks become extremely small and the screen appears fully clear.

If cleanMode becomes true and millis() is greater than cleanUntil at the same time, the code updates cleanUntil to millis() + 3000, which sets a new end time three seconds into the future. This way, the moment the mosaic fully disappears, cleanMode turns on, and even if the volume drops afterward, the mosaic won’t return for three full seconds.

Overall, this code detects the moment when the mosaic becomes completely clear and smoothly keeps that state for three seconds.

`
let cleanMode = false;
let cleanUntil = 0;    
 if (millis() < cleanUntil) {
    cleanMode = true;
  } else {
    cleanMode = (mosaicSize <= 2);
  }
  if (cleanMode && millis() >= cleanUntil) {
    cleanUntil = millis() + 3000;  
  }
`

So this is my final code.

`
let cam;
let mic;
let smoothVol = 0;
let bgOsc1, bgOsc2, bgOsc3;
let beepOsc;
let bgOn = true;
let beepOn = false;
let cleanMode = false; 
let cleanUntil = 0; 
function setup() {
  createCanvas(640, 480);
  pixelDensity(1);
  cam = createCapture(VIDEO);
  cam.size(320, 240);
  cam.hide();
  mic = new p5.AudioIn();
  mic.start();
  noStroke();
  rectMode(CORNER);
  frameRate(30);
  bgOsc1 = new p5.Oscillator('sine');
  bgOsc2 = new p5.Oscillator('sine');
  bgOsc3 = new p5.Oscillator('sine');
  bgOsc1.freq(130.81);
  bgOsc2.freq(293.66);
  bgOsc3.freq(493.88);
  bgOsc1.amp(0.03);
  bgOsc2.amp(0.025);
  bgOsc3.amp(0.02);
  bgOsc1.start();
  bgOsc2.start();
  bgOsc3.start();
  beepOsc = new p5.Oscillator('sine');
  beepOsc.freq(880);
  beepOsc.amp(0);
  beepOsc.start();
}
function draw() {
  background(0);
  let vol = mic.getLevel();
  smoothVol = lerp(smoothVol, vol, 0.1);
  let mosaicSize = int(map(smoothVol, 0, 0.1, 60, 1, true));
  cam.loadPixels();
  if (cam.pixels.length === 0) return;
  if (millis() < cleanUntil) {
    cleanMode = true;
  } else {
    cleanMode = (mosaicSize <= 2);
  }
  if (cleanMode && millis() >= cleanUntil) {
    cleanUntil = millis() + 3000; 
  }
  push();
  translate(width, 0);
  scale(-1, 1);
  if (!cleanMode) {
    for (let y = 0; y < cam.height; y += mosaicSize) {
      for (let x = 0; x < cam.width; x += mosaicSize) {
        let i = (y * cam.width + x) * 4;
        fill(cam.pixels[i], cam.pixels[i+1], cam.pixels[i+2]);
        rect(x * 2, y * 2, mosaicSize * 2, mosaicSize * 2);
      }
    }
    if (beepOn) {
      beepOsc.amp(0, 0.3);
      beepOn = false;
    }
    if (!bgOn) {
      bgOsc1.amp(0.03, 0.5);
      bgOsc2.amp(0.025, 0.5);
      bgOsc3.amp(0.02, 0.5);
      bgOn = true;
    }
  } else {
    image(cam, 0, 0, width, height);
    if (!beepOn) {
      beepOsc.amp(0.2, 0.2);
      beepOn = true;
    }
    if (bgOn) {
      bgOsc1.amp(0, 0.5);
      bgOsc2.amp(0, 0.5);
      bgOsc3.amp(0, 0.5);
      bgOn = false;
    }
  }
  pop();
}
`

##Create a website

I had no information about how to create a website, and I wanted to design it in a minimal style. So I told ChatGPT what I wanted and asked it to create it for me.

![html](img/html.png)

However, although it worked well in p5.js, it didn’t work on the website. So I asked ChatGPT about it.

First, I used meta charset="UTF-8" to prevent special characters from breaking, and I set the page title using the title tag. Then, by using text-align: center;, I aligned all the text and elements to the center.

![micsolve](img/micsolve.png)

I tried making the changes ChatGPT suggested, but it still didn’t work.

`
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Meet Myself Through Shout</title>
  <!-- p5.js (version 1.11.10, as you confirmed works) -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.11.10/p5.min.js"></script>
  <!-- Your sketch file -->
  <script src="sketch.js" defer></script>
  <style>
    body {
      background: #111;
      color: white;
      font-family: "Helvetica", sans-serif;
      text-align: center;
      margin: 0;
      padding: 40px 20px;
    }
    h1 {
      font-size: 2.4rem;
      margin-bottom: 10px;
    }
    p.description {
      max-width: 650px;
      margin: 0 auto 25px auto;
      font-size: 1.1rem;
      line-height: 1.6;
      opacity: 0.9;
    }
    hr {
      width: 70%;
      margin: 40px auto;
      border: 0;
      border-top: 1px solid #444;
    }
    #canvas-container {
      margin-top: 30px;
    }
  </style>
</head>
<body>
  <h1>Meet Myself Through Shout</h1>
  <p class="description">
    The emotions you’ve hidden, the voice you’ve been suppressing.<br>
    The moment the volume rises, your true self, once concealed, awakens.
  </p>
  <hr />
  <!-- p5.js canvas will be placed here -->
  <div id="canvas-container"></div>
</body>
</html>
`

But when I opened it on my phone, it worked. So I asked ChatGPT about it.

![opw](img/opw.png)

ChatGPT explained that on a PC, only button clicks are recognized as gestures, so it wouldn’t work. It suggested adding the code shown below, so I tried it.

![add](img/add.png)

Finally, it worked! The issue was that browsers like Chrome block microphone input unless the user clicks first. Once I addressed that, the problem was resolved.

###Through this process, I learned the basic structure of HTML and how to design a webpage, and I also figured out how to handle microphone input.
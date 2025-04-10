Welcome back to the 3D renderer series. Today we're going to explore what it means to play full-screen with SDL3.

First, I'll simply change the size of the window and remove the borders. This is what we refer to as "fake" full-screen. Then I'll show you how to request a change the video mode to full screen so that it actually scales our desired resolution to full screen.

Let's get started.

## Fake Full Screen
Often referred to as "fake" full-screen is where we query for the full dimensions of the display mode and make the window that size, without borders.

### Removing Borders
First, I provided an additional property for the window to be displayed without borders.
```d
//
```

Now here's what the window looks like when we run.
![Borderless Window]()

## Setting The Display Mode
What we really want is to scale our lower resolution to the full extent of the monitor. Changing the resolution to be full screen would defeat our purpose.

I reset the window width and height back to 800x600. The video mode can be set to full screen using `SDL_SetWindowFullscreen` like so:
```c
//
```

The resulting window should look the same, but we are now utilizing the full screen mode with our intended resolution.

## Make It Configurable
I find it a bit too jarring for the window to be full screen during development. Instead, I created a debug variable I can use so the window will have borders and be a standard size.
```c
//
```

Hopefully, you found this helpful. Thanks for reading.

Until next time.

Take care.  
Stay awesome.

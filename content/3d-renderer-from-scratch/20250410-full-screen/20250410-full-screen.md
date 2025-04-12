Welcome back to the 3D renderer series. Today we're going to explore what it means to play full-screen with SDL3.

First, I'll simply change the size of the window and remove the borders. This is what we refer to as "fake" full-screen. Then I'll show you how to request a change the video mode to full screen so that it actually scales our desired resolution to full screen.

Let's get started.

## Fake Full Screen
Often referred to as "fake" full-screen is where we query for the full dimensions of the display mode and make the window that size, without borders.

### Removing Borders
First, I provided an additional property for the window to be displayed without borders.
```c
SDL_SetBoolProperty(window_properties, SDL_PROP_WINDOW_CREATE_BORDERLESS_BOOLEAN, true);
```

### Using Display Mode
We need to get the full dimensions of our monitor to set our window x and y values. To do this, I use the display mode from SDL. Here I get the displays available to the system and then pass the first one in the array to `SD_GetCurrentDisplayMode`.
```c
SDL_DisplayID* displays = SDL_GetDisplays(NULL);
SDL_DisplayMode* display_mode = SDL_GetCurrentDisplayMode(displays[0]);
if (!display_mode)
{
    SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
    return false;
}
window_width = display_mode->w;
window_height = display_mode->h; 
```

Now here's what the window looks like.
![Fake Full Screen Window]()


## Full Screen Mode
While technically this is a full screen window, of course, it's not really what we want in our case. We don't want to change our render dimenisons, instead, we want to scale our lower resolution to the full extent of the monitor.

Let's remove the display mode code added earlier and reset the window width and height back to 800x600. The video mode can be set to full screen using `SDL_SetWindowFullscreen` at the end of the `initialize_window` function like so:
```c
SDL_SetWindowFullscreen(window, true);
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

Welcome back to the 3D renderer series. Today we're going to explore what it means to create a full-screen window with SDL3.

Let's get started.

## Fake full-screen
Often referred to as "fake" full-screen is when we query for the full dimensions of the display mode and make the window that size without borders.

### Removing Borders
First, I'll provided an additional property for the window to be displayed without borders.
```c
SDL_SetBoolProperty(window_properties, SDL_PROP_WINDOW_CREATE_BORDERLESS_BOOLEAN, true);
```

![Borderless Window]()

### Using Display Mode
We need to get the full dimensions of our monitor to set our window x and y values. To do this, I use the display mode from SDL. Here I get the displays available to the system and then pass the first one in the array to `SD_GetCurrentDisplayMode`. The returned `SDL_DisplayMode` can be used to access the width and height of the window.
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

Now when we run the window will be "fake" full-screen. 

## full-screen Mode
While technically this is a full-screen window, it's not really what we want in our case. We don't want to change our render dimensions, instead, we want to scale our lower resolution to the full extent of the monitor.

Let's remove the display mode code added earlier and reset the window width and height back to 800x600. The video mode can be set to full-screen using `SDL_SetWindowFullscreen`. Add this to the end of the `initialize_window` function:
```c
    ...
    renderer = SDL_CreateRenderer(window, NULL);
    if (!renderer) {
        SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
        return false;
    }

    SDL_SetWindowFullscreen(window, true);

    return true;
}
```

The resulting window should look the same, but we are now utilizing the full-screen mode with our intended resolution.

## full-screen on Release 
During development, I'd rather have the window actually be 800x600. Now that we have full-screen working, we can make that happen only in release mode by wrapping the call to `SDL_SetWIndowFullscreen` in a preprocessor if condition.

Visual Studio on Windows already has a few preprocessor definitions defined for `Debug` and `Release` configurations. In release builds there will be one named `NDEBUG` I'll use.
```c
#if NDEBUG 
    SDL_SetWindowFullscreen(window, true);
#endif
```

You could also bring back the window borders in debug the same way using `DEBUG` if you'd like.
```c
    ...
    SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_X_NUMBER, SDL_WINDOWPOS_CENTERED);
    SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_Y_NUMBER, SDL_WINDOWPOS_CENTERED);
    SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_WIDTH_NUMBER, window_width);
    SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_HEIGHT_NUMBER, window_height);

#if DEBUG 
    SDL_SetBooleanProperty(window_properties, SDL_PROP_WINDOW_CREATE_BORDERLESS_BOOLEAN, true);
#endif
    
    window = SDL_CreateWindowWithProperties(window_properties);
    ...
```

![Final Border Window]()

I hope you found this helpful. 

Until next time.

Take care.  
Stay awesome.

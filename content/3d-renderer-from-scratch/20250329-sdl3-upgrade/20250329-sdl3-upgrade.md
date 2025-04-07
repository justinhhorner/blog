Welcome to another entry to the 3D renderer series. Today I'm documenting my switch from SDL2 to SDL3.  We'll tackle this first on Windows, then macOS and Linux. Let's get to it.

## Windows
Since I keep the SDL include and lib files local to the `msvs` project for Windows, I downloaded `SDL3-devel-3.2.8-VC.zip` file from [GitHub](https://github.com/libsdl-org/SDL/releases/tag/release-3.2.8).

I added the files to `msvs\lib\sdl\include\SDL3\include` and `msvs\lib\sdl\lib\x86`.

## macOS/Linux
Of course, everything is easier on macOS and Linux. Just run the following commands in the terminal.

```bash
# macOS
brew install sdl3

# Linux
sudo apt install libsdl3-dev
```

## Changes
First, don't forget change our include to `SDL3/SDL.h`.

### Logging
Everywhere I am using `fprintf` I've changed to `SDL_Log` like so:
```c
SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
```

### Initialize SDL
Currently we are initializing every SDL subsystem with `SDL_Init`. As part of this upgrade, I'm going to change this to only initialize the video subsystem. This now returns a `bool` instead of an `int`, so we no longer have to check against 0.
```c
if (!SDL_InitSubSystem(SDL_INIT_VIDEO)) {
    SDL_Log("Couldn't initialize SDL: %s", SDL_GetError());
    return false;
}
```

### Create Window
To create a window with SDL 3 we can use the `SDL_CreateWindowWithProperties` and provide it with the configuration we want. First, we make sure we can successfully create a `SDL_PropertiesID`, which is a typedef for a Uint32.
```c
SDL_PropertiesID window_properties = SDL_CreateProperties();
if (!window_properties) {
    SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
    return false;
}
```

We set integer properties by using `SDL_SetNumberProperty`. This is our window properties for a centered position and using our width and height.
```c
SDL_PropertiesID window_properties = SDL_CreateProperties();
if (!window_properties) {
    SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
    return false;
}

SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_X_NUMBER, SDL_WINDOWPOS_CENTERED);
SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_Y_NUMBER, SDL_WINDOWPOS_CENTERED);
SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_WIDTH_NUMBER, window_width);
SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_HEIGHT_NUMBER, window_height);

window = SDL_CreateWindowWithProperties(window_properties);
```

### Create Renderer
There's a slight change to the API for `SDL_CreateRenderer` in that we no longer need to provide it with an int for the index of the rendering driver. Before we passed -1 which will initialize the first one found that supports the flags.

Now the method has two parameters, an SDL_Window and a const char* for the name of the rendering driver. We can provide `NULL` for SDL to choose the best option based on the system.
```c
renderer = SDL_CreateRenderer(window, NULL);
```

### Event Type
There are a few changes to the naming for the events we're using.
```c
switch (event.type) {
    case SDL_EVENT_QUIT:
        is_running = false;
        break;
    case SDL_EVENT_KEY_DOWN:
        if (event.key.key == SDLK_ESCAPE)
            is_running = false;
        break;
```

### Quit Subsystem
Finally, I'm calling `SDL_QuitSubSystem` in `destroy_window` as part of cleanup.
```c
void destroy_window(void) {
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_QuitSubSystem(SDL_INIT_VIDEO);
    SDL_Quit();
}
```

### Summary
With that done, we're now running the latest stable version of SDL3. Here's the resulting code after the upgrade.
```c
#include <stdio.h>
#include <stdbool.h>
#include <SDL3/SDL.h>

SDL_Window* window = NULL;
SDL_Renderer* renderer = NULL;

bool is_running = false;
int window_width = 800;
int window_height = 600;

bool initialize_window(void) {
    if (!SDL_InitSubSystem(SDL_INIT_VIDEO)) {
        SDL_Log("Couldn't initialize SDL: %s", SDL_GetError());
        return false;
    }

    SDL_PropertiesID window_properties = SDL_CreateProperties();
    if (!window_properties) {
        SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
        return false;
    }
    
    SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_X_NUMBER, SDL_WINDOWPOS_CENTERED);
    SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_Y_NUMBER, SDL_WINDOWPOS_CENTERED);
    SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_WIDTH_NUMBER, window_width);
    SDL_SetNumberProperty(window_properties, SDL_PROP_WINDOW_CREATE_HEIGHT_NUMBER, window_height);
    window = SDL_CreateWindowWithProperties(window_properties);

    if (!window) {
        SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
        return false;
    }

    renderer = SDL_CreateRenderer(window, NULL);
    if (!renderer) {
        SDL_Log("Unable to initialize SDL: %s", SDL_GetError());
        return false;
    }

    return true;
}

void destroy_window(void) {
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_QuitSubSystem(SDL_INIT_VIDEO);
    SDL_Quit();
}

void process_input(void) {
    SDL_Event event;
    SDL_PollEvent(&event);

    switch (event.type) {
        case SDL_EVENT_QUIT:
            is_running = false;
            break;
        case SDL_EVENT_KEY_DOWN:
            if (event.key.key == SDLK_ESCAPE)
                is_running = false;
            break;
}

void update(void) {
    // TODO: Something fun here :)
}

void render(void) {
    SDL_SetRenderDrawColor(renderer, 255, 0, 0, 255);
    SDL_RenderClear(renderer);

    // TODO: Render some graphics!

    SDL_RenderPresent(renderer);
}

int main(void) {
    is_running = initialize_window();

    while (is_running) {
        process_input();
        update();
        render();
    }

    destroy_window();
    return 0;
}
```

Until next time.

Take care.  
Stay awesome.

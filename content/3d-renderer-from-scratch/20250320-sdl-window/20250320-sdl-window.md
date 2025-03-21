Welcome to the next article in my 3D graphics programming from scratch series. Previously, we set up the project and called `SDL_Init` to make sure our include and library directories are found successfully.

This, along with all future articles in the series, are going to be quite code and math heavy. Hopefully, that's something you're excited about!

Today you and I are going to create a window that will work across macOS, Linux and even Windows using SDL2. 

Let's get started.

## Create a Window
The first thing we need is a function that will attempt to initialize the SDL resources we need for our window. If it fails, we can display an error and effectively quit the application.

I created a new function called `initialize_window` that accepts no parameters and returns `bool`.

"But wait..." I hear you say. "C doesn't have a bool type. We have to use 0 or 1". We'll, yes that's still an option, but we do have `stdbool` in the standard library now, which gives us access to a `bool` type that contains defines `true` and `false` macros. 

Of course, these are still 0 and 1 integers behind the scenes, but it makes our intent more obvious when using the `bool` type versus an int. I added the include to use it.
```c
#include <stdbool.h>
```

### Initialize SDL
Since we're already initializing SDL resources in this method, it makes sense to move our SDL_Init call here.
```c
if (SDL_Init(SDL_INIT_EVERYTHING) != 0) {
    fprintf(stderr, "Error initializing SDL.\n");
    return false;
}
```

### Create an SDL Window
Now attempt to create an SDL Window. This method takes the following parameters:
- Title - The title of the window in UTF-8 encoding
- X - The x position of the window
- y - THe y position of the window
- w - The width of the window, in screen coordinates
- h - The height of the window, in screen coordinates
- flags - 0, or one or more of the [SDL_WindowFlags](https://wiki.libsdl.org/SDL2/SDL_WindowFlags) OR'd together.

It returns a pointer to an `SDL_Window` if successful or NULL on failure.

I added an SDL_Window pointer variable and initialized it to NULL. Since I need to provide window width and height to create a window, I created two variables to store those.
```c
SDL_Window* window = NULL;
int window_width = 800;
int window_height = 600;
``` 

Now, inside the `initialize_window` function we can finally call [SDL_CreateWindow](https://wiki.libsdl.org/SDL2/SDL_CreateWindow).
```c
#include <stdio.h>
#include <stdbool.h>
#include <SDL2/SDL.h>

SDL_Window* window = NULL;
int window_width = 800;
int window_height = 600;

bool initialize_window(void) {
    if (SDL_Init(SDL_INIT_EVERYTHING) != 0) {
        fprintf(stderr, "Error initializing SDL.\n");
        return false;
    }

    // Create a new SDL Window
    window = SDL_CreateWindow(
        "3D Renderer",
        SDL_WINDOWPOS_CENTERED, 
        SDL_WINDOWPOS_CENTERED,
        window_width,
        window_height,
        0
    );
    if (!window) {
        fprintf(stderr, "Error creating window");
        return false;
    }
}
...
```

A few things to note here. I'm passing `SDL_WINDOWPOS_CENTERED` for both X and Y parameters. I'm passing 0 for flags since I'm not interested in any of them right now. We display an error if we don't get a valid window pointer back and return false to exit the application.

Now we have a window, but what do we do with it? We need a way to be able to set what we want the window to render. To do that, we first need to create a render that will be associated to our window.

## Create an SDL Renderer
Now we need to create an [SDL_Renderer](https://wiki.libsdl.org/SDL2/SDL_Renderer). This is very similar to how we created our window, so I'll skip some of the redundant info. I added a variable for the SDL_Renderer pointer and initialized it to 0; 
```c
#include <stdio.h>
#include <stdbool.h>
#include <SDL2/SDL.h>

SDL_Window* window = NULL;
SDL_Renderer* renderer = NULL; // <- New renderer variable
```

We create an `SDL_Renderer` with, you guessed it, [SDL_CreateRenderer](https://wiki.libsdl.org/SDL2/SDL_CreateRenderer). It has the following parameters:
- Window - The window where rendering is displayed
- Index - The index of the rendering driver to initialize , or -1 to initialize the first one supporting the requested flags
- Flags - 0, or one or more [SDL_RendererFlags](https://wiki.libsdl.org/SDL2/SDL_RendererFlags) OR'd together

```c
bool initialize_window(void) {
    ...

    // Create an SDL Renderer associated with our window
    renderer = SDL_CreateRenderer(window, -1, 0);
    if (!renderer) {
        fprintf(stderr, "Error creating renderer");
        return false;
    }

    return true;
}
```

With that, our initialize window function is complete. Now, let's update the main function to call `initialize_window` and start the game loop if it was successful.

## Update Main Function
Back in the main function, I deleted the code that was there and add a call to `initialize_window`, storing the return value in a variable `is_running`, which I will also declare and initialize at the top.

```c
bool is_running = false;
int window_width = 800;
int window_height = 600;
...
int main(void) {
    is_running = initialize_window();

    return 0;
}
```

Next, I added a loop that will continue to execute while `is_running` is true.

## Game Loop
If we want to keep a window open and responsive, we need to add our game loop in main. Generally speaking, a game loop will have a few major concerns; process input, update the game world, and render the results. I made a few stub functions to execute within the game loop.

```c
int main(void) {
    is_running = initialize_window();

    while (is_running) {
        process_input();
        update();
        render();
    }
    
    return 0;
}
```

### Process Input
Since we're soon going to be running the game full screen, it would be nice to support exiting via the Escape key. Again, we'll use SDL for input handling to achieve this.

I used an instance of SDL_Event to pass to [SDL_PollEvent](https://wiki.libsdl.org/SDL2/SDL_PollEvent). Once we have the data in our structure we can switch on the type and if it's key down and `SDLK_Escape` we set `is_running` to false to quite the application.

```c
void process_input(void) {
    SDL_Event event;
    SDL_PollEvent(&event);

    switch (event.type) {
        case SDL_QUIT:
            is_running = false;
            break;
        case SDL_KEYDOWN:
            if (event.key.keysym.sym == SDLK_ESCAPE)
                is_running = false;
            break;

    }
}
```
Notice I also added a case for `SDL_Quit` because without it the user will have no way to close the window themselves.


### Update
We're not currently doing anything that needs updating, so all we need is an empty function for now.
```c
void update(void) {
    // TODO: Something fun here :)
}
```


### Render
For now we just want to test by rendering a single color in the window. For that, we'll use [SDL_SetRendererDrawColor](https://wiki.libsdl.org/SDL3/SDL_SetRenderDrawColor), followed by [SDL_RenderClear](https://wiki.libsdl.org/SDL2/SDL_RenderClear) to clear the renderer with the drawing color.

Finally, I call [SDL_RenderPresent](https://wiki.libsdl.org/SDL2/SDL_RenderPresent) which will update the screen with the rendering we did since the previous call. If you understand how double buffering works in graphics you should recognize this pattern.


## Free Resources
We've created several resources that we should clean up once we no longer need them. I created a function called `destroy_window` that will take care of cleaning up all the resources we no longer need.

SDL provides destroy functions that I'll use. In future articles you see where we will also free resources we allocate ourselves with `malloc`.
```c
void destroy_window(void) {
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
}
```

We know our application is shutting down when we exit the game loop, so I'll call this function there.
```c
int main(void) {
    is_running = initialize_window();

    while (is_running) {
        process_input();
        update();
        render();
    }

    destroy_window();)
    return 0;
}
```

## Summary
With that we have a working responsive window that can be opened cross-platform and closed the the Escape key. We've also set ourselves up nicely to start our rendering work in following articles with a game loop and input handling.

Here's the final code listing after these changes.
```c
#include <stdio.h>
#include <stdbool.h>
#include <SDL2/SDL.h>

SDL_Window* window = NULL;
SDL_Renderer* renderer = NULL;

bool is_running = false;
int window_width = 800;
int window_height = 600;

bool initialize_window(void) {
    if (SDL_Init(SDL_INIT_EVERYTHING) != 0) {
        fprintf(stderr, "Error initializing SDL.\n");
        return false;
    }

    window = SDL_CreateWindow(
        NULL,
        SDL_WINDOWPOS_CENTERED, 
        SDL_WINDOWPOS_CENTERED,
        window_width,
        window_height,
        0
    );
    if (!window) {
        fprintf(stderr, "Error creating window");
        return false;
    }

    renderer = SDL_CreateRenderer(window, -1, 0);
    if (!renderer) {
        fprintf(stderr, "Error creating renderer");
        return false;
    }

    return true;
}

void destroy_window(void) {
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
}

void process_input(void) {
    SDL_Event event;
    SDL_PollEvent(&event);

    switch (event.type) {
        case SDL_QUIT:
            is_running = false;
            break;
        case SDL_KEYDOWN:
            if (event.key.keysym.sym == SDLK_ESCAPE)
                is_running = false;
            break;

    }
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

    destroy_window();)
    return 0;
}

```

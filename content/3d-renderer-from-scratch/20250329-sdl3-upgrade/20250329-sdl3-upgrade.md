Welcome to another entry to the 3D renderer series. Today I'm documenting my switch from SDL2 to SDL3. Luckily, there's not much to change in my case. 

We'll tackle this first on Windows, then macOS and Linux. Let's get to it.


## Windows
Since I keep the SDL include and lib files local to the `msvs` project for Windows, I downloaded the latest `SDL3-devel-3.2.8-VC.zip` file from [GitHub](https://github.com/libsdl-org/SDL/releases/tag/release-3.2.8).

I added the files to `msvs\lib\sdl\include\SDL3\include` and `msvs\lib\sdl\lib\x86`.

## macOS/Linux

Of course, everything is easier on macOS and Linux. Just run the following commands in the terminal.

```bash
# macOS
brew install sdl3

# Linux
sudo apt install libsdl3-dev
```


## API Changes

### ...
### ...
### ...


## Summary
With that done, we're now running the latest stable version of SDL for our project.

Until next time.

Take care.  
Stay awesome.

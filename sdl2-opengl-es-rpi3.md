> Hi! Can you show me please an SDL2 and OpenGL ES example code that can run on a Raspberry PI 3?
 I'm glad to help you get started with an SDL2 and OpenGL ES example for a Raspberry Pi 3! Here's a simple example that creates a window, initializes OpenGL ES, and clears the screen with a red color.

Please note that this is a basic example and doesn't include error checking or more advanced features. You'll need to install the required libraries on your Raspberry Pi 3 and set up the project accordingly.

First, install the necessary dependencies:

```bash
sudo apt-get update
sudo apt-get install libsdl2-dev libsdl2-image-dev libsdl2-ttf-dev libopenal-dev openal-soft-dev libglew-dev libopengl-dev
```

Now let's create the source files:

main.cpp

```cpp
#include <SDL.h>
#include <SDL_opengl.h>
#include <GL/gl.h>
#include <GL/glew.h>

int main(int argc, char* argv[]) {
    SDL_Window* window = NULL;
    SDL_GLContext gl_context = NULL;

    if (SDL_Init(SDL_INIT_VIDEO | SDL_INIT_OPENGL) != 0) {
        printf("Failed to initialize SDL: %s\n", SDL_GetError());
        return 1;
    }

    SDL_GL_SetAttribute(SDL_ATTR_CONTEXT_FLAGS, 0);
    SDL_GL_SetAttribute(SDL_ATTR_CONTEXT_MAJOR_VERSION, 2);
    SDL_GL_SetAttribute(SDL_ATTR_CONTEXT_MINOR_VERSION, 0);
    SDL_GL_SetAttribute(SDL_ATTR_OPENGL_PROFILE, SDL_GL_PROFILE_ES);
    SDL_GL_SetAttribute(SDL_ATTR_OPENGL_RENDERER, "ES2 MSCC");

    window = SDL_CreateWindow("OpenGL ES Example", SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, SDL_WINDOW_OPENGL | SDL_WINDOW_RESIZABLE);
    if (window == NULL) {
        printf("Failed to create window: %s\n", SDL_GetError());
        SDL_Quit();
        return 1;
    }

    gl_context = SDL_GL_CreateContext(window);
    if (gl_context == NULL) {
        printf("Failed to create OpenGL context: %s\n", SDL_GetError());
        SDL_DestroyWindow(window);
        SDL_Quit();
        return 1;
    }

    glewInit();

    glViewport(0, 0, 800, 600);
    glClearColor(1.0f, 0.0f, 0.0f, 1.0f);

    SDL_Event event;
    while (true) {
        while (SDL_PollEvent(&event)) {
            if (event.type == SDL_QUIT) {
                break;
            }
        }

        glClear(GL_COLOR_BUFFER_BIT);
        SDL_GL_SwapWindow(window);
    }

    SDL_GL_DeleteContext(gl_context);
    SDL_DestroyWindow(window);
    SDL_Quit();

    return 0;
}
```

Now, compile and run the code:

```bash
g++ -std=c++11 -o main main.cpp -lSDL2 -lSDL2_image -lSDL2_ttf -lGLEW -lOpenAL -lOpenGL
./main
```

This should create and run a simple OpenGL ES application on your Raspberry Pi 3, clearing the screen with red color. You can further extend this example by adding more OpenGL ES functions, shaders, and other features as needed.

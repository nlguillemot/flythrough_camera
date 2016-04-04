# flythrough_camera

![demonstration](https://j.gifs.com/mZNvBG.gif)

Single-header single-function C/C++ immediate-mode camera for your graphics demos

Just call `flythrough_camera_update` once per frame.

# example

Below is a fully-functional example program that works under command prompt.

Create a new Visual Studio project, drop this file in it, and it should just work.

```
#define FLYTHROUGH_CAMERA_IMPLEMENTATION
#include "flythrough_camera.h"

#include <Windows.h>
#include <stdio.h>

int main()
{
    printf("flythrough_camera test:\n");
    printf("hold right click, then move the mouse and press WASD/space/left ctrl\n");

    float pos[3] = { 0.0f, 0.0f, 0.0f };
    float look[3] = { 0.0f, 0.0f, 1.0f };
    const float up[3] = { 0.0f, 1.0f, 0.0f };

    LARGE_INTEGER then, now, freq;
    QueryPerformanceFrequency(&freq);
    QueryPerformanceCounter(&then);

    POINT oldcursor;
    GetCursorPos(&oldcursor);

    while (!GetAsyncKeyState(VK_ESCAPE))
    {
        QueryPerformanceCounter(&now);
        float delta_time_sec = (float)(now.QuadPart - then.QuadPart) / freq.QuadPart;

        POINT cursor;
        GetCursorPos(&cursor);

        // only move and rotate camera when right mouse button is pressed
        float activated = GetAsyncKeyState(VK_RBUTTON) ? 1.0f : 0.0f;

        float view[16];
        flythrough_camera_update(
            pos, look, up, view,
            delta_time_sec,
            100.0f * (GetAsyncKeyState(VK_LSHIFT) ? 2.0f : 1.0f) * activated,
            0.5f * activated,
            80.0f,
            cursor.x - oldcursor.x, cursor.y - oldcursor.y,
            GetAsyncKeyState('W'), GetAsyncKeyState('A'), GetAsyncKeyState('S'), GetAsyncKeyState('D'),
            GetAsyncKeyState(VK_SPACE), GetAsyncKeyState(VK_LCONTROL),
            FLYTHROUGH_CAMERA_LEFT_HANDED_BIT);

        if (activated)
        {
            printf("\n");
            printf("pos: %f, %f, %f\n", pos[0], pos[1], pos[2]);
            printf("look: %f, %f, %f\n", look[0], look[1], look[2]);
            printf("view: %f %f %f %f\n"
                   "      %f %f %f %f\n"
                   "      %f %f %f %f\n"
                   "      %f %f %f %f\n",
                 view[0],  view[1],  view[2],  view[3],
                 view[4],  view[5],  view[6],  view[7],
                 view[8],  view[9],  iew[10], view[11],
                view[12], view[13], view[14], view[15]);
            
            Sleep(100);
        }

        then = now;
        oldcursor = cursor;
    }

    return 0;
}
```

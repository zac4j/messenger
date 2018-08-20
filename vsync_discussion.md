## Async Discussion

#### [Understanding VSYNC][vsync]
+ refresh rate:
	how many times a screen can update its display per second.
	This value is specified in hertz like 60Hz and constant based on the hardware.
+ frame rate:
	how many times GPU can draw a frame per second, such as 30fps, higher
	is always better.
+ some scenario:
	- frame rate > refresh rate:seeing some visual problems, known as tearing.
		A solution is double buffering, effectively, when a GPU is done drawing a frame to memory(known as the back buffer),it will copy it to a secondary area of memory known as the frame buffer, when it draws the next frame, it'll begin filling in the back buffer while the frame buffer remains untouched,when the screen comes along to refresh, it'll do so from the frame buffer, which isn't in the middle of a draw operation.This is where VSYNC(vertical synchronization) comes in.VSYNC will basically keep this copy operation from back buffer to frame buffer form happening if the screen is in the middle of refreshing from it.
		Having a frame rate higher than a refresh rate is ideal, since your GPU is refreshing faster than your screen is reading, In this scenario, after a successful screen refresh,your GPU will be VSYNCed waiting for the next screen refresh to occur.
	- frame rate < refresh rate:seeing a smooth animation broke up with sudden pauses,follwoed by the rest of the smooth animation.Always described as jank or hitching or lag or stutter.



[vsync]:https://www.youtube.com/watch?v=1iaHxmfZGGc
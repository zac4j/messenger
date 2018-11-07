## Understanding the Mobile Context

#### Smooth Frame Rate
The jank-free experience we shoot for Android is sub-16 millisecond(ms) frame times. So, applications need to be able to process input, layout, draw ad do anything else required to display the next frame on the screen within that 16ms window. This speed allows the system to render at 60 (1000 / 16 = 62.5)frame per second(fps) during animations and input gestures.

Animations must be able to repaint the necessary parts of the screen at 60fps to achieve perceptibly smooth motion. Less than this rate is detectable by the user as slow, or jerky animation.

Hitting a smooth frame rate means any particular frame needs to execute all rendering code(including the framework processing the rendering commands to send theme to the GPU and the GPU drawing them into the buffer which is then shown on the screen) within 16ms.

#### Dalvik & ART
+ Before Lollipop is Dalvik, after is ART.
+ 30-200+% performance improving through several mechanisms.
+ **moving collector**
	-	Dalvik is not a moving collector, this means that all allocated objects will stay at the same location in the heap, which makes it harder and more time-consuming for Dalvik to find free memory for new objects, especially as the heap is more populated and fragmented over time and whenever there are large objects for which it must find room.Heap fragmentation can also lead to more frequent GC pauses as Dalvik attempts to clear the heap from dead objects. These GC pauses are quite expensive and can easily take 10–20ms on fast devices under normal circumstances.
	- ART is moving collector, it is able to compact the heap when a long pause in the application won’t impact user experience. Also, there is a separate heap for large objects like bitmaps, making it faster to find memory for these large objects without wading through the potentially fragmented regular heap. Pauses in ART are regularly in the realm of 2–3ms.

#### UI Thread & Render Thread
+ Android is a single-threaded UI system, where all operations that happen on views, including drawing those views, happen on the single UI thread in the Activity. Anything else that happens on this same thread while the view hierarchy is trying to draw can cause jank because it simply doesn’t have time to do it all in the limited 16 ms it has to achieve a smooth framerate.
+ Lollipop introduced the "Render Thread", which is now responsible for sending the actual rendering operations to the GPU. This thread offloads some of the processing off of the UI thread.
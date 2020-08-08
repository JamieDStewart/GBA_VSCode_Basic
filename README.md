# GBA_VSCode_Basic
A 'simple' Game Boy Advance development setup using Visual Studio Code and relying on devkitPro and devkitARM to do the heavy lifting.
**If You're on OS X there's a bit at the bottom of this you should read**

## Dependencies

To get this project to compile there are some dependancies that need to be taken into consideration.
Obviously you will need a GBA emulator set up on your sytem, I tend to flip flop between using NO$GBA or lately mGBA. At the minute I use mGBA as it hooks up nicely with gdb debugging. Which I've configured this VS Code set up to make use of, so get a copy of [mGBA](https://mgba.io/downloads.html) (I'm using Version 0.6.2 as version after this at the time of release weren't working correctly with GDB).

### devkitPro

You will need to have devkitPro with devkitARM installed, this can easily be carried out by heading over to the [devkitPro Installer page](https://github.com/devkitPro/installer/releases) and getting the most current version for your development platform.
Try to avoid installing devkitPro to a directory or sub directory with long path names. I tend to install devkitPro to the root of my data drive.
After installing it, you need to **add "%DEVKITPRO%\tools\bin" to the Path**. The installation seems to no make this and is necessary for the debugger to work correctly.
Once devKitPro has done it's thing you can move on to getting VS Code set up.

### VS Code Extensions

 To make life easier install the following extensions 
  [ARM Extension](https://github.com/dan-c-underwood/vscode-arm)
  [C/C++](https://github.com/Microsoft/vscode-cpptools)
  [C++ Intellisense](https://github.com/austin-----/code-gnu-global)
  
### .vscode file modifications

**c_cpp_properties.json** - line 54 of this file may need to be modified. You can modify this line of code to point to the *arm-none-eabi/include folder* within devkitPro/devkitARM/

**launch.json** - There are a few modifications to be made here, again surrounding where you have installed devkitPro to on your machine. 
  - Line 19 the location that *miDebuggerPath* points to will need to change to reflect your devkitPro installation folder path.
  
**tasks.json** - line 29 will need to change to reflect where you have installed mGBA. Only the first part of this line needs to change up to the call to the exe. The latter half of the line need not change. 
Line 35 may need to change, or you can remove the whole **run** label if you choose to. I sometimes use No$GBA as the source debugger is pretty great.

## Debugging the Game
Once all of the above changes have been squared away you should be able to make this project (ctrl+shift+p -> make debug) then press F5 to begin debugging, if all your paths are set up you should be good to go. There was an issue with *"The specified task cannot be tracked"* but pressing the button for "debug anyway" keeps the process going with no ill effects, this issue I've managedd to work around by having the launch task run as non-background and have set a simple sleep value in the shell command as a way to delay the process enough for mGBA to get up and running before attempting to connect with gdb, these didn't seem to be anyother way to do this as mGBA doesn't provide any hooks to inform when it has successfully started and I couldn't find any in the process history.
You should then be presented with a debug view, where you can navigate through the code line by line and track individual values and memory addresses.

Click on the video to view a youtube video going through this GitHub repo. 

[![alt text](https://github.com/JamieDStewart/GBA_VSCode_Basic/blob/master/images/running.PNG "Proof it works!")](https://youtu.be/Rj0lf46iljc "Video Guide")

## OS X Users
While this does 'work' on OS X it's not as perfect as I'd like. To get the gdb debugger to work correctly, make sure to get **mGBA version 0.6.1** as the latest version wasn't connecting properly for me. 
Make sure to codesign the gdb debugger, to do this you need to follow along with [this guide](https://medium.com/@royalstream/how-to-install-and-codesign-gdb-on-os-x-el-capitan-aab3d1172e95) although I ran into an isue with creating the certificate in the 'system' location and had to create it in 'login' then copy it over to the 'system' location (just drag and drop). Then in the final step of actually codesigning, target the devkitpro arm gdb : "codesign -s gdb-cert /opt/devkitpro/devkitARM/bin/arm-none-eabi-gdb"

Ok and now the bit I'm the least satisfied with, there seems to be an issue with VS Code an running mGBA as a background task, mGBA on OS X refuses to pass control back to the terminal to allow the debugger to launch this is no matter what I set the "isBackground" flag to. But, debugging still works, it's just a three step process now. 

1. Press F5 to launch the process
2. Once mGBA is up and displaying it's startup screen, stop the debugger in VS code by pressing the stop button on the debug toolbar
3. Press F5 to relaunch the debugger, this time it will attach itself to the existing instance of mGBA.
4. Happy debugging.

I will come back to address this issue to attempt to get this working without the need to stop and start again.

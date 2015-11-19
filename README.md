ffmpeg-windows-build-helpers
============================

This helper script lets you cross compile a windows 32 or 64 bit version of ffmpeg/mplayer/mp4box.exe, etc.
including many dependency libraries they use.

To run the script...

To build in windows (no VM needed, uses the native'ish cygwin, can take 6 hours or more):

     download repository: 
       zip file: https://github.com/rdp/ffmpeg-windows-build-helpers/archive/master.zip and unzip
       or clone the repository: c:\> git clone https://github.com/rdp/ffmpeg-windows-build-helpers.git
     Next run one of the "native_build/build_locally_XXX.bat" file.  The "fast" one is just libx264, fdk aac, and FFmpeg, and takes about 1 hour.
  
Or to build it from linux (uses cross compiler, takes 2 hours or so but requires linux box):

  In some type of Linux box (VM or native, or you could even create a VM temporarily, ex: digitalocean [1], [use it, then destroy your droplet]):

  download the script (git clone the repo, cd into it, run script, or do the following in a bash prompt) $

    ```bash
    wget https://raw.github.com/rdp/ffmpeg-windows-build-helpers/master/cross_compile_ffmpeg.sh -O cross_compile_ffmpeg.sh
    chmod u+x cross_compile_ffmpeg.sh
    ./cross_compile_ffmpeg.sh
    ```

And for both, answer the prompts.  
It should end up with a working static ffmpeg.exe within the "sandbox/*/ffmpeg_git" director(ies).

Another option is to run quick_cross_compile_ffmpeg_fdk_aac_using_packaged_mingw64.sh
  The "quick" part being the important part here, this one attempts to /just uses your local distributions
  mingw-w64 package for the cross compiler, thus speeding up compilation *dramatically*.
  Ping me if you'd like to see the "main" script use more of this style as well.

OS X users, follow instructions for linux above (it should "just work" no VM needed, cross compiling).

Also NB that it has some command line parameters you can pass it, for instance 
building a shared FFmpeg build (libavcodec-56.dll style), 
building mp4box/mplayer/vlc, 10 bit libx264, etc.
Run it like 
./cross_compile_ffmpeg.sh -h 
to see all the various options available to you.


If you want to customize your FFmpeg final executable even more (remove features you don't need, etc.) then edit the script
1. Add or remove the "--enable-xxx" settings in the build_ffmpeg method (under config_options) near the bottom of the script.  This can enable or disable parts of FFmpeg that you don't need, or want more, etc.
2. Remove or add the # to the lines you're enabling or disabling in the build_dependencies() function
3. You can write custom functions for new features you want to integrate, make sure to add them to the build_dependencies() functions and also include the corresponding "--enable-xxx" settings to the build_ffmpeg() function under the config_options
4. There are some helper method do_XXX etc. for checking out code, running make, etc. that may be useful.

Also NB that you can also "cross compile" mp4box.exe if you pass in the appropriate command line parameter.

Also NB that you can also "cross compile" {mplayer,mencoder}.exe if you pass in the appropriate command line parameter too.

Also NB that you can also "cross compile" vlc.exe if you pass in the appropriate command line parameter too.

Also NB that you can optionally create a "somewhat more machine optimized builds" by modifying an appropriate --cflags parameter, like --cflags=-march=athlon64-sse2 or what not.  
So if you're cross compiling it on the box you'll end up targeting it for, you could build it like --cflags=-march=native to get a slightly faster executable
Unfortunately, after doing some benchmarking, it seems that modifying the CFLAGS (at least if you're using libx264) doesn't end up helping much (it might make a smaller executable?) since libx264 auto detects and auto uses your cpu capabilities anyway, so...until further research is done, this option may not provide significant speedup.  Ping me if you get different results than this.

NB that this may contain slightly older/out of date dependency versions, so there may be a chance of security risk, though FFmpeg itself will be built from git master by default, with all the latest and greatest.

NB that if you have wine installed you may need to run this command first to disable it (if you are building for a different architecture than the building machine, especially), so that it doesn't auto run files like conftest.exe, etc. during the build (they will crash with an annoying popup prompt otherwise)
$ sudo update-binfmts --disable wine
ref: http://askubuntu.com/questions/344088/how-to-ensure-wine-does-not-auto-run-exe-files
NB that using a -march might not significantly improve speed [YMMV], ping me if you want even more aggressive optimization possibilities, I may be able to come up with a few more.

Feedback welcome roger-projects@googlegroups.com

Related projects (similar to this one...):
  https://github.com/jb-alvarado/media-autobuild_suite (native'ish windows using msys2)
  https://github.com/Warblefly/multimediaWin64 (native'ish windows using cygwin)
  there's also the "fast" option see above, within this project

Related projects (that do cross compiling with dependency libraries):

  vlc has a "contribs" building (cross compiling) system for its dependencies: https://wiki.videolan.org/Win32Compile/
    (NB this script has an option to compile VLC as well, though currently it makes huge .exe files :)
  mxe "m cross environment" https://github.com/mxe/mxe is a library for cross compiling many things, including FFmpeg I believe.

[1] if you use a 512MB RAM droplet, make sure to first add some extra swap space: https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-14-04 before starting.  
Here's my digitalocean referral link in case you want it [$10 credit] https://www.digitalocean.com/?refcode=b3030b559d17

to enable Intel QSV (vista+ compatible only) use option --build-intel-qsv=y

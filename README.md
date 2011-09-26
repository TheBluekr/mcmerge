mcmerge
=======

This is a tool for stitching together Minecraft maps that have different areas generated by different algorithms causing ugly transitions, for example when using an existing map from Minecraft 1.7 in Minecraft 1.8.

The tool is inspired by the bestofboth script and follows the same basic idea, check it out at:
https://github.com/gmcnew/bestofboth

It also relies on the very excellent pymclevel to do its map editing, you can find it at:
https://github.com/codewarrior0/pymclevel


As with all these sorts of things, use this tool at your own risk. Everything will probably be fine, but if it all goes horribly wrong I can't be held responsible.


How it works
------------

A river is placed at the boundaries between the old and new map areas, and the surrounding valley is smoothed seamlessly together with adjacent chunks. The smoothing is done with a low pass filter that means every chunk will have its own unique appearance.

Two phases are used to perform the work. First the original map is traced with mcmerge to find the contour of the edge of the existing world. Then continue playing on the map (with your new version of Minecraft) to generate additional areas. Finally mcmerge is used to smooth chunks at the boundary between the old and new areas.

I have included some test files in the appropriately named testfiles directory that you can use to run this script on.


Setting up
----------
To use this tool you will first need to install it. There are two available options.


### Binary package
If you're on Windows you can just grab the mcmerge-win32 zip file available on the project download page. Unzip it and you're ready to run.

If you are missing the requisite Microsoft distributable DLL files, mcmerge will fail. Try installing the Microsoft Visual C++ 2008 Redistributable Package (x86) first and you should be good to go. You can get it here:
http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=29


### Script package
On other platforms or if you want to set up the environment your self you will first need to install Python and some libraries. This tool has not been tested with Python 3.0 so Python 2.7 is recommended. You can get the latest version from her:

http://www.python.org/download/

If you're downloading Python for Windows, it is recommended you install the plain version rather than the X86-64 version as some of the libraries required only come precompiled in 32-bit versions.


Once you have Python installed you will need to install the NumPy library (and optionally SciPy). You can find the download links at:
http://new.scipy.org/download.html

Both NumPy and SciPy have pre-built installable packages for Windows and OS X on the SourceForge pages linked from the above page. Grab the latest version and you're ready to go.

I mentioned before that SciPy is optional. You will only need it if you use the 'gauss' filter option. This is not used by default so you can ignore it.

Finally, if you're on Windows, you should install the python win32 extension. The utility seems to work OK without it but warning messages are displayed and there's no guarantee things will keep working in the future. You can find the download on the project page here:
http://sourceforge.net/projects/pywin32/


Merging your world
------------------

Once you have the required bits installed, you can download this tool then fire up the command line and cd over to the location of the script. Then follow these steps:

1.  Backup your world directory. If you or the tool breaks something you'll be able to recover. You can find your singleplayer maps in the following locations:
   
    - Windows: %AppData%\.minecraft\saves
    - Linux: $HOME/.minecraft/saves
    - OS X: Probably the same as Linux?

2.  Adjust sea height. This step is optional but highly recommended if moving to 1.8 from an older map. The sea has been moved down by 1 block in Minecraft 1.8 and it is recommended you adjust the map accordingly. You can do this with MCEdit, nudge the whole world down by one block but make sure you leave the lowest most block level intact so you still have a solid bedrock.

3.  Run the contour trace phase on the map, for example (use the correct world name for you):

        python mcmerge.py --trace %AppData%\.minecraft\saves\World

    This places the file contour.dat in the world directory which will be read when merging.

4.  Play some Minecraft to generate the new areas bordering with your old map.

5.  Run the merging phase. You can tweak various parameters for the best look. Using the default configuration this will look something like the following (again, make sure you specify the correct world directory):

        python mcmerge.py %AppData%\.minecraft\saves\World

6.  This is optional. The tool used to save the level doesn't always get the lighting right leading to some very dark looking areas. You can probably fix this up using MCEdit by relighting the boundary chunks.

7.  Get back into the game and watch the floating debris disappear.

You can repeat steps 4 - 6 as many times as you need as the tool will keep track of which border areas haven't yet had new areas generated next to them.


Configuration
-------------

To see what options are available for you to fiddle with type: python mcmerge.py --help

A few things worth mentioning. Firstly there are two filters, there is 'smooth' and 'gauss'. The 'smooth' filter is the default (it's a perfect frequency filter). The gaussian filter gives more regular results and can perform much stronger smoothing, how it also tends to look more boring. Note that the --smooth factor means different things for both these filters (for the 'smooth' filter, bigger means less smohting, where as for 'gauss' it's the reverse), however both will give similar results for a value of 1.7.

You can also fiddle with how wide the river and the valley the river flows through are, the height of the river and the height of the river bank (specified with --valley-height), and the sea level at which water will be placed. Finally there's an option for how much the river and valley should be narrowed when a river flows on both sides of a chunk.


Happy merging!


Revision history
----------------

### v0.1
- Initial version
- Places a river at old/new chunk interface and smooths with low pass filter

### v0.2
- Fixed --help switch so works without supplying world
- Modified sea level to be 1.8 compatible and added extra step to fix sea level disconnect
- Ice is no longer treated as terrain to place river in
- Updated to latest version of pymclevel; better support for 1.8 blocks
- River corners now look rounder
- Fixed exception with very wide valley/river values
- Instead of exposed stone being transformed to dirt, the whole top layer is now shifted down; new switch controlling depth of this layer added
- Added win32 executable

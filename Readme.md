---
published: true
layout: article
title: The Maya Configuration Rosetta Stone
abstract: A compendium of all known configuration options for Autodesk Maya.
author_twitter: PaulNendick
author: Paul Nendick
categories:
- articles

---

# Who is this guide for?
Anyone who uses Autodesk Maya in anger, especially those of us doing so on Linux platforms. All details have been culled from official documentation, mailing lists, Autodesk support and the occasional reverse engineering. 

Many of these settings and better information can be gleaned by googling for [Autodesk's official docs](http://lmgtfy.com/?q=maya+2013+user+guide) but there are also quite a few items on this page not listed in any official documentation.

This list is a living work-in-progress. So please share any updates, addendums or corrections using the feedback link at the bottom of this post.

-Paul


# What can be done?
Maya provides to its users an enormous array of sporadically documented features and configuration options. These environment variables, XML config files and the like allow the user fine-grained control over:

* icons
* plug-ins
* modules
* Mel scripts
* Python scripts
* shaders
* image quality
* desktop integration
* tablet performance
* runtime stability
* more

# Prerequisites

* Maya 2011 or newer
* Linux, although much of this likely applies to Maya on Windows and OS X
* bash shell and not csh as the latter is [bad for you](http://www.faqs.org/faqs/unix-faq/shell/csh-whynot)

# Environment Variables
Following is a list of known Maya variables and what they configure. You can override these in your shell or embed them in [wrapper script](http://tldp.org/LDP/abs/html/wrapper.html).

## Personal options

### MAYA_APP_DIR
This variable defines your personal Maya application directory.

    export MAYA_APP_DIR=/opt/autodesk/maya/2013.5

### MAYA_UI_LANGUAGE
This is most useful when you want to run Maya in English on a Japanese version of Windows OS, i.e. never.

    export MAYA_UI_LANGUAGE=en_US
    export MAYA_UI_LANGUAGE=en_US/ja_JP

### AW_JPEG_Q_FACTOR
Alters the quality of jpegs written by Maya.

    export AW_JPEG_Q_FACTOR=95

## Performance options

### MAYA_NO_TBB
This issue is related to threading problem in the Maya Batch executable. Maya uses both TBB and OpenMP threading paths. When rendering we are seeing issues on the TBB side of the threading code were it seems to be calling too many thread counts and driving up the the systems ram causing and causing it to eventually crash almost instantly.

    export MAYA_NO_TBB=1
    
### MAYA_NO_PARALLEL_DRAW
If when using CGFX based shaders in a scene Maya crashes or freezes this will disable drawing of a shader which is still being evaluated.

    export MAYA_NO_PARALLEL_DRAW=1

### MAYA_NO_PARALLEL_MEMCPY
Set this environment variable to 1 to disable parallel memory copy. 

In some cases, parallel memory copy is faster on Opteron and Nehalem based systems. However, it may also be slower on Xeon systems, in which case you may want to disable parallel memory. If you are using Maya 2011 on Xeon system and are faced with speed issues, this might be useful.

    unset MAYA_NO_PARALLEL_MEMCPY
    
### MAYA_FORCE_REF_READ
By default, if you reference the same file twice, on file open Maya copies the existing nodes instead of re-reading them from disk. Occasionally, using this built-in multiple reference optimization feature of file referencing can cause errors. This environment variable turns off the file referencing optimization and forces reference files to be explicitly read in all cases. This fixes Maya’s behavior in some situations that would otherwise be evaluated incorrectly.

    unset MAYA_FORCE_REF_READ

### QT_PLUGIN_PATH
Disable KDE's Qt imageformat plugins as Maya does not need them and they make Maya's startup 4 times slower. If you want to use KDE's Qt image formats and are willing to accept the performance degradation, unset this variable.

    unset QT_PLUGIN_PATH

## Developer options
### XBMLANGPATH
The searth path(s) for icon files, such as those used for shelf buttons. Icons are in BMP or XPM format.

    export XBMLANGPATH=/somewhere/sensible:$XBMLANGPATH

### MAYA_SCRIPT_PATH
The search path(s) for shared MEL scripts.

    export MAYA_SCRIPT_PATH=/somewhere/sensible:$MAYA_SCRIPT_PATH


### MAYA_PLUG_IN_PATH
The search paths(s) for shared compiled plug-ins.

    export MAYA_PLUG_IN_PATH=/somewhere/sensible:$MAYA_PLUG_IN_PATH

### MAYA_MODULE_PATH
Defines the search paths for Maya module files. A module file describes the install location of a plugin which has been distributed as a module. Maya will append subdirectories of this install location to the following path variables: MAYA_PLUG_IN_PATH, MAYA_PRESET_PATH, MAYA_SCRIPT_PATH, PYTHONPATH and XBMLANGPATH. See [Distributing Maya Plug-ins() for more information](http://download.autodesk.com/us/maya/2010help/files/WS73099cc142f48755-4607e7cc11b1cfff5711f67.htm).

    export MAYA_MODULE_PATH=/somewhere/sensible:$MAYA_MODULE_PATH
    
### MAYA_PRESET_PATH
Location of Maya presets.

     export MAYA_PRESET_PATH=

### MAYA_PROJECTS_DIR
The default location of maya projects.

    export MAYA_PROJECTS_DIR=


### MAYA_PROJECT
Maya will allways start in this project.

    export MAYA_PROJECT=

### MAYA_MR_STARTUP_DIR
Location of the maya.rayrc startup file.

    export MAYA_MR_STARTUP_DIR=$MAYA_LOCATION/mentalray

### MI_CUSTOM_SHADER_PATH
Locations of mentalray include *.mi files.

    export MI_CUSTOM_SHADER_PATH=$MAYA_LOCATION/mentalray/include
    
## Debugging

### MAYA_CMD_FILE_OUTPUT
This feature is useful for tracking down error messages when Maya crashes upon startup.

    export MAYA_CMD_FILE_OUTPUT=$TMPDIR/maya.log

### WINEDITOR
Allows you to override the Expression Editor and use your own editor. The editor must be set to run in the foreground.

    export WINEDITOR=/usr/bin/scite

### MAYA_DEBUG_ENABLE_CRASH_REPORTING
Enable writing of crash report logs and dump files to temp directory. Crash report file example: `MayaCrashLog[yymmdd.hhmm].log`

    export MAYA_DEBUG_ENABLE_CRASH_REPORTING=1

### MAYA_HBDOWN_DEBUG
Enable verbose logging of hotbox actions for debugging issues on Linux. Slows Maya considerably.

    export MAYA_HBDOWN_DEBUG=1
    
## Installation options
### MAYA_VERSION

This is a Baseblack invention making it possible to install multiple versions of Maya simultaneously.

    export MAYA_VERSION=default

Which versions can be specified are found issuing the command:

    ls /opt/autodesk/maya/  
    
### MAYA_LOCATION
The path to where Maya is actually installed.

    export MAYA_LOCATION=/opt/autodesk/maya/$MAYA_VERSION

### TMPDIR
Temporary directory for Maya (playblasts, render, etc.).

    export TMPDIR=/tmp/$USER/maya

### MI_LIBRARY_PATH
Location of MentalRay library *.so files.

     export MI_LIBRARY_PATH=$MAYA_LOCATION/mentalray/lib
     
### MAYA_SHADER_LIBRARY_PATH
Path for ShaderLibrary files

    export MAYA_SHADER_LIBRARY_PATH=$MAYA_LOCATION/mentalray/shaderlibrary

### MAYA_HELP_URL
Location of Maya Documentation. Use a URL shortener to make work around Autodesk's habit of moving their documentation URLs about at random.

    export MAYA_HELP_URL=http://s.baseblack.com/maya2011docs
    
### MAYA_IP_TYPE
Use this environment variable to set your IP version

    export MAYA_IP_TYPE=ipv4


### WEBBROWSER
For web browser.

    export WEBBROWSER=/usr/bin/google-chrome


### MAYA_MOVIE_DIR
This variable is used to override the directory where movie files are found. The default is ```$MAYA_LOCATION/movies```.

    export MAYA_MOVIE_DIR=$MAYA_LOCATION/movies

### MAYA_PAINT_EFFECTS_THREADS
Threads to use when working with PaintEffects. Maximum 3.

    export LIBQUICKTIME_PLUGIN_DIR=$MAYA_LOCATION/lib

### LIBQUICKTIME_PLUGIN_DIR
Location of the libquicktime plugins

    export LIBQUICKTIME_PLUGIN_DIR=$MAYA_LOCATION/lib

### MAYA_AUDIO_LIBRARY
By default, this variable is set to 1 for the audio library used in previous versions of Maya. Set to 2 if you want to use the OpenAL audio library, which supports multiple track audio.

    export MAYA_AUDIO_LIBRARY=1

### MAYA_AUDIO_OPENAL_DEVICE
This variable provides a string which lets you specify the OpenAL device driver to use. The system default driver is used by default. (Linux) On Linux this can be set to one of the following: alsa, oos, solaris, dsound, winmm, port, pulse, wave

    export MAYA_AUDIO_OPENAL_DEVICE=pulse

### MAYA_SOUND_SCRUB
Disables the ability to scrub sound.

    unset MAYA_SOUND_SCRUB

## Bug-fixes and workarounds

### MAYA_HBDOWN_ENABLE
Hotbox freezes Maya when switching contexts in Linux. Maya 2009 or later

    export MAYA_HBDOWN_ENABLE=1


### MAYA_HBDOWN_TIME
Hotbox freezes Maya when switching contexts in Linux. Maya 2009 or later

    export MAYA_HBDOWN_TIME=5000

### MAYA_ACTIVE_STEREO_REFRESH
When Active Stereo is on, after the hotbox/menu have been dismissed, the viewport may need to be forced refreshed.

    export MAYA_ACTIVE_STEREO_REFRESH=1

### MAYA_FORCE_SHOW_ACTIVATE
May help in ensuring that popup windows are raised to the top of the list when using Gnome desktop (metacity window manager).

    export MAYA_FORCE_SHOW_ACTIVATE=1

### XLIB_SKIP_ARGB_VISUALS
Disables ARGB visuals from being considered when an application asks for possible visuals to use. Useful when the X desktop composite extension is enabled.

    export XLIB_SKIP_ARGB_VISUALS=1

### MAYA_SET_XERROR
Allows the user to force the XError handler to be re-installed. Maya 2008 SP1P04 or later.

    export MAYA_SET_XERROR=1

### MAYA_DISABLE_BACKSPACE_DELETE
Disable backspace key as delete key.

    export MAYA_DISABLE_BACKSPACE_DELETE=1

### MAYA_SLOW_DRAWPIXELS
For NVIDIA cards so they don’t produce failures when using PaintEffects and ImagePlanes.

    export MAYA _SLOW_DRAWPIXELS=1

### MAYA_NO_VERTEX_ARRAY_SELECT
Use this, when your selection of objects in the viewport is slow (selection lag).

    export MAYA_NO_VERTEX_ARRAY_SELECT=1

### MAYA_NO_XGRAB
Unknown effect.

    export MAYA_NO_XGRAB=1

### MAYA_NON_POWER_TWO_OFF
Enable when you have problem with ImagePlanes in viewports.

    export MAYA_NON_POWER_TWO_OFF=1

### MAYA_GEFORCE_SKIP_OVERLAY
Reduce flickering/stale view, if you are using Nvidia GeForce graphics cards/drivers.

    export MAYA_GEFORCE_SKIP_OVERLAY=1

### MI_MAYA_SOCKETS
Set this flag to 1 so that you can preview your render in progress in imf_disp while rendering from the command line. You may need to adjust your firewall. In Linux you dont need this variable.

unset MI_MAYA_SOCKETS

### MAYA_DISABLE_MRFORMATS
By default, Maya converts non-native image file formats into temporary IFF files so that they can be read. This variable allows you to disable the conversion of mental ray .ct and .st files. To disable it, set the value to 0 (zero) or leave it undefined.

    unset MAYA_DISABLE_MRFORMAT

### MAYA_DISABLE_MRMAP
By default, Maya converts non-native image file formats into temporary IFF files so that they can be read. This variable allows you to disable the conversion of mental ray .map files. Disabling this conversion saves scene export time and render time. To disable it, set the value to 0 (zero) or leave it undefined.

    unset MAYA_DISABLE_MRMAP

### MAYA_HW_FILE_TEXTURE_RESOLUTION_OVERRIDE
If you are using a file texture that uses MirrorUV and the resolution of the hardware rendered texture in the scene view appears degraded, use this environment variable.

    export MAYA_HW_FILE_TEXTURE_RESOLUTION_OVERRIDE=1

### MAYA_OFFSCREEN_HRB
Set this flag to 1 to expose the Render Offscreen option in the Hardware Render Buffer > Render menu. When enabled, an offscreen buffer is used when rendering sequences using the Hardware Render Buffer. Single-frame renders will continue to be rendered into an on-screen buffer. During offscreen rendering, moving windows over the Hardware Render Buffer window does not affect the rendered frames, as would normally be the case.

    export MAYA_OFFSCREEN_HRB=1

### AW_JPEG_Q_FACTOR
Quality of JPEG files that Maya renders out. (1-100) %

    export AW_JPEG_Q_FACTOR=100

### AW_JPEG_SUB_SAMPLING
Allows you to control the subsampling quality of the rendered image. For best quality of JPG set it to “1×1,1×1,1×1″, default is 2×2,1×1,1×1.

    export AW_JPEG_SUB_SAMPLING=1×1,1×1,1×1

### MAYA_NO_JITTER_FINAL_COLOR
Some randomness, or jitter, to the 8-bit color images are now disabled.

    export MAYA_NO_JITTER_FINAL_COLOR=1

### MAYA_RENDERER_RT_BACKGROUND_COLOR
Maya includes the camera background in the calculation of reflection and refraction rays.

    export MAYA_RENDERER_RT+BACKGROUND_COLOR=

### MAYA_MMSET_DEFAULT_XCURSOR
Creates a left pointer cursor to use when resetting the cursor on use of the Marking Menus. Useful when using GNOME window managers that otherwise revert back to the default “X” cursor. Fred's favourite!

export MAYA_MMSET_DEFAULT_XCURSOR=1

### MAYA_MM_PAUSE_EXPOSE
Doesn't exist in 2010, added to 2009 after 2010 released - status in 2011 unknown. Effect unknown

    export MAYA_MM_PAUSE_EXPOSE=1

### WF_IMF_SGI_MATTE
Allows Maya to save SGI format image files with matte.

    export WF_IMF_SGI_MATTE

### WF_IMF_CIN_CORRECTION
Issue#101914 - colour correction needed for Cineon files

    export WF_IMF_CIN_CORRECTION=both

### WF_IMF_CIN_WHITE_POINT
Issue#101914 - colour correction needed for Cineon files

    export WF_IMF_CIN_WHITE_POINT=685


## Licensing options
### LM_LICENSE_FILE
Location of license file.

    export LM_LICENSE_FILE=$MAYA_LOCATION/adlm/maya.lic

### MAYA_ALT_EN
If you have multiple license files in your FlexLM directory you can set this environment variable to uniquely define your Maya license file and speed up the Maya start time.

    export MAYA_ALT_EN=$MAYA_LOCATION/adlm/maya.lic

### MAYA_LICENSE_METHOD
Use network or standalone.

    export MAYA_LICENSE_METHOD=network

### ADSKFLEX_LICENSE_FILE
Location on the network of the license server software. Possibly deprecated with AdLM/Maya2010.

    export ADSKFLEX_LICENSE_FILE=@maya-licenses.baseblack.local

### MAYA_LICENSE
Use 'unlimited' or 'complete'. Possibly deprecated with Maya2010.

    export MAYA_LICENSE=unlimited

### FLEXLM_DIAGNOSTICS
Use 1, 2 or 3 for greater verbosity.

    export FLEXLM_DIAGNOSTICS=3

### AUTODESK_ADLM_THINCLIENT_ENV
This enables the use of the AdLM Thin Client configuration file discussed separately below.

     export AUTODESK_ADLM_THINCLIENT_ENV=$MAYA_LOCATION/adlm/AdlmThinClientCustomEnv.xml

# Configuration Files
In addition to the usual environment variables there is now at least one XML configuration file where one can shoot themselves in the foot with Maya settings. 

## AdlmThinClientCustomEnv.xml
This file is quite powerful in that one relocate the various directories the Maya installer creates into something organised, sensible, supportable and working.

### Setup

From the Licensing options above, you must first enable the use of this file with the following environment variable:

    export AUTODESK_ADLM_THINCLIENT_ENV=$MAYA_LOCATION/adlm/AdlmThinClientCustomEnv.xml

### Use
This is an XML file bearing options documented in the AdLM Thin Client User Guide. Not mentioned in that guide however is the extremely important key:

    <ADLMCUSTOMENV VERSION="1.0.0.0">

This oversight has caused the author many days of existential pain and suffering.


### Example
The following file snippet actually works which is in itself a minor miracle:

    <?xml version="1.0" encoding="utf-8"?>
    <ADLMCUSTOMENV VERSION="1.0.0.0">
    <PLATFORM OS="Linux">
    <KEY ID="ADLM_COMMON_BIN_LOCATION">
    <STRING>$MAYA_LOCATION/adlm/bin</STRING>
    </KEY>
    <KEY ID="ADLM_COMMON_LIB_LOCATION">
    <STRING>$MAYA_LOCATION/adlm/lib64</STRING>
    </KEY>
    <KEY ID="ADLM_COMMON_LOCALIZED_DATA_LOCATION">
    <STRING>$MAYA_LOCATION/adlm/en_US</STRING>
    </KEY>
    <KEY ID="ADLM_ERROR_TABLE_LOCATION">
    <STRING>$MAYA_LOCATION/adlm</STRING>
    </KEY>
    <KEY ID="ADLM_PIT_FILE_LOCATION">
    <STRING>$MAYA_LOCATION/adlm</STRING>
    </KEY>
    <KEY ID="ADLM_CASCADE_FILE_LOCATION">
    <STRING>$MAYA_LOCATION/adlm</STRING>
    </KEY>
    </PLATFORM>
    </ADLMCUSTOMENV>

# Product Keys
Product keys are necessary for the installation of an Autodesk product. Here is a sampling of some of the Autodesk 2012 products:

    657D1 = Maya 2012
    128D1 = Max 2012 
    727D1 = MotionBuilder 2012
    498D1 = Mudbox 2012
    797D1 = Inventor Professional  2012
    240D1 = Revit Architecture 2012 
    001D1 = AutoCAD 2012

From this sampling, you can extrapolate the product keys for the 2011 products:

    657C1 = Maya 2011
    128C1 = Max 2011
    727C1 = MotionBuilder 2011
    498C1 = Mudbox 2011
    797C1 = Inventor Professional  2011
    240C1 = Revit Architecture 2011
    001C1 = AutoCAD 2011

# Acknowledgements

This document was researched using the following resources:

* [http://download.autodesk.com/us/maya/2011help/index.html](http://download.autodesk.com/us/maya/2011help/index.html)

* [http://www.toxik.sk/mayaenv-configuration-of-variables/](http://www.toxik.sk/mayaenv-configuration-of-variables/)

* [http://www.faqs.org/faqs/unix-faq/shell/csh-whynot/](http://www.faqs.org/faqs/unix-faq/shell/csh-whynot/)

* [http://mayastation.typepad.com/maya-station/2010/06/maya-2011-on-xeon-systems.html](http://mayastation.typepad.com/maya-station/2010/06/maya-2011-on-xeon-systems.html)

* [http://studiosysadmins.com/wiki/display/Maya2010_NFS/](http://studiosysadmins.com/wiki/display/Maya2010_NFS/)

* [http://forums.cgsociety.org/archive/index.php/t-822564.html](http://forums.cgsociety.org/archive/index.php/t-822564.html)

* [http://mayastation.typepad.com/maya-station/2010/07/maya-2011-on-linux-and-the-hotbox.html](http://mayastation.typepad.com/maya-station/2010/07/maya-2011-on-linux-and-the-hotbox.html)
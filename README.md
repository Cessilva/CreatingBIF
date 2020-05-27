# CreatingBIF CesSil
Here is an explanation of how to create bif files with the ffmpeg tool in Windows.

This is the guide that Roku gives:
https://developer.roku.com/es-mx/docs/developer-program/media-playback/trick-mode/bif-file-creation.md
Prerequisites for windows:
Tool bftool y ffmpeg 
https://github.com/rokudev/samples/blob/master/utilities/bif%20tool/biftool_windows.zip
https://ffmpeg.zeranoe.com/builds/
Installation guide and add ffmpeg to the path, I recommend that you do the same with biftool, because otherwise you will always put the executable
https://www.youtube.com/watch?v=qjtmgCb8NcE


BIFTool can generate a bif file from a directory of jpg images that correspond to frames in the stream seperated by 
the specified time interval. It does not generate the directory of images, but other tools can.

Here is an example using ffmpeg to create a jpg every 10 seconds, and then creating an abc.bif file from
those images that corresponds to the abc.mp4 stream:

-  mkdir abc
-  cd abc
-  ffmpeg -i ../abc.mp4 -r .1 -f image2 %010.jpg
-  cd ..
-  biftool -t 10000 abc

You'll likely want to generate two .bif archives for each piece of content. One for SD and one for HD. Which archive
is used depends not on the resolution of the content, but on the resolution of the user's UI. That's why it's important 
to generate HD .bif archives even if the content is SD. However, if there is no HD .bif available, 
the player will fallback to using the SD .bif.

So here's one way to generate them:
-  mkdir abc-sd abc-hd
-  ffmpeg -i abc.mp4 -r .1 -s 240x180 abc-sd/%08d.jpg
-  ffmpeg -i abc.mp4 -r .1 -s 320x240 abc-hd/%08d.jpg
-  biftool -t 10000 abc-sd
-  biftool -t 10000 abc-hd

This will result in two new files: abc-sd.bif and abc-hd.bif (ffmpeg separates the images and biftool converts them to a bif file)

There are two caveats here:
1) ffmpeg generates the .jpg files starting with index 1. This means that all the timestamps will be off by 10 seconds.
2) The SD frames should have a width of 240 and the HD frames should have a width of 320. Their height should be specified to coincide with their aspect ratio. The commands above assume a 4x3 aspect ratio. Unfortunately, ffmpeg doesn't let you specify only a width, keeping the original aspect ratio.

You will need to write code/scripts to overcome these caveats.

For bif files have to be uploaded to a server:
http://www.instanttvchannel.com/roku/bif 

Other sources:https://community.roku.com/t5/Roku-Developer-Program/what-is-the-exact-syntax-for-BIFTool/td-p/111994
If you want to use other source from a server:https://trac.ffmpeg.org/ticket/4126


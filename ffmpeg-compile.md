# FFmpeg Compile for Screencast Purpose

I wanted to do a sapreate ffmpeg version because it will decrease breakage from system updates or newer ffmpeg versions.
Long story short, newer versions can break your screencast (ex: **screen tearing, ALSA buffer xrun, underrun, audio choppy, sound dropoff, video lag** ..etc)
So I like to stick to a specific version that I know works well with my setup. For me this is ffmpeg version 0.11.1

* tutorial video: [Link](https://www.youtube.com/user/gotbletu)
* offical website: [Link](https://www.ffmpeg.org/)

### install requirements
    ffmpeg x264 yasm

    
### download requirements
    mkdir -p $HOME/Compile/ffmpeg && mkdir -p $HOME/Compile/ffmpeg/{ffmpeg_build,bin,source}
    cd ~/Compile/ffmpeg/source
    wget http://ffmpeg.org/releases/ffmpeg-0.11.1.tar.gz
    tar -zxvf ffmpeg-0.11.1.tar.gz 
    
### configure
    cd ~/Compile/ffmpeg/source/ffmpeg-0.11.1
    
    ./configure --prefix="$HOME/Compile/ffmpeg/ffmpeg_build" --extra-cflags="-I$HOME/Compile/ffmpeg/ffmpeg_build/include" --extra-ldflags="-L$HOME/Compile/ffmpeg/ffmpeg_build/lib" --bindir="$HOME/Compile/ffmpeg/bin" --extra-libs="-ldl" --enable-gpl --enable-libmp3lame --enable-libtheora --enable-libvorbis --enable-libvpx --enable-libx264 --enable-nonfree --enable-x11grab

### build
    make
    make install
    make distclean
	
### rebuild
just do the **configure** and **build** process


### ffmpeg screencast commands

    ffcast_full() { ~/Compile/ffmpeg/bin/ffmpeg -f alsa -ac 1 -i hw:2,0 -async 1 -f x11grab -r 30 -s $(xwininfo -root | grep 'geometry' |awk '{print $2;}' | cut -d\+ -f1) -i :0.0 -vcodec libx264 -pix_fmt yuv444p -preset ultrafast -crf 0 -acodec libmp3lame -ab 128k -threads 0 -y ~/aa_screencast_baking.mkv ;}

### references

- https://trac.ffmpeg.org/wiki/CompilationGuide/Ubuntu
- http://paste.ubuntu.com/5659586/
- http://ubuntuforums.org/showthread.php?t=1392026


### contact

                 _   _     _      _         
      __ _  ___ | |_| |__ | | ___| |_ _   _ 
     / _` |/ _ \| __| '_ \| |/ _ \ __| | | |
    | (_| | (_) | |_| |_) | |  __/ |_| |_| |
     \__, |\___/ \__|_.__/|_|\___|\__|\__,_|
     |___/                                  

- http://www.youtube.com/user/gotbletu
- https://twitter.com/gotbletu
- https://www.facebook.com/gotbletu
- https://plus.google.com/+gotbletu
- https://github.com/gotbletu
- gotbletu@gmail.com


---
title: "The Linux CDROM interface"
date: 2011-06-10T09:41:56-03:00
---

## Paul Dwerryhouse, paul@dwerryhouse.com.au

\$Revision: 1.0 \$ \$Date: 2004/03/13 12:57:42 \$

---
This document details the CDROM programming interface that Linux provides. It was originally published in 1997, and has now been updated to deal with interface changes in Linux since that time. The latest version of this document can be found at: <http://leapster.org/linux/cdrom/>

---
## 1. Introduction

We’ve all seen the plethora of CD playing programs, such as kscd and workman, that let you program and play any number of different tracks from CD’s, but it’s often hard to come across information about how to actually play, stop and eject CD’s from your own programs. This document intends to give the reader a brief introduction to Linux’s CDROM interface. It expects that you have a working knowledge of the C programming language.

## 2. Starting Steps

Almost all commands involved with controlling a CDROM device are used in conjunction with ioctl. The commands and definitions for accessing the CD drive can be found in */usr/include/linux/cdrom.h*. For example, the program eject.c below will eject the drive platter. It firstly opens up the controlling device (in this case */dev/cdrom*, which will usually be a symbolic link to a real device, such as */dev/hdc*, for IDE devices) and then uses ioctl to send the **CDROMEJECT** command.

\>

```c>
#include <sys/ioctl.h>
#include <fcntl.h>
#include <linux/cdrom.h>
 
#define CDDEVICE "/dev/cdrom"           /* CDROM device */
 
int main(int argc,char **argv)
{
   int cdrom;                           /* CDROM device file descriptor */
 
   /* Open the CDROM device. The linux/cdrom.h header file specifies that
   ** it must be given the O_NONBLOCK flag when opening. My tests showed
   ** that if this isn't done, this program will not work. 
   */
   if ((cdrom = open(CDDEVICE,O_RDONLY | O_NONBLOCK)) < 0) {
        perror("open");
        exit(1);
   }
 
   /* Use ioctl to send the CDROMEJECT command to the device
   */
   if (ioctl(cdrom,CDROMEJECT,0)<0) {
        perror("ioctl");
        exit(1);
   }
 
   close(cdrom);
}
```

## 3. CD audio volume control settings.

The volume control commands revolve around the struct cdrom_volctrl structure, listed below. This structure is used when setting the output volume, when playing audio CDs and also when reading what the values are currently set to. For a stereo CD player, only channels 0 and 1 are used. The values range from zero (no sound) to 255 (loudest possible setting) and the relevent ioctl commands are CDROMVOLCTRL and CDROMVOLREAD for setting and reading the volume, respectively.

\>

```c>
For example, in order to read the values that are currently set:

> <code c>
#define CDDEVICE "/dev/cdrom"
 
/* ... */
 
int cdrom;
struct cdrom_volctrl vol;
 
cdrom = open(CDDEVICE,O_RDONLY | O_NONBLOCK);
ioctl(cdrom,CDROMVOLREAD,(void *) &vol);
 
printf("Channel 0: %d, Channel 1: %d\n",vol.channel0,vol.channel1);
 
/* ... */
```

Conversely, if you wish to set the volume of each channel of the CD player:

\>

```c>
=====  4. Playing, stopping, pausing CDs. =====

There are three structures directly associated with CD track positioning: struct cdrom_tochdr, struct cdrom_ti and struct cdrom_msf. The first, when used with the CDROMREADTOCHDR function, returns the track numbers of the initial and the final tracks on the CD in the drive. The second, allows a particular range of tracks to be played, including the ability to start and stop playing a track from a particular point within a track, when used with CDROMPLAYTRKIND. The third structure is similar, but allows the programmer to specify the position of the CD in minutes, seconds and frames and is used with the CDROMPLAYMSF function (there are CD_FRAMES frames to a second, and CD_FRAMES is defined as 75 at the top of /usr/include/linux/cdrom.h).

> <code c>
struct cdrom_tochdr
{
        u_char  cdth_trk0;      /* start track */
        u_char  cdth_trk1;      /* end track */
};
 
struct cdrom_ti
{
        u_char  cdti_trk0;      /* start track */
        u_char  cdti_ind0;      /* start index */
        u_char  cdti_trk1;      /* end track */
        u_char  cdti_ind1;      /* end index */
};
 
struct cdrom_msf
{
        u_char  cdmsf_min0;     /* start minute */
        u_char  cdmsf_sec0;     /* start second */
        u_char  cdmsf_frame0;   /* start frame */
        u_char  cdmsf_min1;     /* end minute */
        u_char  cdmsf_sec1;     /* end second */
        u_char  cdmsf_frame1;   /* end frame */
};
```

For example, should you wish to play any CD from start to finish, you could use the following:

\>

```c>
Pausing, resuming, stopping and ejecting are done with ioctl calls using CDROMPAUSE, CDROMRESUME, CDROMSTOP and CDROMEJECT respectively:

> <code c>
ioctl(cdrom,CDROMPAUSE,0);    // Pause CD play.
ioctl(cdrom,CDROMRESUME,0);   // Resume CD play from where it was paused.
ioctl(cdrom,CDROMSTOP,0);     // Stop CD play (can't be RESUMEd)
ioctl(cdrom,CDROMEJECT,0);    // Eject CD tray.
```

## 5. Copyright/License.

Copyright (c) 1997-2004 Paul Dwerryhouse

Original: <http://leapster.org/linux/cdrom/>

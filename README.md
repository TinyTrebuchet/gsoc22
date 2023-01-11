# Google Summer of Code 2022 Report

Hi, I am Gaurav Guleria, and was selected as a GSoC 2022 contributor for OpenPrinting @ The Linux Foundation. My project was adding CPDB support to the GTK and Qt print dialogs, and optionally the Chromium print dialog if there was sufficient time left. 

I have managed to add Common Print Dialog Backends (CPDB) support to both GTK and Qt print dialogs. The optional Chromium print dialog could not be treated due to lack of time.

The GSoC 2022 program has ended, and my 1 month extension (due to exams and classes) is also about to come to an end. Here's a brief project report.



-----

## 1. GTK Print dialog

The GTK print dialog had well defined and well planend interfaces for print backends, and hence adding a new backend was relatively easy. However, since there was virtually no documentation for it except for this archived mail: https://mail.gnome.org/archives/desktop-devel-list/2006-December/msg00069.html, it was a bit difficult. So, I had to constantly refer to the other already implemented print backends for help.

I also discovered a memory leak in the CUPS print backend, and got it fixed:  
https://gitlab.gnome.org/GNOME/gtk/-/issues/5019

The merge request has been created upstream, and is under review as of now:  
https://gitlab.gnome.org/GNOME/gtk/-/merge_requests/4930/


-----

## 2. Qt Print Dialog

After the GTK print dialog was treated, we moved on to Qt print dialog. However the situation was even worse for Qt print dialog, again with no documenation, and this time, with no print dialog mainatainer either. Further, Qt didn't have well defined interfaces for implemenation of the print backends. Instead, part of CUPS was hardcoded directly into the print dialog using:
```
#if QT_CONFIG(cups) 
<CUPS Code> 
#endif
```
constructs. This led us to shift our focus (after talking with Till) to Firefox and Thunderbird's print dialog instead. But then, after re-thinking, Till suggested that it was more important that we treat the Qt print dialog instead, since it was not in a good condition: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=908500

So, after deleting and setting up my new Qt VM for the third time, I started working on it. Fortunately, Till talked with Albert Cid on the #kde-devel irc, who had worked on the Qt print dialog before, and was ready to help. With him, I was able to jump start the CPDB support for Qt print dialog. However, sometime after he suggested that we consult the Qt development team directly too, and so we did that: https://lists.qt-project.org/pipermail/development/2022-September/042993.html

We discussed several ways of adding CPDB support to the Qt print dialog, and finally concluded to use the same `#if ... #endif` constructs for CPDB for now. After completing the CPDB support, I created the merge request here:  
https://codereview.qt-project.org/c/qt/qtbase/+/437301


-----

## 3. Improvements to cpdb-libs and cpdb-backend-cups

During the course of my GSoC period and while treating the aforementioned print dialogs, I made several improvements, bug fixes, and feature additions to the cpdb-libs and cpdb-backend-cups. They include:

- Support for default printers in CPDB using user and system config files:  
https://github.com/TinyTrebuchet/cpdb-libs/commit/6545e05265d20bdf357519e6b3a61027b441c908
- Support for asynchronously acquiring printer details. This was necessary for temporary CUPS queue, so that the print dialogs don't freeze while materializing them:  
https://github.com/TinyTrebuchet/cpdb-libs/commit/070b0077586b72e9a8254b4dd9ce78e040528175
- Support for retrieving physical dimensions for a given media-size:  
https://github.com/TinyTrebuchet/cpdb-libs/commit/6af3be93d1a0282de4f56bbfe4d5fc9b5a370fbb
- Support for multiple independent set of media-margins for each media-size:  
https://github.com/TinyTrebuchet/cpdb-libs/commit/f30fd6074a8a72a8068c35e7a214d1836a5570ca
- Support for human readable option and choice strings
https://github.com/OpenPrinting/cpdb-libs/commit/46f8870c77918a63d795572f2459b1cb3cf31998
- Bux fixes and other minor improvements


-----

## Word of Thanks

I would like to thank [@till](https://github.com/tillkamppeter) for his continous and quick support throughout the GSoC period in all areas. This project wouldn't have been possible without you. I also want to sincerely thank [@tsdgeos](https://github.com/tsdgeos) for his help while adding CPDB support to the Qt print dialog. 


-----

## Update : January, 2023

I have added several improvements to CPDB in my winter vacations.  
- Memory leaks in cpdb-libs identified and fixed.  
https://github.com/OpenPrinting/cpdb-libs/commit/a86d7e1549ff3fc5fffc2d4c99666a29d1fd130b  
- CPDB now supports disconnecting & reconnecting to DBus several times unlike before where it couldn't reconnect once disconnected.  
https://github.com/OpenPrinting/cpdb-libs/commit/8408588be0e665e10ffc5ac78500a894b8aee11d  
- CPDB now follows XDG base directory specifications instead of hardcoding system paths.  
https://github.com/OpenPrinting/cpdb-libs/commit/578ead2ac091cb07113e4da7c3b6ad81615a6d9e  
https://github.com/OpenPrinting/cpdb-backend-cups/commit/d2a3afb4d2f87d39b144dc728f6199c62ec57826  
- CPDB now also provides generic groupings for most IPP options so print dialogs can now separate the options into several tabs accordingly.  
https://github.com/OpenPrinting/cpdb-backend-cups/commit/28f7e865a55bb48b34a871e6a337acb05b8c737e  
- Furthermore, CPDB now has full translation support, where the translations are fetched from the backends in user's requested locale. In cpdb-backend-cups, this is achieved by using translation support provided by cups-filters in catalog.h. Certain changes were also made to these functions (cfCatalog...) so that they accept locale as additional parameter instead of just defaulting to en-US.  
https://github.com/OpenPrinting/cpdb-backend-cups/commit/28f7e865a55bb48b34a871e6a337acb05b8c737e  
https://github.com/OpenPrinting/libcupsfilters/commit/86c010177378a32bbbb77e02fd31bed8a446e477  
- Finally, CPDB has now much more verbose debugging which should be really helpful since it'll make bug fixing much easier in the future, considering that CPDB is a fairly new technology.  
https://github.com/OpenPrinting/cpdb-libs/commit/20d62e35a1028800fc3b9926e4ac10184815d270  
https://github.com/OpenPrinting/cpdb-libs/commit/e7eb00de2ea15e858d198a7e72ea1bc7ae16ec80  
https://github.com/OpenPrinting/cpdb-libs/commit/92350c4e3636ad4067816d1b3272cddce3bb6204  


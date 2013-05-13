#BN Development Environment#

**Date: 05.13.13**

**@version: 0.0.03**

Here is my guide to installing the development environment that I am envisioning BN using on the Magento installation. Although I'm usually running Mac OS 10.x on a 64 bit chipset, for this I am going to assume that everyone is on a Windows machine, but you will need to determine if 
you have a 32 bit or 64 bit chip architecture:

    http://support.microsoft.com/kb/827218

I have included a folder in this repository with installer files for 64 bit Windows and will be providing links to the download page for each tool in case a 32 bit installer needs to be retrieved.

64-bit Windows Installers:

    https://github.com/fslone/bn/tree/master/programs

All tools listed here are supported on both Windows and GNU/Unix OS platforms. Some will be OS agnostic and have no differences, while most will be ports for Windows and may differ slightly. Since I'm not looking at a Windows machine currently some steps may differ slightly such as a menu name, etc. 

Please be open minded about using the new tools for the first couple weeks and then we can re-evalute and tweak our setup as necessary. Note that use of all of these techniques is **required** so as to maintain the code properly and allow multiple developers to be working on it.

**Git Server**

`Git` is a version control system used to store files in locations known as `repositories`. These repositories, or "repos," are stored on your local machine as well as usually being sent back for storage on a remote server. For our remote server we will be using the current site, `GitHub.com`, so if you plan on making code edits first you will need to create an account. GitHub will manage the Git server that we are going to be pushing our code to, as well as tracking all changes within the Git `Version Control System`.

Note that Git can be run on any Unix server, but for simplicity we will be using GitHub. Once you have created your account, please e-mail your username to `fslone+bn@gmail.com` with the subject `GH User`.

**Text Editor**

I am recommending Sublime Text Editor because of the availability of plugins to achieve most of what we will be needing on this project.

Download Page:

    http://www.sublimetext.com/2

It is free but will continue to ask you to purchase the software periodically until you do so, although you can decline each time without penalty.

**Debugging Proxy**

A `debugging proxy` will allow you to monitor all TCP/IP traffic, showing you all requests and responses to and from the port your internet connection is using. While this has many uses, we will first be using it to take a local file and serve it through our browser as if it were live on the site. By doing this you can see what effect a code change is going to have on the site, but have the change only be visible to you until it is put up on the live site.

We begin by saving our change to the file on our local machine. We then map it to the request that our browser makes to the website's server for the file that we need to preview. For Windows, Fiddler Proxy is the best alternative.

Fiddler Download:

    http://fiddler2.com/get-fiddler


Once installed, the local file needs to be mapped to the URI for the corresponding remote file. One user explains how to do this using Fiddler:

    Fiddler has this feature; just click the AutoResponder tab and map URLs to local files. Thousands of people do this every day.

    See also video #5 here: http://www.fiddler2.com/fiddler/help/video/default.asp

    **Source: **http://stackoverflow.com/questions/4821224/intercept-and-use-local-files-in-http-requests


**Sublime Package Control**

You'll be installing several plugins for Sublime so that you can perform most necessary tasks from within a single program.

    http://wbond.net/sublime_packages/package_control/installation








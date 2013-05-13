#BN Development Environment [Mac OS X 10.6+, Intel chipset]#

**Date: 05.13.13**

**@version: 0.0.01**

Here is my guide to installing the development environment that I am envisioning BN using on the Magento installation. Several Mac specific programs that are awesome, like `Charles`, `Git`, and `wget`, I already talked about on another project months ago:

    https://github.com/fslone/osxapps/blob/master/README.md

Anything you see about hacking wireless connections and system passes is purely for entertainment purposes :) All the installer files are down one level from the README in `/Utils/` though you might want to hit the download links in the README since this is getting a little old.

    https://github.com/fslone/osxapps/tree/master/Utils

Please be open minded about using the new tools for the first couple weeks and then we can re-evalute and tweak our setup as necessary. Note that use of all of these techniques is **required** so as to maintain the code properly and allow multiple developers to be working on it.

###Register a Git Server###

`Git` is a version control system used to store files in locations known as `repositories`. These repositories, or "repos," are stored on your local machine as well as usually being sent back for storage on a remote server. 

For our remote server we will be using the site you're on, `GitHub.com`, so if you plan on making code edits you will need to create a free account. GitHub is just a service that runs a `Git` enabled server that we are going to be pushing our code to, and it is here we will store all of our code and a record of the changes made to that code. Once you have created your account, please e-mail your username to `fslone+bn@gmail.com` with the subject `GH User`. You can then go to the URL for the master BN `repository` at `https://github.com/fslone/bn` and click `Fork` in the upper right hand corner. This will create a copy of the entire `repository` on your account.

**Overview of Distributed Versioning:**

    Don't worry if you don't get even half of this right away, we'll explore the Git system in much more detail 
    over time and this is just something to get you thinking. Even its creator, Linux creator Linus Torvalds, 
    described Git as the "version control system from hell." I can't stress how worthwhile it is to learn.

Keeping a record of the different versions will give us the ability to review changes and rollback code on a piecewise basis if needed. `Git` is known as a  `Version Control System (VCS)` for this reason. If you have used the other popular versioning system `Subversion` before you will likely be familiar with many of the concepts. Whereas Subversion only has one repository stored on a remote server, Git has a system with a central repository, as well as each user's local repositories on his own computer. In this way Git is a **distributed** versioning system, while Subversion is a **centralized** versioning system. The same model of centralized vs. distributed is the reason Napster, which was centralized, could be shut down as a download service although torrenting, which is distributed, can never be shut down. Just like torrents, you want lots and lots of copies spread across multiple users so that the system can never be brought completely down, meaning your code doesn't ever get totally lost.

Under the distributed model, each user has a local `repo` full of all files and the history of all changes, known as `commits`, up to the time he last performed a `pull` of the master `repo` from the remote server into his own local `repo`. When a change is saved locally and ready to deploy, a user will then `push` the local files back to the master `repo`, which will in most cases perform an automatic merge of the differences between the two repos. Because no current work depends on the current work of another developer, there is nothing to worry about when working on the same files as someone else, all conflicts will be handled at the time of the `merge`. 

Note that Git on the remote server side can be run on any Unix server, but for simplicity we will be using GitHub to handle the administrative tasks for us. 

###Installing Git###

The next step will be to install the Git version control system on your local machine if it isn't pre-installed. To see if it is, fire up Terminal by pressing `command + spacebar` and typing `Terminal` then hit `return`. Once you're at the shell prompt type `git` and hit `return`. If it gives you a command not found error or similar you need to install it. If it gives you a bunch of options about how to use it you can skip this part.

**Download Git for OSX:**

    https://code.google.com/p/git-osx-installer/

Once installed you should be able to type `git` at your Terminal prompt and get a list of program options.

###Editing Source Code###

We will be using Sublime Text Editor because of the availability of plugins to achieve most of what we will be needing on this project.

**Download Sublime:**

    http://www.sublimetext.com/2

It is free but will continue to ask you to purchase the software periodically until you do so, although you can decline each time without penalty.

###Previewing Source Code Changes###

A `debugging proxy` will allow you to monitor all TCP/IP traffic, showing you all requests and responses to the internet through the port your internet connection is using. While this has many uses, we will first be using it to take a local file and serve it through our browser as if it were live on the site. By doing this you can see what effect a code change is going to have on the site but have the change only be visible to you until the file is pushed live.

We begin by saving our change to the file on our local machine, then "map" it to the request that our browser makes to the website's server for the corresponding file. For Mac, Charles Proxy Server is the best alternative.

**Charles Download:**

    http://www.charlesproxy.com/download/


Once installed, the local file needs to be mapped to the URI for the corresponding remote file using the `map local` feature. I'll add more detail on that in the next part of this tutorial, but for now fire it up, go to `http://www.brick-anew.com` in whatever browser you fancy, then swap back to Charles and look under the `Sequence` section (should be button up top). In the `Filter:` text field type in `.css` to see all files that contain that string in the filepath. Find the request for `http://www.brick-anew.com/skin1/skin1.css` so you can come back to it.

Create a local file called `skin1.css` and put in the following code:

    body {
    	display:none !important;
    }

Now go back to Charles, `control + click` the entry and choose `Map Local` from the bottom of the dialog menu. Your protocol for now can just be `http` with the port set to `80`. In the `Local Path` section hit the `Choose File` button and navigate to your newly created `skin1.css`.

Once mapped properly, refresh `http://www.brick-anew.com` in a browser (Charles will intercept ALL internet traffic regardless of application). If working properly the site will appear totally blank, though only to you and only while Charles is running. Be sure to stop all file mapping when you are done, since you'd be quite confused if you forgot and the site seemed to be gone the next time you visited :) To stop all local mapping, go to `Tools > Map Local` in the top menu and click to uncheck and stop. 

Be sure to properly quit Charles when you are totally through, as it will cause a "Proxy Not Found" error in your browser if not shut down properly. If that happens relaunch it and quit it properly.












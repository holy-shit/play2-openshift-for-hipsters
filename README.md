Play framework 2 for OpenShift 
==============================
** DEPRECATED **

for historical purposes only

## quick pre-configuration

Very easy to read and modify.
In fact, you'll have to read it, because you going to live with that.
From use of this config applications do not break and are not modified.

It is not a step by step guide, it requires an understanding of the general principles and a bit of reflection

1) Register on OpenShift, set up it on local computer, write down all info from terminal scrollback

2) Create application with dyi profile, write down all info from terminal scrollback

    rhc app create yourappname -t diy-0.1 --no-git -l yourlogin

3) Create Play app, add it to Git (git init)

4) Download existing repo (pre-configured by OpenShift). Necessary Evil. 
Your_uuid (and all other staff) in your terminal scrollback, just copy-paste it.

    git remote add origin ssh://your_uuid@yourappname-yourdomain.rhcloud.com/~/git/play2demo.git/
    git pull -s recursive -X theirs origin master
    git add .
    git commit -m "initial deploy"

5) Download this quickstart into your project source tree (fear not, I'm not going to delete this quickstart repository for the foreseeable future, but also do not promise to support it)

    git remote add play2openshift4hipsters -m master git@github.com:olegchir/play2-openshift-for-hipsters.git
    git pull -s recursive -X theirs play2openshift4hipsters master

6) Comment "target" in your default Play2 gitignore IF you want to store releases in git repo. It will enable you to use push-to-deploy method out of the box. But if you use free account limited to 1Gb of hdd you probably do not want this. This is the reason why there is this repository, which allows to make manual deploy of your app.

7) Move all important settings from application.conf to openshift.conf, don't forget application.secret. (it is your business to figure out how to make a comfortable synchronization between this files, it depends on your project)

8) Push it to OpenShift (git push)

9) Login with ssh and copy all files from playshift dir (that is part of quickstart repo - and now part of your project too) to app-root/data (or any other $OPENSHIFT_DATA_DIR).

10) Read this scripts line by line. Scripts are short but very important. (Please note, that openshift-{rebuild,continue-rebuild,clean,compile,stage} are just single script with different play params. It is done by intention - you can now modify this scripts to your needs, without hacking through functions and other code-deduplication things)

12) Run playshift-deps to install dependencies. This is very important, long-runing and error-prone command. It downloads and installs scala, sbt, playframework2 and ruby gem compass (I always use sass instead of css - all real hipsters should do this)

13) Run playshift-safe-deploy to deploy your application. As always, important-long-running and error-prone. It is so resource expensive that Openshift can kill it. Repeat it until it will succeed (successfull running of `play stage`, you know how it looks if you are play2 developer) or modify script so it will succeed.

14) Run playshift-start to start app in background. OpenShift have very small ssh inactivity timeout, so you'd better to think how to start all this non-interactively. I use nohup.

15) Run playshift-stop to stop it. You should stop app before re-reploying. Also by default application automatically stops on git push, you can find (and wipe) this code in .openshift/action_hooks/stop

16) PROFIT


### Optional:

17) You can add this line

    if [ -f $OPENSHIFT_DATA_DIR/playshift-bashrc ]; then
        source $OPENSHIFT_DATA_DIR/playshift-bashrc
        export -f playshift-mode
    fi


to your bash profile, e.g. with

    vim app-root/data/.bash_profile

and type just after login

    playshift-mode

It will export all environment variables and so on. You can change into $OPENSHIFT_DATA_DIR by typing 'odd' now. It is useful if you hack production environment without modifying control scripts.

Why you have to type this 'playshift-mode' instead of using .bash_profile directly and typing nothing? Because .bash_profile is sourced in before other scripts that overwrite PATH settings in .bash_profile. I think it's a bug, but this ticket closed as "not a bug": https://bugzilla.redhat.com/show_bug.cgi?id=916434

17) Latest update brings playshift-update script. It will copy control scripts from play2-openshift-for-hipsters repo that will be located in your $OPENSHIFT_DATA_DIR. Please note that openshift-deps will clone this repo by default, but will NOT run update script automatically. This is done in case you have edited control scripts manually, and in case of automatic update playscript-update would have destroyed all of your changes. You are not required to use playshift-update, I created this thing for my own needs.

18) Send me a beer, if you don't mind

19) Files from .openshift dir based on https://github.com/opensas/play2-openshift-quickstart, you can send your beer to opensas too

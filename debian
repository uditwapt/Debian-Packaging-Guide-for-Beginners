Debian packaging (noob guide)
Disclaimer :- There is a well flourished guide on the subject already ,this is just
my version of it .
Links
• (Debian Packaging pre requisites)
https://www.loomio.org/d/LTpSdMuX/debian-packaging-pre-requisites
•
• (Detailed Wiki guide )
https://wiki.debian.org/Javascript/Nodejs/Npm2Deb/Tutorial
https://wiki.debian.org/Javascript/Nodejs/Npm2Deb
• (Get info regarding node-modules)
https://www.npmjs.com/
• (Debian package tracker)
https://tracker.debian.org/
• (Share long output & errors) - just a suggestion feel free to use your own
https://paste.debian.net/
• (Choose a node-module from here)
https://pad.disroot.org/p/node_packaging
From the Debian Packaging pre requisites link I followed docker setup
Arch based distros (installation)
Install docker by running pacman -Sy docker
Start and enable docker systemctl start docker && systemctl enable docker
Pull Debian Sid image from docker hub using the following command
docker pull debian:sid
Create a container with it and start bash on it
docker run --privileged --name "sid" -it debian:sid /bin/bash
Update and upgrade to latest versions of packagesapt-get update && apt-get upgrade
Exit after your work is done. If you need to connect to it later, use the following
commands which will take you to the bash prompt , if you encounter permission
issues try with sudo
docker start sid
docker attach sid
If successful you will be presented with a shell similar to this
root@5c7bdbda2ce5:/#
Setup password for root account
passwd root
Its better to add a normal user and set a password
useradd -m -s /usr/bin/bash -G sudo username && passwd username
Install a text editor (I like vim , for installation run sudo apt install vim ) and
update .bashrc of docker container with the following environment variables
export DEBEMAIL=your@email.domain
export DEBFULLNAME='Your Name'
alias lintian='lintian -iIEcv --pedantic --color auto'
alias git-import-dsc='git-import-dsc --author-is-committer --pristine-tar'
alias clean='fakeroot debian/rules clean'
export LANG=C.UTF-8
export LC_ALL=C.UTF-8
Command ls -la shows the hidden .bashrc file , to edit with vim do vim .bashrc and
add the above and the very end , update your current environment by running
source ~/.bashrc
vim tips
i – insert mode to enter text ,
:wq – write and exit ,:q!– exit without saving
Install the packages
sudo apt install npm2deb git-buildpackage dh-buildinfo
Setup username and email address for git
git config --global user.name "Your name"
git config --global user.email email@domain.tld
Took a node- module called “qw” from https://pad.disroot.org/p/node_packaging
(make sure you add your name next to it to avoid ownership confliction )
workflow
1) Check depedency use npm2deb depends
$ npm2deb depends -b -r qw
Module qw has no dependencies.
Here qw has no depedencies
2) Search for existing work using npm2deb search
$ npm2deb search monocle
Looking for similiar package:
None
Looking for existing repositories:
None
Looking for wnpp bugs:
None
Output shows no existing work and one can start work on it
3) Preview more info using npm2deb view
$ npm2deb view qwName: qw
Version: 1.0.1
Description: None
Homepage: https://github.com/iarna/node-qw#readme
License: ISC
Debian: None (None)
License is automatically recognize as BSD, if this does not happen, please
pass
--upstream-license option during creation to set a correct license.
4) Automate debian package creation using npm2deb create . npm2deb fails to do
it
completely ,our job is to fix those error which is shown by the prefix FIX_ME
$ npm2deb create qw
You may want fix first these issues:
qw/node-qw-1.0.1/debian/control:Description: FIX_ME write the Debian
package
description
qw/node-qw_itp.mail:Subject: ITP: node-qw -- FIX_ME write the Debian
package
description
qw/node-qw_itp.mail: Description
description
: FIX_ME write the Debian package
qw/node-qw_itp.mail: FIX_ME: This ITP report is not ready for submission,
until you are
to
qw/node-qw_itp.mail:FIX_ME: Explain why this package is suitable for adding
Debian. Is
qw/node-qw_itp.mail:FIX_ME: Explain how you intend to consistently
maintain
this package
ls command shows npm2deb created a dir called qw , change directory to
qw it
has the following contents$ ls
qw
$ cd qw/
$ ls
node-qw
node-qw-1.0.1.tar.gz
node-qw_1.0.1-1.dsc
qw_1.0.1- 1_amd64.buildinfo node-qw_1.0.1.orig.tar.gz
node-
node-qw-1.0.1 node-qw_1.0.1-1.debian.tar.xz node-qw_1.0.1-1_all.deb
node- qw_1.0.1-1_amd64.changes node-qw_itp.mail
node-qw is a buggy folder created by npm2deb its better to delete it to
avoid
confusion later
$ rm -rf node-qw
5) Import package to git
$ gbp import-dsc --pristine-tar node-qw_1.0.1-1.dsc
now we get a new dir called node-qw which is git tracked , cd into the new
created dir node-qw and see git branch and git tag
$ git branch
* master
pristine-tar
upstream
$ git tag
debian/1.0.1-1
upstream/1.0.1
Delete the debian tag by running git tag -d debian/1.0.1-1
6) File ITPFiling itp is a method by which we take ownership of the module by mailing
to
submit@bugs.debian.org , in return we get a bug number , a sample mailing
templete is created by npm2deb for our use
$ cat node-qw_itp.mail (to view the template )
update the template by fixing the error with the prefix FIX_ME:
sample itp is attached below
https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=881423
7) Make package lintian clean
/qw/node-qw$ ls
LICENSE README.md debian package.json qw.js test
/qw/node-qw$ dpkg-buildpackage && lintian ../node-qw_1.0.1-1.dsc
lintian points out the errors we need to fix
I: node-qw source: file-contains-fixme-placeholder debian/control:20
FIX_ME
W: node-qw source: out-of-date-standards-version 4.1.1 (current is 4.1.2)
places where errors where fixed regarding this module
debian/control
debian/copyright
debian/changelog
make sure to commit your changes as they are fixed by using the git
command
git commit -m “ commnet description” path/of/fixed/file
for reference -https://anonscm.debian.org/cgit/pkg-javascript/node-
qw.git/tree/
8) Enable tests if presentfind the test command from package.json
$ cat package.json
{
"name": "qw",
"version": "1.0.1",
"description": "Quoted word literals!",
"main": "qw.js",
"scripts": {
"test": "tap test"
},
"keywords": [],
"author": "Rebecca Turner <me@re-becca.org> (http://re-
becca.org/)",
"license": "ISC",
"devDependencies": {
"tap": "^8.0.0"
},
add the test command under override_dh_auto_test from debian/rules
override_dh_auto_test:
tap -J test/*.js
add the test framework (mocha, node-tape etc) as a build dependency in
debian/control
Build-Depends:
debhelper (>= 9)
, dh-buildinfo, nodejs
, node-tap
Standards-Version: 4.1.2
add a Test-Command section to debian/tests/control
$ cat tests/control
Tests: require
Depends: node-qw
Test-Command: tap -J test/*.js
Depends: @, node-tap
refrence link ;-https://anonscm.debian.org/cgit/pkg-javascript/node-
qw.git/tree/debian
run dpkg-buildpackage and check if the tests ran sucessfully and change
debian/changelog to release by running dch -r.
9) upload to https://git.fosscommunity.in (recomment this since its a freesoftware
and
the team manintains it)
create a new project as per your node-module name
git remote add origin gitlab@git.fosscommunity.in:username/node-
module-name.git (eg link)
git push -u origin --all –follow-tags
this is a temperory repo once you mature ,will get officail repo access
10) Clean build with sbuild
$ sudo apt-get install sbuild
$ sudo sbuild-adduser $LOGNAME
... *logout* and *re-login* or use `newgrp sbuild` in your current shell$ sudo sbuild-createchroot --include=eatmydata,ccache,gnupg unstable
/srv/chroot/unstable-amd64-sbuild http://deb.debian.org/debian
$ sudo sbuild -A -d unstable ../node-qw_1.0.1-1.dsc
11)Install deb package
apt-get install autopkgtest
dpkg -i ../node-qw_1.0.1-1_all.deb

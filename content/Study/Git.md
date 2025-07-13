---
title: '"Git"'
draft: false
tags:
  - "#basicTools"
---

# Git
command line is the most available,unlimited
### Git's level
1. system (for all users)
2. global (all repositories of the current user)
3. Local (the current repository)
### Git's configuration
  #### **Git's setting include** 
- **name,**
- **email,**
- **default Editor,**
- **how to handle end of line** 
>git config --global user.name \<name\> *use ""when you have a space in the value*
>git config --global user.email \<email\> 
>git config --global core.editor "\<default editor --wait\>" 
>use wait command to tell the terminal to wait until we close the new instance of code
 
 >[!info] in fact, all the global configuration settings are stored in a text file.To edit the file by default editor, type:
 >git config --global -e
### now learn to config how to handle EOL
-  on windows, EOL are marked with two special characters:\r(carriage return) and then \n(line feed)
 - on linux/macs EOL are indicated with \n
 To prevent some weird problems between different system ,configure ___core.autocrlf___
 - For windows users, set the property to **true**.Hence a windows user check in his code into the repository, git could remove the \r.Similarly, when he check out his code from the repository, git could update EOL and add a \r before \n.
 - For Mac/Linux users,set the property to **input**.Hence,git shouldn't touch EOL when you check out the code from the repository,meanwhile it could remove the \r you accidentally add into the EOL.
 >git config --global core.autocrlf input
### Basic interpret with git repository
- git has a storage place between the repository and your file.
- when change a file , firstly add it to the storage,then commit it to the repository
>git add file1
>git commit

- show the current storage status by command:
>git status -s

>[!info] -s stands for -simplify."M"means moved(changed),and"A"means added.RED color means that the change hasn't been added to the storage place
### diff command configuration
- the primitive diff command on the command line use "-"to symbolize the documents in the storage area (before changed),"+" to symbolize the documents changed.
- It's not a good choice to show every diff information on the command line.We wanna use vscode as our default difftool:
>git config --global diff.tool vscode 

>[!info] we just give a name of the default difftool

>git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE"

>[!info] *the --diff tells this cmd is going to be used as a difftool, two dallor signals are two placeholders for the old and the new copies of a file.*

>git difftool

>[!info] vscode show what's different between the storage place and the un-added change

>git difftool --staged
 
>[!info] vscode show the difference between the repository file and the storage place.
### History
>git log
>git log --oneline
>git log --oneline --reverse

type the line and browse the history log of your commitment.
now let's show the change of each log
>git show HEAD(~n)

>[!info] you can also type unique identifier.
> ~ + number means the \<number\> steps to go back from the HEAD pointer

Type

>git show HEAD~1:.gitignore

to show exact code of the file 
## **Referrences**:

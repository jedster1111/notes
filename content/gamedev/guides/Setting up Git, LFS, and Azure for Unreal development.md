---
title: Setting up Git, LFS, and Azure for Unreal development
---
Here I'll be showing you my current setup for version control with Unreal Engine.  
I've been using this for learning game development, so haven't tested this setup with larger teams. 
But for solo devs, or small teams, this feels like a great way to setup version control for your projects.  
I'm running on windows, but I think using this setup on other platforms shouldn't be too different.

## Why?

### Version control?

I won't go into too much detail, but using version control brings a lot of benefits, without too much effort.  

Firstly, being able to push your code into a remote repository means that your work is safe and backed up. If 
something were to happen to your development machine, you'd be able to pull your work down onto another machine 
and continue working.  

Splitting your work up into small commits can help mentally to break big tasks into smaller ones. 
Even better, if you run into a bug, and are not sure when it was introduced, you can easily walk back through your commit 
history until you find the point where the issue was introduced.  

Allows you to easily start collaborating with other people.  

For me, using version control is quite motivating, and I like looking back at the commits I've added at the end of the day.  

There's definitely lots more reasons, but I'll leave those for now...

### Git? Why not Perforce?

I come from a software engineering background, so have some experience with git, and find it very easy to use and setup. 
In contrast, I've setup a Perforce server before, and really did not have a great time. Maybe it gets better but I found 
it pretty clunky to use. Especially for now, while I'm learning and mostly working on my own, using git seems ideal, and 
I imagine others feel the same way.

### LFS?

[https://git-lfs.com/](https://git-lfs.com/)

#### Why do we need it?

Because of the way that Git works, without LFS your `.git` folder will grow in size rapidly. Every time you commit a change 
to a binary file, Git will have to keep a copy of both the old and new versions. If you're making changes to large files, 
this will add up quickly.

#### What does Git LFS do differently?

Instead of saving binary files, like `.uasset`, directly in git, instead only pointers to the various versions of your files will be saved. 
Then those binary files will be uploaded to a Git LFS server, which in most cases will also be where 
your code (non-binary files) will be uploaded to. This means that you only need to download the version of your binary 
files that you currently have checked out, saving space on your hard drive. It also helps you get around Git repository size 
restrictions, which you'll hit quickly with an Unreal project.  

This sounds a bit complicated, but in practice you don't really have to worry about it. 
For the most part, you just commit and push as usual.

#### Locking

Since Git can't produce a sensible diff between two versions of a binary file, you have to ensure that two people are not 
working on the same file at the same time. Git cannot resolve conflicts between binary files. 
Git LFS supports locking, which can help enforce this. If someone locks a file, no one else will be able to make any changes 
to that file until the file has been unlocked.  

Honestly, the support for this isn't the best, and will probably be one of the biggest disincentives to using git as your 
team size grows.

### Azure?

#### Why not GitHub?

GitHub is great, and supports Git LFS, but has a very limited free tier for storage space, uploads and downloads. You will 
hit these limits almost immediately, and it gets expensive pretty quickly after that.

#### What about Azure?

**Unlimited Git LFS storage is completely free.** This is crazy to me. I'm surprised more people aren't using this.  
I find the UI a bit clunkier than GitHub, and you can't use GitHub Actions (although Azure does have a pipelines 
feature that I haven't tried yet), but hey... free is free.

## Setup

### Prerequisites

Have the following installed
- Git
- Unreal Engine

### Install Git LFS

Here are the docs on [how to install Git LFS](https://docs.github.com/en/repositories/working-with-files/managing-large-files/installing-git-large-file-storage?platform=windows).

Check that it's installed:
```sh
$ git lfs install
> Git LFS initialized.
```

### Setup your Unreal project

Create a new Unreal project.

### Initialise Git

Open a terminal in the top level folder where your project lives.
```sh
$ git init
> Initialized empty Git repository in E:/GameMaking/GitLFSTest/.git/
```

Let's also setup a sensible `.gitignore` so we don't add things to Git that we don't want to track.  
So create a new `.gitignore` file and fill it with [the content found here](https://gist.github.com/jedster1111/5a86c675ae71130072733983ce9c2640#file-gitignore).  
This `.gitignore` contains more than we really need, so feel free to tweak it or find another one online.

### Setup Git LFS

Add a `.gitattributes` file and fill with [the content found here](https://gist.github.com/jedster1111/5a86c675ae71130072733983ce9c2640#file-gitattributes).  
This tells LFS which files should be stored by LFS, and also whether a file is lockable or not.

Let's make sure everything is working

```sh
# Make sure lfs is setup
$ git lfs install
> Git LFS initialized.

# Let's stage our changes
$ git add .

# Let's check to see if LFS is going to handle our .uasset files correctly.
# Notice `Git` next to the .ini files, but `LFS` next to our .uasset files.
$ git lfs status
> Objects to be committed:
>
>        .gitattributes (Git: 431770b)
>        .gitignore (Git: 51d0b1e)
>        .vsconfig (Git: 5fffaa6)
>        Config/DefaultEditor.ini (Git: 8c349cd)
>        Config/DefaultEditorPerProjectUserSettings.ini (Git: 0c07f9a)
>        Config/DefaultEngine.ini (Git: 2583dea)
>        Config/DefaultGame.ini (Git: 941b44a)
>        Config/DefaultInput.ini (Git: 5473b9a)
>        Content/FPWeapon/Audio/FirstPersonTemplateWeaponFire02.uasset (LFS: a795536)
>        Content/FPWeapon/Materials/BaseMaterial.uasset (LFS: e915f80)
>        Content/FPWeapon/Materials/FirstPersonProjectileMaterial.uasset (LFS: 1cf7b9b)
>        Content/FPWeapon/Materials/M_FPGun.uasset (LFS: fe7d865)
>        Content/FPWeapon/Materials/MaterialLayers/ML_GlossyBlack_Latex_UE4.uasset (LFS: a9e1752)
>        Content/FPWeapon/Materials/MaterialLayers/ML_Plastic_Shiny_Beige.uasset (LFS: 5681547)
> ...
```

### First commit

```sh
# Optional: Use the following line to use VSCode to edit your commit messages
# Now when you commit, VSCode will open. Write your commit message, save and close.
# It will also give a handy UI when doing interactive rebases.
$ git config --global core.editor "code --wait"

# Lets make sure git uses the correct email to make commits
$ git config user.name "Your Name Here"

# The email should match what you use to sign up to azure later
$ git config user.email your@email.example

$ git commit

# Let's check everything was committed.
$ git status
> On branch main
> nothing to commit, working tree clean
```

### Azure setup

Go to [https://azure.microsoft.com/en-gb/products/devops/?nav=min](https://azure.microsoft.com/en-gb/products/devops/?nav=min)  

Click "Start free"  

Sign in or create an account.  

You should be redirected to [https://dev.azure.com/thenameyoupicked](https://dev.azure.com/thenameyoupicked) where you should see 
a screen to create a new project.  

Create a new project.  

Once created, you can click the "Repos" button to see your empty repository.

### Let's push

Before we push, there's a configuration change you need to make to avoid an issue.  
See [this thread](https://github.com/git-lfs/git-lfs/issues/4655) for a discussion around it.

```sh
git config http.version HTTP/1.1

# If you're worried you might forget to set this in future projects, you can set the config globally.
git config --global http.version HTTP/1.1
```

On your empty "Repos" page, you can find instructions on how to push to your remote.  
Make sure you are using HTTPS to setup your remote, as Git LFS doesn't have good support for SSH unfortunately.  

```sh
$ git remote add origin https://thenameyoupicked@dev.azure.com/thenameyoupicked/Git%20LFS%20Test/_git/Git%20LFS%20Test

# When you push, a pop up should open prompting you to login to the Azure account we created earlier
$ git push -u origin --all
> Locking support detected on remote "origin". Consider enabling it with:
>   $ git config lfs.https://thenameyoupicked@dev.azure.com/thenameyoupicked/Git%20LFS%20Test/_git/Git%20LFS%20Test.git/info/lfs.locksverify true
> Uploading LFS objects: 100% (421/421), 822 MB | 51 MB/s, done.
> Enumerating objects: 591, done.
> Counting objects: 100% (591/591), done.
> Delta compression using up to 16 threads
> Compressing objects: 100% (575/575), done.
> Writing objects: 100% (591/591), 84.22 KiB | 1.29 MiB/s, done.
> Total 591 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
> remote: Analyzing objects... (591/591) (21 ms)
> remote: Validating commits... (1/1) done (2 ms)
> remote: Storing packfile... done (31 ms)
> remote: Storing index... done (45 ms)
> To https://dev.azure.com/thenameyoupicked/Git%20LFS%20Test/_git/Git%20LFS%20Test
>  * [new branch]      main -> main
> branch 'main' set up to track 'origin/main'.
```

Now if you refresh your Repository page, you should see your code!  

This is pretty cool, and at this point, you're pretty much good to go. You can work on your project, 
save your progress, and push it to a remote. All for free.  

If you want to leave it here, you can. But if you want to take things a bit further there's a couple 
more things we can do.

## Further Improvements

### Enable locking support

You might have noticed the message when pushing, prompting you to enable locking support.
Since Azure support Git LFS locking, let's enable it!

```sh
git config lfs.https://thenameyoupicked@dev.azure.com/thenameyoupicked/Git%20LFS%20Test/_git/Git%20LFS%20Test.git/info/lfs.locksverify true
```

### Unreal git integration

#### The built-in git integration

There is an experimental git plugin, that comes installed by default in Unreal.  

Unfortunately (at the time of writing) it's not great. My biggest grievance is it makes some operations 
painfully slow. Moving folders is particularly bad, as it seems to perform git operations for every file, one at a time.  

#### Project Borealis' Git plugin

[https://github.com/ProjectBorealis/UEGitPlugin](https://github.com/ProjectBorealis/UEGitPlugin)

Fortunately, there is a plugin you can use, which from my use so far has been great.  

It runs much faster than the default plugin, and will automatically lock files as you edit them. It will also 
unlock files once you've pushed your changes remotely.

#### Installation

Go to [the releases page](https://github.com/ProjectBorealis/UEGitPlugin/releases) and download the "Source code" zip file for 
the latest version.  

Extract the folder, and copy the folder inside, called `UEGitPlugin-x.xx`.  

Create a `Plugins` folder in your Unreal project's top directory.  

Paste the `UEGitPlugin-x.xx` folder into your Unreal project's Plugins folder.  

Your file structure should look like `E:\GameMaking\MyUnrealGameProject\Plugins\UEGitPlugin-x.xx`, and inside the UEGitPlugin folder 
you should see a bunch of files and folders, like a `README`, a `LICENSE` file, and a `Source` folder.  

Let's follow [the setup instructions outlined in the plugin's README](https://github.com/ProjectBorealis/UEGitPlugin?tab=readme-ov-file#note-about-unreal-configuration).  

Add the following to `Config/DefaultEditorPerProjectUserSettings.ini`

```sh
[/Script/UnrealEd.EditorLoadingSavingSettings]
bSCCAutoAddNewFiles=False
bAutomaticallyCheckoutOnAssetModification=True
bPromptForCheckoutOnAssetModification=False
bAutoloadCheckedOutPackages=True
```

Add the following to `Config/DefaultEngine.ini`
```sh
[SystemSettingsEditor]
r.Editor.SkipSourceControlCheckForEditablePackages=1
```

I've just copied the above from the README, so I'd recommend checking the instructions in the README as they're 
more likely to stay up to date.  

Open your project in Unreal, and you should be prompted to build. Click ok.

Open `Edit -> Plugins` and search for git.  

You should see `Git LFS 2` by Project Borealis. Ensure it's enabled.  

Close the Plugin window.  

In the bottom right of the editor you should see a "Revision Control" button. Click it and then press "Connect to Revision Control".  

In the Provider dropdown, select `Git LFS 2`. The default settings should be good, so press ok.  

Let's make another commit, since we've added and configured our plugin.

```sh
$ git add .
$ git commit -m "Add UEGitPlugin"
$ git push
```

#### How to use

Lets see locking in action!

Make a small test change to a `.uasset` file. I'm going to move something in my starter level. Save your changes.

In your terminal run the following:

```sh
$ git lfs locks
> Content/FirstPerson/Blueprints/BP_FirstPersonCharacter.uasset                                   Jed Thompson    ID:4
> Content/FirstPerson/Maps/FirstPersonMap.umap                                                    Jed Thompson    ID:1
> Content/__ExternalActors__/FirstPerson/Maps/FirstPersonMap/8/CF/ZNIOCRD90Z70MMV9NMW778.uasset   Jed Thompson    ID:2
```

The plugin has automatically locked these 3 files for me. If anyone else tried editing the same files on another machine, 
they would be stopped.  

Lets commit.

```sh
$ git add .
$ git commit -m "Made some small test changes"
```

If we want our files to be unlocked automatically, **we need to push from within the Unreal editor!**  

If we just push from the terminal, we'll have to manually unlock our files.  

So go to your unreal project and, in the bottom right, click `Revision Control->Push pending local commits`.  

Go to Azure, and you should see your commit.  

And in the terminal, we can see that our locks have been released.  

```sh
$ git status
> On branch main
> Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean

$ git lfs locks
> 
```

Again, I think this is really cool!

### Automatic unlocking when pushing from the terminal

But what if you don't want to have to remember to push from within Unreal?  

Check out this project by negokaz! - [https://github.com/negokaz/git-lfs-auto-unlock](https://github.com/negokaz/git-lfs-auto-unlock).  

Create a new file: `E:\GameMaking\MyGameProject\.git\hooks\reference-transaction` and fill with the content found [here](https://github.com/negokaz/git-lfs-auto-unlock/blob/85f3484b385fce73a9e5eba80602453e282750f5/git-hooks/reference-transaction).  

Let's test it out!  

Make a small change to a `.uasset` file and save.  

```sh
$ git status
> On branch main
> Your branch is up to date with 'origin/main'.

> Changes not staged for commit:
>   (use "git add <file>..." to update what will be committed)
>   (use "git restore <file>..." to discard changes in working directory)
>         modified:   Content/FirstPerson/Blueprints/BP_FirstPersonCharacter.uasset

> no changes added to commit (use "git add" and/or "git commit -a")

$ git lfs locks
> Content/FirstPerson/Blueprints/BP_FirstPersonCharacter.uasset                                   Jed Thompson    ID:6
> Content/__ExternalActors__/FirstPerson/Maps/FirstPersonMap/0/XV/VTAF5COFGLO9YT51MQCVTR.uasset   Jed Thompson    ID:7

$ git add .

$ git commit -m "Some more small changes"
> [main aa2bf44] Some more small changes
>  2 files changed, 4 insertions(+), 4 deletions(-)

$ git push
> Consider unlocking your own locked files: (`git lfs unlock <path>`)
> * Content/FirstPerson/Blueprints/BP_FirstPersonCharacter.uasset
> * Content/__ExternalActors__/FirstPerson/Maps/FirstPersonMap/0/XV/VTAF5COFGLO9YT51MQCVTR.uasset
> Uploading LFS objects: 100% (2/2), 30 KB | 0 B/s, done.
> Enumerating objects: 25, done.
> Counting objects: 100% (25/25), done.
> Delta compression using up to 16 threads
> Compressing objects: 100% (10/10), done.
> Writing objects: 100% (13/13), 1.06 KiB | 542.00 KiB/s, done.
> Total 13 (delta 5), reused 0 (delta 0), pack-reused 0 (from 0)
> remote: Analyzing objects... (13/13) (118 ms)
> remote: Validating commits... (1/1) done (0 ms)
> remote: Storing packfile... done (28 ms)
> remote: Storing index... done (35 ms)
> To https://dev.azure.com/jedster1111com/Git%20LFS%20Test/_git/Git%20LFS%20Test
>    a3ea6e7..aa2bf44  main -> main
> Unlocked Content/FirstPerson/Blueprints/BP_FirstPersonCharacter.uasset
> Unlocked Content/__ExternalActors__/FirstPerson/Maps/FirstPersonMap/0/XV/VTAF5COFGLO9YT51MQCVTR.uasset

$ git lfs locks
> 
```

Again, super cool. Now we can push from the terminal and still unlock files that we've locked.  

Also worth noting that both the UEGitPlugin and the hook by negokaz, are clever enough to only unlock the files 
that you've pushed.  

## Conclusion

That's all I'm going to cover for now. The UEGitPlugin seems quite advanced, and does handle some branching cases, 
but I haven't experimented with it, so can't comment.  
I'm not the best technical writer, so what I wrote was a bit dense, but hopefully it's understandable and useful.  
Thanks for reading.
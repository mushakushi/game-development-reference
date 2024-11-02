# Game Development Reference
A non-exhaustive reference document containing high-level guidelines on how to collaborate on any game development project. 

Looking to learn about more positions in game-dev? Check this resource [here](https://github.com/notpresident35/awesome-learn-gamedev).

# Role-Agnostic Advice

## General Project Structure
An important consideration when beginning development on any size project, regardless of engine, is project structure. Good structure ensures that your project is maintainable and scalable, as well as promoting quality code standards. 

There’s no one correct structure, but I heavily recommend one like this, which is a combination of official recommendations from Unreal and Unity, simplified a little:

```bash
|-- [Project]
	|-- Audio
		|-- Music
		|-- Sound
	|-- Art
		|-- Materials
		|-- Models 
		|-- Textures 
	|-- Level
		|-- Levels (e.g. Maps or Scenes)
		|-- User Interfaces 
		|-- [Prefabs in Unity can also go here]
	|-- Source (or Scripts)1
	|-- Narrative2 
		|-- Dialogue 
			|-- English 
			|-- [Other locales]
		|-- Scripts (i.e., narrative scripts and storyboards)
	|-- Docs3
|-- [Non-source settings or assets pertaining to built-in features]
|-- [Any Third Party Asset Folders]4
```

$^1$ See the Source Folder Organization section.  
$^2$ This is my own advice and is largely speculative.  
$^3$ Prefer to put documentation on a shared repository or document rather than in the game’s files.  
$^4$ **Read the EULA for any and all assets you use.**  

### Unity
Read more about [best practices for organizing your Unity project](https://unity.com/how-to/organizing-your-project) (which is also corroborated by this [article](https://docs.unity3d.com/Manual/cus-layout.html)). 

### Unreal 
Read more about [the recommended project structure for Unreal Engine 5](https://github.com/Allar/ue5-style-guide#structure). 

### Godot
Read more about [project organization guidelines for Godot](https://docs.godotengine.org/en/stable/tutorials/best_practices/project_organization.html).

## Using Git

### Importance of Git
While you can use GitHub Desktop as an easy first way to work with your repository, I highly recommend that you start to learn the Git CLI as soon as possible — especially since you will not get all the features of Git via GitHub Desktop.

**Getting started:** https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F  
**Git docs:** https://git-scm.com/docs  

>[!NOTE]
>Local: Only on your machine. Remote: Shared on a server.

### Cloning Remote
- If you want to start working with a git repository you first need to clone it using this command, which will set the **origin** alias to the same url:   
```bash
git clone <git-url>
```
Add the `--recursive` command if the repository contains git submodules.

- If, for whatever reason, the origin URL changes, update it with:  
```bash
git remote set-url origin <new-git-url>
```

- To, instead, create a **remote** repository from a **local**, unversioned (e.g. non-git repo), folder read [this](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github#initializing-a-git-repository).

### Standard Git Workflow
A standard Git workflow tends to follow these steps in order.  

#### Creating and Switching Branches
- To create a local branch that does not sync with an existing remote branch, use: 
```bash
git branch <local-branch>
```

- To create a local branch that syncs with an existing remote branch, use: 
```bash
git branch <local-branch> origin/<remote-branch>
```
> This creates a local branch, “<local-branch>,” that is synced with a remote branch, “<remote-branch>.” The two branches don’t need to, but should, share the same name.

- ⭐ To create and switch to a local branch that syncs with an existing remote branch, use: 
```bash
git checkout -b <local-branch> origin/<remote-branch>
```

- To create and switch to a local branch that does not sync with an existing remote branch, use: 
```bash
git switch -c <branch>
```

- ⭐ To switch to an already existing local branch, use:
```bash
git switch <branch>
```
#### Getting Remote Changes
- To fetch the latest changes in an existing remote branch, use one: 
```bash
git fetch origin <branch>
```
```bash
git fetch
```

- After fetching, check the status of the current local branch compared to its remote using: 
```bash
git status 
```

- ⭐ To fetch and download  the latest changes in an existing remote branch, use one: 
```bash
git pull origin <branch>
```
```bash
git pull
```

The `git status` command will let you know if your local repository is behind the local **reference** “origin/<branch>”, which corresponds to a branch on a remote called _origin_. 

This status doesn't reflect the remote branch's current state – but the state of the local **reference** based on the latest fetched data. The command `git pull` updates this status by first running `git fetch` to synchronize with the remote repository, updating the local origin/master reference, and then performs a `git merge` to integrate these updates into your current branch.

If there are any discrepancies between the status you see locally and what you see remotely, it’s likely you didn’t run `git fetch`. 

>[!CAUTION]
>So many things will go wrong if you don’t understand these things and stay up-to-date

#### Applying Local Changes 
At this point, you can make changes by editing the project as you normally would. Once you are done for the day follow these steps: 

1. Use `git add .` to **track** all your changes (use `git reset git` or `git reset <file>` to undo this step). 
2. Use `git commit -m <commit-message>` to **stage** your tracked changes for a commit. 
3. Use  `git push` to **apply** those changes to remote.

If there is no tracking information for a branch use, 
```bash
git push -u origin <remote-branch>
```
Alternatively, 
```bash
git branch --set-upstream-to=origin/<remote-branch> <local-branch>
```
to add tracking information without pushing first. 

>[!IMPORTANT]
>This step should always be performed – unless you already specified this information on branch creation.

#### Push Daily
You should be pushing code every day you work on it, regardless of what branch it is to -- or how little the contribution is.

### Git Submodules 
Sometimes, it is impossible to store some assets in the same repository as your game code. For example, some third-party assets might be protected by an EULA that prevents you from redistributing, and you certainly shouldn’t “open-source” paid assets. Moving assets to a separate repository can allow you reduce space usage limits. Popular solutions include [Perforce](https://www.perforce.com/)$^1$, [Git LFS](https://docs.github.com/en/repositories/working-with-files/managing-large-files/installing-git-large-file-storage), [Unity DevOps](https://docs.unity.com/ugs/manual/devops/manual/unity-devops-home), but it’s more practical to use a private repository as a git submodule, which you can read about [here](https://github.blog/open-source/git/working-with-submodules/). 

You can specify the folder to place a submodule in using: 
```bash
git submodule add <submodule-origin-url> <relative-path-to-submodule>
```

To pull the latest changes from a submodule for the first time use `git submodule update --init --recursive --remote`, after which use: `git pull --recurse-submodules`. Like mentioned earlier, set up tracking information; this can be done by `cd`ing into the submodule's root directory and running `git checkout -b <branch> <origin>/<branch>` ([see more](https://stackoverflow.com/a/36375256/25169483)).

To push a commit to a submodule, you'll need to `cd` into each submodule, from which you can issue git commands on that repository as usual. Alternatively, you can use [this command](https://stackoverflow.com/a/72219242/25169483): 
```bash
git submodule foreach "git add . && git commit -m <commit-message> && git push"
```

$^1$ _Perforce (or a self-hosted HelixCore instance) is an industry standard and, if money is not an issue, should be preferred._

### Merge Requests
Once the work on your branch is completed, you can merge your branch into master using a merge request, which you can read about [here](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/merging-a-pull-request).

### Branching Strategy
Despite [conventional wisdom](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow), [a number of professional gaming studios](a number of professional gaming studios) recommend pushing directly to the current production branch (e.g. main). This is known as _trunk-based development_, which you can read about [here](https://trunkbaseddevelopment.com/). This style of development significantly reduces merge conflict errors and keeps a whole team up to date with the latest work. You can manually mark releases in most, if not all, git managers (e.g. GitHub); use this feature. 

### Deleting Branches
You don’t need a branch that has already been merged to main; these can be deleted off of Github but still need to be removed locally. 

- ⭐ To delete a local branch that has been merged to remote, use: 
```bash
git branch -d <branch-or-branches>
```

- To delete a local branch that may not have been merged to remote, use: 
```bash
git branch -D <branch-or-branches>
```
You can list multiple branches separated by a space (see [more](https://stackoverflow.com/questions/3670355/can-you-delete-multiple-branches-in-one-command-with-git)). 

### Merging Branches
Merging can be performed like this:
```bash
git switch A
git pull
git merge B
```
Then, Fix any merge conflicts and commit and push your changes. In some cases, this can be done directly in GitHub in one click.

>[!NOTE]
>More on branching and merging: https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging

### Resolving Merge Conflicts
In many cases, a merge conflict can be avoided by simply remembering to stay up-to-date with the remote changes (I’ve had this happen by Git not recognizing a Unity scene file as having merge commit markers). In the case this fails, you can resolve a conflict by using an editor such as Rider, VScode, or Meld. If, for some reason, git does not recognize the merge conflict, you can try to [manually mark it as conflicted](https://stackoverflow.com/questions/2780483/is-there-a-way-to-make-git-mark-a-file-as-conflicted), and retry. If this still fails to work, you may need to resolve it by hand. Understand that the skeleton of a merge conflict looks like this:

```bash
<<<<<<< HEAD 
Changes from the current branch (e.g., the changes YOU made). 
======= 
Existing changes from a branch you're trying to merge into your current branch (e.g., the changes SOMEONE ELSE made).
>>>>>>> <branch>
```

Decide which change you want to accept, and delete everything else in the merge conflict, including the merge conflict markers to resolve it. Note that an empty change denotes deletion.

For example, to accept only your changes, the above conflict would be resolved as: 

```bash
Incoming changes from the branch you are merging into (i.e., changes from the current branch – e.g., the changes YOU made).
```

### Discarding Local Changes
To completely discard any and all local **tracked** changes, replacing them with the latest remote changes, use this **irreversible** command:
```bash
git reset --hard <branch>
```
If any local changes still persist after running this command, first, make sure your changes are tracked; then, run this command that will permanently erase every local change not in remote such as “temporary build artifacts or merge conflict files” ([git clean docs]([url](https://git-scm.com/book/id/v2/Appendix-C%3A-Git-Commands-Basic-Snapshotting#:~:text=The%20git%20clean%20command%20is,in%20Cleaning%20your%20Working%20Directory))): 
```bash
git clean -df
```

### Fixing Detached Head State 	
A deatched head means that you have checkouted a specific commit instead of a branch, you will get a warning in the format `HEAD detached at <short-commit-hash>`. 

To save any changes you've made in this stage, follow these steps: 
1. Put your changes on a temporary, local branch using `git branch <temporary-branch>
2. Checkout your desired branch (e.g. main) using `git checkout <target-branch>`

If you want to instead discard your local changes, see the above section. 

Finally run `git merge <temporary-branch>` while on `<target-branch`. 

### Exiting Vim Git Commit Editor
On Windows, press `Esc` then type `:wq`; press `Enter`. 

You _may_ have gotten this if you used `git commit`, instead of `git commit -m “<commit-message>”` – which is OK if you prefer it that way; however, It can happen for a number of other reasons.

### Configuring Git
If you receive the error `Filename too long` on Windows, run the following command in an administrator command prompt ([source](https://stackoverflow.com/questions/22575662/filename-too-long-in-git-for-windows)): 

```bash
git config --system core.longpaths true
```

### Using Git With Unity
Unity’s scenes and prefabs are both stored in very long, erratic files that are often mistaken by git as binaries even when represented as text. This makes them difficult to merge. For this reason, **only one person should be working on a given scene or prefab at any time**. If you were using a different Git manager, like Perforce, you could lock these types of files. 

## Continous Integration & Development

### Introduction
You can use continuous integration tools (e.g. [Game CI](https://game.ci/docs/)), in combination with merge requests for merge requests, which you can read more about [here](https://trunkbaseddevelopment.com/continuous-review/).

See an example CI/CD pipeline from Riot Games [here](https://technology.riotgames.com/news/legends-runeterra-cicd-pipeline). Read a thesis [here](https://comserv.cs.ut.ee/ati_thesis/datasheet.php?id=74718&language=en). This section is a stub, because it is not affordable for small scale development projects – locally run tests are much more useful within these constraints, in my opinion.

# Narrative Design

## Ink vs. Yarn Spinner 
[Ink](https://www.inklestudios.com/ink/) and [Yarn Spinner](https://docs.yarnspinner.dev/) are the most popular, open-sourced systems for writing game scripts (i.e. dialogue) that should be preferred over a built-in system (such as using lists, strings, plain text, etc.). Although some people prefer Ink’s writing experience over Yarn’s, Ink [lacks localization support](https://github.com/inkle/ink/issues/98) and [a built-in way to add speaker names to text](https://github.com/inkle/ink/issues/228#issuecomment-261188478). Yarn also has a VSCode extension, which makes writing scripts very straightforward. I prefer to use Yarn for these reasons. 

# Level Design

## Resources

| Topic | URL | 
| --- | --- |
| Spatial Communication | https://www.youtube.com/watch?v=AKeUZVikPV8&list=WL&index=2 |
| Non-Linear Design | https://www.youtube.com/watch?v=CTBor4rhnQs&list=WL&index=1 |

# Programming 

## Source Folder Organization
The organization of the Source folder will look different depending on the engine’s needs. The general idea is to ensure that each feature-set has a single responsibility and to minimize dependencies and circular references between them.

Briefly, here are some common examples of multiple features mistaken as one:

| Misconception | Reason |
| --- | --- |
| Logic and its user interface (e.g. inventory and inventory UI) |  UI should not depend on logic (see the MVC pattern). |
| Input and logic that depends on input (e.g. input handling and player movement) | Input handling is a deceptively complex feature that should not be coupled with anything. |
| A math library, which is always reusable, and logic that depends on it (e.g. AI algorithms and AI) | Libraries should be standardized across all features. It’s counterintuitive and unmaintainable to have multiple libraries with the same purpose. |

>[!NOTE]
> By separating out these into unique features we can better maintain code.

### Unity
```bash
|-- Source 
	|-- [Project Top Level Folder]
|-- [Feature]
		|-- Editor
			|-- [Project].[Feature].Editor.asmdef
	|-- Runtime
			|-- [Project].[Feature].asmdef
		|-- Tests
		|-- Editor
			|-- [Project].[Feature].Editor.Tests.asmdef
		|-- Runtime
			|-- [Project].[Feature].Tests.asmdef
|-- Shaders 
|-- [Feature]
```

Files ending with `.asmdef` are Assembly definition files, which you can read more about [here](https://docs.unity3d.com/6000.0/Documentation/Manual/ScriptCompilationAssemblyDefinitionFiles.html): in short, they allow a programmer to build isolated features that don’t need to be recompiled if no changes are made to them and their dependencies.

### Unreal Engine
```bash
|-- Source
	|-- [Project Top Level Folder]
		|-- [Module]
			|-- Private
				|-- Tests
					|-- [Feature]Tests.cpp
				|-- [Module]Module.cpp
			|-- Public
				|-- Tests
					|-- [Feature]Tests.h  # if any
				|-- [Module]Module.h
			|-- [Module].Build.cs # if any1
	|-- [Third-party libraries. Same layout as above]
```

$^1$ You can just use `FDefaultGameModuleImpl` if there is nothing unique to write.

### Use JetBrains Rider
In most cases, prefer to use [Rider](https://www.jetbrains.com/rider/) over any other IDE, if it is affordable or if you are a university student. 

When using this IDE, you may find it convenient to put every feature in a top level folder of the same name as the project, below the Source folder, for better automatic namespacing.
Using a Packages
Third-party assets, most commonly as packages, are also relevant to game development. Prefer to install third-party assets as currently maintained packages, rather than in separate, top-level folders.

The package manager of choice for Unity is [OpenUPM](http://openupm.com). Both [Godot](https://github.com/godotengine/godot-proposals/issues/142) and Unreal Engine (although Plugins can be considered a substitute) do not have package managers. 

### Creating Package 

| Engine | Method | 
| --- | --- |
| Unity | https://docs.unity3d.com/Manual/CustomPackages.html|
| Unreal Engine (using Plugins) | https://dev.epicgames.com/documentation/en-us/unreal-engine/game-features-and-modular-gameplay-in-unreal-engine <br/> https://www.unrealengine.com/en-US/blog/modular-game-features-in-ue5-plug-n-play-the-unreal-way | 

## Programming Principles 
These make collaborating much easier and the game much more maintainable. It’s good to start using these now.

Former Unity and Ubisoft principal engineer, Sebastian Aaltonen writes, 
> People seem to have vastly different opinion[s] about good code. Some people think it’s coe that is so good that it stays in the product for a long time and has lots of users. But I think good code is code that you can delete easily without breaking other code [and has] minimal dependencies.  
> https://x.com/SebAaltonen/status/1826589641962639740

and avoids overcomplicating things. 

For example, if you’re not writing automated tests, there is no reason to separate commands and command handlers – even though that is the technically “correct” way of going about things. 

Please, don’t mistake a lack of understanding for complexity; if something is too complex, you should thoroughly understand why and a better way of fixing it.

This section only goes over a limited set of information and should be complementary to a proper computer science education. 

### SOLID Principles 
You may already be familiar with these, but don’t forget to use them!

### SRP for Scene Objects
In the same way – and for the same reason, objects placed in a scene (e.g. Mono Behaviours, UObjects, nodes, etc) should adhere to the Single Responsibility principle (SRP). For example, this means that you should not have one object called “Manager” with every single management script; instead, create a “Scene Manager,” “Audio Manager,” “Input Manager,” etc. 

### Abstract Base Class vs Interface
This is an important discussion to have when talking about most of the SOLID principles. According to [this StackOverflow post](https://stackoverflow.com/a/479168/25169483): 

> When we talk about abstract classes we are defining characteristics of an object type; specifying what an object is. When we talk about an interface and define capabilities that we promise to provide, we are talking about establishing a contract about what the object can do.

On top of the considerations in the above thread, I rarely find the need for to use an abstract class and only encourage its use if you have shared behavior – most of the time this is not the case (e.g. an `IComparable<T>` in C#). 

### Singletons
TL;DR don’t use these ever. They violate the Dependency Inversion Principle. Instead, rely on Dependency Injection or static functions.

Not to be confused with just having a [single instance](https://softwareengineering.stackexchange.com/a/40610) – like having a static, singly-responsible manager class that is not depended on.

## Testing 

### Introduction
Writing tests allow you to ensure the project works as intended without relying on human error. Further, writing tests encourage SOLID principle conformity … because they’re largely impossible to write without them.

### Automatic Testing
On writing tests, I highly recommend this GDC talk: https://www.youtube.com/watch?v=X673tOi8pU8, although I will add the most important test is that your game actually builds. 

Tests should be written for code that has a clear definition of what a “correct” implementation looks like such as a math and physics library, or if the code is not expected to change. You should additionally implement a test whenever a bug is introduced to prevent regression bugs. 

### Test Driven Development 
A good, three part article on test-driven development: https://gamesfromwithin.com/stepping-through-the-looking-glass-test-driven-game-development-part-1.

> The results of the case studies indicate that the pre-release defect density of the four products decreased between 40% and 90% relative to similar projects that did not use the TDD practice. Subjectively, the teams experienced a 15–35% increase in initial development time after adopting TDD.
> https://www.microsoft.com/en-us/research/wp-content/uploads/2009/10/Realizing-Quality-Improvement-Through-Test-Driven-Development-Results-and-Experiences-of-Four-Industrial-Teams-nagappan_tdd.pdf

Test driven development does not appear to be useful to games that need to be produced within a short amount of time, or games that will not be maintained actively past shipping. Some people argue that this form of development is not helpful to game development due to its ever-changing nature.

Some articles from Riot Games on testing [here](https://technology.riotgames.com/tags/testing).

### Manual Testing
In addition to automated tests, always use manual testing. Consider creating an isolated scene for each feature, which you can go and manually debug if something breaks. In this way, you can still effectively squash regression bugs. Compared to the alternative this can become less thorough and maintainable – especially in large codebases.

## Unity Tips 

| Tip | Information |
| --- | --- |
| Serialized Dictionaries | Serialized dictionaries do exist in the CoreRP library ([docs](https://docs.unity3d.com/Packages/com.unity.render-pipelines.core@17.0/api/UnityEngine.Rendering.SerializedDictionary-2.html)); for whatever reason, they are not part of the Unity Engine core. | 
| Observer Scriptable Object Pattern | When using the [Observer pattern](https://refactoring.guru/design-patterns/observer), prefer to use [Scriptable Object Event Channels](https://youtu.be/WLDgtRNK2VE?si=OjcJ6IuyUfzevii7&t=354), as it additionally allows for cross-scene communication. <br/> Keep in mind that [C# events](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/) are an implementation of the Observer pattern and are to be used when no additional event handling behavior is used. |
| Use OpenUPM over Git Packages | Packages maintained by a registry are better, in my opinon, because they can include third-party dependencies, are installed automatically, and overall I find the experience to be better, especially with their CLI. |
| Use Awaitables over Coroutines | For versions of Unity after 2023 use Unity’s [Awaitables](https://docs.unity3d.com/6000.0/Documentation/Manual/async-await-support.html). UniTask predates this solution. is supported on versions of Unity before 2023, and can be found [here](https://openupm.com/packages/com.cysharp.unitask/). |
| Use Serializable Interfaces | Don’t use abstract monobehaviours because you can’t serialize an interface – in fact, with [this package](https://openupm.com/packages/com.mackysoft.serializereference-extensions/), you can. |
| Use Animancer over Mecanim | [Animancer](https://assetstore.unity.com/packages/tools/animation/animancer-pro-116514?utm_source=google&utm_medium=cpc&utm_campaign=as_as_as_amer_amer-t1_en_pu_dsp-pmax_acq_pr_2024-04_x_cc3022_ev_id:71700000118169934&utm_content=_id:_SpringSalePMAX2023_&utm_term=&gad_source=1&gclid=Cj0KCQjw5ea1BhC6ARIsAEOG5pwiRAp3VMMG5r1iOAO7jF_GXpoP6T7m4289YTUM09uIS9u7dTR0l-0aAsYPEALw_wcB&gclsrc=aw.ds) is expensive and needs to be bought per seat but eliminates issues with Mecanim |

## Unreal Engine Tips 
| Tip | Information |
| --- | --- |
| Unreal Engine notes | https://ari.games/ | 
| Eryk’s Unreal Engine & GameDev Notes | https://rootkiller.pl/gamedev | 

# Game Development Reference
A non-exhaustive reference document containing high-level guidelines on how to collaborate on any game development project. 

Looking to learn about more positions in game-dev? Check this resource [here](https://github.com/notpresident35/awesome-learn-gamedev).

# Role-Agnostic Advice

## General Project Structure
An important consideration when beginning development on any size project, regardless of engine, is project structure. Good structure ensures that your project is maintainable and scalable, as well as promoting quality code standards. 

There’s no one correct structure, but I heavily recommend one like this, which is a combination of official recommendations from Unreal and Unity, simplified a little:

```
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
>**Getting started:** https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F  
**Git docs:** https://git-scm.com/docs  
**More on branching and merging:** https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging

>Local: Only on your machine. Remote: Shared on a server.

### Cloning Remote
- If you want to start working with a git repository you first need to clone it using. This will set the **origin** alias to the same url:   
```bash
git clone <git-url>
```

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

> So many things will go wrong if you don’t understand these things and stay up-to-date

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

> This step should always be performed – unless you already specified this information on branch creation.

#### Push Daily
You should be pushing code every day you work on it, regardless of what branch it is to -- or how little the contribution is.


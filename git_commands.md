

## Configuration
Remove the `--global` to do a config for a local git repo. 
```sh
git config --global user.name "Your Name"  # Set global username
git config --global user.email "your.email@example.com"  # Set global email
git config --list  # View current configurations
git config --global --unset user.email  # Remove a config
git config --global core.editor "nano -w"  # Change / choose .txt editor
git config --global init.defaultBranch main  # Set default branch name
git config --global core.autocrlf input  # Tell git to always use the same line ending between mac, linux, and windows (this is the Linux and mac command to run)
git config --global pull.rebase false # attempts to merge only, not rebase when doing a git pull 
```

## Repository Setup
```sh
git init             # Initialize a new repository
git clone <url>      # Clone a repository
```

## Staging & Committing
```sh
git status           # Check status of working directory
git status --ignored # Check status of untracked files
git add <file>       # Stage a specific file for commit
git add .            # Stage all changes, including new untracked files
git add -u .         # Stage modifications and deletions of tracked files only
git commit -m "Message"  # Commit staged changes
git commit --amend   # Amend last commit
```

## Branching
```sh
git branch           # See what Branch your on 
git branch -a        # List out the Branches
git branch <name>    # Create a new branch
git checkout <name>  # Switch branches
git switch <name> # Switch to branch <name>
git checkout -b <name>  # Create and switch to a new branch
git merge <branch>   # Merge branch into current branch
git rebase <branch>  # Rebase current branch onto another
git push --set-upstream origin <branch> # Create branch on upstream
git fetch origin # Pulls remote branches so you can switch into them
git fetch upstream # this does the same thing but with forks?? 
git branch -r # Show remote branches
git branch -a # Show all branches, local + remote

```

## Remote Repositories
```sh
git remote -v        # Show remote repositories
git remote add origin <url>  # Add remote repository
git push origin <branch>  # Push branch to remote
git pull origin <branch>  # Pull latest changes
```

## Undoing Changes
```sh
git reset <file>     # Unstage file but keep changes, this means the git add stuff
git reset --hard HEAD  # Reset to last commit, discard changes
git revert <commit>  # Create a new commit that undoes changes
git checkout -- <file>  # Discard changes in working directory
git restore <file> # roll back to most recent commit 
git restore --source=<commit> <file> # restores back to a specific commit, commit is the ID number, can use -s 
git restore --staged <file> # 

```

## Logs & History
```sh
git log             # Show commit history
git log --oneline   # Show compact commit history
git diff  <file>    # Show changes between commits
git diff HEAD~i <file> # Show changes between commits since last commit, the ~i indicates how far back to look
```


## Stop Tracking Files & Renaming them 
This doesn't delete them, it stops git from tracking the file.  Maybe it does delete it.

```sh
git rm <file>            # Remove a file from the working directory and staging area
git rm --cached <file>   # Remove a file from the staging area but keep it in the working directory
git rm -r <directory>    # Recursively remove a directory and its contents
git commit -m "Removed file"  # Commit the removal of the file

git mv <old_filename> <new_filename>  # Rename the file in both the working directory and staging area
git commit -m "Renamed <old_filename> to <new_filename>"  # Commit the change


```
## Tagging
```sh
git tag <tagname>   # Create a new tag
git tag             # List tags
git show <tagname>  # Show tag details
```

## Submodules
```sh
git submodule add <url>  # Add a submodule
git submodule update --init --recursive  # Initialize and update submodules
```


## Ignoring Files Using `.gitignore`
A `.gitignore` file tells Git which files (or patterns) it should ignore. This is useful for excluding files that are not needed in version control, such as temporary files, build artifacts, and IDE configurations. Place it in the root git dir. You want the `.gitignore` file tracked by git. Use for sensitive files. 

There is a global git ignore! `touch ~/.gitignore_global` always ignore `.fits`, `.fil`,`.npz` etc  files, maybe keys. (doesn't have to be in the home dir)
My global git ignore is `~/.gitignore_global`

```.gitignore
# gitignore
# Ignore node_modules directory
node_modules/

# Ignore Python bytecode files
*.pyc
*.pyo

# Ignore log files
*.log

# Ignore .fits files 
*.fits

# Ignore macOS system files
.DS_Store

# Ignore environment files
.env

# Ignore compiled files
*.class
```


## Updating local repo to Match upstream:

```sh
# Fetch the latest changes from the remote repository without modifying local branches
git fetch origin

# Pull the latest changes from a specific branch of the remote repository and merge them into the current local branch
git pull origin <branch-name>

```


### Making a New Directory 

```sh
# 1. Create a new directory for your project and enter it
mkdir <project_name>
cd <project_name>

# 2. Initialize a new git repository
git init

# 3. Create a README file (optional but recommended)
echo "# <project_name>" >> README.md

# 4. Stage your files and commit
git add README.md
git commit -m "Initial commit with README"

# 5. Create a new repo on GitHub (replace <user> and <project_name>)
# You can do this manually on GitHub website or use GitHub CLI:
gh repo create <user>/<project_name> --ssh --public --confirm

# 6. Link your local repo to the GitHub remote
git remote add origin git@github.com:<user>/<project_name>.git

# 7. Push your initial commit to the main branch on GitHub
git branch -M main
git push -u origin main

```


## Commitzen commands 
- [Commitzen](https://commitizen-tools.github.io/commitizen/) is used for formatting commits, and tagging for collaborations
```sh

cz commit # Make a conventional commit with an interactive prompt

cz check  # Check that commit messages meet conventional standards

cz bump   # Bump version based on commits and update changelog

cz changelog # Generate changelog manually

cz c # Use instead of git commit, use git add normally 
```




_This is a basic reference. For more details, consult `git help <command>`.

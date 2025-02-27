

## Configuration
```sh
git config --global user.name "Your Name"  # Set global username
git config --global user.email "your.email@example.com"  # Set global email
git config --list  # View current configuration
git config --global --unset user.email  # Remove a config

git config --global core.editor "nano -w"  # Change / choose .txt editor
git config --global init.defaultBranch main  # Set default branch name
git config --global core.autocrlf input  # Tell git to always use the same line ending between mac, linux, and windows (this is the Linux and mac command to run)
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
git add <file>       # Stage a file for commit
git add .            # Stage all changes
git commit -m "Message"  # Commit staged changes
git commit --amend   # Amend last commit
```

## Branching
```sh
git branch           # List branches
git branch <name>    # Create a new branch
git checkout <name>  # Switch branches
git checkout -b <name>  # Create and switch to a new branch
git merge <branch>   # Merge branch into current branch
git rebase <branch>  # Rebase current branch onto another
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

There is a global git ignore! `touch ~/.gitignore_global` always ignore `.fits`, `.fil`, files, maybe keys. (doesn't have to be in the home dir)


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

_This is a basic reference. For more details, consult `git help <command>`.
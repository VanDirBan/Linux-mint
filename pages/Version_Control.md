- **Git Basics**
	- **Definition**:
		- Git is a distributed version control system that helps track changes in code, collaborate on projects, and manage versions of files.
	- **Key Concepts**:
		- **Repository (Repo)**: A storage for your project's files and their version history.
		- **Commit**: A snapshot of your changes.
		- **Branch**: A separate line of development.
		- **Merge**: Combining changes from one branch into another.
		- **Staging Area**: A place where changes are prepared before committing.
		- **HEAD**: A pointer to the current branch or commit.
	- **Basic Commands**:
		- **Initialize a repository**:
		  ```bash
		  git init
		  ```
			- Creates a new Git repository in the current directory.
		- **Clone a repository**:
		  ```bash
		  git clone <repository_url>
		  ```
			- Copies an existing repository to your local machine.
		- **Check repository status**:
		  ```bash
		  git status
		  ```
			- Displays the state of the working directory and staging area.
		- **Add changes to the staging area**:
		  ```bash
		  git add <file>
		  git add .
		  ```
			- Adds a specific file or all files to the staging area.
		- **Commit changes**:
		  ```bash
		  git commit -m "Your commit message"
		  ```
			- Records changes in the repository with a message describing the changes.
		- **View commit history**:
		  ```bash
		  git log
		  ```
			- Shows the history of commits.
		- **View file differences**:
		  ```bash
		  git diff
		  ```
			- Displays changes between files or commits.
	- **Branching and Merging**:
		- **Create a new branch**:
		  ```bash
		  git branch <branch_name>
		  ```
		- **Switch to a branch**:
		  ```bash
		  git checkout <branch_name>
		  ```
			- Or:
			  ```bash
			  git switch <branch_name>
			  ```
		- **Create and switch to a branch**:
		  ```bash
		  git checkout -b <branch_name>
		  ```
			- Or:
			  ```bash
			  git switch -c <branch_name>
			  ```
		- **Merge a branch**:
		  ```bash
		  git merge <branch_name>
		  ```
			- Merges the specified branch into the current branch.
		- **Delete a branch**:
		  ```bash
		  git branch -d <branch_name>
		  ```
			- Deletes a local branch.
	- **Remote Repositories**:
		- **View remote repositories**:
		  ```bash
		  git remote -v
		  ```
		- **Add a remote repository**:
		  ```bash
		  git remote add <name> <url>
		  ```
		- **Fetch changes from a remote repository**:
		  ```bash
		  git fetch
		  ```
		- **Pull changes from a remote repository**:
		  ```bash
		  git pull
		  ```
			- Fetches and merges changes from the remote repository into the current branch.
		- **Push changes to a remote repository**:
		  ```bash
		  git push <remote> <branch>
		  ```
			- Pushes commits to a remote branch.
	- **Undoing Changes**:
		- **Unstage changes**:
		  ```bash
		  git reset <file>
		  ```
			- Removes a file from the staging area.
		- **Undo changes in a file**:
		  ```bash
		  git checkout -- <file>
		  ```
			- Reverts a file to its last committed state.
		- **Revert a commit**:
		  ```bash
		  git revert <commit_hash>
		  ```
			- Creates a new commit that undoes the changes from the specified commit.
	- **Gitignore**:
		- **Purpose**: Exclude files and directories from being tracked by Git.
		- **Usage**:
			- Create a `.gitignore` file in the root of your repository.
			- Add patterns for files or directories to ignore:
			  ```
			  node_modules/
			  *.log
			  .env
			  ```
	- **Aliases** (Optional):
		- Add shortcuts for common commands:
		  ```bash
		  git config --global alias.st status
		  git config --global alias.co checkout
		  git config --global alias.br branch
		  git config --global alias.cm commit
		  ```
	- **Basic Workflow**:
	  1. Make changes to your files.
	  2. Check the status of your changes:
	     ```bash
	     git status
	     ```
	  3. Stage the changes:
	     ```bash
	     git add .
	     ```
	  4. Commit the changes:
	     ```bash
	     git commit -m "Description of your changes"
	     ```
	  5. Push the changes to the remote repository:
	     ```bash
	     git push
	     ```
	- **Tips**:
		- Commit frequently with clear messages to keep a good history.
		- Use branches to experiment or work on features without affecting the main codebase.
		- Regularly pull changes from the remote repository to stay updated.
- **Git Advanced**
	- **Interactive Rebase**:
		- **Purpose**: Reorder, edit, squash, or remove commits in your branch.
		- **Usage**:
		  ```bash
		  git rebase -i HEAD~<number_of_commits>
		  ```
			- Opens an editor where you can:
				- `pick`: Keep the commit as is.
				- `reword`: Change the commit message.
				- `edit`: Edit the commit content.
				- `squash`: Combine the commit with the previous one.
				- `drop`: Remove the commit.
		- **Example**:
		  ```bash
		  git rebase -i HEAD~3
		  ```
			- Interactively rebase the last 3 commits.
	- **Cherry-pick**:
		- **Purpose**: Apply specific commits from another branch to the current branch.
		- **Usage**:
		  ```bash
		  git cherry-pick <commit_hash>
		  ```
		- **Example**:
		  ```bash
		  git cherry-pick a1b2c3d
		  ```
			- Applies commit `a1b2c3d` to the current branch.
	- **Stashing**:
		- **Purpose**: Save changes temporarily without committing them.
		- **Usage**:
			- Save changes:
			  ```bash
			  git stash
			  ```
			- List stashes:
			  ```bash
			  git stash list
			  ```
			- Apply the latest stash:
			  ```bash
			  git stash apply
			  ```
			- Apply a specific stash:
			  ```bash
			  git stash apply stash@{n}
			  ```
			- Drop a stash after applying:
			  ```bash
			  git stash pop
			  ```
			- Delete a specific stash:
			  ```bash
			  git stash drop stash@{n}
			  ```
	- **Merge Strategies**:
		- **Fast-forward merge**:
			- Simply moves the branch pointer without creating a merge commit.
			- Default behavior when no divergent commits exist.
			  ```bash
			  git merge <branch>
			  ```
		- **No fast-forward merge**:
			- Always creates a merge commit, even if the history is linear.
			  ```bash
			  git merge --no-ff <branch>
			  ```
		- **Squash merge**:
			- Combines all commits from the merged branch into a single commit.
			  ```bash
			  git merge --squash <branch>
			  ```
	- **Resolving Conflicts**:
		- **Detect conflicts**:
			- During a merge or rebase, Git will pause if conflicts are found.
		- **Resolve conflicts manually**:
			- Edit conflicting files and remove conflict markers (`<<<<<<`, `======`, `>>>>>>`).
		- **Mark as resolved**:
		  ```bash
		  git add <file>
		  ```
		- **Continue merge/rebase**:
		  ```bash
		  git merge --continue
		  git rebase --continue
		  ```
	- **Reflog**:
		- **Purpose**: View and recover lost references to commits or branches.
		- **Usage**:
		  ```bash
		  git reflog
		  ```
			- Shows the history of HEAD changes.
		- **Restore a lost commit**:
		  ```bash
		  git checkout <commit_hash>
		  ```
	- **Reset**:
		- **Purpose**: Undo changes in the repository.
		- **Types**:
			- **Soft reset**:
				- Keeps changes in the working directory and staging area.
				  ```bash
				  git reset --soft <commit_hash>
				  ```
			- **Mixed reset**:
				- Keeps changes in the working directory but removes them from the staging area.
				  ```bash
				  git reset --mixed <commit_hash>
				  ```
			- **Hard reset**:
				- Discards all changes in the working directory and resets to the specified commit.
				  ```bash
				  git reset --hard <commit_hash>
				  ```
	- **Submodules**:
		- **Purpose**: Include other repositories as subdirectories in your project.
		- **Add a submodule**:
		  ```bash
		  git submodule add <repository_url> <path>
		  ```
		- **Clone a repository with submodules**:
		  ```bash
		  git clone --recurse-submodules <repository_url>
		  ```
		- **Update submodules**:
		  ```bash
		  git submodule update --init --recursive
		  ```
	- **Worktrees**:
		- **Purpose**: Allow multiple working directories linked to a single repository.
		- **Create a new worktree**:
		  ```bash
		  git worktree add <path> <branch>
		  ```
		- **Example**:
		  ```bash
		  git worktree add ../new-feature feature-branch
		  ```
	- **Tags**:
		- **Purpose**: Mark specific commits (e.g., for releases).
		- **Create a lightweight tag**:
		  ```bash
		  git tag <tag_name>
		  ```
		- **Create an annotated tag**:
		  ```bash
		  git tag -a <tag_name> -m "Tag message"
		  ```
		- **Push tags to a remote repository**:
		  ```bash
		  git push origin <tag_name>
		  ```
		- **List all tags**:
		  ```bash
		  git tag
		  ```
	- **Amending Commits**:
		- **Purpose**: Edit the most recent commit (e.g., to fix a message or add changes).
		- **Amend the last commit**:
		  ```bash
		  git commit --amend
		  ```
			- Opens an editor to change the commit message or includes new changes staged.
	- **Bisect**:
		- **Purpose**: Find a commit that introduced a bug using binary search.
		- **Start bisecting**:
		  ```bash
		  git bisect start
		  git bisect bad
		  git bisect good <commit_hash>
		  ```
		- **Mark a commit as good or bad during testing**:
		  ```bash
		  git bisect good
		  git bisect bad
		  ```
		- **End bisecting**:
		  ```bash
		  git bisect reset
		  ```
	- **Hooks**:
		- **Purpose**: Automate actions triggered by Git events (e.g., commits, merges).
		- **Common hooks**:
			- `pre-commit`: Runs before committing changes.
			- `post-merge`: Runs after merging branches.
			- `pre-push`: Runs before pushing to a remote repository.
		- **Create a hook**:
			- Add a script to the `.git/hooks/` directory with the name of the hook (e.g., `pre-commit`).
			- Make the script executable:
			  ```bash
			  chmod +x .git/hooks/pre-commit
			  ```
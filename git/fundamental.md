 
# **Git and Version Control** 

---

## **1. Version Control**

Version control is a system that tracks changes to files over time. There are three types of version control systems:

- **Local Version Control**: Tracks changes locally on a single system.
- **Centralized Version Control**: Tracks changes on a central server, requiring all collaborators to connect to it.
- **Distributed Version Control**: Allows each collaborator to have a full copy of the repository, enabling offline work and better collaboration (e.g., Git).

---

## **2. Setting Up Git**

### **Install VS Code**
1. Download and install [Visual Studio Code](https://code.visualstudio.com/).
2. Use it as your preferred code editor.

### **Configure Git**
Run the following commands to set up your Git environment:

```bash
$ git config --global user.name "Your Name"
$ git config --global user.email "your.email@example.com"
```

### **Integrate VS Code with Git**
```bash
$ git config --global core.editor "code --wait" # Use VS Code as the default editor
$ git config --global diff.tool "default-difftool"
$ git config --global difftool.default-difftool.cmd "code --wait --diff $LOCAL $REMOTE"
```

### **View All Configurations**
```bash
$ git config --list --show-origin
```

---

## **3. Repository Basics**

### **Initialize a Repository**
```bash
$ git init
```

### **Check Repository Status**
```bash
$ git status
```
- Tracks changes to files (e.g., new files, staged files, or modified files).

---

### **The Three States of Git**
1. **Modified**: Files have been changed but not yet staged.
2. **Staged**: Changes are marked for the next commit.
3. **Committed**: Changes are saved permanently in the repository.

---

### **Three Sections of Git**
1. **Working Directory**: The current state of files being worked on.
2. **Staging Area**: Files prepared for the next commit.
3. **Repository**: The database where all committed files and their version history are stored.

---

## **4. Key Git Concepts**

### **Hash**
- Git creates a unique **hash** for each commit using the SHA-1 algorithm.
- Ensures data integrity.

### **Snapshot**
- A snapshot is a collection of changes in files at the time of a commit.
- Each snapshot generates a unique hash.

### **HEAD**
- A pointer to the latest commit in the current branch.

---

## **5. Common Git Commands**

### **View Changes**
```bash
$ git diff
```

### **Add Changes to Staging**
```bash
$ git add .
```

### **Commit Changes**
```bash
$ git commit -m "Your commit message"
```

### **View Commit Logs**
```bash
$ git log
$ git log --oneline
$ git log --oneline --graph
```

### **View Commit Details**
```bash
$ git show <hash>
```

### **Compare Commits**
```bash
$ git diff <hash1> <hash2>
```

---

## **6. Undoing Changes**

### **Undo Changes in Working Directory**
```bash
$ git clean -f         # Remove untracked files
$ git restore <file>   # Revert changes to a file
```

### **Unstage Changes**
```bash
$ git restore --staged <file>
```

### **Undo a Commit**
- Use **reset** or **revert**:
```bash
$ git reset <mode> <hash>
$ git revert <hash>
```

---

## **7. Branching**

### **View and Manage Branches**
```bash
$ git branch --list           # List all branches
$ git branch --show-current   # Show current branch
$ git branch -m <new-name>    # Rename branch
$ git branch -d <branch>      # Delete branch
```

### **Switch Branches**
```bash
$ git switch <branch>
$ git checkout <branch>
```

### **Create a New Branch**
```bash
$ git branch <new-branch>
```

---

## **8. Merging**

### **Merge Branches**
```bash
$ git merge <branch>
```

### **Resolve Merge Conflicts**
- Conflicts must be resolved manually.
```bash
$ git merge --abort   # Abort the merge
```

### **Fix Conflicts**
After resolving conflicts, commit the changes:
```bash
$ git commit -m "Resolve merge conflicts"
```

---

## **9. Advanced Git Features**

### **Tagging**
- Tags are used to mark specific commits (e.g., for releases).
```bash
$ git tag <tag-name> <commit-hash>
$ git push origin <tag-name>
$ git tag --list
$ git tag -d <tag-name>       # Delete a local tag
$ git push --delete origin <tag-name>  # Delete a remote tag
```

### **Stashing**
- Save temporary changes without committing.
```bash
$ git stash push -m "message"
$ git stash list
$ git stash apply <stash-id>
$ git stash drop <stash-id>
$ git stash clear
```

### **Cherry-Pick**
- Apply specific commits from one branch to another.
```bash
$ git cherry-pick <commit-id>
```

### **Rebase**
- Rewrites commit history to create a linear timeline.
```bash
$ git rebase <branch>
```

---

## **10. Remote Repositories**

### **Add a Remote Repository**
```bash
$ git remote add <name> <url>
```

### **View Remote Repositories**
```bash
$ git remote
$ git remote get-url <name>
```

### **Push Changes**
```bash
$ git push <remote> <branch>
$ git push origin --all       # Push all branches
```

### **Fetch Changes**
```bash
$ git fetch <remote> <branch>
```

### **Pull Changes**
```bash
$ git pull <remote> <branch>
```

---

## **11. Submodules**

- Include another repository as a submodule.
```bash
$ git submodule add <url> <folder>
$ git submodule update --remote
$ git submodule init
```

---

## **12. Git Workflows**

### **Gitflow Workflow**
- A structured branching model for feature development and releases.
- [Learn more](https://nvie.com/posts/a-successful-git-branching-model/).

### **Trunk-Based Development**
- Focuses on delivering features quickly by maintaining a single main branch.
- [Learn more](https://trunkbaseddevelopment.com/).

### **Forking Workflow**
- Popular in open-source projects.
- Contributors fork a repository, make changes, and submit pull requests.
- [Learn more](https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow).

---

## **13. Git Servers**

- **GitHub**: [https://github.com](https://github.com)
- **GitLab**: [https://gitlab.com](https://gitlab.com)
- **Bitbucket**: [https://bitbucket.org](https://bitbucket.org)

---

## **14. SSH and Remote Access**

### **Generate SSH Key**
```bash
$ ssh-keygen
```

### **Test SSH Connection**
```bash
$ ssh -T git@github.com
```

---

## **15. Git Aliases**

Create shortcuts for commonly used commands:
```bash
$ git config --global alias.ko commit
$ git config --global alias.logone "log --oneline"
```

---

## **16. Pull Requests**

- Use Git server platforms (e.g., GitHub) to submit and review pull requests.
- Resolve merge conflicts manually if they occur.

 

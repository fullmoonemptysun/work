1. log -p gives patch logs
2. log -p 2 gives last 2 patch logs

  

1. log -stat prints little bit of statistics (lines changed, added, removed)

  

1. log -pretty formats logs. there are preset formats but we can create our own formats too(useful for log parsing in a machine).

  

1. The most interesting option value is `format`, which  
    allows you to specify your own log output format.  
    This is especially useful when you’re generating output for machine  
    parsing — because you specify the format explicitly, you know it won’t  
    change with updates to Git:  
    
    `$ git log --pretty=format:"%h - %an, %ar : %s" ca82a6d - Scott Chacon, 6 years ago : Change version number 085bb3b - Scott Chacon, 6 years ago : Remove unnecessary test a11bef0 - Scott Chacon, 6 years ago : Initial commit`
    
    [Useful specifiers for](https://git-scm.com/book/en/v2/ch00/pretty_format) `[git log --pretty=format](https://git-scm.com/book/en/v2/ch00/pretty_format)` lists some of the more useful specifiers that `format` takes.
    

|   |   |
|---|---|
|Specifier|Description of Output|
|`%H`|Commit hash|
|`%h`|Abbreviated commit hash|
|`%T`|Tree hash|
|`%t`|Abbreviated tree hash|
|`%P`|Parent hashes|
|`%p`|Abbreviated parent hashes|
|`%an`|Author name|
|`%ae`|Author email|
|`%ad`|Author date (format respects the `--date=option`)|
|`%ar`|Author date, relative|
|`%cn`|Committer name|
|`%ce`|Committer email|
|`%cd`|Committer date|
|`%cr`|Committer date, relative|
|`%s`|Subject|

1. log —graph gives a fancy graph (with branches and merges) format. it also accepts oneline format
2. —since date and —until date options show logs since until a date.
3. —author for look for logs with certain author(s)
4. —grep to look for keywords and patterns
5. Another really helpful filter is the `-S` option  
    (colloquially referred to as Git’s “pickaxe” option), which takes a  
    string and shows only those commits that changed the number of  
    occurrences of that string.  
    For instance, if you wanted to find the last commit that added or  
    removed a reference to a specific function, you could call:  
    
    `$ git log -S function_name`
    

  

1. The last really useful option to pass to `git log` as a filter is a path.  
    If you specify a directory or file name, you can limit the log output to commits that introduced a change to those files.  
    This is always the last option and is generally preceded by double dashes (  
    `--`) to separate the paths from the options:
    
    `$ git log -- path/to/file`
    
    There’s different options for authors and committers
    
      
    
      
    

  

# Undoing Things

1. if we forget to stage some changes, (adding files or wrong commit message) we can do git commit --amend after adding the new changes to the stage(it will let us commit these changes and change the last commits name and then this becomes the commit.)
2. Only amend commits that are still local and have not been pushed somewhere.  
    Amending previously pushed commits and force pushing the branch will cause problems for your collaborators.  
    For more on what happens when you do this and how to recover if you’re on the receiving end read  
    

  

1. git reset HEAD \<file> will unstage a file from being commited
2. git checkout -- \<file> will undo all local changes and revert the file back to the last staged/ commited version. NO WAY TO GET LOCAL CHANGES BACK

  

# Git data structure: what is it? how does it work?

### 📦 Core Concepts

- Git stores **snapshots**, not diffs.
- Everything is content-addressed using **SHA-1 (or SHA-256)** hashes.

### 🧱 Git Object Types

1. **Blob** – file contents (hashed)
2. **Tree** – directory structure (points to blobs/trees)
3. **Commit** – snapshot + metadata (points to a tree & parents)
4. **Tag** – named reference to a commit

### 📁 Directory Structure

```Plain
pgsql
CopyEdit
.git/
├── HEAD              → current branch
├── objects/          → all blobs, trees, commits (hashed)
├── refs/             → branches & tags
├── index             → staging area
└── config            → repo settings

```

### 🔗 Hashing Example

Blob hash = `SHA1("blob <size>\0<content>")`

### ⚙️ Workflow Summary

- `git add` → stores blob, updates index
- `git commit` → creates commit pointing to tree
- Branch = pointer to a commit
- History = DAG of commits via parent links

### 🗜️ Compression

- Objects stored zlib-compressed
- Packfiles contain many objects with delta compression
- Deltas = “this object = base + patch”

  

### Key Concepts

- Git tracks **content**, not file names.
- `git add` updates the **index** and overwrites earlier staged content.
- Only what is staged at the time of `git commit` is saved.
- Previous blobs remain in the database but are not part of history unless committed.

  

**Example: Adding and committing a file (**`**hehe.txt**`**) in Git**

1. **Create a new file**
    - `echo "hello" > hehe.txt`
    - This creates a file in the working directory. Git is not aware of it yet.
2. **Stage the file**
    - `git add hehe.txt`
    - Git computes the SHA-1 of the file content (`blob <size>\0hello`)
    - The file content is stored as a compressed blob object in `.git/objects/`
    - KEYWORD: **BLOB:** This is just a normal text file written by the c code based on the file content by git that is compressed with zlib. So is a **COMMIT.**
    - Git writes a new entry to the `.git/index`:
        - Includes file path, mode, file size, modification times
        - Includes the 20-byte binary SHA-1 of the blob
3. **Commit the change**
    - `git commit -m "HEHE"`
    - Git reads the index file and builds a tree object: **TREE is also just a normal text file structure compressed by zlib.**
        - For each file, adds an entry: `<mode> <filename>\0<blob-sha>`
        - The full list is compressed and stored as a tree object in `.git/objects/`
    - Git builds the commit object content:
        - Includes: `tree <tree-sha>`, optional `parent`, `author`, `committer`, and the message
        - This full buffer is hashed with SHA-1
        - The resulting commit object is zlib-compressed and saved in `.git/objects/`
    - Git updates the current branch reference (e.g. `.git/refs/heads/master`) to point to the new commit SHA
    - `HEAD` follows this reference
4. **After commit**
    
    - The commit object, tree object, and blob object are all stored in `.git/objects/`
    - The blob contains the file content
    - The tree contains the file list and blob references
    - The commit points to the tree and previous commit (if any)
    
    ==**CRITICAL CLICK POINT**==: _All hashes are created for addressing (indexing)/pointing/referencing purposes only. The files are only compressed and stored that’s it. They’re not stored as hashes._
    

## Git Resolving deltas

When you clone a big Git repo like the Linux kernel, Git tries to save bandwidth and space. It doesn’t fetch every full file as-is. Instead, it fetches a **packfile**—a compressed archive of many Git objects (commits, trees, blobs). To make it compact, Git stores many objects as **deltas**—which are like instructions for how to turn one object into another.

For example:

- Let’s say version A of a file is already in the pack.
- Version B is similar, so Git stores it as: “start with A, then insert these lines, delete those.”
- This is **delta compression**.

**Resolving deltas** is the process of:

1. **Reading the base object** (like A).
2. **Applying the delta** (change instructions) to get the target object (B).
3. Doing this for **millions** of objects if needed.

This happens in the final stage of a clone to rebuild the repository's object database.

Think of it as **reconstructing an optimized, compressed git repo into a fully usable one.**



In Git, **"global" configuration** refers to settings that apply to **your entire user account on the system**, across **all repositories** you work with.

### Key points:

- Stored in: `~/.gitconfig` (your home directory)
    
- Set using: `git config --global`
    
- Affects **all repositories** you use unless overridden by local settings
    

### Example:

bash

CopyEdit

`git config --global user.name "Vinnie Kumar" git config --global user.email "vinnie@example.com"`

This means every Git repo you use on your machine will default to this name and email **unless** the repo has its own local `.git/config` with different values.

### Configuration levels in Git:

1. **System** – Applies to all users (in `/etc/gitconfig`)
    
2. **Global** – Applies to your user (in `~/.gitconfig`)
    
3. **Local** – Applies to a specific repo (in `.git/config`)
    

Local settings **override** global, which override system.

So, "global" = **user-wide defaults**.
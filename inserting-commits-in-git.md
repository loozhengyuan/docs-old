# Inserting Commits in Git

Its often common that we somehow left out some commits while using Git. Of course, inserting commits is a practice frown upon on public repositories so you should never do it. Nonetheless, if you so decide to, we first need to find out the SHA1 hash of the relevant commits.

```bash
git log --online
```

```text
6e5f4b2 (HEAD -> master) commit4
293d3bd commit3
80036e7 commit2
f4627ac commit1
```

Say, for example, we have decided to insert a commit between `commit2` and `commit3`. With that, you need to find the SHA1 hash of one commit earlier which, in this case, would be `80036e7` of `commit2`. When running the following command, do take note of the cap `^` at the end of the SHA1 hash. 

```bash
git rebase -i 80036e7^
```

```text
pick 80036e7 commit2
pick 293d3bd commit3
pick 6e5f4b2 commit4
...
```

You should realise that you are viewing at the list of commits are the children of `80036e7`. Now, we need to change the `pick` of the first commit to `edit` as shown below:

```text
edit 80036e7 commit2
pick 293d3bd commit3
pick 6e5f4b2 commit4
...
```

With this, you can close your text editor \(`CTRL-X` then `Y` then `Enter` if you're using `nano`\) and make changes to your files. Once done, stage all changes by running the following commands.

```bash
git add .
```

```bash
git status
```

Once you are confirmed of the changes, we can now proceed to commit the staged changes.

```bash
git commit
```

You will be prompted the text editor to write your commit message. Again, once done, you can close your editor and the commit will be saved.

```text
This is a commit message
# ...
```

If you wish, you can repeat this process multiple times. Once you have finished inserting all the relevant commits, you can continue rebasing the rest of the commits.

```bash
git rebase --continue
```




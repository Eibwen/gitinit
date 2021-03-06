---
title: Branching
template: article.jade
order: 4
----

##Branching

So far all of the work we have been doing has been on the `master` branch.  As we discussed earlier this is the default or trunk branch in a Git repository.  If you are working on a very simple project this may be enough.  However parallel development can be desirable for a number of different reasons.  Perhaps you wish to try something out.  Perhaps you are working on refactoring part of the system while others on your team continue to work on a new feature.  Whatever the reason it is not uncommon to wish to work in parallel (or isolation) from the master branch.

###Creating a Branch

    git branch [branch name]

  This command will create a branch with the name specified.  In order to work on this branch you will need to change the repository to be focused on that branch using the checkout command.

    git checkout [branch name]
 
Since this is a common Workflow there is a shortcut version of this using only the checkout command: 

    git checkout -b [branch name]

So let's assume you are refactoring while others continue to develop on master.  To create a branch called refactoring you issue the command `git checkout -b refactoring`.  At this point your repository could look like this: 
        
    7576855 --- dfa5664 --- 10a326c --- 68ca42e (master)(origin\master)
                                         \ (refactoring)
        
The `origin` server will look like this:

    7576855 --- dfa5664 --- 10a326c --- 68ca42e (master)
      
Note that the branch will not show up on the `origin` server until we push it there.  So now you make some change and your repository looks like this: 

    10a326c --- 68ca42e --- 3c07fd --- 4f7d6a (refactoring)
                     \ (master)

In the mean time your teammates have been working on the master branch so origin now looks like this:
        
    10a326c --- 68ca42e --- d17089 --- 32860a (master)
           
You want to stay current with what they are doing so you fetch the changes using `git fetch`, but you are not ready to share your work so we are going to merge the changes into our refactoring branch.

    git merge master 

Now your local repository will look something like this:

    10a326c --- 68ca42e --- d17089 --- 32860a (master)
                 \                          \
                  \ --- 3c07fd --- 4f7d6a --- 375a4d (refactoring)

You can (and probably should) push these changes to the remote.  You already know the command to push changes is `git push`, but in this case we also want Git to remember that our local refactoring branch should push to the new refactoring branch on origin.  So the command will look like this:

    git push -u origin refactoring

This will push the new refactoring branch to origin and setup the local refactoring branch to track it.  This makes the local refactoring branch a tracking branch of the origin refactoring branch:

    10a326c --- 68ca42e --- d17089 --- 32860a (master)(origin/master)
                 \                          \
                  \ --- 3c07fd --- 4f7d6a --- 375a4d (refactoring)(origin/refactoring)

Now as you continue to work on the refactoring branch you can use fetch and push without having to specify the remote branch you wish to fetch from or push to.  At some point you will be done with the work on the refactoring branch.  Then you will want to merge the changes back to master.  Let's say that your repository looks like this:

    10a326c --- 68ca42e --- d17089 --- 32860a -- 3f450a (master)(origin/master)
                 \                          \
                  \ --- 3c07fd --- 4f7d6a --- 375a4d --- 3f670a --- 4d2a3c (refactoring)(origin/refactoring)

To merge the changes to the master branch you will checkout the master branch and then merge:

    git checkout master
    git merge refactoring

After resolving and conflicts your repository will look like this:

                                                   /(origin/master)
    10a326c --- 68ca42e --- d17089 --- 32860a -- 3f450a ----------------- 276a54 (master)
                 \                          \                           /
                  \ --- 3c07fd --- 4f7d6a --- 375a4d --- 3f670a --- 4d2a3c (refactoring)(origin/refactoring)

  Now you are ready to push the changes to share with the rest of your team.

### Merging vs. Rebasing

In the previous section we used the `git rebase` command to combine the changes from the remote master branch with the changes on our master branch.  This resulted in all the changes having a straight line history.  In this section we have been using merge to combine changes from different branches.  This creates a new commit on the current branch with all the changes from the other branch.  This is done, so that the history in the repository will tell the story we want it to tell.  When you are working a branch and someone else is also working on that same branch we want the history in the repository to show that as a single branch.  So in this case we use rebase and this:

    9227e1 --- ca8249 --- ca93a4 --- 495254 (master)
                    \ --- 390b06 --- 181df7 (origin/master)

Becomes this:

    9227e1 --- ca8249 --- ca93a4 --- 495254 --- f62e30 --- a993fe (master)(origin/master)

On the other hand When we where working on on the refactoring branch we want that to show as a branch of development so we used the merge command:

    10a326c --- 68ca42e --- d17089 --- 32860a -- 3f450a ----------------- 276a54 (master)(origin/master)
                 \                          \                           /
                  \ --- 3c07fd --- 4f7d6a --- 375a4d --- 3f670a --- 4d2a3c (refactoring)(origin/refactoring)

The choice of merge vs. Rebase is a choice of how the repository will be structured when we are done.  So you will typically use rebase to work on a branch and merge to bring branches back together.

That gives you what you need to know to work with Git.


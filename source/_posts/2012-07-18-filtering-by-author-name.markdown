---
layout: post
title: "Filtering by Author Name"
date: 2012-07-18 00:46
comments: true
categories:
- Git-Log
- Git
- MSysGit
- Script 
- Bash
---
It's unbelievable the kind of attention something simple can get. I'm still suprised at how many up-votes [this answer](http://stackoverflow.com/questions/4259996/how-can-i-view-a-git-log-of-just-one-users-commits) is getting. 

In Git, filtering by author name is easy. Most people simply use the name of the committer that they are interested in. However, it's a little more powerful due to the fact that the author option on git log is actually interpreted as regex. So for looking for commits by "Adam Dymitruk" it's easier to just type `git log --author="Adam"` or use the last name if there more contributors with the same first name.

You can also match on multiple authors by supplying the regex pattern. So to list commits by Jonathan or Adam, you can do this:

    git log --author='\(Adam\)\|\(Jon\)'

However it's tricky to exclude commits by a particular author or set of authors using regular expressions as noted [here](http://stackoverflow.com/questions/406230/regular-expression-to-match-string-not-containing-a-word). Instead, turn to bash and piping you can exclude commits authored by Adam by:

    git log --format='%H %an' |  # get a list of all commit hashes followed by the author name
      grep -v Adam |             # match the name but return the lines that *don't* contain the name
      cut -d ' ' -f1 |           # from this extract just the first part of the line which is commit ref
      xargs -n1 git log -1       # call git log from that commit stopped after 1 commit

A limitation of this is that some log options that you would want are not available such as `--graph` due to the mechanics of calling `git log` multiple times.

## A Few More Details

The cut command is treating spaces as delimiters and is only returning the first field which is the sha1 of the commit. 

If you want to exclude commits commited (but not necessarily authored) by Adam, you can replace `%an` with `%cn`. This has the same effect as using `git log --committer=Adam` instead of author in the first example but for exclusions.

Don't be afraid to split your piped commands onto multiple lines. As long as a line ends with a pipe, bash knows there is more and will prompt for the next line. You can continue to do this until you have written what you want or pasted a multiline snippet from an example online. When you search history, it will be recalled as one line with proper semi-colons inserted if you used while loops or other flow control.

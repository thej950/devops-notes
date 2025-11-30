amend Git Session 4th time 44:00 
--------------------------------------------
1.here soft reset do one step back 
	commited to stagging area
2.here mixed reset do two step back 
	commited to stagging to untracked area 
3.those two activities can be triggred via amend command 
	$ git commit --amend -m "f" ----- m for modify 



- in git amend command will modify the most recent commit 
- it will allow us to make changes to the commit messages to the commit when we forget initially 
	
	syntax: git commit --amend
		- above command will open a vim editor to modify commit messages of the most recent commit 

- if we add some information into files then we insert some data into it the perform git add and commit with new commit message 

	git commit --amend -m "new_message"
		or 
	git commit --amend 

Note: amending commits is generally safe for commits that haven't been pushed to a shared repository if we pushed the commits to a shared repository and othrs have based their work on it amending can cause the problem in such cases better to create a new commit with the necessary changes instead of amending an existing one 
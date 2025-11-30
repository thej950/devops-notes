STASH -- Git session 4th time 16:00 to 34:00
------------------------------
1.stash means once files sent into stash git unable to touch them files
2.using stash files hiding temporarly
3.stash able to hide files area wise, it means suppose there are 100 files in untracked area stash will hide all
  in the untracked area 
4.stash is section in Git. 
5.stash did not work on indivudual files it hide areas 
-------------
1.to stash all the files present in the stagiging area 
	$ git stash
2.to stash all the files in the stagging area and untracked section and .gitignore
	$ git stash -a
3.to see the list of all the stashes
	$ git stash list
4.to unstash a latest stash 
	$ git stash pop
5.to unstash an older stash 
	$ git stash pop stash{stashno}
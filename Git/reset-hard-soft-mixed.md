reset -- three types-- Git 3rd session time 33:00 min
--------------------
-->hard reset -- mostly devops will perform
-->soft reset
-->mixed reset 
hard reset
----------
1.git stores old version code and latest version code 
2.to move one version to another version use command reset
	--> $ git reset --hard commitid --this commit go into latest commit (HEAD)
--------------------
soft reset -- Git session 4th time 00:30 sec
--------------------
1.to understand soft and mixed reset. firstly understand from basic point of view files  
2.there are tree type files 
	-->untracked files
	-->index files (STG)
	-->commited files (LR) -- here commited files go to repository
3.so here soft reset means one step back from commited files to index files when you perform soft reset
	-->commited file to index file
	$ git reset --soft previous_commited_id 
	note:previous commited id meas after head commit next latest commit 
	     suppose if head commit is C next to that B commit id 
	     -now C commit will go into index files to check for that use command $ git status
-------------------------------------------------------
mixed reset -- Git session 4th time 00:30 sec
---------------------
1.mixed reset means commited files to untracked file two step back 
2.same like soft reset will back to one step and mixed reset will back to two step 
	--> $ git reset --mixed previous_commited_id

-----------------------------------------------------------

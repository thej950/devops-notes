tags  git 5th session 00:28 sec
---------------
1.tag is nothing but adding extraname to commit to identify
  important of the commit
2.tag is like bookmarks
3.in realtime we have large commit history to identify them 
  we use tags
4.to assign a tag to commit_id command 
	$ git tag Release-2.0 
		--tag is command
		--Release-2.0  it is a name
		--by default assigning a tag it will directly 
		  assign to head commit 
5.assigning for specific commit_id 
	$ git tag release-1.0 commit_id
6.to delete a tag 
	$ git tag -d release-1.0
7.to display all tags
	$ git tag
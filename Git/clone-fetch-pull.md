git clone, fetch, pull, git hub setup
--------------------------------------------------
###create github account###
-->create a repository in github to upload code inside github
==>create a new repository-->provide repository name(myrepo)-->go with private-->click on create new repository
1.now establish link between myrepo to local repository (it showing a command simply copy paste)
$ git remote add origin https:address  -->it will establish link 
$ git branch -M master  -->it will make master (if master available simply bypass this )
$ git push -u origin master  -->it will upload code from local repository to remote repository which is (myrepo)
2.now it asking token (token is a password for upload code from local machine to remote github) 
##below steps are generating token in github###
-->open github-->goto settings-->find developer settigs and click on developer settings-->select personal access tokens-->go with token (classic)-->generate new token-->provide name in note(mytoken)-->set no expiration-->under scops provide permission (simply select all )-->generate token
-------------------------
###upload code into github is called as check-in operation###
clone-->it will download a remote repository into local repository
$ git clone https://GithubRemotelink-address
----------------------------------
fetch -->it will download modified data from the remote repository into local machine with new remote branch that will be available on local machine to see remote repository on local machine 
$ git branch -a
$ git fetch 
------------------
pull
-->pull command actually work if data present on remote machine the same data available on local machine git pull command not reflect data 
-->but if modified data on the remote machine then if perform git pull ommand it will refelct on local machine
---------------------------
$ git push -->it will upload only current branch 
-->To upload all branches into remote repository like master and child branches 
$ git push -u origin --all -->it will upload all branches data into remote repository 
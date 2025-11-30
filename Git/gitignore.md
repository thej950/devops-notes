.gitignore
================
This is a special configuration file that is used to
store private files info.Any file whose name is stored in
.gitignore will not be accessed by git

1 Create few file
  touch file1 file2 file3 file4

2 Check the git status
  git status
  It will show the above 4 files as untracked

3 Create .gitignore and store the above 4 filenames in it
  cat > .gitignore
  file1
  file2
  file3
  file4

5 Check the status of git
  It will no longer show file 1-4

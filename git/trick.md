**gitignore不生效** 

*当想忽略的文件已经被加入到了记录中时*
```shell script
git rm -r --cached .
git add .
git commit -m "Untracked files issue resolved to fix .gitignore"
```

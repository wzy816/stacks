# svn

```bash
# search file
svn ls -R <svn_repo_path>  | grep <keyword>

# clone dir tree only
svn ls -R <svn_repo_path>  | grep "/$" | xargs -I {} mkdir -p "{}"
```

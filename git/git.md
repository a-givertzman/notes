# Git

## Count lines of code

- total count  
`git ls-files | xargs wc -l`

- total  count only in .js files  
`git ls-files | grep '\.js' | xargs wc -l`

# Git

## Count lines of code

- total count  
`git ls-files | xargs wc -l`

- total  count only in .js files  
`git ls-files | grep '\.js' | xargs wc -l`


## Отчет последних изменений в репозитории

`git log --graph --all --pretty=format:"%h %ad%d %s [%an]" --date=format:"%Y-%m-%d %H:%M" --date-order`

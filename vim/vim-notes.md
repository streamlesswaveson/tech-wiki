
# Delete lines matching a specific pattern

Delete all lines containing `foo`
```vim
:g/foo/d
```
[Delete Lines matching pattern](https://kifarunix.com/delete-lines-matching-specific-pattern-in-a-file-using-vim/)



# Quantifiers syntax

The quantifier `\{n,m}` needs a backslash
```
:%s/^\s\{2,2}//g
```
[vimregex.com](http://www.vimregex.com/)
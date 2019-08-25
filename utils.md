# Clear Bash History

```shell
cat /dev/null > ~/.bash_history && history -c && exit
```

# Set Shell Variable with Text File

```shell
VARIABLE_NAME = $(cat file_name_and_path)
```

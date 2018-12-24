# GitHub

## Create a GitHub Repo from the Command Line

Use the GitHub API.

```shell
curl -u "$username:$token" https://api.github.com/user/repos -d '{"name":"'$repo_name'"}'
```

This requires a API token from your GitHub account which is accessible via the Developer Settings.

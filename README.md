# Multi-User-Git-Configuration
Work with multible accounts

user.name, user.email and credentails are used based on folder prefix in path


## .gitconfig

```ini
[user]
    name = standardAccount
    email = standardAccount@example.com

[credential]
    helper = store --file ~/.standardAccount-credentials

[includeIf "gitdir:**/userA*/**"]
    path = ${HOME}/.userA_gitconfig
```

## ${HOME}/.userA_gitconfig

```ini
[user]
    name = userA_Account
    email = userA@example.com

[credential]
    helper = store --file ~/.userA-credentials
```

## .userA-credentials

### GitHub - Personal Access Tokens (PATs)

1. Go to GitHub Settings
2. Click on "Personal access tokens"
3. Click on "Tokens (classic)"
4. Click on "Generate new token"
5. Copy the token and paste it into `.userA-credentials`:

```plaintext
https://userA:userAToken@github.com
```

## Daily Work

### Clone a Repository

```sh
git clone https://github.com/YourUsername/YourRepository.git userA_YourRepository
```

Or initialize within `../userA_something/...`

## Did You Already Use the Wrong User?

**ATTENTION:** This will change the commit hash and name - you may not want to do this if others have already used this.

### Correct the Last Commit

```sh
git commit --amend --reset-author
```

### Correct a Specific Commit

```sh
git rebase -i <commit-hash>^
# Change 'pick' to 'edit' for the corresponding commit
git commit --amend --reset-author
git rebase --continue
```

### Correct All Commits on a Branch

```sh
git filter-branch --env-filter '
if [ "$GIT_COMMITTER_EMAIL" = "old.email@example.com" ]; then
    GIT_COMMITTER_NAME="New Name"
    GIT_COMMITTER_EMAIL="new.email@example.com"
fi
if [ "$GIT_AUTHOR_EMAIL" = "old.email@example.com" ]; then
    GIT_AUTHOR_NAME="New Name"
    GIT_AUTHOR_EMAIL="new.email@example.com"
fi
' -- --all
```

### Push Local to Remote

**ATTENTION:** This will change the commit hash and name - you may not want to do this if others have already used this.

```sh
git push --force
```

## PowerShell - Windows Credentials

Sometimes on a Windows PC, there are already credentials for Git, and they may be problematic.

### Install Credential Manager

```powershell
Install-Module -Name CredentialManager
```

### Back-up

```powershell
$credentials = Get-StoredCredential -Target "git:https://github.com/"
Export-Clixml -InputObject $credentials -Path "C:\Path\to\YourBackup.xml"
```

**ATTENTION:** keep this YourBackup.xml as private as your password


### Restore

```powershell
$credentials = Import-Clixml -Path "C:\Path\to\YourBackup.xml"
New-StoredCredential -Target $credentials.Target -UserName $credentials.UserName -SecurePassword $credentials.Password -Persist LocalMachine
```

### Delete

```powershell
Remove-StoredCredential -Target "git:https://github.com/"
```

---

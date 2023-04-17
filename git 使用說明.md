# Git 使用說明

## Git 的流程

<img src="https://raw.githubusercontent.com/kospota100/markdown_notes/main/image/git.png"
     width=700px>

## 常見使用流程
1. `git clone / pull` (`git config user.name / user.email`)
2. `add / delete / edit file`
3. `git add`
4. `git commit`
5. `git push`

## Git 的常用指令

查詢狀態、資訊
---

### git status
- `git status`

### git config
- `git config user.name [Username]`
- `git config user.email [Email]`

### git log
- `git log --oneline`
- `git log -5`

編輯
---

### git init

### git add
- `git add [File/Folder]`

### git clone
- `git clone [Repository URL]`

### git remote
- `git remote add [Repo name] [Repo URL]`
- `git remote remove [Repo name]`
- `git remote get-url [Branch]`
- `git remote set-url [Branch] [Repo URL]`

### git commit
- `git commit -m [Commit message]`

### git pull
- `git pull [Repo] [Branch]`

### git push
- `git push -u [Repo] [Branch]`

### git branch

---

## 進階 Git 功能與操作

### .gitattributes 檔

### .gitignore 檔

### git rebase

### git reset

### git checkout

### 在電腦上設定用於不同 GitHub 帳號的 SSH key
1. 使用 ssh-keygen 生成金鑰
```
ssh-keygen -t rsa -C "[SSH key comment - Email or Username]"
```

2. 輸入 SSH key 檔案路徑與檔名。
ex：
```
~/.ssh/id_rsa_alpha
```

3. 為金鑰設定密碼（選擇性）

4. 為另一個帳號重複同樣的步驟，並於最後將 `.ssh` 資料夾下 `.pub` 檔案的內容加入對應的 GitHub 帳號中

5. 在 `.ssh/` 下，建立 `config` 檔案，寫入以下內容
```
# 這是註解
# iam96alpha
Host github.com-alpha                   # *之後連線 Git 服務時要用的 Host 名稱*
     HostName github.com                # Git 服務的 HostName
     IdentityFile ~/.ssh/id_rsa_alpha   # 剛剛儲存的金鑰路徑

# iam955beta
Host github.com-beta
     HostName github.com             
     IdentityFile ~/.ssh/id_rsa_beta
```

6. 以上步驟皆完成後，便可從 GitHub 使用 SSH 連線了。但若要使用不同的 key，則須將 SSH URL 中的 Host 名稱更改為 config 中對應的名稱
- `git@github.com:iam96alpha/my_repo.git` 改為 `git@github.com-alpha:iam96alpha/my_repo.git`
- `git@github.com:iam955beta/my_repo.git` 改為 `git@github.com-beta:iam955beta/my_repo.git`
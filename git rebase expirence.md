# Git rebase experience（rebase 踩坑紀錄）

rebase 的操作：  
[Rebase — Practical guide for git users 0.1 文档](https://git-tutorial.readthedocs.io/zh/latest/rebase.html#id3)  
```md
pick:	保留此提交
reword:	修改提交的訊息(只改提交訊息)
edit:	保留此提交，但是需要做一些修改(例如在程式裡面多加些註解)
squash:	保留此提交，但是將上面的提交一併併入此提交，此動作會顯示提交訊息供人編輯
fixup:	與squash相似，但是此提交的提交訊息會被上面的提交訊息取代
exec:	執行 shell 指令，例如 exec make test 進行一些測試，可以隨意穿插在提交點之間
```
rebase 的指令：  
[Rebase — Practical guide for git users 0.1 文档](https://git-tutorial.readthedocs.io/zh/latest/rebase.html#id6)  
```md
git rebase (--continue | --abort | --skip)
```



## A. 建立 SSH 金鑰與 GPG 金鑰

<details> <summary>建立 SSH 金鑰與 GPG 金鑰（若已建立並新增至 GitHub 則可跳過）</summary>

### 1. 建立 SSH 金鑰

#### a. 產生 SSH 金鑰對
照著 [GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key) 的步驟，在電腦上產生你的 SSH 金鑰  
```sh
ssh-keygen -t ed25519 -C "your_email@example.com"
```

#### b. 在電腦上加入 SSH 私鑰
在上一步驟產生完 SSH 金鑰對後，照著 [GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent) 的步驟，將私鑰加入至你的電腦中（適用於 Windows 系統）  
```ps1
# start the ssh-agent in the background
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
Start-Service ssh-agent
```
```ps1
ssh-add C:\Users\YOU\.ssh\id_ed25519
```

#### c. 在 GitHub 帳號上加入 SSH 公鑰
參考：[Adding a new SSH key to your GitHub account - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)  

前往 GitHub 中的 [SSH and GPG keys](https://github.com/settings/keys) 頁面，點選 New SSH Key 後。將先前產生的 **`id_ed25519.pub`** 檔案的內容填入 Key 文字框中  

#### d. 測試 SSH 金鑰是否成功加入
[Testing your SSH connection - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)  
```sh
ssh -T git@github.com
# Attempts to ssh to GitHub
```

---
### 2. 建立 GPG 金鑰

#### a. 產生 GPG 金鑰對
照著 [GitHub Docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key#generating-a-gpg-key) 的步驟，在電腦上產生你的 GPG 金鑰  
```sh
gpg --full-generate-key
```

#### b. 在電腦上匯出 GPG 公鑰
[Generating a new GPG key - GitHub Docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key#generating-a-gpg-key)  
1. 檢視所有的 GPG 金鑰
```sh
gpg --list-secret-keys --keyid-format=long
```
在範例中，剛才產生的 GPG 金鑰 id 為 `3AA5C34371567BD2`，複製你電腦上的這段文字，黏貼到下一段指令中  
```sh
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot <hubot@example.com>
ssb   4096R/4BB6D45482678BE3 2016-03-10
```
2. 匯出指定的 GPG 金鑰
```sh
gpg --armor --export 3AA5C34371567BD2
# Prints the GPG key ID, in ASCII armor format
```
3. 複製 GPG 公鑰
將輸出中 `-----BEGIN PGP PUBLIC KEY BLOCK-----` 到 `-----END PGP PUBLIC KEY BLOCK-----` 的內容全部複製  

#### c. 在 GitHub 帳號上加入 GPG 公鑰
前往 GitHub 中的 [SSH and GPG keys](https://github.com/settings/keys) 頁面，點選 New GPG Key 後。將先前複製的 GPG 公鑰填入 Key 文字框中  

</details>

---
## B. 使用 SSH 金鑰與 GPG 金鑰

### 1. 使用 SSH 金鑰
> 自 2021 年 8 月 13 日起，GitHub 不再支援以 SSH key, Personal token, GitHub API 以外的方式進行 Git 操作  
:: [iThome Online](https://www.ithome.com.tw/news/146185)  
`clone` 指令仍可使用 HTTPS 的方式進行，但其他如 `pull`, `push` 等指令皆須透過新方式驗證

### 2. 使用 GPG 金鑰

#### a. 對 commits 簽章
[Signing commits - GitHub Docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits)  
可使用 `git config commit.gpgsign true` 自動對當前 repo 的所有 commits 進行簽章  
或使用 `git config --global commit.gpgsign true` 自動對電腦上全部 repo 的所有 commits 進行簽章  
也可在 commit 時加入 `-S` 對單一 commits 進行簽章  
```sh
$ git commit -S -m "YOUR_COMMIT_MESSAGE"
# Creates a signed commit
```

#### a. 對 tags 簽章
[Signing tags - GitHub Docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-tags)  
[How to Sign Tags and Commits with Git | InMotion Hosting](https://www.inmotionhosting.com/support/website/git/how-to-sign-tags-and-commits-with-git-2/)  
對 commit 加上 tag：[git rebase and keep tags update - Stack Overflow](https://stackoverflow.com/questions/55468378/git-rebase-and-keep-tags-update/55468738#55468738)  
將 tag 推上 repo：[How do you push a tag to a remote repository using Git? - Stack Overflow](https://stackoverflow.com/questions/5195859/how-do-you-push-a-tag-to-a-remote-repository-using-git/5195913#5195913)  
1. 先使用 `git tag "tag name"` 指令建立 tag
2. 再用 `git tag -s "tag name" -m "tag message"` 指令對 tag 進行簽章
3. 簽章後用 `git tag -v "tag name"` 指令驗證這個 tag 
4. 之後用 `git tag -f "tag name" "commit hash"` 對 commit 加上 tag
5. 最後使用 `git push origin "tag name" -f ` 將 tag 推上 repo

---
## C. rebase 修改時的問題
### 1. commit 時間不是原本的時間
[GIT: change commit date to author date - Stack Overflow](https://stackoverflow.com/questions/28536980/git-change-commit-date-to-author-date/28537098#28537098)  
[git rebase without changing commit timestamps - Stack Overflow](https://stackoverflow.com/questions/2973996/git-rebase-without-changing-commit-timestamps)  
```sh
git rebase --committer-date-is-author-date
```
### 2. 進階操作
[Git 筆記 - 作者(Auhtor)與提交者(Commmitter)差異實驗-黑暗執行緒](https://blog.darkthread.net/blog/git-author-n-committer/)  
[Git-rebase 小筆記 – Yu-Cheng Chuang’s Blog](https://blog.yorkxin.org/posts/git-rebase/)  
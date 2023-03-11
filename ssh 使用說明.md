# SSH 使用說明

### A. 一般連線

#### 1. 確認 SSH service 是否正在執行

在 Termux 上，可使用以下的指令確認 SSH 是否在執行

```bash
if pgrep ssh > /dev/null; then
 echo "ssh Running"; else
 echo "ssh Not running"
fi
```
###### from [How do I view the status of a service : termux](https://www.reddit.com/r/termux/comments/cd8u5h/comment/etscyc0/?context=3)

在其他 Linux 系統上，則可使用 `service ssh status` 確認 SSH 是否在執行

#### 2. 確認 Host 端的 IP 位址與使用者名稱

   - 使用 `ip -4 addr` 查詢主機上 `eth0` 中的 IP Address

   - 使用 `whoami` 查詢目前的使用者名稱

     - 如果不知道目前帳號的密碼，可以使用 `passwd` 重新設置

#### 3. 如果 SSH service 並未執行，可以輸入 `sshd` 來直接啟用，或使用 `sshd -d` 在偵錯模式下啟用

   - 若已經存在 SSH 連線，並想要關閉時，可以使用 `pkill sshd` 停止原本的 SSH service

   - 若想要關閉多個 SSH 連線中的特定連接埠時，可以使用 `ps ax | grep "sshd -p [PORT]"` 或 `ps au | grep "sshd"` 找出要關閉的 SSH 程序的 PID，再使用 `kill [PID]` 來停止 SSH 程序

   - 若需要開啟新的 SSH 連線時，可以使用 `sshd -p [NEW_PORT]` 在另一個 port 開啟 SSH service

#### 4. 當 SSH service 成功執行後，便可在其他主機上使用 `ssh host_user@host_ip_address` 來連線至 Host 主機
   - 若 Host 端使用的不是 SSH 預設 Port 22 的情況，則需在後方加上參數 `-p [HOST_PORT]` 才可成功連線

#### 5. 當要中斷連線時，可使用 `pkill sshd` 來關閉 Host 端的 SSH 服務，或使用 `logout` 來從 Client 端中斷連線

---

### B. SSH Host 功能無法使用時的連線

假設有一台無法使用 SSH Host 功能的主機 A

與另一台可以使用 SSH Host 功能的主機 B。

當想在主機 A 上使用 `sshd` 指令時，可以透過在 A 主機上輸入以下指令，來在主機 A 上建立一個連接至主機 B 的反向通道，並且在主機 B 上啟用 SSH 服務。

```bash
ssh -fN -R 2222:localhost:22 B_user@B_ip_address
```

之後便可在主機 B 上使用以下指令連線至主機 A。

```bash
ssh -p 2222 A_user@localhost
```

---

#### ((可能可以作為參考的 ChatGPT 解釋

```
請解釋這段指令 `ssh -fN -R 2222:localhost:22 user@remote_host`
```

> 這是一個 ssh 指令，用於建立 SSH 連接並創建一個反向通道（reverse tunnel），將本地端口 2222 轉發到遠端主機（remote_host）的本地端口 22 上，以便從遠端主機連接本地端口 2222。
> 
> 具體來說，每個遠端主機都有一個本地端口號。這個指令會建立一個 SSH 連接到遠端主機並創建一個反向通道。通道會將遠端主機的本地端口 2222 與本地主機的端口 22 相關聯，使得任何連接到遠端主機的本地端口 2222 的數據都會被轉發到本地主機的端口 22 上。
> 
> 此外，-f 指定 SSH 連接在背景運行（daemonize），而 -N 則指定不執行遠程命令。因此，此命令僅創建反向通道，不執行任何其他操作。
> 
> 最後，user@remote_host 指定了遠端主機的用戶名和主機名。

`from: chat.openai.com`

<img src="https://cdn.discordapp.com/attachments/714721972214300692/1083599434941743175/image.png"
     width=700px>
---

```
請解釋這段指令 `ssh -fN -R 2222:localhost:22 user@remote_host`
```

> 這個指令會在遠端主機 remote_host 上建立一個反向 SSH 通道，讓遠端主機可以連線至執行這個指令的本機。以下是指令中使用的選項內容：
> 
>     ssh：啟動 SSH 客戶端程式。
>     -f：將 SSH 指令執行在背景模式，不會阻塞其他工作。
>     -N：不執行遠端命令，通常用於只是轉發連接埠。
>     -R：指定將一個遠端主機的連接埠轉發到一台本機。
>     2222:localhost:22：將遠端主機端口 2222 轉發到本機的 SSH 端口(通常是22)。
>     user@remote_host：要通過 SSH 連接的使用者和遠程主機。
> 
> 總的來說，這個指令建立了一個通道，將位於遠端主機上的端口 2222 的所有流量轉發到本機的 SSH 端口(通常是 22) 上，使使用者可以以安全的方式連接到本機的 SSH 服務器。

`from you.com`

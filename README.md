# Multi-User-Chat-System-ECE361-Computer-Networks (C + Pthreads)

A simple multi-user TCP chat system with:
- User authentication (from `userlist.txt`)
- Multiple chat sessions (create/join/leave)
- Session switching (choose which session you’re “engaged” in for sending messages)
- User list + joined sessions query
- Session invitations (`/invite`)

## Requirements

### Linux
1. Install build tools:
```
sudo apt update
sudo apt install -y build-essential
```
2. In terminal, cd to folder of this project, then build:
```
cd <directory of project>
make clean
make
```
3. Make sure you get `./server` and `./client`

### macOS
1. Install Xcode Command Line Tools:
```
xcode-select --install
```
2. Go to folder of this project and build:
```
make
```
If `make` isn't found, install it via Homebrew:
```
brew install make
```
3. Make sure you get `./server` and `./client`

### Windows
1. Install WSL + Ubuntu
In PowerShell (Admin):
```
wsl --install
```
then open Ubuntu from the Start menu.
2. Inside Ubuntu (WSL), install build tools:
```
sudo apt update
sudo apt install -y build-essential
```
3. Go to project folder (either clone inside the WSL, or access Windows files):
- If cloned inside WSL:
```
cd ~/your-repo-folder
```
- If your code is in Windows filesystem (example path):
```
cd /mnt/c/Users/<YourName>/Desktop/your-repo-folder
```
4. Build:
```
make clean
make
```
5. Make sure you get `./server` and `./client`

## Running the Server

The server reads one line from stdin in the format:
server <PORT>

Example:
```
./server
server 5000
```

You should see something like:
```
Server: starting at port 5000...
Server: waiting for connections...
```

Notes:
The server loads accounts from userlist.txt.
By default it uses IPv4 (AF_INET) and TCP.

## Running the Client

1) Start the client in a new terminal:
```
./client
```

2) Login:
```
/login <client_id> <password> <server_ip> <server_port>
```
Example:
```
/login jill 12345 127.0.0.1 5000
```
If successful you'll see `login successful.`

3) Client Commands (See Details Below)

## Accounts (Default)
From `userlist.txt`
| username  | password |
| ------------- | ------------- |
| jill  | 12345  |
| jack  | 00000  |
| james  | 12  |

You can edit userlist.txt to add more users (one per line).

## Client Command Reference
### General Notes
- You can join up to 3 sessions (MAX_SESSION = 3).
- You can only send messages to the currently engaged session (/switchsession sets it).
- Typing a plain message sends it to the engaged session.
### Commands Table
| command  | usage | what it does | example |
| ------------- | ------------- | ------------- | ------------- |
| Login  | `/login <user> <pass> <ip> <port>`  | Connect to server and authenticate | `/login jill 12345 127.0.0.1 5000` |
| Logout  | `/logout`  | Disconnect from server, leave sessions, stop receiving thread | `/logout` |
| Create session  | `/createsession`  | Create a new session on server and auto-join it | `createsession` |
| Join session | `/joinsession <session_id>` | Join an existing session | `/joinsession 1` |
| Leave session | `/leavesession <session_id>` | Leave a joined session | `/leavesession 1` |
| List users/sessions | `/list` | Show logged-in users and which sessions they joined | `/list` |
| Switch engaged session | `/switchsession <session_id>` | Set which session outgoing messages go to | `/switchsession 2` |
| Disengage | `/switchsession -1` | Stop engaging any session (cannot send messages) | `/switchsession -1` |
| Invite user | `/invite <user_id> <session_id>` | Invite a user to join a session you are in | `/invite jack 1` |
| Quit client | `/quit` | Logout then exit client program | `/quit` |


### Sending Messages
Anything that is not a recognized command is treated as chat text:
```
this is a message
```

### Invitations
If someone invites you, you'll see:
```
<user> is inviting you to session #<id>, do you accept? (y/n)
```
Type:
- y to accept (client will automatically issue `/joinsession <id>`)
- n to decline

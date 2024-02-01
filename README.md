# babylon-node-setup
Updates and required packages
```console
# Update system and install build tools
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y

# Install Go
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME

curl https://dl.google.com/go/go1.20.1.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF

source $HOME/.profile
go version
```
Installing the node
```console
cd $HOME
rm -rf babylon
git clone https://github.com/babylonchain/babylon.git
cd babylon

git checkout v0.7.2
make install
babylond version
```
Initalization processes
```console
# Replace monikerName with your own nickname.
babylond init monikerName --chain-id=bbn-test-2

# Genesis
curl -Ls https://ss-t.babylon.nodestake.top/genesis.json > $HOME/.babylond/config/genesis.json 

# Addrbook
curl -Ls https://ss-t.babylon.nodestake.top/addrbook.json > $HOME/.babylond/config/addrbook.json
```
Service file creation
```console
sudo tee /etc/systemd/system/babylond.service > /dev/null <<EOF
[Unit]
Description=babylond Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which babylond) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable babylond
```
Start and monitor your node
```console
sudo systemctl restart babylond
journalctl -u babylond -f
```

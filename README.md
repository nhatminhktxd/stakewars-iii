# Stake Wars: Episode III
Thời gian: 13/7 - 9/9 năm 2022
# Thử thách 001:
### Hãy thử lệnh này để biết thiết bị của bạn có hỗ trợ không
```
lscpu | grep -P '(?=.*avx )(?=.*sse4.2 )(?=.*cx16 )(?=.*popcnt )' > /dev/null \
  && echo "Supported" \
  || echo "Not supported"
  ```
  ![2022-07-18_16-37-57](https://user-images.githubusercontent.com/107829569/179731328-588a893c-fdbb-4cc2-bbb1-04f520e03d0d.png)
### Tạo ví
Hãy tạo cho mình một cái ví trên mạng Shardnet: https://wallet.shardnet.near.org/
### Cập nhật Linux
```
sudo apt update && sudo apt upgrade -y
```
### Cài đặt developer tools, Node.js, và npm
```
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -  
apt install build-essential nodejs
PATH="$PATH"
```
### Kiểm tra
```
node -v
```
18.x.x
```
npm -v
```
8.x.x
### Cài đặt NEAR-CLI
```
sudo npm install -g near-cli
```
### Thiết lập môi trường Shardnet cho Chunk-only producer
```
echo 'export NEAR_ENV=shardnet' >> ~/.profile && source .profile
```
# Thử thách 002
### Cài đặt developer tools
```
sudo apt install -y git binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ python docker.io protobuf-compiler libssl-dev pkg-config clang llvm cargo
```
### Cài đặt Python pip
```
sudo apt install python3-pip
```
### Đặt cấu hình
```
USER_BASE_BIN=$(python3 -m site --user-base)/bin
export PATH="$USER_BASE_BIN:$PATH"
```
### Cài đặt Building env
```
sudo apt install clang build-essential make
```
### Cài đặt Rust & Cargo
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
### Nhấn 1 và ENTER
### Sau đó:
```
source $HOME/.cargo/env
```
### Sao chép nearcore từ Github
```
git clone https://github.com/near/nearcore
cd nearcore
git fetch
```
### Sau đó
```
cargo build -p neard --release --features shardnet
```
### Sau đó
```
./target/release/neard --home ~/.near init --chain-id shardnet --download-genesis
```
### Thay thế config.json
```
rm ~/.near/config.json
wget -O ~/.near/config.json https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/config.json
```
### Chạy node
```
cd nearcore

./target/release/neard --home ~/.near run
```
### Cấp quyền ví cục bộ
```
near login
```
### Sao chép liên kết ra trình duyệt
![2022-07-19_17-30-48](https://user-images.githubusercontent.com/107829569/179731344-cc58773f-fb73-4965-a374-e64c474e43c7.png)
### Cấp quyền truy cập vào NEAR CLI
![2022-07-19_17-29-24](https://user-images.githubusercontent.com/107829569/179731336-c5bf933c-7d06-4234-9a3b-d097d6407c68.png)
### Sau đó trình duyệt sẽ hiện ra như sau
![2022-07-19_17-30-11](https://user-images.githubusercontent.com/107829569/179731340-a336f37c-df6e-445a-9450-4143e18f8e86.png)
### Quay trở lại và nhập địa chỉ ví Shardnet của bạn và sẽ nhận được thông báo
![2022-07-19_17-31-04](https://user-images.githubusercontent.com/107829569/179731346-c8ac9b32-21d7-47f3-aa7c-01b8026d20ff.png)
### Tạo tệp validator_key từ ví của bạn
```
cp ~/.near-credentials/shardnet/YOUR_WALLET.json ~/.near/validator_key.json
```
### Chỉnh sửa validator_key
```
nano $HOME/.near/validator_key.json
```
Sửa "account_id":"**YOUR_WALLET**.factory.shardnet.near","
Sửa “private_key” thành “secret_key”
### Tạo service cho Stakewars
```
sudo tee /etc/systemd/system/neard.service > /dev/null <<EOF
[Unit]
Description=NEARd Daemon Service

[Service]
Type=simple
User=$USER
#Group=near
WorkingDirectory=$home/root/.near
ExecStart=/root/nearcore/target/release/neard run
Restart=on-failure
RestartSec=30
KillSignal=SIGINT
TimeoutStopSec=45
KillMode=mixed

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl enable neard
sudo systemctl start neard
sudo systemctl reload neard
journalctl -n 100 -f -u neard
```
### Kiểm tra logs
```
journalctl -fu neard | ccze -A
```
# Thử thách 003
### Triển khai Staking pool
```
near call factory.shardnet.near create_staking_pool '{"staking_pool_id": "<pool id>", "owner_id": "<accountId>", "stake_public_key": "<public key>", "reward_fee_fraction": {"numerator": 5, "denominator": 100}, "code_hash":"DD428g9eqLL8fWUxv8QSpVFzyHi1Qd16P8ephYCTmMSZ"}' --accountId="<accountId>" --amount=30 --gas=300000000000000
```
### Sửa
```<pool id>```: ID Pool của bạn, không kèm theo biến ~~.factory.shardnet.near~~ đằng sau: e.g: “nntuan1975” not “~~nntuan1975.factory…~~”

```<accountId>```: địa chỉ ví Shardnet của bạn

```<public_key>```: tìm thấy trong validator_key.json

```<accountId>``` : địa chỉ ví Shardnet của bạn
### Stake NEAR
```
near call <pool_id> deposit_and_stake --amount <NEAR amount> --accountId <account_id> --gas=300000000000000
```
### Unstake NEAR
```
near call <staking_pool_id> unstake '{"amount": "<amount yoctoNEAR>"}' --accountId <accountId> --gas=300000000000000
```
### Rút
```
near call <staking_pool_id> withdraw '{"amount": "<amount yoctoNEAR>"}' --accountId <accountId> --gas=300000000000000
```
### Ping
```
near call <pool_id> ping '{}' --accountId <account_id> --gas=300000000000000
```
### Tạo Cronjob để chạy ping vô hạn
```
crontab -e 

# Điều này cần được đặt lại nếu không nó sẽ tìm kiếm testnet theo mặc định
NEAR_ENV=shardnet
# Kỷ nguyên hiện tại là khoảng ~ 2,5 giờ, chúng ta sẽ gọi ping mỗi giờ vào lúc này
0 * * * * near call nntuan1975.factory.shardnet.near ping '{}' --accountId nntuan1975.shardnet.near --gas=300000000000000 >> $HOME/cron.log
```
# Thử thách 4
### Lệnh systemd
```
journalctl -n 100 -f -u neard | ccze -A
```
### Cài đặt RPC
```
sudo apt install curl jq
```
### Lệnh thông thường
```
####### Check your node version: Command:
`curl -s http://127.0.0.1:3030/status | jq .version`

####### Check Delegators and Stake Command:
`near view <your pool>.factory.shardnet.near get_accounts '{"from_index": 0, "limit": 10}' --accountId <accountId>.shardnet.near`

####### Check Reason Validator Kicked Command:
`curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.prev_epoch_kickout[] | select(.account_id | contains ("<POOL_ID>"))' | jq .reason`

####### Check Blocks Produced / Expected Command:
`curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.current_validators[] | select(.account_id | contains ("POOL_ID"))'`
```
#Done!

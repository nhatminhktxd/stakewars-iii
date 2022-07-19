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
Chúng ta sẽ thiết lập môi trường Shardnet cho Chunk-only producer
```
# Thử thách 002

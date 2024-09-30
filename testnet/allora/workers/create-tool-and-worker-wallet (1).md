# Create tool and worker wallet

#### Cài allorad:[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#c%C3%A0i-allorad) <a href="#cai-allorad" id="cai-allorad"></a>

```
curl -sSL https://raw.githubusercontent.com/allora-network/allora-chain/main/install.sh | bash -s -- v0.2.7
```

Kết quả cài đặt thành công ví dụ như sau:

```
Installation complete. The allorad is now available in /root/.local/bin
To make allorad available from any terminal session, add the following line to your .bashrc or .zshrc:
export PATH="$PATH:/root/.local/bin"
```

**Thêm vào file .bashrc để có thể sử dụng `allorad` từ mọi terminal:**[**​**](https://nodium.xyz/vi/docs/allora/install-worker/intro#th%C3%AAm-v%C3%A0o-file-bashrc-%C4%91%E1%BB%83-c%C3%B3-th%E1%BB%83-s%E1%BB%AD-d%E1%BB%A5ng-allorad-t%E1%BB%AB-m%E1%BB%8Di-terminal)

```
vi ~/.bashrc
```

* Nhấn `i` để chuyển sang chế độ chỉnh sửa.

Thêm dòng sau vào cuối file, ở ví dụ trên là:

```
export PATH="$PATH:/root/.local/bin"
```

* Lưu và thoát bằng cách nhấn `Esc` sau đó gõ `:wq` và nhấn `Enter`. (Để thoát mà không lưu, nhấn `Esc` sau đó gõ `:q!` và nhấn `Enter`)

**Load lại file .bashrc:**[**​**](https://nodium.xyz/vi/docs/allora/install-worker/intro#load-l%E1%BA%A1i-file-bashrc)

```
source ~/.bashrc
```

#### Tạo một ví mới cho worker node:[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#t%E1%BA%A1o-m%E1%BB%99t-v%C3%AD-m%E1%BB%9Bi-cho-worker-node) <a href="#tao-mot-vi-moi-cho-worker-node" id="tao-mot-vi-moi-cho-worker-node"></a>

```
allorad keys add net1_worker
```

* Nhập 2 lần mật khẩu mới cho ví mới tạo, nhớ để truy cập vào ví sau này.
* Lưu lại các thông tin ví mới vào file txt, quan trọng là: `address` và `24 từ mnemonic`.

#### Dùng faucet để nhận uallo: (Địa chỉ ví `address` ở lệnh phía trên)[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#d%C3%B9ng-faucet-%C4%91%E1%BB%83-nh%E1%BA%ADn-uallo-%C4%91%E1%BB%8Ba-ch%E1%BB%89-v%C3%AD-address-%E1%BB%9F-l%E1%BB%87nh-ph%C3%ADa-tr%C3%AAn) <a href="#dung-faucet-de-nhan-uallo-dia-chi-vi-address-o-lenh-phia-tren" id="dung-faucet-de-nhan-uallo-dia-chi-vi-address-o-lenh-phia-tren"></a>

* Mỗi topic khi chạy lần đầu worker sẽ gởi tx đăng ký topic đó nên cần có uallo để gởi tx.
*   Vào [https://faucet.testnet-1.testnet.allora.network/](https://faucet.testnet-1.testnet.allora.network/) để nhận uallo. Faucet thành công như hình dưới:&#x20;

    <figure><img src="https://nodium.xyz/vi/assets/images/faucet-token-fbfe334db59378747ab99e2c852f7deb.png" alt=""><figcaption></figcaption></figure>
* Nếu bị lỗi không faucet được thì thử lại nhiều lần, sau đó thực hiện lệnh dưới để kiểm tra số dư ví: (thay `<địa chỉ ví>` thành địa chỉ ví của bạn)

```
allorad query bank balances <địa chỉ ví> --node=https://allora-rpc.testnet-1.testnet.allora.network
```

#### Xem lại ví đã tạo:[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#xem-l%E1%BA%A1i-v%C3%AD-%C4%91%C3%A3-t%E1%BA%A1o) <a href="#xem-lai-vi-da-tao" id="xem-lai-vi-da-tao"></a>

```
allorad keys list
```

### Cài đặt Git và Docker[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#c%C3%A0i-%C4%91%E1%BA%B7t-git-v%C3%A0-docker) <a href="#cai-dat-git-va-docker" id="cai-dat-git-va-docker"></a>

#### Cài đặt Git:[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#c%C3%A0i-%C4%91%E1%BA%B7t-git) <a href="#cai-dat-git" id="cai-dat-git"></a>

```
sudo apt update
sudo apt install git
```

#### Kiểm tra Git đã cài đặt thành công:[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#ki%E1%BB%83m-tra-git-%C4%91%C3%A3-c%C3%A0i-%C4%91%E1%BA%B7t-th%C3%A0nh-c%C3%B4ng) <a href="#kiem-tra-git-da-cai-dat-thanh-cong" id="kiem-tra-git-da-cai-dat-thanh-cong"></a>

```
git --version
```

#### Cài đặt Docker:[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#c%C3%A0i-%C4%91%E1%BA%B7t-docker) <a href="#cai-dat-docker" id="cai-dat-docker"></a>

```
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce -y
```

#### Kiểm tra Docker đã cài đặt thành công:[​](https://nodium.xyz/vi/docs/allora/install-worker/intro#ki%E1%BB%83m-tra-docker-%C4%91%C3%A3-c%C3%A0i-%C4%91%E1%BA%B7t-th%C3%A0nh-c%C3%B4ng) <a href="#kiem-tra-docker-da-cai-dat-thanh-cong" id="kiem-tra-docker-da-cai-dat-thanh-cong"></a>

* Kiểm tra trạng thái Docker:

```
sudo systemctl status docker
```

Ấn `q` để thoát.

* Kiểm tra phiên bản Docker:

```
docker --version
```

Kết quả trả về ví dụ: `Docker version 26.1.4, build 5650f9b`

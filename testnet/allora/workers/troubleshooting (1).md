# Troubleshooting

### 1. Lỗi không đăng ký được topic:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#1-l%E1%BB%97i-kh%C3%B4ng-%C4%91%C4%83ng-k%C3%BD-%C4%91%C6%B0%E1%BB%A3c-topic) <a href="#id-1-loi-khong-dang-ky-duoc-topic" id="id-1-loi-khong-dang-ky-duoc-topic"></a>

Log hiển thị lỗi:

```
net1-worker1-topic-3    | 2024-07-11T13:43:45Z INF Failed: register node, retrying... (Retry 3/3) error="rpc error: code = NotFound desc = rpc error: code = NotFound desc = account allo... not found: key not found"
```

#### Khởi động lại toàn bộ worker:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#kh%E1%BB%9Fi-%C4%91%E1%BB%99ng-l%E1%BA%A1i-to%C3%A0n-b%E1%BB%99-worker) <a href="#khoi-dong-lai-toan-bo-worker" id="khoi-dong-lai-toan-bo-worker"></a>

Thì restart lại toàn bộ worker bằng lệnh sau:

```
docker compose restart
```

#### Stop all workers <a href="#hoac-chi-khoi-dong-lai-nhung-worker-bi-loi" id="hoac-chi-khoi-dong-lai-nhung-worker-bi-loi"></a>

```bash
docker stop $(docker ps -a -q)
```

#### Hoặc chỉ khởi động lại những worker bị lỗi:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#ho%E1%BA%B7c-ch%E1%BB%89-kh%E1%BB%9Fi-%C4%91%E1%BB%99ng-l%E1%BA%A1i-nh%E1%BB%AFng-worker-b%E1%BB%8B-l%E1%BB%97i) <a href="#hoac-chi-khoi-dong-lai-nhung-worker-bi-loi" id="hoac-chi-khoi-dong-lai-nhung-worker-bi-loi"></a>

Ví dụ khởi động lại worker-topic-1:

```
docker restart net1-worker1-topic-1
```

Tương tự với các worker khác.

```
docker restart net1-worker1-topic-3
docker restart net1-worker1-topic-5
docker restart net1-worker2-topic-2
docker restart net1-worker2-topic-4
docker restart net1-worker2-topic-6
```

Hoặc dùng lệnh dưới để xem các worker đang chạy và lấy tên của worker cần khởi động lại:

```
docker compose ps
```

#### Hết lỗi đăng ký topic:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#h%E1%BA%BFt-l%E1%BB%97i-%C4%91%C4%83ng-k%C3%BD-topic) <a href="#het-loi-dang-ky-topic" id="het-loi-dang-ky-topic"></a>

Đến khi có log:

`net1-worker1-topic-3 | 2024-07-11T13:51:50Z INF Success: register node Tx Hash:=...`

là đã đăng ký topic thành công topic 3. Các topic khác cũng tương tự.

Topic đã có tx đăng ký rồi thì các lần chạy lại sau hiển thị như sau:

`net1-worker1-topic-3 | 2024-07-11T14:00:06Z INF node already registered for topic topic=3`

### 2. Không kết nối được đến Head Node:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#2-kh%C3%B4ng-k%E1%BA%BFt-n%E1%BB%91i-%C4%91%C6%B0%E1%BB%A3c-%C4%91%E1%BA%BFn-head-node) <a href="#id-2-khong-ket-noi-duoc-den-head-node" id="id-2-khong-ket-noi-duoc-den-head-node"></a>

![Error connect head node](https://nodium.xyz/vi/assets/images/error-connect-head-d5fb6f2b0ab04822eef53b4835e30746.png)

* Hình trên là lỗi của worker topic 2 không kết nối được đến head node của Allora, log hiển thị dòng cuối cùng là `DBG host started peer discovery component=host` và không có log hiển thị nào khác của worker này.
* Cần khởi động lại worker của topic đó để kết nối lại đến head node, tương tự: [`Khởi động lại chỉ worker bị lỗi`](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#ho%E1%BA%B7c-ch%E1%BB%89-kh%E1%BB%9Fi-%C4%91%E1%BB%99ng-l%E1%BA%A1i-nh%E1%BB%AFng-worker-b%E1%BB%8B-l%E1%BB%97i).
* Có thể dùng lệnh khởi động lại worker này nhiều lần và tại các thời điểm khác nhau, vì có thể do head node của Allora giới hạn kết nối đồng thời.
* Nếu worker đã chạy bình thường, nhưng vẫn xem log vì nếu có log là `Resetting up chain connection` ở dòng cuối cùng, thì cần khởi động lại worker này, tương tự: [`Khởi động lại chỉ worker bị lỗi`](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#ho%E1%BA%B7c-ch%E1%BB%89-kh%E1%BB%9Fi-%C4%91%E1%BB%99ng-l%E1%BA%A1i-nh%E1%BB%AFng-worker-b%E1%BB%8B-l%E1%BB%97i).

### 3. Lỗi kết nối đến peer:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#3-l%E1%BB%97i-k%E1%BA%BFt-n%E1%BB%91i-%C4%91%E1%BA%BFn-peer) <a href="#id-3-loi-ket-noi-den-peer" id="id-3-loi-ket-noi-den-peer"></a>

* Lỗi này không ảnh hưởng vì worker này đang kết nối đến các worker khác của những người khác đã đăng ký đến head node của Allora, nên không lỗi này quan trọng, bỏ qua.
* Thường khi có lỗi này thì worker vẫn chạy bình thường vì đã kết nối đến head node nên mới nhận được ip và port của các worker khác.

### 4. Kiểm tra Worker hoạt động không:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#4-ki%E1%BB%83m-tra-worker-ho%E1%BA%A1t-%C4%91%E1%BB%99ng-kh%C3%B4ng) <a href="#id-4-kiem-tra-worker-hoat-dong-khong" id="id-4-kiem-tra-worker-hoat-dong-khong"></a>

#### Kiểm tra worker topic 1:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#ki%E1%BB%83m-tra-worker-topic-1) <a href="#kiem-tra-worker-topic-1" id="kiem-tra-worker-topic-1"></a>

```
curl --location 'https://heads.testnet-1.testnet.allora.network/api/v1/functions/execute' \
--header 'Content-Type: application/json' \
--data '{
    "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm",
    "method": "allora-inference-function.wasm",
    "parameters": null,
    "topic": "1",
    "config": {
        "env_vars": [
            {
                "name": "BLS_REQUEST_PATH",
                "value": "/api"
            },
            {
                "name": "ALLORA_ARG_PARAMS",
                "value": "ETH"
            }
        ],
        "number_of_nodes": -1,
        "timeout": 2
    }
}'
```

Xem log hiển thị như sau là đã chạy thành công:&#x20;

<figure><img src="https://nodium.xyz/vi/assets/images/test-worker-topic-1-c7dcd9937287b4dee0c0648c411b8886.png" alt=""><figcaption></figcaption></figure>

Có những lần kiểm tra chỉ hiển thị `INF reporting for roll call`, nhưng không có `command executed successfully` và `Result from WASM:` thì thử lại lệnh `curl` ở trên nhiều lần để xem có kết quả như hình không.

#### Tương tự kiểm tra worker của các topic khác:[​](https://nodium.xyz/vi/docs/allora/install-worker/error-worker#t%C6%B0%C6%A1ng-t%E1%BB%B1-ki%E1%BB%83m-tra-worker-c%E1%BB%A7a-c%C3%A1c-topic-kh%C3%A1c) <a href="#tuong-tu-kiem-tra-worker-cua-cac-topic-khac" id="tuong-tu-kiem-tra-worker-cua-cac-topic-khac"></a>

* Topic 3: đổi `"topic": "1"` thành `"topic": "3"`, `"value": "ETH"` thành `"value": "BTC"`.
* Topic 5: đổi `"topic": "1"` thành `"topic": "5"`, `"value": "ETH"` thành `"value": "SOL"`.
* Topic 2: đổi `"topic": "1"` thành `"topic": "2"`.
* Topic 4: đổi `"topic": "1"` thành `"topic": "4"`, `"value": "ETH"` thành `"value": "BTC"`.
* Topic 6: đổi `"topic": "1"` thành `"topic": "6"`, `"value": "ETH"` thành `"value": "SOL"`.

`Mỗi topic chỉ cần kiểm tra chạy thành công 1 lần là đủ, nếu khởi động lại worker topic nào thì cũng nên kiểm tra worker topic đó lại.`

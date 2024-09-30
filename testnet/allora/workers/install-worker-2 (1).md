---
description: >-
  Cài đặt Worker chạy topic 2, 4, 6 Topic 2, 4, 6 là chạy dự đoán giá ETH, BTC,
  SOL trong 24 giờ. Trang Campaign:
  https://app.allora.network/points/campaign/run-inference-24h
---

# Install Worker 2

#### Trờ về thư mục chính:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#tr%E1%BB%9D-v%E1%BB%81-th%C6%B0-m%E1%BB%A5c-ch%C3%ADnh) <a href="#tro-ve-thu-muc-chinh" id="tro-ve-thu-muc-chinh"></a>

```
cd ..
```

#### Hiển thị thư mục hiện tại:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#hi%E1%BB%83n-th%E1%BB%8B-th%C6%B0-m%E1%BB%A5c-hi%E1%BB%87n-t%E1%BA%A1i) <a href="#hien-thi-thu-muc-hien-tai" id="hien-thi-thu-muc-hien-tai"></a>

```
pwd
```

* Kiểm tra có đúng với thư mục ở bước [`Hiển thị thư mục hiện tại`](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-1-3-5#hi%E1%BB%83n-th%E1%BB%8B-th%C6%B0-m%E1%BB%A5c-hi%E1%BB%87n-t%E1%BA%A1i) không. Nếu sai, cần dùng lệnh `cd` để di chuyển đến thư mục đó. Ví dụ:

```
cd /root
```

#### Clone repository:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#clone-repository) <a href="#clone-repository" id="clone-repository"></a>

```
git clone https://github.com/nhunamit/basic-coin-prediction-node.git
```

#### Đổi tên thư mục repository vừa clone:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#%C4%91%E1%BB%95i-t%C3%AAn-th%C6%B0-m%E1%BB%A5c-repository-v%E1%BB%ABa-clone) <a href="#doi-ten-thu-muc-repository-vua-clone" id="doi-ten-thu-muc-repository-vua-clone"></a>

```
mv basic-coin-prediction-node worker-face-24h
```

#### Di chuyển vào thư mục vừa đổi tên:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#di-chuy%E1%BB%83n-v%C3%A0o-th%C6%B0-m%E1%BB%A5c-v%E1%BB%ABa-%C4%91%E1%BB%95i-t%C3%AAn) <a href="#di-chuyen-vao-thu-muc-vua-doi-ten" id="di-chuyen-vao-thu-muc-vua-doi-ten"></a>

```
cd worker-face-24h
```

#### Xem các nhánh có sẵn:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#xem-c%C3%A1c-nh%C3%A1nh-c%C3%B3-s%E1%BA%B5n) <a href="#xem-cac-nhanh-co-san" id="xem-cac-nhanh-co-san"></a>

```
git branch -a
```

#### Chuyển sang nhánh `worker-face-24h`:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#chuy%E1%BB%83n-sang-nh%C3%A1nh-worker-face-24h) <a href="#chuyen-sang-nhanh-worker-face-24h" id="chuyen-sang-nhanh-worker-face-24h"></a>

```
git checkout worker-face-24h
```

#### Kiểm tra lại nhánh đã thay đổi sang `worker-face-24h`:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#ki%E1%BB%83m-tra-l%E1%BA%A1i-nh%C3%A1nh-%C4%91%C3%A3-thay-%C4%91%E1%BB%95i-sang-worker-face-24h) <a href="#kiem-tra-lai-nhanh-da-thay-doi-sang-worker-face-24h" id="kiem-tra-lai-nhanh-da-thay-doi-sang-worker-face-24h"></a>

```
git branch -a
```

Như hình dưới là đã chuyển sang nhánh `worker-face-24h`:

#### Tạo thư mục data cho các worker:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#t%E1%BA%A1o-th%C6%B0-m%E1%BB%A5c-data-cho-c%C3%A1c-worker) <a href="#tao-thu-muc-data-cho-cac-worker" id="tao-thu-muc-data-cho-cac-worker"></a>

```
mkdir -p worker-topic-2-data
chmod 777 worker-topic-2-data
mkdir -p worker-topic-4-data
chmod 777 worker-topic-4-data
mkdir -p worker-topic-6-data
chmod 777 worker-topic-6-data
```

#### Sửa 24 từ mnemonic trong file `docker-compose.yml`:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#s%E1%BB%ADa-24-t%E1%BB%AB-mnemonic-trong-file-docker-composeyml) <a href="#sua-24-tu-mnemonic-trong-file-docker-composeyml" id="sua-24-tu-mnemonic-trong-file-docker-composeyml"></a>

```
nano docker-compose.yml
```

* Nhấn `i` để chuyển sang chế độ chỉnh sửa.
* `Tìm 3 dòng` có chữ: `--allora-chain-restore-mnemonic='just clap slim ...'` và sửa `just clap slim ...` thành 24 từ mnemonic của ví mới tạo ở bước.
* Chỉnh đoạn `container_name` có `net1` thành `allora` từ  dòng updater trở đi
* Lưu và thoát bằng cách nhấn `Esc` sau đó gõ `:wq` và nhấn `Enter`. (Để thoát mà không lưu, nhấn `Esc` sau đó gõ `:q!` và nhấn `Enter`)

#### Chạy Docker Compose:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#ch%E1%BA%A1y-docker-compose) <a href="#chay-docker-compose" id="chay-docker-compose"></a>

```
docker compose up -d
```

#### Kiểm tra log:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#ki%E1%BB%83m-tra-log) <a href="#kiem-tra-log" id="kiem-tra-log"></a>

```
docker compose logs -f
```

Nhấn `Ctrl + C` để thoát khỏi log.

#### Xem riêng log của từng worker:[​](https://nodium.xyz/vi/docs/allora/install-worker/install-worker-2-4-6#xem-ri%C3%AAng-log-c%E1%BB%A7a-t%E1%BB%ABng-worker) <a href="#xem-rieng-log-cua-tung-worker" id="xem-rieng-log-cua-tung-worker"></a>

* Hiển thị danh sách các worker đang chạy:

```
docker compose ps
```

Hoặc xem toàn bộ các container đang chạy:

```
docker ps
```

* Lấy tên `NAMES` của worker cần xem log từ danh sách trên.
* Ví dụ xem log của net1-worker2-topic-2:

```
docker logs net1-worker2-topic-2 -f
```

# Usage Commands

1️⃣ : Stop Node:

```bash
pkill -f dill-node
```

2️⃣ : Kiểm tra xem đã stop chưa:

```bash
./health_check.sh -v
```

3️⃣ : Xóa folder Light\_node:&#x20;

```bash
rm -rf light_node
```

4️⃣ : Restart lại Light validator node:

```bash
./start_light.sh -p walletPw.txt
```

5️⃣ : Chờ node sync xong, check health node, kiểm tra Node đã xác thực chưa bằng lệnh :

```bash
curl -s localhost:<Thay Host của mình>/metrics | grep -e "validator_correctly_voted_head" -e "validator_correctly_voted_source" -e "validator_correctly_voted_target" -e "validator_successful_attestations" -e "validator_last_attested_slot" -e "validator_next_attestation_slot" | grep pubkey
```

\=> Hiện có thông tin validator\_successful\_attestations nghĩa là Light validator Node của mình đã Vote và xác thực thành công => Reward tăng, theo dõi reward tại:&#x20;

{% embed url="https://andes.dill.xyz/validators?p=8&ps=100&pubkey=" %}

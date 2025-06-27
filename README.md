# VietID: Blockchain Layer-1 Vietnam

VietID là một nền tảng Blockchain Layer-1 thuần Việt, 
hướng đến phục vụ các ứng dụng định danh số, giao dịch minh bạch, 
công chứng điện tử và tích hợp các dịch vụ công trong quá trình chuyển đổi số quốc gia.

## 🚀 Tính năng chính
- ✅ Consensus DBFT (Delegated Byzantine Fault Tolerance)
- 🧱 Hệ thống Shard độc lập, hỗ trợ Cross-shard Transaction
- 🆔 DID (Decentralized Identifier) theo chuẩn W3C
- 🔐 Ký số ECC + Schnorr Signature
- 📡 Giao tiếp mạng P2P bảo mật bằng WebSocket + TLS
- 🔁 API RESTful mở: gửi giao dịch, truy vấn số dư, DID, block
- 💾 Snapshot trạng thái blockchain dạng nén nhẹ (zlib)

## 🧩 Kiến trúc hệ thống
```
+-----------------------------+
|     REST API (Flask)       |
+-----------------------------+
|    Blockchain Core (DBFT)  |
|  - Transaction, Block, VM  |
+-----------------------------+
|      P2P Node (asyncio)    |
+-----------------------------+
|       StateDB, Snapshot    |
+-----------------------------+
```
## 🛠 Cài đặt
### 1. Yêu cầu hệ thống
- Python >= 3.10
- OpenSSL & ECDSA
- Flask & Websocket

### 2. Cài đặt thư viện
pip install -r requirements.txt

### 3. Khởi tạo Node
```
https://vietid-1.onrender.com/
```
```
https://vietid-2.onrender.com/
```
```
https://vietid-3.onrender.com/
```
## 📦 Cấu trúc thư mục
```
├── vietid17.py               # Lõi blockchain
├── p2p_node.py               # Giao tiếp P2P
├── api_server_quart.py       # API REST Flask
├── run_nodeX.py              # File khởi chạy node cụ thể
├── requirements.txt          # Danh sách thư viện
└── README.md                 # Tài liệu này
```

## 📡 Danh sách API REST
```
| Method | Endpoint                               |                   Mô tả                       |
|--------|----------------------------------------|-----------------------------------------------|
| GET    | `/status`                             | Trạng thái node và blockchain |
| GET    | `/block/latest`                       | Block mới nhất |
| GET    | `/blocks/recent/<count>`              | Lấy `count` block gần nhất |
| GET    | `/block/<hash hoặc index>`            | Truy vấn block cụ thể |
| GET    | `/balance/<address>`                  | Tra số dư ví |
| GET    | `/did/list`                           | Danh sách tất cả DID đã đăng ký |
| GET    | `/did/<did>`                          | Truy vấn thông tin DID cụ thể |
| GET    | `/mempool`                            | Giao dịch đang chờ xử lý |
| GET    | `/tx/<txid>`                          | Truy vấn thông tin giao dịch cụ thể |
| GET    | `/governance/proposals`               | Danh sách đề xuất đang được bỏ phiếu |
| GET    | `/governance/votes/<proposal_id>`     | Chi tiết kết quả bỏ phiếu |
| POST   | `/tx/send/TRANSFER`                   | Gửi giao dịch chuyển tiền `TRANSFER` |
| POST   | `/tx/send/DID`                        | Gửi giao dịch đăng ký định danh `DID_REGISTER` |
| POST   | `/tx/send/PROPOSE`                    | Gửi giao dịch tạo đề xuất `PROPOSE` |
| POST   | `/tx/send/VOTE`                       | Gửi giao dịch bỏ phiếu đề xuất `VOTE` |
| POST   | `/tx/send/MINT`                       | Gửi giao dịch nạp token `MINT` |
| POST   | `/tx/send/CROSS_TRANSFER`             | Gửi giao dịch liên Shard CROSS_TRANSFER |
```
## 📥 Mẫu gửi giao dịch bằng curl
### Giao dịch DID_REGISTER (đăng ký định danh số)
```
curl -X POST https://vietid-1.onrender.com/tx/send/DID -H "Content-Type: application/json" -d "{\"alias\": \"Validator Node 1\"}"
```
```
curl -X POST https://vietid-2.onrender.com/tx/send/DID -H "Content-Type: application/json" -d "{\"alias\": \"Validator Node 2\"}"
```
```
curl -X POST https://vietid-3.onrender.com/tx/send/DID -H "Content-Type: application/json" -d "{\"alias\": \"Validator Node 3\"}"
```
### Giao dịch MINT (phát hành token mới)
```
curl -X POST https://vietid-1.onrender.com/tx/send/MINT -H "Content-Type: application/json" -d "{\"recipient\": \"<Thay thế address node 1>\",\"amount\": 1000}"
```
### Giao dịch TRANSFER (chuyển token)
```
curl -X POST https://vietid-1.onrender.com/tx/send/TRANSFER -H "Content-Type: application/json" -d "{\"recipient\": \"<Thay thế address node 2>\",\"amount\": 50}"
```
### Giao dịch liên phân đoạn (Cross-shard)
```
curl -X POST https://vietid-1.onrender.com/tx/send/CROSS_TRANSFER -H "Content-Type: application/json" -d "{\"from_shard\": 0, \"to_shard\": 1, \"recipient\": \"<Thay thế address node 2>\", \"amount\": 100}"
```
### Giao dịch PROPOSE (đề xuất)
```
curl -X POST https://vietid-1.onrender.com/tx/send/PROPOSE -H "Content-Type: application/json" -d "{\"proposal_id\": \"mint_to_node3\", \"title\":\"Mint token node 3\", \"description\": \"This is mint token node 3\", \"action\": \"MINT\", \"mint_target\": \"<Address node 3>\", \"amount\": 1000}"
```
### Giao dịch VOTE (bỏ phiếu)
Node 1 biểu quyết:
```
curl -X POST https://vietid-1.onrender.com/tx/send/VOTE -H "Content-Type: application/json" -d "{\"proposal_id\": \"mint_to_node3\", \"vote\": \"YES\"}"
```
Node 2 biểu quyết:
```
curl -X POST https://vietid-2.onrender.com/tx/send/VOTE -H "Content-Type: application/json" -d "{\"proposal_id\": \"mint_to_node3\", \"vote\": \"YES\"}"
```
Node 3 biểu quyết:
```
curl -X POST https://vietid-3.onrender.com/tx/send/VOTE -H "Content-Type: application/json" -d "{\"proposal_id\": \"mint_to_node3\", \"vote\": \"YES\"}"
```
## 🔬 Demo
- Video demo:
  ```
  https://drive.google.com/drive/u/0/folders/1kMaqn2UkTp76dhghRWo0mKZDTN1J8URm
  ```
- URL testnet:
  ```
  https://vietid-1.onrender.com/
  ```
  ```
  https://vietid-2.onrender.com/
  ```
  ```
  https://vietid-1.onrender.com/
  ```

## 🔒 License
MIT License - Mã nguồn mở, sử dụng tự do với điều kiện ghi nhận tác giả.

## 👥 Đội phát triển
- Võ Văn Tân – Kiến trúc, DBFT, API, DevOps
- Võ Thành Đại – P2P & bảo mật
- Trần Hồng Nhung – Pháp lý & tích hợp

## 📚 Tài liệu tham khảo
- [W3C DID Spec](https://www.w3.org/TR/did-core/)
- [Cosmos SDK Docs](https://docs.cosmos.network)
- [Tendermint Consensus](https://docs.tendermint.com)
- [RFC 7519 - JWT](https://datatracker.ietf.org/doc/html/rfc7519)

> Dự án được phát triển trong khuôn khổ cuộc thi Blockchain Layer-1 Việt Nam 2025. Mọi đóng góp, phản hồi và ý tưởng hợp tác đều được chào đón!

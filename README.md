# About this project : [CanvaLink](https://www.canva.com/design/DAHDEj-yncI/-WtOqzOxSZzBzQWyynw74A/edit?utm_content=DAHDEj-yncI&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)  

# 夥伴共編方式
```
# Clone 倉庫到本地
git clone https://github.com/MocuAcqu/DB_finalPJ_TKP.git
cd DB_finalPJ_TKP
```
```
# 先轉移到 main，同步 main 分支
git switch main
git pull origin main
```
```
# 創建並切換到你的新分支
# ex: git checkout -b feature/environment_setup
git checkout -b <新分支名字>
```
```
# 拉取了最新的程式碼後，只需執行以下命令即可安裝所有必要的套件
pip install -r requirements.txt
```

```
# 若完成開發要 push
git add .
git commit -m "這裡放留言(ex: 登入功能完成)"
```
```
# ex: git push -u origin feature/environment_setup
git push -u origin <分支名字>
```
接著在 github 會有 pull requests 的相關確認，可以做到這一步的時候敲我一下，或是要直接就 merge 上去也可以。

# 啟動方式
```
.\venv\Scripts\activate
python app.py
```
# 資料結構
```
├── app/
│   ├── __init__.py         
│   ├── static/             # 存放 CSS, JS, 圖片等靜態檔案
│   │   └── css/
│   │   │   └── style.css
│   │   ├── js/
│   │   │   └── base.js  
│   ├── templates/          # 存放 HTML 模板
│   │   ├── layouts/
│   │   │   └── base.html       # 基礎模板 
│   │   ├── partials/      
│   │   │   ├── _header.html       # 頁面 Header
│   │   │   ├── _profile_login_required      # 未登入提醒畫面
│   │   │   └── _search_block.html # 搜尋欄位
│   │   │
│   │   ├── auth/
│   │   │   ├── register.html      
│   │   │   └── login.html
│   │   │
│   │   ├── items/
│   │   │   ├── edit.html       #持有物品編輯刪除
│   │   │   └── upload.html     #持有物品上傳
│   │   ├── seller/            # 賣家
│   │   │   ├── tab_items.html         # 賣家的「我的物品」
│   │   │   ├── tab_profile.html       # 賣家個人資料
│   │   │   └── tab_responses.html     # 賣家管理回應（買賣/租借/交換）
│   │   │
│   │   ├── user/              # 用戶
│   │   │   ├── tab_buy_sell.html      # 買賣列表
│   │   │   ├── tab_exchange.html      # 交換列表
│   │   │   ├── tab_rent.html          # 租借列表
│   │   │   ├── tab_notifications      # 通知
│   │   │   └── tab_profile.html       # 使用者的個人資料頁
│   │   │
│   │   └── index.html          # 主頁面
│   ├── routes.py           # 存放所有路由 (URL 對應的處理函式)
│   ├── models.py           
│   ├── auth.py           
│   └── config.py           # 載入環境變數的設定檔
│
├── .env                    # (本地) 存放環境變數，不上傳 Git
├── .env.example            # 環境變數範本檔(給大家知道要設定哪些環境變數)
├── .gitignore
├── app.py                  
└── requirements.txt       
```
# ERD
<img src="https://github.com/MocuAcqu/DB_finalPJ_TKP/blob/main/readme_img/TKP.png" width="500">

# | CRUD Overview

## 1. Items (Product Management)

| Action | Route | Method | Description | Role |
|---|---|---|---|---|
| **Create** | `/items/upload` | POST | Create a new item and upload its image to GridFS. | Seller |
| **Read** | `/` | GET | Search, filter, and paginate all available items. | User / Seller |
| **Read** | `/user/my_items` | GET | View all items uploaded by the seller. | Seller |
| **Read** | `/item/detail/<id>` | GET | View detailed item information. | User / Seller |
| **Read (image)** | `/image/<image_id>` | GET | Retrieve item image from GridFS. | User / Seller |
| **Update** | `/items/edit/<id>` | POST | Update item information and replace its image. | Seller |
| **Delete** | `/items/delete/<id>` | POST | Delete an item and automatically cancel related transactions/exchanges. | Seller |


## 2. Transactions (Buy / Rent Requests)

| Action | Route | Method | Description | Role |
|---|---|---|---|---|
| **Create** | `/express_interest` | POST | Create a transaction record when a user expresses interest in an item (buy/rent). | User |
| **Read** | `/` | GET | View incoming buy/rent requests for the seller's items (dashboard "responses" view). | Seller |
| **Read** | `/` | GET | View the status of the user's own buy/rent requests (dashboard "notifications" view). | User |
| **Update** | `/update_interest_status` | POST | Update the transaction status (e.g., contacted / done / cancelled). | Seller |
| **Delete (batch)** | `/delete-interests` | POST | Batch delete selected transaction records from the seller's management view. | Seller |

## 3. Exchanges (Item-to-Item Trades)

| Action | Route | Method | Description | Role |
|---|---|---|---|---|
| **Create** | `/exchange/create` | POST | Create an exchange proposal, possibly offering multiple items in return for a target item. | User |
| **Read** | `/` | GET | View exchange proposals received for the seller's items ("responses" view). | Seller |
| **Read** | `/` | GET | View exchange proposals created by the user ("notifications" view). | User |
| **Read (messages)** | `/exchange/<id>/messages` | GET | Retrieve the private message history for a specific exchange. | User / Seller |
| **Update (message)** | `/exchange/<id>/add_message` | POST | Add a new chat message inside an exchange. | User / Seller |
| **Update (status)** | `/update_exchange_status` | POST | Update the status of an exchange (e.g., accepted / rejected / cancelled). | Seller |
| **Delete (batch)** | `/delete-exchanges` | POST | Batch delete selected exchange records from the seller's management view. | Seller |

## 4. Comments (Public Item Comments in "Exchange" Function)

| Action | Route | Method | Description | Role |
|---|---|---|---|---|
| **Create** | `/item/<id>/comments` | POST | Add a public comment under a specific item. | User |
| **Read** | `/item/<id>/comments` | GET | Retrieve the list of public comments for a specific item. | User / Seller |

## 5. User Profile

| Action | Route | Method | Description | Role |
|---|---|---|---|---|
| **Update** | `/profile/update` | POST | Update user contact information (e.g., Email, LINE ID, Facebook). | User |



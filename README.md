# 加密貨幣套利交易機器人

一個自動化的加密貨幣套利交易系統，支援在多個交易所之間進行價差套利交易，本專案沒有主程式，有需要請另行聯絡作者。

## 📋 系統概述

本專案實現了一個多交易所套利交易系統，目前支援以下交易所配對：
- **BitoPro ↔ Binance** - 台灣本土交易所與全球最大交易所的套利
- **BitoPro ↔ OKX** - 台灣本土交易所與知名衍生品交易所的套利

## 🏗️ 系統架構

```
Arbitrage/
├── bito_binance.py     # BitoPro與Binance套利模組
├── bito_okx.py         # BitoPro與OKX套利模組
└── README.md           # 專案說明文檔
```

## ⚡ 核心功能

### 套利檢測
- **即時價差監控** - 持續監控兩個交易所之間的價格差異
- **手續費計算** - 自動計算交易手續費，確保套利可行性
- **最小套利比例** - 設定最小獲利門檻，避免無效交易

### 風險管理
- **餘額檢查** - 即時監控各交易所帳戶餘額
- **最小訂單限制** - 遵循各交易所的最小訂單量規定
- **方向性檢查** - 智能判斷套利方向的可行性

### 訂單執行
- **雙向套利** - 支援兩個方向的套利操作
- **數量策略** - 支援固定數量、市場比例、資金比例三種策略
- **即時執行** - 多線程架構確保快速執行

## 🔧 系統配置

### 必要依賴
```python
# 交易所API客戶端
from binance.spot import Spot
from binance.um_futures import UMFutures
from bito.client import Client

# 工具庫
from loguru import logger
import threading
import requests
import math
import asyncio
```

### API金鑰配置
```python
# BitoPro 配置
bito_key = "your_bito_api_key"
bito_secret = "your_bito_secret_key"
bito_email = "your_bito_email"
bito_vip_level = "0"  # VIP等級 (0-6 或 "market maker")

# Binance 配置
binance_key = "your_binance_api_key"
binance_secret = "your_binance_secret_key"

# OKX 配置 (若使用OKX模組)
okx_key = "your_okx_api_key"
okx_secret = "your_okx_secret_key"
okx_passphrase = "your_okx_passphrase"
```

## 💰 手續費結構

### BitoPro VIP等級手續費
| VIP等級 | 手續費率 |
|---------|----------|
| 0       | 0.2%     |
| 1       | 0.194%   |
| 2       | 0.15%    |
| 3       | 0.14%    |
| 4       | 0.13%    |
| 5       | 0.12%    |
| 6       | 0.11%    |
| 造市商   | 0%       |

### 交易策略類型

#### 1. 固定數量策略 (`"fixed"`)
- 每次套利使用固定的交易數量
- 適合穩定的套利操作

#### 2. 市場比例策略 (`"market"`)
- 根據市場掛單深度的比例進行交易
- 適應市場流動性變化

#### 3. 資金比例策略 (`"equity"`)
- 根據帳戶餘額比例進行交易
- 動態調整交易規模

## 🚀 使用範例

### BitoPro-Binance 套利
```python
from bito_binance import BitoBinanceArbitrage

# 初始化套利機器人
arbitrage = BitoBinanceArbitrage()

# 設定交易對
arbitrage.quote = "BCH"
arbitrage.base = "USDT"

# 登入交易所
arbitrage.exchange_1_login(bito_key, bito_secret, bito_email)
arbitrage.exchange_2_login(binance_key, binance_secret)

# 配置策略參數
arbitrage.arbitrage_quantity = 0.1  # 交易數量
arbitrage.arbitrage_quantity_type = "fixed"  # 策略類型
arbitrage.arbitrage_ratio = 0.005  # 最小套利比例 (0.5%)
arbitrage.get_trading_fee("0")  # 設定VIP等級

# 啟動套利監控
arbitrage.start()
```

## ⚠️ 風險警告

1. **市場風險** - 加密貨幣市場波動劇烈，套利機會稍縱即逝
2. **技術風險** - 網路延遲、API限制可能影響套利效果
3. **流動性風險** - 市場深度不足可能導致滑點損失
4. **法規風險** - 請確保遵循當地相關法規

## 📊 監控指標

系統會即時追蹤以下關鍵指標：
- **價差百分比** - 兩交易所之間的價格差異
- **可用餘額** - 各交易所的可交易餘額
- **套利方向** - 當前可執行的套利方向
- **執行次數** - 累計套利交易次數
- **獲利統計** - 每筆交易的獲利情況

## 🔒 安全性考量

- 使用唯讀API權限進行價格監控
- 交易API金鑰請妥善保管
- 建議使用IP白名單限制API存取
- 定期檢查帳戶餘額變化

## 📝 注意事項

⚠️ **重要提醒：此為核心套利邏輯的部分代碼，需要完整的主程序來運行整個系統。**

- 本專案將於近期轉為私人倉庫
- 請在使用前進行充分的回測和小額測試
- 建議在測試網環境先行驗證策略邏輯
- 實盤交易前請確保充分理解所有風險

## 🤝 貢獻

本專案目前為私人開發項目，如有建議或問題，請通過相關渠道聯繫。

## 📄 免責聲明

本軟體僅供學習和研究使用。使用者需自行承擔所有交易風險，開發者不對任何投資損失負責。請在充分了解相關風險的情況下謹慎使用。

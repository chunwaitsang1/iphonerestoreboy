name: Apple Stock Checker

on:
  schedule:
    - cron: '*/5 * * * *'
  workflow_dispatch:

jobs:
  check-stock:
    runs-on: ubuntu-latest

    steps:
      - name: 檢出專案程式碼
        uses: actions/checkout@v4

      - name: 設定 Python 環境
        uses: actions/setup-python@v5
        with:
          python-version: '3.10'

      - name: 安裝 Requests 套件
        run: |
          python -m pip install --upgrade pip
          pip install requests

      # 🔑 關鍵步驟：恢復與儲存狀態檔快取
      - name: 讀取/寫入狀態快取
        uses: actions/cache@v4
        with:
          path: stock_state.json
          key: stock-state-${{ github.run_id }}
          restore-keys: |
            stock-state-

      - name: 執行查貨腳本
        env:
          BOT_TOKEN: ${{ secrets.BOT_TOKEN }}
          CHAT_ID: ${{ secrets.CHAT_ID }}
        run: python main.py

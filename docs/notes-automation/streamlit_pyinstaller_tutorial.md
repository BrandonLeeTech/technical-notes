# Streamlit + PyInstaller 教學筆記

## 1. Streamlit 是什麼？

[Streamlit](https://streamlit.io/) 是一個讓 Python 開發者能夠快速建立互動式網頁應用的框架，適用於資料科學、機器學習模型展示、測試平台等用途。

### 特點：
- 不需寫 HTML/CSS/JS
- 即時預覽修改內容
- 支援多種互動元件（按鈕、輸入欄、圖表等）

---

## 2. 建立基本 Streamlit 應用

```python
# app.py
import streamlit as st

st.title("Hello Streamlit")
name = st.text_input("請輸入你的名字：")
st.write(f"你好，{name}！")```


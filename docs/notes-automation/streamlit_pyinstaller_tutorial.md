# Streamlit + PyInstaller 打包教學

## 1. Streamlit 是什麼？

[Streamlit](https://streamlit.io/) 是 Python 網頁應用框架，能協助開發者快速建立互動式介面，常用於資料科學、機器學習模型展示和測試工具。

### Streamlit 的特色
- 不必自行撰寫 HTML、CSS 或 JavaScript。
- 修改程式後可以即時預覽結果。
- 提供按鈕、輸入欄位和圖表等互動元件。

---

## 2. 建立 Streamlit 應用程式

```python
# app.py
import streamlit as st

st.title("Hello Streamlit")
name = st.text_input("請輸入你的名字：")
st.write(f"你好，{name}！")```


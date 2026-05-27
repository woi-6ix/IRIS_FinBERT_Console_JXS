# Streamlit Cloud deployment fix

Upload/commit these files at the root of your GitHub repo:

- `JXS-Sentiment-HuggingFace-XGBoost-StockBot-Streamlit.py`
- `requirements.txt`
- `.streamlit/config.toml`

In Streamlit Cloud, also set the app's Python version to 3.12 or 3.11 in the app settings/advanced settings, then reboot or redeploy the app.

Why:
- The log shows Cloud is only installing Streamlit's base dependencies, then failing on `import feedparser`.
- `requirements.txt` tells Cloud to install the packages your script imports.
- `.streamlit/config.toml` disables file watching, which avoids the PyTorch watcher crash seen in the earlier logs.

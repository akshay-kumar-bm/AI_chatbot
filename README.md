# Customer Support Agent Chatbot (LangGraph + Groq + ChromaDB)

A tool-calling customer-support agent for an online store (the code calls it an elastic-products / flower-shop store). It answers FAQs, recommends products, looks up and places orders, and emails confirmations. Based on a customer-support-agent tutorial series (the original README is "Episode 5") and extended with MongoDB chat history.

## Features
- LangGraph agent loop (`agent` node, conditional edge to a `ToolNode`, back to `agent`) with state `messages` + `user_id`.
- LLM: Groq (`llama-3.1-70b-versatile`) via `langchain-groq`, bound to 7 tools.
- Tools (`tools.py`): `query_knowledge_base` (FAQ RAG), `search_for_product_reccommendations` (inventory RAG), `data_protection_check`, `create_new_customer`, `retrieve_existing_customer_orders`, `place_order`, `send_order_confirmation_email` (Gmail SMTP).
- Vector search: ChromaDB `PersistentClient` with two collections (FAQ, inventory) using a HuggingFace embedding model through llama-index; data loaded from `FAQ.json` and `inventory.json`.
- Per-user chat history stored in MongoDB (`chat_history_db.chat_histories`), trimmed to the last 5 messages before each LLM call.
- Streamlit chat frontend (`streamlit_frontend.py`).
- `chatbot_juptier.ipynb`: notebook prototype.

## Structure
```
chatbot.py            # LangGraph agent, Mongo history
tools.py              # tool definitions
vector_store.py       # Chroma collections, embeddings
streamlit_frontend.py # UI
FAQ.json, inventory.json
chatbot_juptier.ipynb
requirments.txt
.chroma_db/           # committed vector DB (~3.7 MB)
```

## Setup
```
pip install -r requirments.txt        # note the filename spelling
```
Environment variables (`.env`): `GROQ_API_KEY`, `MONGODB_URI`, `SENDER_EMAIL`, `SENDER_PASSWORD` (Gmail app password); `OPENAI_API_KEY` is also listed in the file. Run:
```
streamlit run streamlit_frontend.py
```

## Limitations
- Customer/order "database" in `tools.py` is a stand-in, not production storage; orders and customer data are simplistic.
- `requirments.txt` omits some imports (e.g. pymongo, langchain-groq, llama-index core) so it may need extra installs.
- Groq model id may be deprecated.

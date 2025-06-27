# LANGGRAPH-
his project demonstrates how to build a stateful chatbot using LangGraph, LangChain, and Groq LLM. The architecture uses a StateGraph to control the flow of logic and maintain the conversation state across multiple turns.

🛠️ Installation
Run the following commands to install the necessary libraries:

bash
Copy
Edit
!pip install langgraph langsmith
!pip install langchain langchain_groq langchain_community
🔐 Set API Keys
Use the google.colab.userdata interface to securely access your API keys:

python
Copy
Edit
from google.colab import userdata

GOOGLE_API_KEY = userdata.get('GOOGLE_API_KEY')
langsmith_api_key = userdata.get('langsmith_api_key')
groq_api_key = userdata.get('groq_api_key')
Set environment variables:

python
Copy
Edit
import os
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_PROJECT"] = "CourseLanggraph"
os.environ["LANGCHAIN_API_KEY"] = langsmith_api_key
🤖 Initialize Groq LLM
python
Copy
Edit
from langchain_groq import ChatGroq

llm = ChatGroq(groq_api_key=groq_api_key, model_name="Gemma2-9b-It")
🧠 LangGraph Setup
python
Copy
Edit
from typing import Annotated
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
Define State
python
Copy
Edit
class State(TypedDict):
    messages: Annotated[list, add_messages]
Build Graph
python
Copy
Edit
graph_builder = StateGraph(State)
Add Node
python
Copy
Edit
def chatbot(State: State):
    return {"messages": llm.invoke(State['messages'])}

graph_builder.add_node("chatbot", chatbot)
Add Edges
python
Copy
Edit
graph_builder.add_edge(START, "chatbot")
graph_builder.add_edge("chatbot", END)
Compile Graph
python
Copy
Edit
graph = graph_builder.compile()
(Optional) Visualize Graph
python
Copy
Edit
from IPython.display import display, Image

try:
    display(Image(graph.get_graph().draw_mermaid_png()))
except Exception:
    pass
🗣️ Chatbot Loop
python
Copy
Edit
while True:
    user_input = input("User: ")
    if user_input.lower() in ["quit", "q"]:
        print("GOOD BYE")
        break
    for event in graph.stream({'messages': ("user", user_input)}):
        print(event.values())
        for value in event.values():
            print("Assistant:", value["messages"].content)
📦 Features
Uses LangGraph's StateGraph to manage chatbot flow.

Shared state makes the chatbot context-aware.

Powered by Groq's Gemma2-9b-It LLM.

Simple graph-based design for scalability.

📌 Requirements
Python 3.8+

Google Colab (for secure API key use)

Accounts for Groq and LangChain (for API keys)


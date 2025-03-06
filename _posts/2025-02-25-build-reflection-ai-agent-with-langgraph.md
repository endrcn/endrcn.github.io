---
layout: post
title: "LangGraph ile Reflection AI Agent Nasıl Yapılır?"
author: endrcn
date: "2025-02-25"
categories: [ Yapay Zeka ]
image: assets/images/ai/build_reflection_ai_agent_with_langgraph.png
featured: true
hidden: true
---

Merhaba arkadaşlar! Bugün **LangGraph** kütüphanesini kullanarak, kendi kendini değerlendirebilen ve iyileştirebilen bir AI Agent geliştireceğiz.

Bu projemizde **Reflection Design Pattern**'i kullanarak, agent'ımızın düşünme ve karar verme süreçlerini nasıl optimize edebileceğimizi detaylıca inceleyeceğiz.

Projemizde **Streamlit** ile basit bir arayüz oluşturacak, **typing** modülü ile tip güvenliğini sağlayacak ve **LangChain**'in temel bileşenlerini kullanarak modüler bir sistem tasarlayacağz.

Geleneksel AI sistemlerinin aksine, **Reflection Pattern** kullanan bir agent, kendi kararlarını değerlendirebilir ve gerektiğinde stratejisini değiştirebilir. Bu, daha akıllı ve adaptif sistemler geliştirmemize olanak sağlar.

Hadi başlayalım ve birlikte adım adım geliştirelim!

<iframe width="1200" height="450" src="https://www.youtube.com/embed/TqCPm5WYGok?si=yohX7buErzIkNjB1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

## Proje Kurulumu

### 1. Proje klasörü ve dosya yapısı

**app.py** dosyasını oluşturalım ve aşağıdaki kütüpahaneleri projeye dahil edelim:

```python
import streamlit as st
from typing import Annotated
from typing_extensions import TypedDict

from langchain_core.messages import AIMessage, HumanMessage
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langgraph.checkpoint.memory import MemorySaver
```

---

### 2. State Tanımlamaları

Agent'ın durumunu tutacak **State** sınıfını oluşturalım:

```python
class State(TypedDict):
    messages: Annotated[list, add_messages]
```

Burada **messages** attribute'unu bir **Annotated** olarak tanımladık. Yeni mesajların listeye eklenmesi için **add_messages** fonksiyonunu kullandık. Bu sayede, her yeni mesaj önceki mesajın üzerine yazılmayacak.

---

### 3. Graph Tanımlamaları

**StateGraph** oluşturalım ve **State** sınıfını parametre olarak verelim:

```python
graph_builder = StateGraph(State)
```

---

### 4. Prompt Tanımlamaları

#### **4.1. Generation Prompt**

AI Agent'in metin üretmesi için bir **prompt** oluşturalım:

```python
prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            "You are an essay assistant tasked with writing excellent 5-paragraph essays."
            " Generate the best essay possible for the user's request"
            " If the user provides critique, respond with a revised version of your previous attempts."
        ),
        MessagesPlaceholder(variable_name="messages"),
    ]
)
```

**LLM modeli olarak GPT-4o kullanacağız:**

```python
llm = ChatOpenAI(model="gpt-4o")
```

**Prompt ile modelin çıktısını oluşturalım:**

```python
generate = prompt | llm
```

#### **4.2. Reflection Prompt**

AI Agent'in üretimi değerlendirecek bir **reflection** prompt'u oluşturalım:

```python
reflection_prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            "You are a teacher grading an essay submission. Generate critique and recommendations for the user's submission."
            " Provide detailed recommendations, including requests for length, depth, style, etc."
        ),
        MessagesPlaceholder(variable_name="messages"),
    ]
)
```

**Reflection modelini oluşturalım:**

```python
reflect = reflection_prompt | llm
```

---

### 5. Node Tanımlamaları

#### **5.1. Generation Node**

```python
def generation_node(state: State):
    return {"messages": [generate.invoke(state["messages"])]}
```

#### **5.2. Reflection Node**

```python
def reflection_node(state: State):
    cls_map = {"ai": HumanMessage, "human": AIMessage}
    translated = [state["messages"][0]] + [
        cls_map[msg.type](content=msg.content) for msg in state["messages"][1:]
    ]
    res = reflect.invoke(translated)

    return {"messages": [HumanMessage(content=res.content)]}
```

---

### 6. Graph Kenar ve Koşullarını Tanımlama

```python
graph_builder.add_node("generate", generation_node)
graph_builder.add_node("reflect", reflection_node)

graph_builder.add_edge(START, "generate")
```

#### **Koşul Tanımlama**

```python
def should_continue(state: State):
    if len(state["messages"]) > 6:
        return END
    return "reflect"

graph_builder.add_conditional_edges("generate", should_continue)
graph_builder.add_edge("reflect", "generate")
```

---

### 7. Memory Kaydedici ve Graph Derleme

```python
memory = MemorySaver()
graph = graph_builder.compile(checkpointer=memory)
```

---

### 8. Streamlit Arayüzü

```python
def stream_graph_updates(user_input: str):
    for event in graph.stream({"messages": [HumanMessage(content=user_input)]}, config):
        state = graph.get_state(config)
        last_message = state.values["messages"][-1]
        st.text(ChatPromptTemplate.from_messages([last_message]).pretty_repr())

def main():
    st.title("Reflection Agent")
    st.write("Enter your task description below:")

    task_description = st.text_area("Task Description", height=200)

    if (st.button("Run Task")):
        with st.spinner("Running Task..."):
            stream_graph_updates(task_description)
            st.success("Task Completed!")

if __name__ == "__main__":
    main()
```

---

Bu yazıda **LangGraph ile Reflection Design Pattern** kullanarak, kendi kendini eleştiren ve iyileştiren bir AI Agent oluşturduk.

Beğendiyseniz **videoyu beğenmeyi ve kanala abone olmayı unutmayın!** 🚀


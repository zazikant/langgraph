Here's a **simplified explanation** of using **state data to pass information sequentially** in LangGraph:

---

### ✅ **Core Idea: One Shared "Notebook" (State) Passed Step-by-Step**

- Imagine your workflow as a **relay race**.
- There’s **one notebook** (the **state**) that gets **passed from runner to runner** (node to node).
- Each runner (**node**) **reads the notebook**, **does their task**, **writes new info** into it, and **hands it to the next runner**.
- The **referee** (**edge function**) **only looks at the latest notebook** to decide: *“Who runs next?”*

---

### 🔁 **How It Works – Step by Step**

1. **Start**: Notebook is empty or has the user’s question.  
   → `state = {"query": "Do you have Nike shoes size 10?"}`

2. **Node A (Classifier)**:  
   - Reads `query`  
   - Figures out intent → writes it down  
   → `state = {"query": "...", "intent": "product_search"}`

3. **Edge Function**:  
   - Sees `"intent": "product_search"`  
   - Says: “Send notebook to **ProductSearchNode**”

4. **Node B (ProductSearch)**:  
   - Reads `query` and `intent`  
   - Checks inventory → adds result  
   → `state = {..., "in_stock": true, "product_id": "nike_3455"}`

5. **Edge Function**:  
   - Sees `in_stock` → routes to **ResponseNode**

6. **Node C (Response)**:  
   - Uses all info in notebook to craft final reply  
   → `state = {..., "response": "Yes, size 10 is available!"}`

7. **Done!**

---

### 📌 Key Rules (Simple!)

- **Only one notebook** (state) per user request.
- **Each node adds or updates** info—**never waits for a human**.
- **Next step is decided ONLY by what’s in the notebook**.
- **No skipping ahead**—everyone waits their turn.

---

### 💡 Pro Tip for Big Data
If a node creates **huge data** (e.g., 10MB JSON):  
→ **Don’t put it in the notebook!**  
→ Save it in a database or file, and just write the **ID or link** in the state:  
```python
state["report_url"] = "s3://bucket/report_123.json"
```

---

That’s it! **State = shared notebook. Nodes = workers updating it. Edges = traffic signs reading the notebook to decide the next stop.**
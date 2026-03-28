# Semantic vs Syntactic

**These vectors capture semantic and syntactic relation ships** 
Meaning of is semantic and syntactic

In that sentence, **semantic** and **syntactic** refer to two different types of relationships that language (and embeddings) can capture:

---

### **1. Semantic relationships = meaning**

These capture **what words mean and how they relate in concept**.

* Example relationships:

  * *king* ↔ *queen* (both royalty)
  * *doctor* ↔ *hospital*
  * *cat* ↔ *animal*

👉 These words may be different, but they are **related in meaning or context**.

---

### **2. Syntactic relationships = structure (grammar)**

These capture **how words function in language (form, tense, grammar, usage patterns)**.

* Example relationships:

  * *run* ↔ *running* (verb forms)
  * *quick* ↔ *quickly* (adjective → adverb)
  * *eat* ↔ *ate* (tense change)

👉 These words are related by **grammatical rules**, not meaning.

---

### **In vector embeddings (like Word2Vec, etc.)**

When we say:

> “vectors capture semantic and syntactic relationships”

It means:

* Words with **similar meanings** are close in vector space (semantic)
* Words with **similar grammatical behavior** also form patterns (syntactic)

---

### **Classic example**

* *king − man + woman ≈ queen*

✔ Semantic: gender + royalty meaning
✔ Syntactic: consistent word roles and transformations

---

### **Quick summary**

* **Semantic = meaning-based similarity**
* **Syntactic = grammar/form-based similarity**

---

### From the book - "AI Agents and Applications" by Roberto Infante
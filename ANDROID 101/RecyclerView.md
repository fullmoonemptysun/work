## 🧠 What Is RecyclerView?

RecyclerView is a UI widget in Android used to efficiently **display scrollable lists** (vertical or horizontal). It’s the successor of `ListView`, designed to be more powerful and flexible.

It **recycles views**, meaning it doesn’t create a new view for every item, but instead reuses existing views. This makes it memory- and performance-efficient — especially for large lists.

---

## 📦 Components of a RecyclerView

A basic RecyclerView setup has **5 main pieces**:

1. **RecyclerView (Widget)**  
    The visual container that shows a list of items.
    
2. **LayoutManager**  
    Tells RecyclerView _how_ to arrange items — vertical list? grid? staggered grid?
    
3. **Adapter**  
    The _middleman_ that binds your data to views. Think of it like:  
    “Hey, here’s a Pokémon object. Please show its name and image in the list.”
    
4. **ViewHolder**  
    Holds the individual UI elements (TextView, ImageView) for a single item.
    
5. **Data Source**  
    Your list of Pokémon — could be from an API, database, or hardcoded list.
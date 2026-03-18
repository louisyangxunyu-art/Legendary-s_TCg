# Legendary-s_TCg
Pokemon shop
// 🚀 REAL STORE + STRIPE-READY + DEPLOY-READY
// Works in Vite React. Includes Stripe Checkout redirect + API hook (you must add backend URL)

import { useState, useEffect } from "react";

export default function App() {
  const [cart, setCart] = useState([]);
  const [showCart, setShowCart] = useState(false);
  const [orders, setOrders] = useState([]);
  const [loading, setLoading] = useState(false);

  // LOAD
  useEffect(() => {
    setCart(JSON.parse(localStorage.getItem("cart")) || []);
    setOrders(JSON.parse(localStorage.getItem("orders")) || []);
  }, []);

  // SAVE
  useEffect(() => {
    localStorage.setItem("cart", JSON.stringify(cart));
  }, [cart]);

  useEffect(() => {
    localStorage.setItem("orders", JSON.stringify(orders));
  }, [orders]);

  const products = [
    { id: 1, name: "Mystic Flames Pack", price: 4.99, image: "https://via.placeholder.com/300x400" },
    { id: 2, name: "Shadow Elite Pack", price: 9.99, image: "https://via.placeholder.com/300x400" },
    { id: 3, name: "Dragon Collector Box", price: 29.99, image: "https://via.placeholder.com/300x400" },
    { id: 4, name: "Galaxy Premium Set", price: 49.99, image: "https://via.placeholder.com/300x400" }
  ];

  const addToCart = (product) => {
    setCart((prev) => {
      const exist = prev.find((i) => i.id === product.id);
      if (exist) {
        return prev.map((i) => i.id === product.id ? { ...i, qty: i.qty + 1 } : i);
      }
      return [...prev, { ...product, qty: 1 }];
    });
  };

  const removeFromCart = (id) => {
    setCart((prev) => prev.filter((i) => i.id !== id));
  };

  const total = cart.reduce((s, i) => s + i.price * i.qty, 0);

  // 🔥 REAL CHECKOUT (CONNECT TO BACKEND)
  const checkout = async () => {
    if (cart.length === 0) return alert("Cart is empty");

    setLoading(true);

    try {
      const res = await fetch("https://YOUR-BACKEND-URL/create-checkout-session", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ cart }),
      });

      const data = await res.json();

      // redirect to Stripe checkout
      window.location.href = data.url;
    } catch (err) {
      alert("Payment failed setup");
    }

    setLoading(false);
  };

  return (
    <div style={{ background: "#0b0618", color: "white", minHeight: "100vh", padding: 20 }}>

      {/* HEADER */}
      <div style={{ display: "flex", justifyContent: "space-between" }}>
        <h1>CardVault TCG</h1>
        <button onClick={() => setShowCart(!showCart)}>Cart ({cart.length})</button>
      </div>

      {/* HERO */}
      <div style={{ textAlign: "center", margin: "30px 0" }}>
        <h2>🔥 Premium Drops</h2>
        <p>Secure checkout with Stripe</p>
      </div>

      {/* PRODUCTS */}
      <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fit, minmax(220px,1fr))", gap: 20 }}>
        {products.map((p) => (
          <div key={p.id} style={{ background: "#1a1333", padding: 15, borderRadius: 12 }}>
            <img src={p.image} style={{ width: "100%" }} />
            <h3>{p.name}</h3>
            <p style={{ color: "#22c55e" }}>${p.price}</p>
            <button onClick={() => addToCart(p)} style={{ width: "100%" }}>
              Add to Cart
            </button>
          </div>
        ))}
      </div>

      {/* CART */}
      {showCart && (
        <div style={{ position: "fixed", right: 0, top: 0, width: 320, height: "100%", background: "#020617", padding: 20 }}>
          <h2>Cart</h2>

          {cart.map((c) => (
            <div key={c.id}>
              <p>{c.name} x {c.qty}</p>
              <button onClick={() => removeFromCart(c.id)}>Remove</button>
            </div>
          ))}

          <h3>Total: ${total.toFixed(2)}</h3>

          <button onClick={checkout} disabled={loading} style={{ width: "100%", marginTop: 10 }}>
            {loading ? "Processing..." : "Pay with Card"}
          </button>
        </div>
      )}

      {/* INFO */}
      <div style={{ marginTop: 40, fontSize: 14, opacity: 0.7 }}>
        ⚠️ To enable real payments, you must connect a backend (Stripe API).
      </div>

    </div>
  );
}

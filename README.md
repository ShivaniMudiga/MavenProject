# MavenProject
this is maven project
hello ...

import React, {
  useState,
  useEffect,
  useMemo,
  useRef,
  useContext,
  createContext
} from "react";

// Create Context
const ThemeContext = createContext();


// Component that uses Context
function ThemeButton() {
  const { theme, setTheme } = useContext(ThemeContext);

  return (
    <button
      onClick={() =>
        setTheme(theme === "light" ? "dark" : "light")
      }
    >
      Toggle {theme === "light" ? "Dark" : "Light"} Theme
    </button>
  );
}


// Main Component
function App() {

  // -----------------------------
  // useState
  // -----------------------------
  const [products, setProducts] = useState([]);
  const [search, setSearch] = useState("");
  const [theme, setTheme] = useState("light");


  // -----------------------------
  // useRef
  // -----------------------------
  const nameRef = useRef(null);
  const priceRef = useRef(null);


  // -----------------------------
  // Add Product
  // -----------------------------
  const addProduct = () => {
    const name = nameRef.current.value;
    const price = parseFloat(priceRef.current.value);

    if (name && !isNaN(price)) {

      setProducts([
        ...products,
        {
          name: name,
          price: price
        }
      ]);

      // Clear input fields
      nameRef.current.value = "";
      priceRef.current.value = "";
    }
  };


  // -----------------------------
  // useMemo - Filter Products
  // -----------------------------
  const filteredProducts = useMemo(() => {
    return products.filter((product) =>
      product.name
        .toLowerCase()
        .includes(search.toLowerCase())
    );
  }, [products, search]);


  // -----------------------------
  // useMemo - Calculate Total
  // -----------------------------
  const totalValue = useMemo(() => {
    return filteredProducts.reduce(
      (sum, product) => sum + product.price,
      0
    );
  }, [filteredProducts]);


  // -----------------------------
  // useEffect - Theme
  // -----------------------------
  useEffect(() => {
    document.body.style.backgroundColor =
      theme === "light" ? "#ffffff" : "#333333";

    document.body.style.color =
      theme === "light" ? "#000000" : "#ffffff";
  }, [theme]);


  return (
    <ThemeContext.Provider
      value={{ theme, setTheme }}
    >
      <div
        style={{
          textAlign: "center",
          padding: "40px",
          fontFamily: "Arial, sans-serif"
        }}
      >

        <h1>📦 Inventory Management</h1>


        {/* Search */}
        <input
          type="text"
          placeholder="Search product..."
          value={search}
          onChange={(e) => setSearch(e.target.value)}
        />


        <br />
        <br />


        {/* Product Name */}
        <input
          ref={nameRef}
          type="text"
          placeholder="Product Name"
        />


        {/* Product Price */}
        <input
          ref={priceRef}
          type="number"
          placeholder="Price"
        />


        <button onClick={addProduct}>
          Add Product
        </button>


        <br />
        <br />


        {/* Theme Button */}
        <ThemeButton />


        {/* Total Inventory Value */}
        <h3>
          Total Inventory Value: ₹{totalValue}
        </h3>


        {/* Product List */}
        <ul
          style={{
            listStyle: "none",
            padding: 0
          }}
        >
          {filteredProducts.map((product, index) => (
            <li
              key={index}
              style={{
                margin: "10px",
                padding: "10px"
              }}
            >
              {product.name} - ₹{product.price}
            </li>
          ))}
        </ul>

      </div>
    </ThemeContext.Provider>
  );
}

export default App




Program 4 — E-commerce App with React Router
Aim: Demonstrate routing, Context API, product details, login, protected routes and cart operations.
Original / supplied version
import React, { createContext, useContext, useState } from "react";
import {
 BrowserRouter, Routes, Route, Link, NavLink,
 Navigate, useParams, useNavigate, useLocation,
} from "react-router-dom";
const PRODUCTS = [
 { id: 1, name: "Laptop", price: 55000, desc: "14-inch, 16GB RAM, 512GB SSD" },
 { id: 2, name: "Headphones", price: 2500, desc: "Wireless, noise cancelling" },
 { id: 3, name: "Smart Watch", price: 4000, desc: "Heart-rate and sleep tracking" },
 { id: 4, name: "Backpack", price: 1200, desc: "Water-resistant, 25L" },
];
const AppContext = createContext();
const useApp = () => useContext(AppContext);
function AppProvider({ children }) {
 const [user, setUser] = useState(null);
 const [cart, setCart] = useState([]);
 const addToCart = (product) => setCart(c => [...c, product]);
 const removeFromCart = (index) => setCart(c => c.filter((_, i) => i !== index));
 return (
 <AppContext.Provider value={{ user, setUser, cart, addToCart, removeFromCart }}>
 {children}
 </AppContext.Provider>
 );
}
function ProtectedRoute({ children }) {
 const { user } = useApp();
 const location = useLocation();
 if (!user) return <Navigate to="/login" state={{ from: location.pathname }} replace />;
 return children;
}
function Navbar() {
 const { user, setUser, cart } = useApp();
 const navigate = useNavigate();
 const style = ({ isActive }) => ({
 marginRight: 15, textDecoration: "none",
 fontWeight: isActive ? "bold" : "normal", color: isActive ? "#c0392b" : "#333",
 });
 return (
 <nav style={{ padding: 12, background: "#eee", display: "flex", alignItems: "center" }}>
 <NavLink to="/" style={style}>Home</NavLink>
 <NavLink to="/products" style={style}>Products</NavLink>
 <NavLink to="/contact" style={style}>Contact</NavLink>
 <NavLink to="/cart" style={style}>Cart ({cart.length})</NavLink>
 <span style={{ marginLeft: "auto" }}>
 {user ? (
 <>
 Hi, {user}{" "}
 <button onClick={() => { setUser(null); navigate("/"); }}>Logout</button>
 </>
 ) : <NavLink to="/login" style={style}>Login</NavLink>}
 </span>
 </nav>
 );
}
const Home = () => (
 <div>
 <h2>Welcome to MyShop</h2>
 <p>Browse our products and add your favourites to the cart.</p>
 <Link to="/products">Shop now</Link>
 </div>
);
const Products = () => (
 <div>
 <h2>Products</h2>
 {PRODUCTS.map(p => (
 <div key={p.id} style={{ border: "1px solid #ccc", padding: 10, margin: "10px 0" }}>
 <h3>{p.name}</h3><p>Rs.{p.price}</p>
 <Link to={`/products/${p.id}`}>View details</Link>
 </div>
 ))}
 </div>
);
function ProductDetails() { React Practical Programs 1–6 Page 6
 const { id } = useParams();
 const { addToCart } = useApp();
 const product = PRODUCTS.find(p => p.id === Number(id));
 if (!product) return <h2>Product not found</h2>;
 return (
 <div>
 <h2>{product.name}</h2><p>{product.desc}</p><p><b>Rs.{product.price}</b></p>
 <button onClick={() => addToCart(product)}>Add to cart</button>{" "}
 <Link to="/products">Back</Link>
 </div>
 );
}
function Contact() {
 const [sent, setSent] = useState(false);
 return (
 <div>
 <h2>Contact Us</h2>
 {sent ? <p>Thanks! We will get back to you.</p> : <>
 <input placeholder="Your email" /><br /><br />
 <textarea placeholder="Message" /><br /><br />
 <button onClick={() => setSent(true)}>Send</button>
 </>}
 </div>
 );
}
function Login() {
 const { setUser } = useApp();
 const navigate = useNavigate();
 const location = useLocation();
 const [name, setName] = useState("");
 const [pass, setPass] = useState("");
 const [error, setError] = useState("");
 const handleLogin = () => {
 if (name && pass === "1234") {
 setUser(name);
 navigate(location.state?.from || "/", { replace: true });
 } else setError("Enter a username and the password 1234");
 };
 return (
 <div>
 <h2>Login</h2>
 <input placeholder="Username" value={name} onChange={e => setName(e.target.value)} /><br /><br />
 <input type="password" placeholder="Password (1234)" value={pass}
 onChange={e => setPass(e.target.value)} /><br /><br />
 <button onClick={handleLogin}>Login</button>
 {error && <p style={{ color: "red" }}>{error}</p>}
 </div>
 );
}
function Cart() {
 const { cart, removeFromCart } = useApp();
 const total = cart.reduce((sum, p) => sum + p.price, 0);
 return (
 <div>
 <h2>Your Cart</h2>
 {cart.length === 0 ? <p>Cart is empty.</p> : <>
 {cart.map((p, i) => <p key={i}>
 {p.name} - Rs.{p.price} <button onClick={() => removeFromCart(i)}>Remove</button>
 </p>)}
 <h3>Total: Rs.{total}</h3>
 </>}
 </div>
 );
}
const NotFound = () => (
 <div><h2>404 - Page Not Found</h2><Link to="/">Go back home</Link></div>
);
export default function App() {
 return (
 <AppProvider>
 <BrowserRouter>
 <Navbar />
 <div style={{ padding: 20 }}>
 <Routes>
 <Route path="/" element={<Home />} />
 <Route path="/products" element={<Products />} />
 <Route path="/products/:id" element={<ProductDetails />} />
 <Route path="/contact" element={<Contact />} />
 <Route path="/login" element={<Login />} />
 <Route path="/cart" element={<ProtectedRoute><Cart /></ProtectedRoute>} />
 <Route path="*" element={<NotFound />} />
 </Routes>
 </div>
 </BrowserRouter> React Practical Programs 1–6 Page 7
 </AppProvider>
 );
} React Practical Programs 1–6 Page 8
Program 4 — Condensed Version
import React, { createContext, useContext, useState } from "react";
import { BrowserRouter, Routes, Route, Link, NavLink, Navigate, useParams, useNavigate, useLocation } from 
 "react-router-dom";
const PRODUCTS = [
 { id: 1, name: "Laptop", price: 55000, desc: "14-inch, 16GB RAM, 512GB SSD" },
 { id: 2, name: "Headphones", price: 2500, desc: "Wireless, noise cancelling" },
 { id: 3, name: "Smart Watch", price: 4000, desc: "Heart-rate and sleep tracking" },
 { id: 4, name: "Backpack", price: 1200, desc: "Water-resistant, 25L" },
];
const AppContext = createContext();
const useApp = () => useContext(AppContext);
function AppProvider({ children }) {
 const [user, setUser] = useState(null);
 const [cart, setCart] = useState([]);
 const addToCart = p => setCart(c => [...c, p]);
 const removeFromCart = i => setCart(c => c.filter((_, j) => i !== j));
 return <AppContext.Provider value={{ user, setUser, cart, addToCart, removeFromCart 
 }}>{children}</AppContext.Provider>;
}
function ProtectedRoute({ children }) {
 const { user } = useApp();
 const location = useLocation();
 return user ? children : <Navigate to="/login" state={{ from: location.pathname }} replace />;
}
function Navbar() {
 const { user, setUser, cart } = useApp();
 const navigate = useNavigate();
 return <nav>
 <NavLink to="/">Home</NavLink>{" "}
 <NavLink to="/products">Products</NavLink>{" "}
 <NavLink to="/contact">Contact</NavLink>{" "}
 <NavLink to="/cart">Cart ({cart.length})</NavLink>{" "}
 {user ? <>Hi, {user} <button onClick={() => { setUser(null); navigate("/"); }}>Logout</button></> : <NavLink
 to="/login">Login</NavLink>}
 </nav>;
}
const Home = () => <div><h2>Welcome to MyShop</h2><p>Browse our products and add your favourites to the 
 cart.</p><Link to="/products">Shop now</Link></div>;
const Products = () => <div><h2>Products</h2>
 {PRODUCTS.map(p => <div key={p.id}><h3>{p.name}</h3><p>Rs.{p.price}</p><Link to={`/products/${p.id}`}>View 
 details</Link></div>)}
</div>;
function ProductDetails() {
 const { id } = useParams();
 const { addToCart } = useApp();
 const p = PRODUCTS.find(p => p.id === Number(id));
 if (!p) return <h2>Product not found</h2>;
 return <div><h2>{p.name}</h2><p>{p.desc}</p><p>Rs.{p.price}</p><button onClick={() => addToCart(p)}>Add to 
 cart</button>{" "}<Link to="/products">Back</Link></div>;
}
function Contact() {
 const [sent, setSent] = useState(false);
 return <div><h2>Contact Us</h2>{sent ? <p>Thanks! We will get back to you.</p> : <>
 <input placeholder="Your email" /><textarea placeholder="Message" /><button onClick={() => 
 setSent(true)}>Send</button>
 </>}</div>;
}
function Login() {
 const { setUser } = useApp();
 const navigate = useNavigate();
 const location = useLocation();
 const [name, setName] = useState("");
 const [pass, setPass] = useState("");
 const [error, setError] = useState("");
 const handleLogin = () => {
 if (name && pass === "1234") { setUser(name); navigate(location.state?.from || "/", { replace: true }); }
 else setError("Enter a username and the password 1234");
 };
 return <div><h2>Login</h2>
 <input placeholder="Username" value={name} onChange={e => setName(e.target.value)} />
 <input type="password" placeholder="Password" value={pass} onChange={e => setPass(e.target.value)} />
 <button onClick={handleLogin}>Login</button>{error && <p>{error}</p>}
 </div>;
}
function Cart() {
 const { cart, removeFromCart } = useApp();
 const total = cart.reduce((sum, p) => sum + p.price, 0);
 return <div><h2>Your Cart</h2>{cart.length === 0 ? <p>Cart is empty.</p> : <> React Practical Programs 1–6 Page 9
 {cart.map((p, i) => <p key={i}>{p.name} - Rs.{p.price} <button onClick={() => 
 removeFromCart(i)}>Remove</button></p>)}
 <h3>Total: Rs.{total}</h3>
 </>}</div>;
}
const NotFound = () => <div><h2>404 - Page Not Found</h2><Link to="/">Go back home</Link></div>;
export default function App() {
 return <AppProvider><BrowserRouter><Navbar /><Routes>
 <Route path="/" element={<Home />} />
 <Route path="/products" element={<Products />} />
 <Route path="/products/:id" element={<ProductDetails />} />
 <Route path="/contact" element={<Contact />} />
 <Route path="/login" element={<Login />} />
 <Route path="/cart" element={<ProtectedRoute><Cart /></ProtectedRoute>} />
 <Route path="*" element={<NotFound />} />
 </Routes></BrowserRouter></AppProvider>;
}
Key points: useParams reads product ID; Context shares user/cart state; ProtectedRoute redirects logged-out
users to Login. React Practical Programs 1–6 Page 10
Program 5 — Dynamic Student Registration Form
Aim: Generate a form dynamically from a configuration object and handle controlled inputs.
Original / supplied version
import React, { useState } from "react";
const formConfig = {
 title: "Student Registration",
 fields: [
 { name: "fullName", label: "Full Name", type: "text", placeholder: "Enter name", required: true },
 { name: "email", label: "Email", type: "email", placeholder: "Enter email", required: true },
 { name: "age", label: "Age", type: "number", placeholder: "Enter age" },
 { name: "gender", label: "Gender", type: "radio", options: ["Male", "Female", "Other"] },
 { name: "course", label: "Course", type: "select", options: ["BCA", "B.Tech", "MCA", "MBA"] },
 { name: "skills", label: "Skills", type: "checkbox", options: ["React", "Java", "Python"] },
 { name: "about", label: "About You", type: "textarea", placeholder: "Short description" },
 ],
};
const buildInitialState = (config) => {
 const state = {};
 config.fields.forEach((f) => {
 state[f.name] = f.type === "checkbox" ? [] : "";
 });
 return state;
};
export default function App() {
 const [formData, setFormData] = useState(buildInitialState(formConfig));
 const [submitted, setSubmitted] = useState(null);
 const handleChange = (e) => {
 const { name, value, type, checked } = e.target;
 if (type === "checkbox") {
 setFormData((prev) => ({
 ...prev,
 [name]: checked ? [...prev[name], value] : prev[name].filter((v) => v !== value),
 }));
 } else {
 setFormData((prev) => ({ ...prev, [name]: value }));
 }
 };
 const handleSubmit = (e) => {
 e.preventDefault();
 setSubmitted(formData);
 };
 const renderField = (f) => {
 switch (f.type) {
 case "select":
 return (
 <select name={f.name} value={formData[f.name]} onChange={handleChange} required={f.required}>
 <option value="">-- Select --</option>
 {f.options.map((o) => <option key={o} value={o}>{o}</option>)}
 </select>
 );
 case "radio":
 return f.options.map((o) => (
 <label key={o} style={{ marginRight: 12 }}>
 <input type="radio" name={f.name} value={o}
 checked={formData[f.name] === o} onChange={handleChange} /> {o}
 </label>
 ));
 case "checkbox":
 return f.options.map((o) => (
 <label key={o} style={{ marginRight: 12 }}>
 <input type="checkbox" name={f.name} value={o}
 checked={formData[f.name].includes(o)} onChange={handleChange} /> {o}
 </label>
 ));
 case "textarea":
 return <textarea name={f.name} placeholder={f.placeholder}
 value={formData[f.name]} onChange={handleChange} />;
 default:
 return <input type={f.type} name={f.name} placeholder={f.placeholder}
 value={formData[f.name]} onChange={handleChange} required={f.required} />;
 }
 };
 return (
 <div style={{ maxWidth: 480, margin: "30px auto", fontFamily: "sans-serif" }}>
 <h2>{formConfig.title}</h2>
 <form onSubmit={handleSubmit}>
 {formConfig.fields.map((f) => (
 <div key={f.name} style={{ marginBottom: 14 }}>
 <label style={{ display: "block", fontWeight: "bold", marginBottom: 4 }}>
 {f.label}{f.required && " *"} React Practical Programs 1–6 Page 11
 </label>
 {renderField(f)}
 </div>
 ))}
 <button type="submit">Submit</button>
 </form>
 {submitted && (
 <div style={{ marginTop: 25, padding: 12, background: "#f3f3f3" }}>
 <h3>Submitted Data</h3>
 {formConfig.fields.map((f) => (
 <p key={f.name}>
 <b>{f.label}:</b>{" "}
 {Array.isArray(submitted[f.name]) ? submitted[f.name].join(", ") : submitted[f.name]}
 </p>
 ))}
 </div>
 )}
 </div>
 );
} React Practical Programs 1–6 Page 12
Program 5 — Condensed Version
import React, { useState } from "react";
const formConfig = {
 title: "Student Registration",
 fields: [
 { name: "fullName", label: "Full Name", type: "text", required: true },
 { name: "email", label: "Email", type: "email", required: true },
 { name: "age", label: "Age", type: "number" },
 { name: "gender", label: "Gender", type: "radio", options: ["Male", "Female", "Other"] },
 { name: "course", label: "Course", type: "select", options: ["BCA", "B.Tech", "MCA", "MBA"] },
 { name: "skills", label: "Skills", type: "checkbox", options: ["React", "Java", "Python"] },
 { name: "about", label: "About You", type: "textarea" },
 ],
};
export default function App() {
 const [data, setData] = useState(Object.fromEntries(
 formConfig.fields.map(f => [f.name, f.type === "checkbox" ? [] : ""])
 ));
 const [submitted, setSubmitted] = useState(null);
 const handleChange = e => {
 const { name, value, type, checked } = e.target;
 setData(p => ({
 ...p,
 [name]: type === "checkbox"
 ? checked ? [...p[name], value] : p[name].filter(v => v !== value)
 : value
 }));
 };
 const renderField = f => {
 const props = { name: f.name, onChange: handleChange };
 switch (f.type) {
 case "select":
 return <select {...props} value={data[f.name]}>
 <option value="">Select</option>
 {f.options.map(o => <option key={o}>{o}</option>)}
 </select>;
 case "radio":
 case "checkbox":
 return f.options.map(o => <label key={o}>
 <input {...props} type={f.type} value={o}
 checked={f.type === "radio" ? data[f.name] === o : data[f.name].includes(o)} />
 {o}
 </label>);
 case "textarea":
 return <textarea {...props} value={data[f.name]} />;
 default:
 return <input {...props} type={f.type} value={data[f.name]} required={f.required} />;
 }
 };
 return <div>
 <h2>{formConfig.title}</h2>
 <form onSubmit={e => { e.preventDefault(); setSubmitted(data); }}>
 {formConfig.fields.map(f => <div key={f.name}>
 <label>{f.label} {f.required && "*"}</label>
 {renderField(f)}
 </div>)}
 <button type="submit">Submit</button>
 </form>
 {submitted && <div>
 <h3>Submitted Data</h3>
 {formConfig.fields.map(f => <p key={f.name}>
 <b>{f.label}:</b> {Array.isArray(submitted[f.name])
 ? submitted[f.name].join(", ") : submitted[f.name]}
 </p>)}
 </div>}
 </div>;
}
Key points: formConfig defines fields; useState stores values; handleChange updates inputs; renderField
selects the input type; submit displays the data. React Practical Programs 1–6 Page 13
Program 6 — SPA with Home and Users Routes
Aim: Demonstrate React Router, useState, useEffect, delayed local data, loading and error states.
Original / supplied version
import React, { useState, useEffect } from "react";
import { BrowserRouter, Routes, Route, NavLink } from "react-router-dom";
const USERS_DATA = [
 { id: 1, name: "Leanne Graham", email: "leanne@example.com", city: "Hyderabad" },
 { id: 2, name: "Ervin Howell", email: "ervin@example.com", city: "Chennai" },
 { id: 3, name: "Clementine Bauch", email: "clem@example.com", city: "Mumbai" },
 { id: 4, name: "Patricia Lebsack", email: "patricia@example.com", city: "Delhi" },
 { id: 5, name: "Chelsey Dietrich", email: "chelsey@example.com", city: "Pune" },
];
const fetchUsers = (shouldFail = false) =>
 new Promise((resolve, reject) => {
 setTimeout(() => {
 shouldFail ? reject(new Error("Failed to load users")) : resolve(USERS_DATA);
 }, 2000);
 });
const Home = () => (
 <div>
 <h2>Home</h2>
 <p>Open the Users page to see data loaded with useEffect.</p>
 </div>
);
function Users() {
 const [users, setUsers] = useState([]);
 const [loading, setLoading] = useState(true);
 const [error, setError] = useState(null);
 useEffect(() => {
 fetchUsers()
 .then(data => setUsers(data))
 .catch(err => setError(err.message))
 .finally(() => setLoading(false));
 }, []);
 if (loading) return <p>Loading users...</p>;
 if (error) return <p>{error}</p>;
 return (
 <div>
 <h2>Users</h2>
 <table border="1">
 <thead>
 <tr><th>ID</th><th>Name</th><th>Email</th><th>City</th></tr>
 </thead>
 <tbody>
 {users.map(u => (
 <tr key={u.id}>
 <td>{u.id}</td><td>{u.name}</td><td>{u.email}</td><td>{u.city}</td>
 </tr>
 ))}
 </tbody>
 </table>
 </div>
 );
}
export default function App() {
 return (
 <BrowserRouter>
 <nav>
 <NavLink to="/">Home</NavLink>
 <NavLink to="/users">Users</NavLink>
 </nav>
 <Routes>
 <Route path="/" element={<Home />} />
 <Route path="/users" element={<Users />} />
 </Routes>
 </BrowserRouter>
 );
} React Practical Programs 1–6 Page 14
Program 6 — Condensed Version
import React, { useState, useEffect } from "react";
import { BrowserRouter, Routes, Route, NavLink } from "react-router-dom";
const USERS_DATA = [
 { id: 1, name: "Leanne Graham", email: "leanne@example.com", city: "Hyderabad" },
 { id: 2, name: "Ervin Howell", email: "ervin@example.com", city: "Chennai" },
 { id: 3, name: "Clementine Bauch", email: "clem@example.com", city: "Mumbai" },
 { id: 4, name: "Patricia Lebsack", email: "patricia@example.com", city: "Delhi" },
 { id: 5, name: "Chelsey Dietrich", email: "chelsey@example.com", city: "Pune" },
];
const fetchUsers = (shouldFail = false) => new Promise((resolve, reject) =>
 setTimeout(() => shouldFail ? reject(new Error("Failed to load users")) : resolve(USERS_DATA), 2000)
);
const Home = () => <div><h2>Home</h2><p>Open the Users page to see data loaded with useEffect.</p></div>;
function Users() {
 const [users, setUsers] = useState([]);
 const [loading, setLoading] = useState(true);
 const [error, setError] = useState(null);
 useEffect(() => {
 fetchUsers().then(data => setUsers(data))
 .catch(err => setError(err.message))
 .finally(() => setLoading(false));
 }, []);
 if (loading) return <p>Loading users...</p>;
 if (error) return <p>{error}</p>;
 return <div><h2>Users</h2><table border="1">
 <thead><tr><th>ID</th><th>Name</th><th>Email</th><th>City</th></tr></thead>
 <tbody>{users.map(u => <tr key={u.id}>
 <td>{u.id}</td><td>{u.name}</td><td>{u.email}</td><td>{u.city}</td>
 </tr>)}</tbody>
 </table></div>;
}
export default function App() {
 return <BrowserRouter><nav>
 <NavLink to="/">Home</NavLink>{" "}
 <NavLink to="/users">Users</N

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

export default App;

# Week-2-Day-4-Vendor-Dashboard
create a Vendor Dashboard that brings the vendor's store and product information into one place.


1. Create VendorDashboard.jsx

Create:

frontend/src/pages/VendorDashboard.jsx
import { useEffect, useState } from "react";
import { Link } from "react-router-dom";
import API from "../services/api";

function VendorDashboard() {
  const [store, setStore] = useState(null);
  const [products, setProducts] = useState([]);
  const [message, setMessage] = useState("");

  const fetchDashboardData = async () => {
    try {
      const storeResponse = await API.get(
        "/stores/my-store"
      );

      const productResponse = await API.get(
        "/products/my-products"
      );

      setStore(storeResponse.data.store);
      setProducts(productResponse.data.products);

    } catch (error) {
      setMessage(
        error.response?.data?.message ||
        "Unable to load dashboard"
      );
    }
  };

  useEffect(() => {
    fetchDashboardData();
  }, []);

  const totalStock = products.reduce(
    (total, product) => total + product.stock,
    0
  );

  return (
    <div className="dashboard">

      <h1>Vendor Dashboard</h1>

      <p className="dashboard-message">
        {message}
      </p>

      {store && (
        <div className="store-info">

          <h2>{store.name}</h2>

          <p>
            {store.description ||
              "No store description available."}
          </p>

          <Link to="/store-management">
            Manage Store
          </Link>

        </div>
      )}

      <div className="stats-container">

        <div className="stat-card">
          <h3>Total Products</h3>
          <p>{products.length}</p>
        </div>

        <div className="stat-card">
          <h3>Total Stock</h3>
          <p>{totalStock}</p>
        </div>

        <div className="stat-card">
          <h3>Store Status</h3>
          <p>
            {store?.isActive
              ? "Active"
              : "Inactive"}
          </p>
        </div>

      </div>

      <div className="dashboard-actions">

        <Link to="/products">
          Manage Products
        </Link>

        <Link to="/store-management">
          Manage Store
        </Link>

      </div>

      <h2>Recent Products</h2>

      <div className="dashboard-products">

        {products.length === 0 ? (
          <p>
            No products found. Add your first product.
          </p>
        ) : (

          products.slice(0, 5).map((product) => (

            <div
              className="dashboard-product"
              key={product._id}
            >

              {product.image && (
                <img
                  src={product.image}
                  alt={product.name}
                />
              )}

              <div>
                <h3>{product.name}</h3>

                <p>
                  Price: ₹{product.price}
                </p>

                <p>
                  Stock: {product.stock}
                </p>

                <p>
                  Category: {product.category}
                </p>
              </div>

            </div>

          ))

        )}

      </div>

    </div>
  );
}

export default VendorDashboard;
2. Update App.jsx

You currently have a temporary VendorDashboard function inside App.jsx.

Remove that function.

At the top, add:

import VendorDashboard from "./pages/VendorDashboard";

Then your vendor route should become:

<Route
  path="/vendor-dashboard"
  element={
    <ProtectedRoute allowedRoles={["vendor"]}>
      <VendorDashboard />
    </ProtectedRoute>
  }
/>

Now the real dashboard will open after vendor login.

3. Add Dashboard CSS

Open:

frontend/src/index.css

Add:

.dashboard {
  max-width: 1100px;
  margin: 40px auto;
  padding: 20px;
}

.dashboard h1 {
  margin-bottom: 25px;
}

.dashboard-message {
  margin-bottom: 15px;
}

.store-info {
  background: white;
  padding: 25px;
  border-radius: 10px;
  margin-bottom: 25px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.store-info h2 {
  margin-bottom: 10px;
}

.store-info a {
  display: inline-block;
  margin-top: 15px;
}

.stats-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  margin-bottom: 25px;
}

.stat-card {
  background: white;
  padding: 25px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.stat-card h3 {
  margin-bottom: 10px;
}

.stat-card p {
  font-size: 28px;
  font-weight: bold;
}

.dashboard-actions {
  display: flex;
  gap: 15px;
  margin-bottom: 30px;
}

.dashboard-actions a {
  padding: 12px 20px;
  background: #222;
  color: white;
  text-decoration: none;
  border-radius: 6px;
}

.dashboard-products {
  display: grid;
  gap: 15px;
  margin-top: 20px;
}

.dashboard-product {
  display: flex;
  gap: 20px;
  align-items: center;
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.dashboard-product img {
  width: 100px;
  height: 100px;
  object-fit: cover;
  border-radius: 8px;
}
4. Test the Dashboard

Make sure both servers are running.

Backend
cd backend
node server.js
Frontend

Open another terminal:

cd frontend
npm run dev

Then open:

http://localhost:5173

Login as a vendor.

You should be redirected to:

/vendor-dashboard

You should see something similar to:

Vendor Dashboard

┌──────────────────────────────┐
│ My Fashion Store             │
│ Quality clothing products    │
│ Manage Store                 │
└──────────────────────────────┘

┌────────────┐ ┌────────────┐ ┌────────────┐
│ Products   │ │ Stock      │ │ Status     │
│     5      │ │    120     │ │   Active   │
└────────────┘ └────────────┘ └────────────┘

[Manage Products] [Manage Store]

Recent Products

┌─────────────────────────────────┐
│ Image   Blue T-Shirt            │
│         ₹699                    │
│         Stock: 30               │
└─────────────────────────────────┘

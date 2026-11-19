<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>RentEase - Find Rooms, PGs & Flats</title>
  <style>
    * { box-sizing: border-box; font-family: Arial, sans-serif; margin: 0; padding: 0; }
    body { background: #f4f6f9; color: #222; padding-bottom: 40px; }
    header { background: #0056b3; color: white; padding: 18px 16px; text-align: center; }
    header h1 { font-size: 22px; margin-bottom: 4px; }
    header p { font-size: 13px; opacity: 0.9; }

    .container { max-width: 900px; margin: 16px auto; padding: 0 14px; }

    /* Search & Filter Bar */
    .filters {
      background: white; padding: 14px; border-radius: 10px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.08);
      display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 18px;
    }
    .filters input, .filters select, .filters button {
      padding: 10px; border: 1px solid #ccc; border-radius: 6px; font-size: 14px;
    }
    .filters button {
      background: #0056b3; color: white; border: none; font-weight: bold; cursor: pointer;
    }

    /* Post Property Form */
    .post-box {
      background: white; padding: 16px; border-radius: 10px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.08); margin-bottom: 20px;
    }
    .post-box h2 { font-size: 17px; margin-bottom: 10px; color: #0056b3; }
    .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
    .form-grid input, .form-grid select {
      padding: 10px; border: 1px solid #ccc; border-radius: 6px; font-size: 14px; width: 100%;
    }
    .full { grid-column: span 2; }
    .submit-btn {
      margin-top: 10px; width: 100%; padding: 11px; background: #28a745;
      color: white; border: none; border-radius: 6px; font-size: 15px; font-weight: bold; cursor: pointer;
    }

    /* Property Cards */
    .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 16px; }
    .card {
      background: white; border-radius: 10px; overflow: hidden;
      box-shadow: 0 2px 8px rgba(0,0,0,0.08); display: flex; flex-direction: column;
    }
    .card img { width: 100%; height: 170px; object-fit: cover; background: #ddd; }
    .card-body { padding: 14px; flex: 1; display: flex; flex-direction: column; justify-content: space-between; }
    .badge {
      display: inline-block; background: #e7f1ff; color: #0056b3;
      padding: 3px 8px; border-radius: 4px; font-size: 12px; font-weight: bold; margin-bottom: 6px;
    }
    .price { font-size: 19px; font-weight: bold; color: #111; margin: 4px 0; }
    .loc { font-size: 13px; color: #666; margin-bottom: 10px; }
    .contact-btn {
      display: block; text-align: center; background: #25D366; color: white;
      text-decoration: none; padding: 10px; border-radius: 6px; font-weight: bold; font-size: 14px;
    }

    @media (max-width: 550px) {
      .filters, .form-grid { grid-template-columns: 1fr; }
      .full { grid-column: span 1; }
    }
  </style>
</head>
<body>

  <header>
    <h1>RentEase</h1>
    <p>Find Flats, PGs & Rooms Directly from Owners</p>
  </header>

  <div class="container">

    <!-- Search & Filter -->
    <div class="filters">
      <input type="text" id="searchLocality" placeholder="Search area / locality..." oninput="renderProperties()" />
      <select id="filterType" onchange="renderProperties()">
        <option value="All">All Property Types</option>
        <option value="1 BHK">1 BHK</option>
        <option value="2 BHK">2 BHK</option>
        <option value="PG / Hostel">PG / Hostel</option>
        <option value="Single Room">Single Room</option>
      </select>
      <input type="number" id="maxBudget" placeholder="Max Rent (₹)" oninput="renderProperties()" />
      <button onclick="resetFilters()">Reset Filters</button>
    </div>

    <!-- Owner Post Property Form -->
    <div class="post-box">
      <h2>+ Post a Rental Property</h2>
      <form id="propertyForm" onsubmit="addProperty(event)">
        <div class="form-grid">
          <input type="text" id="title" placeholder="Title (e.g. Spacious 1 BHK Near Main Road)" required />
          <select id="type" required>
            <option value="1 BHK">1 BHK</option>
            <option value="2 BHK">2 BHK</option>
            <option value="PG / Hostel">PG / Hostel</option>
            <option value="Single Room">Single Room</option>
          </select>
          <input type="text" id="locality" placeholder="Locality / Area" required />
          <input type="number" id="rent" placeholder="Monthly Rent (₹)" required />
          <input type="tel" id="phone" placeholder="Owner WhatsApp Number (e.g. 919876543210)" required />
          <input type="url" id="image" placeholder="Photo URL (optional)" />
        </div>
        <button type="submit" class="submit-btn">Publish Listing</button>
      </form>
    </div>

    <!-- Property Listings -->
    <div class="grid" id="propertyList"></div>

  </div>

  <script>
    // Default sample listings
    const defaultProperties = [
      {
        title: "Furnished 1 BHK Flat with Balcony",
        type: "1 BHK",
        locality: "Central Hope Town",
        rent: 8500,
        phone: "919876543210",
        image: "https://images.unsplash.com/photo-1522708323590-d24dbb6b0267?auto=format&fit=crop&w=600&q=80"
      },
      {
        title: "Boys & Girls PG with Wi-Fi & Meals",
        type: "PG / Hostel",
        locality: "Rajpur Road",
        rent: 6500,
        phone: "919876543210",
        image: "https://images.unsplash.com/photo-1555854877-bab0e564b8d5?auto=format&fit=crop&w=600&q=80"
      },
      {
        title: "2 BHK Family Apartment - Ground Floor",
        type: "2 BHK",
        locality: "Prem Nagar",
        rent: 13000,
        phone: "919876543210",
        image: "https://images.unsplash.com/photo-1502672260266-1c1ef2d93688?auto=format&fit=crop&w=600&q=80"
      }
    ];

    // Load saved properties from browser storage or use defaults
    let properties = JSON.parse(localStorage.getItem("rental_properties")) || defaultProperties;

    function renderProperties() {
      const list = document.getElementById("propertyList");
      const search = document.getElementById("searchLocality").value.toLowerCase();
      const type = document.getElementById("filterType").value;
      const maxBudget = Number(document.getElementById("maxBudget").value);

      list.innerHTML = "";

      const filtered = properties.filter(p => {
        const matchesLoc = p.locality.toLowerCase().includes(search) || p.title.toLowerCase().includes(search);
        const matchesType = type === "All" || p.type === type;
        const matchesBudget = !maxBudget || p.rent <= maxBudget;
        return matchesLoc && matchesType && matchesBudget;
      });

      if (filtered.length === 0) {
        list.innerHTML = "<p>No matching properties found. Try changing filters or post one above!</p>";
        return;
      }

      filtered.forEach(p => {
        const imgUrl = p.image || "https://images.unsplash.com/photo-1560448204-e02f11c3d0e2?auto=format&fit=crop&w=600&q=80";
        const msg = encodeURIComponent(`Hi, I saw your property "${p.title}" in ${p.locality} for ₹${p.rent}/month on RentEase. Is it available?`);
        list.innerHTML += `
          <div class="card">
            <img src="${imgUrl}" alt="${p.title}" />
            <div class="card-body">
              <div>
                <span class="badge">${p.type}</span>
                <h3>${p.title}</h3>
                <div class="price">₹${p.rent.toLocaleString("en-IN")} / month</div>
                <div class="loc">📍 ${p.locality}</div>
              </div>
              <a class="contact-btn" href="https://wa.me/${p.phone}?text=${msg}" target="_blank">
                Chat on WhatsApp
              </a>
            </div>
          </div>
        `;
      });
    }

    function addProperty(e) {
      e.preventDefault();
      const newProp = {
        title: document.getElementById("title").value,
        type: document.getElementById("type").value,
        locality: document.getElementById("locality").value,
        rent: Number(document.getElementById("rent").value),
        phone: document.getElementById("phone").value.replace(/\D/g, ""),
        image: document.getElementById("image").value
      };

      properties.unshift(newProp);
      localStorage.setItem("rental_properties", JSON.stringify(properties));
      document.getElementById("propertyForm").reset();
      renderProperties();
    }

    function resetFilters() {
      document.getElementById("searchLocality").value = "";
      document.getElementById("filterType").value = "All";
      document.getElementById("maxBudget").value = "";
      renderProperties();
    }

    renderProperties();
  </script>
</body>
</html>

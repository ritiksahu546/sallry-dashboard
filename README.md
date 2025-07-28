<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>KRA Salary Dashboard</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f2f2f2;
      padding: 20px;
    }
    h2 {
      color: #333;
    }
    input, select, button {
      padding: 10px;
      margin: 5px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
      background: #fff;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 10px;
      text-align: center;
    }
    .total {
      margin-top: 20px;
      font-weight: bold;
      font-size: 18px;
    }
  </style>
</head>
<body>

  <h2>👥 KRA-Based Salary Dashboard (With Permanent Storage)</h2>

  <label for="employeeName">Select Name:</label>
  <select id="employeeName" onchange="renderTable()">
    <option value="Ritik">Ritik</option>
    <option value="Vipul">Vipul</option>
    <option value="Abhishek">Abhishek</option>
  </select>

  <br/>

  <input type="date" id="kraDate">
  <input type="number" id="kraPercent" placeholder="Enter KRA%">
  <button onclick="addKRA()">Add Entry</button>

  <table>
    <thead>
      <tr>
        <th>Date</th>
        <th>KRA%</th>
        <th>Salary (₹)</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody id="kraTableBody"></tbody>
  </table>

  <div class="total" id="totalSalary">Total Salary: ₹0</div>

  <script>
    let data = JSON.parse(localStorage.getItem("kraData")) || [];

    function calculateSalary(kra) {
      if (kra >= 150) return 1100;
      if (kra >= 135) return 1000;
      if (kra >= 120) return 900;
      if (kra >= 100) return 750;
      if (kra >= 80) return 500;
      if (kra >= 60) return 300;
      return 0;
    }

    function addKRA() {
      const name = document.getElementById("employeeName").value;
      const date = document.getElementById("kraDate").value;
      const kra = parseFloat(document.getElementById("kraPercent").value);

      if (!date || isNaN(kra)) {
        alert("Please enter valid date and KRA%");
        return;
      }

      const salary = calculateSalary(kra);
      data.push({ name, date, kra, salary });

      localStorage.setItem("kraData", JSON.stringify(data));

      document.getElementById("kraDate").value = "";
      document.getElementById("kraPercent").value = "";

      renderTable();
    }

    function renderTable() {
      const tbody = document.getElementById("kraTableBody");
      tbody.innerHTML = "";
      const selectedName = document.getElementById("employeeName").value;

      let total = 0;

      // ✅ Filter + Sort by Date (latest first)
      const sortedData = data
        .filter(item => item.name === selectedName)
        .sort((a, b) => new Date(b.date) - new Date(a.date)); // change to a.date - b.date for oldest first

      sortedData.forEach(item => {
        total += item.salary;
        const index = data.indexOf(item);
        const row = document.createElement("tr");
        row.innerHTML = `
          <td>${item.date}</td>
          <td>${item.kra}%</td>
          <td>₹${item.salary}</td>
          <td>
            <button onclick="editRow(${index})">Edit</button>
            <button onclick="deleteRow(${index})">Delete</button>
          </td>
        `;
        tbody.appendChild(row);
      });

      document.getElementById("totalSalary").innerText = `Total Salary for ${selectedName}: ₹${total}`;
    }

    function deleteRow(index) {
      data.splice(index, 1);
      localStorage.setItem("kraData", JSON.stringify(data));
      renderTable();
    }

    function editRow(index) {
      const item = data[index];
      document.getElementById("kraDate").value = item.date;
      document.getElementById("kraPercent").value = item.kra;
      document.getElementById("employeeName").value = item.name;
      deleteRow(index);
    }

    window.onload = renderTable;
  </script>

</body>
</html>

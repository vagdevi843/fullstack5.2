# fullstack5.2
// studentApp.js
const express = require("express");
const mongoose = require("mongoose");
const bodyParser = require("body-parser");

// ============================================
// 1️⃣ CONNECT TO MONGODB
// ============================================
mongoose
  .connect("mongodb://127.0.0.1:27017/studentMVCDB")
  .then(() => console.log("✅ Connected to MongoDB"))
  .catch((err) => console.error("❌ MongoDB Connection Error:", err));

// ============================================
// 2️⃣ DEFINE MODEL (Student Schema + Model)
// ============================================
const studentSchema = new mongoose.Schema({
  name: { type: String, required: true },
  department: { type: String, required: true },
  marks: { type: Number, required: true },
});

const Student = mongoose.model("Student", studentSchema);

// ============================================
// 3️⃣ INITIALIZE EXPRESS APP
// ============================================
const app = express();
app.use(bodyParser.json());

// ============================================
// 4️⃣ CONTROLLERS (Business Logic)
// ============================================

// ➕ Create Student
app.post("/students", async (req, res) => {
  try {
    const student = new Student(req.body);
    await student.save();
    res.status(201).json({ message: "✅ Student added successfully!", student });
  } catch (error) {
    res.status(500).json({ message: "❌ Error adding student", error });
  }
});

// 📋 Get All Students
app.get("/students", async (req, res) => {
  try {
    const students = await Student.find();
    res.json(students);
  } catch (error) {
    res.status(500).json({ message: "❌ Error fetching students", error });
  }
});

// ✏️ Update Student by ID
app.put("/students/:id", async (req, res) => {
  try {
    const student = await Student.findByIdAndUpdate(req.params.id, req.body, {
      new: true,
    });
    if (!student)
      return res.status(404).json({ message: "❌ Student not found" });
    res.json({ message: "✅ Student updated successfully!", student });
  } catch (error) {
    res.status(500).json({ message: "❌ Error updating student", error });
  }
});

// ❌ Delete Student by ID
app.delete("/students/:id", async (req, res) => {
  try {
    const student = await Student.findByIdAndDelete(req.params.id);
    if (!student)
      return res.status(404).json({ message: "❌ Student not found" });
    res.json({ message: "✅ Student deleted successfully!" });
  } catch (error) {
    res.status(500).json({ message: "❌ Error deleting student", error });
  }
});

// ============================================
// 5️⃣ SIMPLE FRONTEND VIEW (HTML RESPONSE)
// ============================================
app.get("/", (req, res) => {
  res.send(`
    <html>
      <head>
        <title>Student Management System</title>
      </head>
      <body style="font-family: Arial; margin: 40px;">
        <h2>🎓 Student Management System (MVC with MongoDB)</h2>
        <p>Use Postman or any REST client to test the API:</p>
        <ul>
          <li>POST /students → Add a new student</li>
          <li>GET /students → View all students</li>
          <li>PUT /students/:id → Update a student</li>
          <li>DELETE /students/:id → Delete a student</li>
        </ul>
      </body>
    </html>
  `);
});

// ============================================
// 6️⃣ START SERVER
// ============================================
const PORT = 5000;
app.listen(PORT, () => {
  console.log(`🚀 Server running on http://localhost:${PORT}`);
});

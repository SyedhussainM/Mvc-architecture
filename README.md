let students = []; 

class Student {
    constructor(id, name, age, department) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.department = department;
    }

    static getAll() {
        return students;
    }

    static getById(id) {
        return students.find(student => student.id === id);
    }

    static add(student) {
        students.push(student);
    }

    static update(id, updatedData) {
        const student = this.getById(id);
        if (student) {
            Object.assign(student, updatedData);
        }
        return student;
    }

    static delete(id) {
        students = students.filter(student => student.id !== id);
    }
}

module.exports = Student;
// app/controllers/studentController.js

const Student = require('../models/studentModel');

// Get all students
exports.getAllStudents = (req, res) => {
    res.json(Student.getAll());
};

// Get a student by ID
exports.getStudentById = (req, res) => {
    const student = Student.getById(parseInt(req.params.id));
    if (student) {
        res.json(student);
    } else {
        res.status(404).json({ error: "Student not found" });
    }
};

// Add a new student
exports.addStudent = (req, res) => {
    const { id, name, age, department } = req.body;
    const newStudent = new Student(id, name, age, department);
    Student.add(newStudent);
    res.status(201).json(newStudent);
};

// Update a student
exports.updateStudent = (req, res) => {
    const updatedData = req.body;
    const student = Student.update(parseInt(req.params.id), updatedData);
    if (student) {
        res.json(student);
    } else {
        res.status(404).json({ error: "Student not found" });
    }
};

// Delete a student
exports.deleteStudent = (req, res) => {
    const id = parseInt(req.params.id);
    const student = Student.getById(id);
    if (student) {
        Student.delete(id);
        res.json({ message: "Student deleted successfully" });
    } else {
        res.status(404).json({ error: "Student not found" });
    }
};


const express = require('express');
const studentController = require('../controllers/studentController');

const router = express.Router();

router.get('/', studentController.getAllStudents);
router.get('/:id', studentController.getStudentById);
router.post('/', studentController.addStudent);
router.put('/:id', studentController.updateStudent);
router.delete('/:id', studentController.deleteStudent);

module.exports = router;


const express = require('express');
const bodyParser = require('body-parser');
const studentRoutes = require('./app/routes/studentRoutes');

const app = express();
const PORT = 3000;

// Middleware1
app.use(bodyParser.json());

// Routes
app.use('/students', studentRoutes);

// Start the server
app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
});

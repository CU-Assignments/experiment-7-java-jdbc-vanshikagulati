[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/D9ntAWdC)

# Create a Java program to connect to a MySQL database and fetch data from a single table. The program should:
 ## Use DriverManager and Connection objects.
 ## Retrieve and display all records from a table named Employee with columns EmpID, Name, and Salary.

import java.sql.*;
public class EmployeeData {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/company";
        String user = "root";
        String password = "password"; // Replace with your password

        try {
            // Step 1: Load JDBC driver
            Class.forName("com.mysql.cj.jdbc.Driver");

            // Step 2: Establish connection
            Connection con = DriverManager.getConnection(url, user, password);
            System.out.println("Connected to the database!");

            // Step 3: Execute query
            Statement stmt = con.createStatement();
            ResultSet rs = stmt.executeQuery("SELECT * FROM Employee");

            // Step 4: Display result
            while (rs.next()) {
                System.out.println("EmpID: " + rs.getInt("EmpID") + ", Name: " + rs.getString("Name") +
                        ", Salary: " + rs.getDouble("Salary"));
            }

            // Step 5: Close connection
            con.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

# Build a Java program to perform CRUD operations (Create, Read, Update, Delete) on a database table Product with columns:

## ProductID, ProductName, Price, and Quantity.
## The program should include:
## Menu-driven options for each operation.
## Transaction handling to ensure data integrity.

import java.sql.*;
import java.util.Scanner;

public class ProductManager {

    static Connection connection;

    public static void connectDatabase() {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/store", "root", "password");
            System.out.println("Database connected.");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void createProduct() {
        try {
            Scanner scanner = new Scanner(System.in);
            System.out.print("Enter Product ID: ");
            int id = scanner.nextInt();
            scanner.nextLine();
            System.out.print("Enter Product Name: ");
            String name = scanner.nextLine();
            System.out.print("Enter Price: ");
            double price = scanner.nextDouble();
            System.out.print("Enter Quantity: ");
            int quantity = scanner.nextInt();

            String query = "INSERT INTO Product (ProductID, ProductName, Price, Quantity) VALUES (?, ?, ?, ?)";
            PreparedStatement ps = connection.prepareStatement(query);
            ps.setInt(1, id);
            ps.setString(2, name);
            ps.setDouble(3, price);
            ps.setInt(4, quantity);
            ps.executeUpdate();
            System.out.println("Product added successfully.");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void readProducts() {
        try {
            Statement stmt = connection.createStatement();
            ResultSet rs = stmt.executeQuery("SELECT * FROM Product");

            while (rs.next()) {
                System.out.println("ProductID: " + rs.getInt("ProductID") + ", ProductName: " + rs.getString("ProductName") +
                        ", Price: " + rs.getDouble("Price") + ", Quantity: " + rs.getInt("Quantity"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void updateProduct() {
        try {
            Scanner scanner = new Scanner(System.in);
            System.out.print("Enter Product ID to update: ");
            int id = scanner.nextInt();
            System.out.print("Enter new Price: ");
            double price = scanner.nextDouble();

            String query = "UPDATE Product SET Price = ? WHERE ProductID = ?";
            PreparedStatement ps = connection.prepareStatement(query);
            ps.setDouble(1, price);
            ps.setInt(2, id);
            ps.executeUpdate();
            System.out.println("Product updated successfully.");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void deleteProduct() {
        try {
            Scanner scanner = new Scanner(System.in);
            System.out.print("Enter Product ID to delete: ");
            int id = scanner.nextInt();

            String query = "DELETE FROM Product WHERE ProductID = ?";
            PreparedStatement ps = connection.prepareStatement(query);
            ps.setInt(1, id);
            ps.executeUpdate();
            System.out.println("Product deleted successfully.");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        connectDatabase();
        Scanner scanner = new Scanner(System.in);
        int choice;

        while (true) {
            System.out.println("1. Add Product\n2. View Products\n3. Update Product\n4. Delete Product\n5. Exit");
            System.out.print("Enter choice: ");
            choice = scanner.nextInt();

            switch (choice) {
                case 1 -> createProduct();
                case 2 -> readProducts();
                case 3 -> updateProduct();
                case 4 -> deleteProduct();
                case 5 -> {
                    System.out.println("Exiting...");
                    System.exit(0);
                }
                default -> System.out.println("Invalid choice. Please try again.");
            }
        }
    }
}

# Develop a Java application using JDBC and MVC architecture to manage student data. The application should:

## Use a Student class as the model with fields like StudentID, Name, Department, and Marks.
## Include a database table to store student data.
## Allow the user to perform CRUD operations through a simple menu-driven view.
## Implement database operations in a separate controller class.

import java.sql.*;
import java.util.*;

public class StudentController {
    private Connection connection;

    public StudentController() {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/school", "root", "password");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Create Student
    public void addStudent(Student student) {
        try {
            PreparedStatement ps = connection.prepareStatement("INSERT INTO students (StudentID, Name, Department, Marks) VALUES (?, ?, ?, ?)");
            ps.setInt(1, student.getStudentID());
            ps.setString(2, student.getName());
            ps.setString(3, student.getDepartment());
            ps.setInt(4, student.getMarks());
            ps.executeUpdate();
            System.out.println("Student added successfully.");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Read Students
    public void displayStudents() {
        try {
            Statement stmt = connection.createStatement();
            ResultSet rs = stmt.executeQuery("SELECT * FROM students");
            while (rs.next()) {
                System.out.println(rs.getInt("StudentID") + " | " + rs.getString("Name") + " | " +
                        rs.getString("Department") + " | " + rs.getInt("Marks"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Update Student
    public void updateStudentMarks(int studentID, int marks) {
        try {
            PreparedStatement ps = connection.prepareStatement("UPDATE students SET Marks=? WHERE StudentID=?");
            ps.setInt(1, marks);
            ps.setInt(2, studentID);
            ps.executeUpdate();
            System.out.println("Student marks updated.");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Delete Student
    public void deleteStudent(int studentID) {
        try {
            PreparedStatement ps = connection.prepareStatement("DELETE FROM students WHERE StudentID=?");
            ps.setInt(1, studentID);
            ps.executeUpdate();
            System.out.println("Student deleted.");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

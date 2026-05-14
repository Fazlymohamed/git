import java.io.*;
import java.util.*;
import java.awt.Desktop;

public class java {

    static Scanner sc = new Scanner(System.in);
    static final String USERS_FILE = "users.txt";
    static final String BOOKS_FILE = "books.txt";

    public static void main(String[] args) {
        System.out.println("😊😊Welcome to the Elab");
        String[] eye = {
                "   *   ",
                "  ***  ",
                "  ***  ",
                "  ***  ",
                "  ***  "
        };

        String[] dash = {
                "       ",
                "       ",
                "*****  ",
                "       ",
                "       "
        };

        String[] l = {
                "**   ",
                "**   ",
                "**   ",
                "**   ",
                "*****"
        };

        String[] a = {
                " * ",
                "***",
                "***",
                "***",
                "***"
        };

        String[] b = {
                "**** ",
                "*   *",
                "**** ",
                "*   *",
                "**** "
        };

        String[] s = {
                " ****",
                "*    ",
                " *** ",
                "    *",
                "**** "
        };


        // Combine all into a word
        for (int i = 0; i < 5; i++) {
            System.out.println(eye[i] + "  " + dash[i] + "  " + l[i] + "  " + a[i] + "  " + b[i] + "  " + s[i]);
        }


        System.out.println();


        System.out.println("1. Register");
        System.out.println("2. Login");
        System.out.print("Choose option: ");
        int choice = sc.nextInt();
        sc.nextLine();

        if (choice == 1) registerUser();

        if (loginUser()) {
            dashboard1();
        } else {
            System.out.println("Login failed. Exiting...");
        }
    }

    // Register new user
    static void registerUser() {
        System.out.print("Enter new username: ");
        String username = sc.nextLine();
        System.out.print("Enter new password: ");
        String password = sc.nextLine();

        try (FileWriter fw = new FileWriter(USERS_FILE, true)) {
            fw.write(username + "," + password + "\n");
            System.out.println("User registered successfully!");
        } catch (IOException e) {
            System.out.println("Error writing to user file.");
        }
    }

    // Login user
    static boolean loginUser() {
        System.out.print("Enter username: ");
        String username = sc.nextLine();
        System.out.print("Enter password: ");
        String password = sc.nextLine();

        try (BufferedReader br = new BufferedReader(new FileReader(USERS_FILE))) {
            String line;
            while ((line = br.readLine()) != null) {
                String[] parts = line.split(",");
                if (parts.length == 2 && parts[0].equals(username) && parts[1].equals(password)) {
                    System.out.println("Login successful!");
                    return true;
                }
            }
        } catch (IOException e) {
            System.out.println("Error reading user file.");
        }
        return false;
    }

    // Dashboard 1 - Main Menu
    static void dashboard1() {
        int choice;
        do {
            System.out.println("\n--- Dashboard 1 ---");
            System.out.println("1.➕Add Book (PDF)");
            System.out.println("2.📚View All Books");
            System.out.println("3.📔Delete Account");
            System.out.println("4.😔Logout");
            System.out.print("Choose option: ");
            choice = sc.nextInt();
            sc.nextLine();

            switch (choice) {
                case 1 -> addBook();
                case 2 -> dashboard2();
                case 3 -> deleteUserAccount();
                case 4 -> System.out.println("Logged out.");
                default -> System.out.println("Invalid choice.");
            }
        } while (choice != 4);
    }

    // Dashboard 2 - View and Manage Books
    static void dashboard2() {
        List<String> books = new ArrayList<>();
        List<String> bookTitles = new ArrayList<>();  // To store titles for easy deletion

        // Read books from file
        try (BufferedReader br = new BufferedReader(new FileReader(BOOKS_FILE))) {
            String line;
            while ((line = br.readLine()) != null) {
                books.add(line);
                String[] parts = line.split(",");
                if (parts.length == 2) {
                    bookTitles.add(parts[0]);
                }
            }
        } catch (IOException e) {
            System.out.println("Error reading book file.");
            return;
        }

        if (books.isEmpty()) {
            System.out.println("No books available.");
            return;
        }

        int option;
        do {
            System.out.println("\n--- Dashboard 2: View and Manage Books ---");
            for (int i = 0; i < books.size(); i++) {
                String[] parts = books.get(i).split(",");
                System.out.println((i + 1) + ". " + parts[0] + " | Link: " + parts[0]);
            }
            System.out.println((books.size() + 1) + ". Delete Book");
            System.out.println((books.size() + 2) + ". Back");
            System.out.print("Choose option: ");
            option = sc.nextInt();
            sc.nextLine();

            if (option >= 1 && option <= books.size()) {
                // Open PDF if selected
                String[] parts = books.get(option - 1).split(",");
                String filePath = parts[1];
                File pdfFile = new File(filePath);
                if (pdfFile.exists()) {
                    try {
                        Desktop.getDesktop().open(pdfFile);
                        System.out.println("Opening PDF: " + filePath);
                    } catch (IOException e) {
                        System.out.println("Unable to open PDF file.");
                    }
                }
            } else if (option == books.size() + 1) {
                // Delete Book Option
                deleteBook(books, bookTitles);
            } else if (option == books.size() + 2) {
                // Go back to Dashboard 1
                System.out.println("Returning to Dashboard 1...");
            } else {
                System.out.println("Invalid choice.");
            }
        } while (option != books.size() + 2);
    }

    // Method to delete a book
    static void deleteBook(List<String> books, List<String> bookTitles) {
        System.out.println("\n--- Delete Book ---");
        for (int i = 0; i < bookTitles.size(); i++) {
            System.out.println((i + 1) + ". " + bookTitles.get(i));
        }
        System.out.print("Enter the number of the book to delete: ");
        int bookNumber = sc.nextInt();
        sc.nextLine();

        if (bookNumber >= 1 && bookNumber <= bookTitles.size()) {
            String bookToDelete = bookTitles.get(bookNumber - 1);
            System.out.println("Are you sure you want to delete the book: " + bookToDelete + "? (y/n)");
            String confirmation = sc.nextLine();
            if (confirmation.equalsIgnoreCase("y")) {
                books.remove(bookNumber - 1);
                bookTitles.remove(bookNumber - 1);
                System.out.println("Book deleted successfully!");

                // Rewriting the updated list of books to the file
                try (FileWriter fw = new FileWriter(BOOKS_FILE)) {
                    for (String book : books) {
                        fw.write(book + "\n");
                    }
                } catch (IOException e) {
                    System.out.println("Error updating book file.");
                }
            } else {
                System.out.println("Book deletion canceled.");
            }
        } else {
            System.out.println("Invalid choice.");
        }
    }

    // Add book (with PDF file path
    static void addBook() {
        System.out.print("Enter book title: ");
        String title = sc.nextLine();
        System.out.print("Enter full PDF : ");
        String filePath = sc.nextLine();

        try (FileWriter fw = new FileWriter(BOOKS_FILE, true)) {
            fw.write(title + "," + filePath + "\n");
            System.out.println("Book added successfully!");
        } catch (IOException e) {
            System.out.println("Error writing to book file.");
        }
    }

    // Delete User Account
    static void deleteUserAccount() {
        System.out.print("Are you sure you want to delete your account? This action cannot be undone. (y/n): ");
        String confirmation = sc.nextLine();
        if (confirmation.equalsIgnoreCase("y")) {
            String username = getLoggedInUsername();
            List<String> users = new ArrayList<>();
            boolean accountDeleted = false;

            // Read all users from the file
            try (BufferedReader br = new BufferedReader(new FileReader(USERS_FILE))) {
                String line;
                while ((line = br.readLine()) != null) {
                    String[] parts = line.split(",");
                    if (!parts[0].equals(username)) {
                        users.add(line); // Keep all users except the current one
                    } else {
                        accountDeleted = true;
                    }
                }

                // If the account was found, rewrite the file with updated users list
                if (accountDeleted) {
                    try (FileWriter fw = new FileWriter(USERS_FILE)) {
                        for (String user : users) {
                            fw.write(user + "\n");
                        }
                    }
                    System.out.println("Account deleted successfully!");
                } else {
                    System.out.println("Account not found.");
                }
            } catch (IOException e) {
                System.out.println("Error reading or writing user file.");
            }
        } else {
            System.out.println("Account deletion canceled.");
        }
    }

    // Helper method to get the logged-in username
    static String getLoggedInUsername() {
        System.out.print("Enter your username (for deletion): ");
        return sc.nextLine();
    }
}

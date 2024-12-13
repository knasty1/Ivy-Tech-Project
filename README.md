# Ivy-Tech-Project

Final draft

import tkinter as tk
from tkinter import filedialog, messagebox

class JewelryApp(tk.Tk):
    def __init__(self):
        super().__init__()

        self.title("Deran's Jewels Jewelry Management System")
        self.geometry("600x400")

        # Lists to store inventory and sales history
        self.inventory = []
        self.sales = []

        # Setup the main menu
        self.setup_menu()

    def setup_menu(self):
        """Sets up the main menu with buttons for various actions."""
        menu_frame = tk.Frame(self)
        menu_frame.pack(pady=20)

        # Add Jewelry button
        add_item_button = tk.Button(menu_frame, text="Add Jewelry", command=self.open_add_item_window)
        add_item_button.grid(row=0, column=0, padx=10)

        # View Inventory button
        view_inventory_button = tk.Button(menu_frame, text="View Inventory", command=self.display_inventory)
        view_inventory_button.grid(row=0, column=1, padx=10)

        # Track Sales button
        track_sales_button = tk.Button(menu_frame, text="Track Sales", command=self.open_sales_window)
        track_sales_button.grid(row=0, column=2, padx=10)

    def open_add_item_window(self):
        """Opens the window to add a new jewelry item."""
        self.add_item_window = AddItemWindow(self)
        self.add_item_window.grab_set()

    def open_sales_window(self):
        """Opens the sales tracking window."""
        self.sales_window = SalesWindow(self)
        self.sales_window.grab_set()

    def display_inventory(self):
        """Displays the inventory of jewelry items."""
        inventory_window = tk.Toplevel(self)
        inventory_window.title("Jewelry Inventory")
        inventory_window.geometry("600x400")

        tk.Label(inventory_window, text="Jewelry Inventory", font=("Arial", 16)).pack(pady=10)

        # Listbox to display items in the inventory
        inventory_listbox = tk.Listbox(inventory_window, width=50, height=10)
        inventory_listbox.pack(padx=10, pady=10)

        # Add each inventory item to the listbox
        for item in self.inventory:
            inventory_listbox.insert(tk.END, f"{item['name']} - {item['description']} - ${item['price']} - Qty: {item['quantity']}")

        # Back button to close inventory window
        back_button = tk.Button(inventory_window, text="Back", command=inventory_window.destroy)
        back_button.pack(pady=10)


class AddItemWindow(tk.Toplevel):
    def __init__(self, master):
        super().__init__(master)

        self.title("Add New Jewelry Item")
        self.geometry("400x400")

        # Label and Entry widgets for item details
        tk.Label(self, text="Item Name:").pack(pady=5)
        self.name_entry = tk.Entry(self)
        self.name_entry.pack(pady=5)

        tk.Label(self, text="Description:").pack(pady=5)
        self.description_entry = tk.Entry(self)
        self.description_entry.pack(pady=5)

        tk.Label(self, text="Price:").pack(pady=5)
        self.price_entry = tk.Entry(self)
        self.price_entry.pack(pady=5)

        tk.Label(self, text="Quantity:").pack(pady=5)
        self.quantity_entry = tk.Entry(self)
        self.quantity_entry.pack(pady=5)

        self.image_path = ""
        # Button to upload an image for the jewelry item
        upload_image_button = tk.Button(self, text="Upload Image", command=self.choose_image)
        upload_image_button.pack(pady=10)

        # Submit and Back buttons
        submit_button = tk.Button(self, text="Submit", command=self.submit_item)
        submit_button.pack(pady=10)

        back_button = tk.Button(self, text="Back", command=self.destroy)
        back_button.pack(pady=10)

    def choose_image(self):
        """Allows the user to choose an image for the jewelry item."""
        self.image_path = filedialog.askopenfilename(title="Select Image", filetypes=[("Image Files", "*.png;*.jpg;*.jpeg;*.gif")])

    def submit_item(self):
        """Submits the jewelry item and adds it to the inventory."""
        name = self.name_entry.get()
        description = self.description_entry.get()
        price = self.price_entry.get()
        quantity = self.quantity_entry.get()

        # Check if all fields are filled in
        if not name or not description or not price or not quantity:
            messagebox.showerror("Incomplete Information", "Please fill in all fields before submitting.")
            return

        # Validate price and quantity inputs
        try:
            price = float(price)
            quantity = int(quantity)
        except ValueError:
            messagebox.showerror("Invalid Data", "Price must be a number and quantity must be an integer.")
            return

        # Create the jewelry item dictionary and add it to the inventory
        jewelry_item = {
            "name": name,
            "description": description,
            "price": price,
            "quantity": quantity,
            "image": self.image_path
        }
        self.master.inventory.append(jewelry_item)
        messagebox.showinfo("Item Added", "The jewelry item has been added to the inventory.")
        self.destroy()


class SalesWindow(tk.Toplevel):
    def __init__(self, master):
        super().__init__(master)

        self.title("Sales Tracking")
        self.geometry("400x400")

        # Sales entry fields and log display
        tk.Label(self, text="Log a Sale", font=("Arial", 16)).pack(pady=10)

        tk.Label(self, text="Item Sold:").pack(pady=5)
        self.item_sold_entry = tk.Entry(self)
        self.item_sold_entry.pack(pady=5)

        tk.Label(self, text="Quantity Sold:").pack(pady=5)
        self.sales_quantity_entry = tk.Entry(self)
        self.sales_quantity_entry.pack(pady=5)

        # Button to log a sale
        log_sale_button = tk.Button(self, text="Log Sale", command=self.record_sale)
        log_sale_button.pack(pady=10)

        # Sales history list
        tk.Label(self, text="Sales History", font=("Arial", 16)).pack(pady=10)

        self.sales_listbox = tk.Listbox(self, width=50, height=10)
        self.sales_listbox.pack(padx=10, pady=10)

        # Add sales records to listbox
        for sale in self.master.sales:
            self.sales_listbox.insert(tk.END, sale)

        # Back and Exit buttons
        back_button = tk.Button(self, text="Back", command=self.destroy)
        back_button.pack(pady=10)

        exit_button = tk.Button(self, text="Exit", command=self.quit_application)
        exit_button.pack(pady=10)

    def record_sale(self):
        """Records a sale and updates the sales history."""
        item_sold = self.item_sold_entry.get()
        quantity = self.sales_quantity_entry.get()

        # Ensure that all fields are filled in
        if not item_sold or not quantity:
            messagebox.showerror("Missing Information", "Please complete all fields.")
            return

        try:
            quantity = int(quantity)
        except ValueError:
            messagebox.showerror("Invalid Input", "Quantity must be an integer.")
            return

        # Log the sale
        sale_record = f"Sold {quantity} x {item_sold}"
        self.master.sales.append(sale_record)
        self.sales_listbox.insert(tk.END, sale_record)
        messagebox.showinfo("Sale Recorded", "The sale has been successfully logged.")

    def quit_application(self):
        """Quits the main application and closes the window."""
        self.master.quit()
        self.destroy()


if __name__ == "__main__":
    app = JewelryApp()
    app.mainloop()


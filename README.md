# Ivy-Tech-Project
Rough draft
import tkinter as tk
from tkinter import filedialog, messagebox

class JewelryApp(tk.Tk):
    def_init_(self):
        super()._init__() 

        self.title("Deran's Jewels Jewelry Managegment System")
        self.geometry("600x400")

        self.inventory = []
        salf.sales = []

        self.create_menu()
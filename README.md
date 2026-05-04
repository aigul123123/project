import tkinter as tk
from tkinter import ttk
import random
import string
import json
import os

HISTORY_FILE = "history.json"

# --- Генерация пароля ---
def generate_password():
    length = length_var.get()

    chars = ""
    if digits_var.get():
        chars += string.digits
    if letters_var.get():
        chars += string.ascii_letters
    if symbols_var.get():
        chars += string.punctuation

    if not chars:
        result_var.set("Выберите хотя бы один тип символов")
        return

    password = "".join(random.choice(chars) for _ in range(length))
    result_var.set(password)

    add_to_history(password)
    save_history()

# --- История ---
history = []

def add_to_history(password):
    history.append(password)
    history_listbox.insert(tk.END, password)

def save_history():
    with open(HISTORY_FILE, "w") as f:
        json.dump(history, f)

def load_history():
    if os.path.exists(HISTORY_FILE):
        with open(HISTORY_FILE, "r") as f:
            data = json.load(f)
            for item in data:
                history.append(item)
                history_listbox.insert(tk.END, item)

# --- GUI ---
root = tk.Tk()
root.title("Password Generator")

# Переменные
length_var = tk.IntVar(value=8)
digits_var = tk.BooleanVar(value=True)
letters_var = tk.BooleanVar(value=True)
symbols_var = tk.BooleanVar(value=False)
result_var = tk.StringVar()

# Ползунок
tk.Label(root, text="Длина пароля").pack()
tk.Scale(root, from_=4, to=32, orient="horizontal", variable=length_var).pack()

# Чекбоксы
tk.Checkbutton(root, text="Цифры", variable=digits_var).pack()
tk.Checkbutton(root, text="Буквы", variable=letters_var).pack()
tk.Checkbutton(root, text="Спецсимволы", variable=symbols_var).pack()

# Кнопка
tk.Button(root, text="Сгенерировать", command=generate_password).pack(pady=10)

# Результат
tk.Entry(root, textvariable=result_var, width=30).pack()

# История
tk.Label(root, text="История").pack()
history_listbox = tk.Listbox(root, height=8)
history_listbox.pack()

# Загрузка истории
load_history()

root.mainloop()

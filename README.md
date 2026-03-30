# noe
import tkinter as tk
from tkinter import messagebox
import json
import os

# ==============================
# CONFIGURACIÓN
# ==============================
ARCHIVO = "tareas.json"

ventana = tk.Tk()
ventana.title("Lista de Tareas PRO")
ventana.geometry("450x450")
ventana.config(bg="#eef2f3")

tareas = []

# ==============================
# FUNCIONES DE ARCHIVO
# ==============================

def guardar_tareas():
    with open(ARCHIVO, "w") as f:
        json.dump(tareas, f)

def cargar_tareas():
    global tareas
    if os.path.exists(ARCHIVO):
        with open(ARCHIVO, "r") as f:
            tareas = json.load(f)

# ==============================
# FUNCIONES PRINCIPALES
# ==============================

def añadir_tarea(event=None):
    texto = entrada.get().strip()
    if texto != "":
        tareas.append({"texto": texto, "completada": False})
        actualizar_lista()
        guardar_tareas()
        entrada.delete(0, tk.END)
    else:
        messagebox.showwarning("Aviso", "Escribe una tarea")

def marcar_tarea():
    try:
        i = lista.curselection()[0]
        tareas[i]["completada"] = not tareas[i]["completada"]
        actualizar_lista()
        guardar_tareas()
    except:
        messagebox.showwarning("Aviso", "Selecciona una tarea")

def eliminar_tarea():
    try:
        i = lista.curselection()[0]
        if messagebox.askyesno("Confirmar", "¿Eliminar tarea?"):
            tareas.pop(i)
            actualizar_lista()
            guardar_tareas()
    except:
        messagebox.showwarning("Aviso", "Selecciona una tarea")

def actualizar_lista():
    lista.delete(0, tk.END)
    for t in tareas:
        if t["completada"]:
            lista.insert(tk.END, "✔ " + t["texto"])
        else:
            lista.insert(tk.END, "✖ " + t["texto"])

# ==============================
# INTERFAZ
# ==============================

titulo = tk.Label(ventana, text="📝 Lista de Tareas PRO", 
                  font=("Arial", 16, "bold"), bg="#eef2f3")
titulo.pack(pady=10)

entrada = tk.Entry(ventana, width=35)
entrada.pack(pady=5)
entrada.bind("<Return>", añadir_tarea)

frame = tk.Frame(ventana, bg="#eef2f3")
frame.pack(pady=5)

tk.Button(frame, text="Añadir", command=añadir_tarea, bg="#4CAF50", fg="white").grid(row=0, column=0, padx=5)
tk.Button(frame, text="Completar", command=marcar_tarea, bg="#2196F3", fg="white").grid(row=0, column=1, padx=5)
tk.Button(frame, text="Eliminar", command=eliminar_tarea, bg="#f44336", fg="white").grid(row=0, column=2, padx=5)

lista = tk.Listbox(ventana, width=45, height=15)
lista.pack(pady=10)

# Doble clic = completar
lista.bind("<Double-Button-1>", lambda e: marcar_tarea())

# ==============================
# INICIO
# ==============================

cargar_tareas()
actualizar_lista()

ventana.mainloop()

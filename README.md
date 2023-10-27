from tkinter import messagebox, PhotoImage
from customtkinter import CTk, CTkFrame, CTkEntry, CTkLabel,CTkButton
import mysql.connector

def conectar_db():
    conn = mysql.connector.connect(
        host="localhost",
        user="root",
        password="",
        database="proyecto"
    )
    return conn

def registrar_producto(id, nombre, precio, cantidad, vencimiento):
    conn = conectar_db()
    cursor = conn.cursor()
    query = "INSERT INTO producto (id, nombre, precio, cantidad,vencimiento) VALUES (%s, %s, %s, %s, %s)"
    valores = (id,nombre,precio,cantidad,vencimiento)
    cursor.execute(query, valores)
    conn.commit()
    conn.close()

def registrar():
    id = entry_id.get()
    nombre = entry_nombre.get()
    precio = entry_precio.get()
    cantidad = entry_cantidad.get()
    vencimiento = entry_vencimiento.get()
    registrar_producto(id,nombre,precio,cantidad,vencimiento)
    messagebox.showinfo("Éxito", "Producto registrado con éxito")
    ver_productos()

def ver_productos():
    listbox_productos.delete(0, tk.END)
    conn = conectar_db()
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM producto")
    productos = cursor.fetchall()
    for producto in productos:
        producto_str = f"ID: {producto[0]}, Nombre: {producto[1]}, Precio: {producto[2]:.2f}, Cantidad: {producto[3]}, Vencimiento: {producto[4]}"
        listbox_productos.insert(tk.END, producto_str)
    conn.close()

def borrar_producto(id):
    conn = conectar_db()
    cursor = conn.cursor()
    query = "DELETE FROM producto WHERE id = %s"
    cursor.execute(query, (id,))
    conn.commit()
    conn.close()

def borrar():
    id = entry_borrar.get()
    borrar_producto(id)
    messagebox.showinfo("Éxito", "Producto borrado con éxito")
    ver_productos()

def actualizar_producto(id, nombre, precio, cantidad, vencimiento):
    conn = conectar_db()
    cursor = conn.cursor()
    query = "UPDATE producto SET nombre = %s, precio = %s, cantidad = %s, vencimiento = %s WHERE id = %s"
    valores = (nombre, precio, cantidad, vencimiento, id)
    cursor.execute(query, valores)
    conn.commit()
    conn.close()

def actualizar():
    id = entry_id.get()
    nombre = entry_nombre.get()
    precio = entry_precio.get()
    cantidad = entry_cantidad.get()
    vencimiento = entry_vencimiento.get()
    actualizar_producto(id,nombre,precio,cantidad,vencimiento)
    messagebox.showinfo("Éxito", "Producto actualizado con éxito")
    ver_productos()

root = CTk()
logo = PhotoImage(file ='/home/eddie/Imágenes/logo.png')

root.geometry("800x600")
root.maxsize(800, 600)  
root.configure(bg="#010101")

root.title("MICRO FAMILY")

frame_entrada = CTkFrame(root)
frame_entrada.pack(pady=10)

label_datos = CTkLabel(frame_entrada, text="INGRESAR DATOS", font=("Sans Serif", 16), background='black', foreground='white')
label_datos.grid(row=0, column=0, columnspan=2)

label_id = CTkLabel(frame_entrada, text="ID:", font=("Sans Serif", 16), background='black', foreground='white')
label_id.grid(row=1, column=0)
entry_id = CTkEntry(frame_entrada)
entry_id.grid(row=1, column=1)

label_nombre = CTkLabel(frame_entrada, text="Nombre:", font=("Sans Serif", 16), background='black', foreground='white')
label_nombre.grid(row=2, column=0)
entry_nombre = CTkEntry(frame_entrada)
entry_nombre.grid(row=2, column=1)

label_precio = CTkLabel(frame_entrada, text="Precio:", font=("Sans Serif", 16), background='black', foreground='white')
label_precio.grid(row=3, column=0)
entry_precio = CTkEntry(frame_entrada)
entry_precio.grid(row=3, column=1)

label_cantidad = CTkLabel(frame_entrada, text="Cantidad:", font=("Sans Serif", 16), background='black', foreground='white')
label_cantidad.grid(row=4, column=0)
entry_cantidad = CTkEntry(frame_entrada)
entry_cantidad.grid(row=4, column=1)

label_vencimiento = CTkLabel(frame_entrada, text="Vencimiento:", font=("Sans Serif", 16), background='black', foreground='white')
label_vencimiento.grid(row=5, column=0)
entry_vencimiento = CTkEntry(frame_entrada)
entry_vencimiento.grid(row=5, column=1)

frame_botones = CTkFrame(root)
frame_botones.pack(pady=10)

button_registrar = CTkButton(frame_botones,text="Registrar",font=("Sans Serif",14),bg="green",command=registrar)
button_registrar.grid(row=0,column=0,padx=10)

button_ver_productos = CTkButton(frame_botones,text="Ver Productos",font=("Sans Serif",14),bg="blue",command=ver_productos)
button_ver_productos.grid(row=0,column=1,padx=10)

button_actualizar = CTkButton(frame_botones, text="Actualizar", font=("Sans Serif", 14),bg="yellow", command=actualizar)
button_actualizar.grid(row=0, column=2, padx=10)

label_borrar = CTkLabel(frame_botones, text="ID a borrar:", font=("Sans Serif", 16), background='black', foreground='white')
label_borrar.grid(row=1, column=0)
entry_borrar = CTkEntry(frame_botones)
entry_borrar.grid(row=1, column=1)

button_borrar = CTkButton(frame_botones, text="Borrar", font=("Sans Serif", 16),bg="red", command=borrar)
button_borrar.grid(row=1, column=2)

frame_listbox = CTkFrame(root)
frame_listbox.pack(pady=10)

scrollbar = tk.Scrollbar(frame_listbox)
scrollbar.pack(side=tk.RIGHT, fill=tk.Y)

listbox_productos = tk.Listbox(frame_listbox, font=("Sans Serif", 16), height=20, width=65, yscrollcommand=scrollbar.set)
listbox_productos.pack(side=tk.LEFT, fill=tk.BOTH)

scrollbar.config(command=listbox_productos.yview)

root.call('wm', 'iconphoto', root._w, photo)
root.mainloop()

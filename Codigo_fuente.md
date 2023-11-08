# Documentacion Interna

###### Librerias
	import tkinter as tk  
###### es un módulo que proporciona funcionalidades para crear interfaces gráficas.es un módulo que proporciona funcionalidades para crear interfaces gráficas.

	from tkinter import filedialog 
###### crear cuadros de diálogo de apertura y guardado de archivos en aplicaciones de interfaz gráfica de usuario (GUI).

	from tkinter import messagebox
###### messagebox para crear ventanas emergentes de mensaje personalizadas.

	from tkinter import ttk
###### Los widgets de ttk están diseñados para verse más modernos y proporcionar una experiencia de usuario más agradable

	import webbrowser  # Importa el módulo webbrowser
###### este módulo para abrir páginas web en un navegador web predeterminado, 


# habilitar las funciones de deshacer y rehacer
	class CustomText(tk.Text):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs, undo=True)  # Habilitar deshacer y rehacer
        self.undo_stack = []
        self.redo_stack = []

        # Vincular combinaciones de teclas
        self.bind("<Control-z>", self.undo)
        self.bind("<Control-y>", self.redo)
        self.bind("<Control-a>", self.select_all)
        self.bind("<Button-3><ButtonRelease-3>", self.show_context_menu)

    def undo(self, event=None):
        if self.edit_modified():
            self.edit_separator()
            self.undo_stack.append(self.get(1.0, tk.END))
            self.edit_modified(False)
        if self.undo_stack:
            self.edit_undo()
            self.redo_stack.append(self.undo_stack.pop())

    def redo(self, event=None):
        if self.redo_stack:
            self.edit_redo()
            self.undo_stack.append(self.redo_stack.pop())

    def select_all(self, event=None):
        self.tag_add(tk.SEL, "1.0", tk.END)
        self.mark_set(tk.SEL_FIRST, "1.0")
        self.mark_set(tk.SEL_LAST, tk.END)
        self.see(tk.SEL_FIRST)

    def show_context_menu(self, event):
        menu = tk.Menu(self, tearoff=0)
        menu.add_command(label="Cortar", command=lambda: self.event_generate("<<Cut>>"))
        menu.add_command(label="Copiar", command=lambda: self.event_generate("<<Copy>>"))
        menu.add_command(label="Pegar", command=lambda: self.event_generate("<<Paste>>"))
        menu.post(event.x_root, event.y_root)

	class TextEditorApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Text Editor")
        

        # Variable para rastrear el estado del tema (claro u oscuro)
        self.theme_mode = tk.StringVar(value="claro")

        # Crear el área de texto personalizado
        self.text_widget = CustomText(self.root)
        self.text_widget.pack(fill="both", expand=True)

        # Crear el menú
        menubar = tk.Menu(self.root)
        self.root.config(menu=menubar)

        file_menu = tk.Menu(menubar, tearoff=0)
        menubar.add_cascade(label="Archivo", menu=file_menu)
        file_menu.add_command(label="Abrir", command=self.open_file)
        file_menu.add_command(label="Guardar", command=self.save_file)
        file_menu.add_command(label="Guardar como...", command=self.save_file_as)
        file_menu.add_separator()
        file_menu.add_command(label="Salir", command=self.close_app)

        edit_menu = tk.Menu(menubar, tearoff=0)
        menubar.add_cascade(label="Editar", menu=edit_menu)
        edit_menu.add_command(label="Deshacer", command=self.text_widget.undo)
        edit_menu.add_command(label="Rehacer", command=self.text_widget.redo)
        edit_menu.add_separator()
        edit_menu.add_command(label="Seleccionar Todo", command=self.text_widget.select_all)

         # Menú Diseño
        design_menu = tk.Menu(menubar, tearoff=0)
        menubar.add_cascade(label="Diseño", menu=design_menu)

        # Submenú Temas
        theme_menu = tk.Menu(design_menu, tearoff=0)
        design_menu.add_cascade(label="Temas", menu=theme_menu)
        theme_menu.add_radiobutton(label="Tema Claro", variable=self.theme_mode, value="claro", command=self.toggle_theme)
        #theme_menu.add_radiobutton(label="Tema Oscuro", variable=self.theme_mode, value="oscuro, command=self.toggle_theme")
        theme_menu.add_radiobutton(label="Tema Oscuro", variable=self.theme_mode, value="oscuro", command=self.toggle_theme)


        # Menú Ayuda
        help_menu = tk.Menu(menubar, tearoff=0)
        menubar.add_cascade(label="Ayuda", menu=help_menu)
        help_menu.add_command(label="Información", command=self.show_info)
        help_menu.add_command(label="Integrantes", command=self.show_integrantes)  # Agregar opción Integrantes
        help_menu.add_command(label="Manual de Usuario", command=self.show_user_manual)  # Agregar la opción Manual de Usuario

        self.current_file = None


    # Manual de Usuario
    def show_user_manual(self):
        # Abre la URL del manual de usuario en un navegador web
        webbrowser.open("https://github.com/Yovanygt/Aplicacion_Interfaz_grafica")  # Reemplaza con la URL de tu manual de usuario

###### # Tema blanco o osucuro
    def toggle_theme(self):
        theme = self.theme_mode.get()
        if theme == "claro":
            # Configurar el tema claro
            self.text_widget.config(bg="white", fg="black")
        else:
            # Configurar el tema oscuro
            self.text_widget.config(bg="black", fg="white")

             # Forzar una actualización de la apariencia
        self.root.update()

    def show_info(self):
        info = """
        Text Editor v1.0
        Esta es una aplicación de ejemplo de un editor de texto.
        Licencia: Crack valido al año 2100
        """
        messagebox.showinfo("Información de la Aplicación", info)

    def show_integrantes(self):
        integrantes_info = """
        Integrantes del Grupo:
        - Nombre Lester Gabriel Orozco Xoná = 7690-23-12491
        - Nombre Rode Emanuel Toledo Morales = 7690-23-16895
        - Nombre Edgar Yovany Garcia Luis = 7690-23-13075
        """
        messagebox.showinfo("Integrantes del Grupo", integrantes_info)



    def open_file(self):
        file_path = filedialog.askopenfilename()
        if file_path:
                with open(file_path, "r") as file:
                    content = file.read()
                    self.text_widget.delete(1.0, tk.END)
                    self.text_widget.insert(tk.END, content)
                self.current_file = file_path  # Establece el archivo actual
            
           

######  La función save_file se encarga de guardar el contenido del área de texto en un archivo
    def save_file(self):    def save_file(self):
        content = self.text_widget.get(1.0, tk.END)
        if self.current_file:
            with open(self.current_file, "w") as file:
                file.write(content)
        else:
            file_path = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=[("Archivos de texto", "*.txt")])
            if file_path:
                with open(file_path, "w") as file:
                    file.write(content)

    def save_file_as(self):
        content = self.text_widget.get(1.0, tk.END)
        file_path = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=[("Archivos de texto", "*.txt")])
        if file_path:
            with open(file_path, "w") as file:
                file.write(content)

    def close_app(self):
        self.root.destroy()

###### cuando ejecutas este script, se creará una ventana de aplicación de editor de texto utilizando la clase TextEditorApp y la interfaz gráfica de tkinter, y podrás interactuar con ella para editar texto, abrir archivos, guardarlos, cambiar temas, etc.
	if __name__ == "__main__":
    root = tk.Tk()
    app = TextEditorApp(root)
    root.mainloop()

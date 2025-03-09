# Clase Libro
class Libro:
    def __init__(self, titulo, autor, categoria, isbn):
        # Se usan tuplas para autor y titulo, ya que son inmutables
        self.titulo = titulo
        self.autor = autor
        self.categoria = categoria
        self.isbn = isbn

    def __str__(self):
        return f"'{self.titulo}' de {self.autor} - Categoría: {self.categoria} (ISBN: {self.isbn})"


# Clase Usuario
class Usuario:
    def __init__(self, nombre, id_usuario):
        self.nombre = nombre
        self.id_usuario = id_usuario
        self.libros_prestados = []  # Lista de libros actualmente prestados

    def __str__(self):
        return f"Usuario: {self.nombre} (ID: {self.id_usuario})"

    def listar_libros_prestados(self):
        if not self.libros_prestados:
            return f"{self.nombre} no tiene libros prestados."
        return f"Libros prestados a {self.nombre}: " + ", ".join(str(libro) for libro in self.libros_prestados)


# Clase Biblioteca
class Biblioteca:
    def __init__(self):
        self.libros = {}  # Diccionario donde la clave es el ISBN y el valor es un objeto Libro
        self.usuarios = set()  # Conjunto de usuarios registrados

    def añadir_libro(self, libro):
        """Añadir un libro a la biblioteca."""
        if libro.isbn in self.libros:
            print(f"El libro con ISBN {libro.isbn} ya está en la biblioteca.")
        else:
            self.libros[libro.isbn] = libro
            print(f"Libro '{libro.titulo}' añadido con éxito.")

    def quitar_libro(self, isbn):
        """Quitar un libro de la biblioteca."""
        if isbn in self.libros:
            del self.libros[isbn]
            print(f"Libro con ISBN {isbn} ha sido retirado de la biblioteca.")
        else:
            print(f"No se encontró un libro con el ISBN {isbn}.")

    def registrar_usuario(self, usuario):
        """Registrar un nuevo usuario en la biblioteca."""
        self.usuarios.add(usuario.id_usuario)
        print(f"Usuario {usuario.nombre} registrado con éxito.")

    def dar_de_baja_usuario(self, id_usuario):
        """Dar de baja a un usuario de la biblioteca."""
        if id_usuario in self.usuarios:
            self.usuarios.remove(id_usuario)
            print(f"Usuario con ID {id_usuario} dado de baja.")
        else:
            print(f"No se encontró un usuario con el ID {id_usuario}.")

    def prestar_libro(self, isbn, id_usuario):
        """Prestar un libro a un usuario."""
        if isbn not in self.libros:
            print(f"El libro con ISBN {isbn} no está disponible en la biblioteca.")
            return

        libro = self.libros[isbn]
        usuario = next((u for u in self.usuarios if u.id_usuario == id_usuario), None)
        if not usuario:
            print(f"No se encontró el usuario con ID {id_usuario}.")
            return

        # Verificar si el libro ya está prestado
        if libro in usuario.libros_prestados:
            print(f"El usuario {usuario.nombre} ya tiene prestado el libro '{libro.titulo}'.")
        else:
            usuario.libros_prestados.append(libro)
            print(f"Libro '{libro.titulo}' prestado a {usuario.nombre}.")

    def devolver_libro(self, isbn, id_usuario):
        """Devolver un libro prestado por un usuario."""
        usuario = next((u for u in self.usuarios if u.id_usuario == id_usuario), None)
        if not usuario:
            print(f"No se encontró el usuario con ID {id_usuario}.")
            return

        libro = next((l for l in usuario.libros_prestados if l.isbn == isbn), None)
        if libro:
            usuario.libros_prestados.remove(libro)
            print(f"Libro '{libro.titulo}' devuelto por {usuario.nombre}.")
        else:
            print(f"El usuario {usuario.nombre} no tiene prestado el libro con ISBN {isbn}.")

    def buscar_libro(self, criterio, valor):
        """Buscar libros por título, autor o categoría."""
        if criterio not in ["titulo", "autor", "categoria"]:
            print("Criterio de búsqueda inválido. Use 'titulo', 'autor' o 'categoria'.")
            return

        libros_encontrados = [libro for libro in self.libros.values() if getattr(libro, criterio).lower() == valor.lower()]
        if libros_encontrados:
            print(f"Libros encontrados por {criterio}:")
            for libro in libros_encontrados:
                print(libro)
        else:
            print(f"No se encontraron libros con {criterio} = '{valor}'.")

    def listar_libros_prestados(self, id_usuario):
        """Listar los libros prestados a un usuario."""
        usuario = next((u for u in self.usuarios if u.id_usuario == id_usuario), None)
        if usuario:
            print(usuario.listar_libros_prestados())
        else:
            print(f"No se encontró el usuario con ID {id_usuario}.")


# Código de prueba

# Crear instancias de la biblioteca y usuarios
biblioteca = Biblioteca()

usuario1 = Usuario("Juan Pérez", 1)
usuario2 = Usuario("Ana Gómez", 2)

# Registrar usuarios
biblioteca.registrar_usuario(usuario1)
biblioteca.registrar_usuario(usuario2)

# Crear libros
libro1 = Libro("El Quijote", "Miguel de Cervantes", "Novela", "1234567890")
libro2 = Libro("Cien Años de Soledad", "Gabriel García Márquez", "Novela", "0987654321")
libro3 = Libro("La Odisea", "Homero", "Épica", "1122334455")

# Añadir libros a la biblioteca
biblioteca.añadir_libro(libro1)
biblioteca.añadir_libro(libro2)
biblioteca.añadir_libro(libro3)

# Prestar libros
biblioteca.prestar_libro("1234567890", 1)
biblioteca.prestar_libro("0987654321", 2)

# Listar libros prestados
biblioteca.listar_libros_prestados(1)
biblioteca.listar_libros_prestados(2)

# Devolver libros
biblioteca.devolver_libro("1234567890", 1)

# Buscar libros
biblioteca.buscar_libro("titulo", "El Quijote")
biblioteca.buscar_libro("autor", "Gabriel García Márquez")

# Quitar un libro de la biblioteca
biblioteca.quitar_libro("1122334455")

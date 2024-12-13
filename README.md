import sqlite3

def inicializar_base_datos():
    conexion = sqlite3.connect("librorecetas.db")
    cursor = conexion.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS recetas (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nombre TEXT UNIQUE NOT NULL,
            ingredientes TEXT NOT NULL,
            pasos TEXT NOT NULL
        )
    ''')
    conexion.commit()
    conexion.close()

def agregar_nueva_receta():
    nombre = input("Introduce el nombre de la receta: ")
    ingredientes = input("Lista los ingredientes (separa cada uno con comas): ")
    pasos = input("Describe los pasos de preparación (separa cada paso con comas): ")

    conexion = sqlite3.connect("librorecetas.db")
    cursor = conexion.cursor()
    try:
        cursor.execute(
            "INSERT INTO recetas (nombre, ingredientes, pasos) VALUES (?, ?, ?)",
            (nombre, ingredientes, pasos)
        )
        conexion.commit()
        print("\n¡Receta añadida exitosamente!\n")
    except sqlite3.IntegrityError:
        print("\nError: Ya existe una receta con ese nombre.\n")
    conexion.close()

def modificar_receta():
    nombre = input("Escribe el nombre de la receta que deseas modificar: ")

    conexion = sqlite3.connect("librorecetas.db")
    cursor = conexion.cursor()
    cursor.execute("SELECT * FROM recetas WHERE nombre = ?", (nombre,))
    receta = cursor.fetchone()

    if receta:
        print("\nReceta encontrada:")
        print(f"- Ingredientes actuales: {receta[2]}")
        print(f"- Pasos actuales: {receta[3]}\n")

        nuevos_ingredientes = input("Ingresa los nuevos ingredientes (deja vacío para conservar los actuales): ")
        nuevos_pasos = input("Ingresa los nuevos pasos (deja vacío para conservar los actuales): ")

        nuevos_ingredientes = nuevos_ingredientes if nuevos_ingredientes else receta[2]
        nuevos_pasos = nuevos_pasos if nuevos_pasos else receta[3]

        cursor.execute(
            "UPDATE recetas SET ingredientes = ?, pasos = ? WHERE nombre = ?",
            (nuevos_ingredientes, nuevos_pasos, nombre)
        )
        conexion.commit()
        print("\n¡La receta ha sido actualizada con éxito!\n")
    else:
        print("\nError: No se encontró ninguna receta con ese nombre.\n")
    conexion.close()

def eliminar_receta():
    nombre = input("Escribe el nombre de la receta que deseas eliminar: ")

    conexion = sqlite3.connect("librorecetas.db")
    cursor = conexion.cursor()
    cursor.execute("SELECT * FROM recetas WHERE nombre = ?", (nombre,))
    receta = cursor.fetchone()

    if receta:
        cursor.execute("DELETE FROM recetas WHERE nombre = ?", (nombre,))
        conexion.commit()
        print("\nLa receta ha sido eliminada correctamente.\n")
    else:
        print("\nError: No se encontró ninguna receta con ese nombre.\n")
    conexion.close()

def mostrar_lista_recetas():
    conexion = sqlite3.connect("librorecetas.db")
    cursor = conexion.cursor()
    cursor.execute("SELECT nombre FROM recetas")
    recetas = cursor.fetchall()

    if recetas:
        print("\nRecetas registradas:")
        for receta in recetas:
            print(f"- {receta[0]}")
        print()
    else:
        print("\nNo hay recetas registradas en el sistema.\n")
    conexion.close()

def consultar_receta():
    nombre = input("Escribe el nombre de la receta que deseas consultar: ")

    conexion = sqlite3.connect("librorecetas.db")
    cursor = conexion.cursor()
    cursor.execute("SELECT ingredientes, pasos FROM recetas WHERE nombre = ?", (nombre,))
    receta = cursor.fetchone()

    if receta:
        print("\nIngredientes:")
        print(receta[0])
        print("\nPasos:")
        print(receta[1])
        print()
    else:
        print("\nError: No se encontró ninguna receta con ese nombre.\n")
    conexion.close()

def menu_principal():
    while True:
        print("\n--- Libro de Recetas ---")
        print("1. Añadir una nueva receta")
        print("2. Modificar una receta existente")
        print("3. Eliminar una receta")
        print("4. Mostrar todas las recetas")
        print("5. Consultar los detalles de una receta")
        print("6. Salir")

        opcion = input("Elige una opción: ").strip()
        if opcion == '1':
            agregar_nueva_receta()
        elif opcion == '2':
            modificar_receta()
        elif opcion == '3':
            eliminar_receta()
        elif opcion == '4':
            mostrar_lista_recetas()
        elif opcion == '5':
            consultar_receta()
        elif opcion == '6':
            print("\n¡Gracias por usar el libro de recetas!n")
            break
        else:
            print("\nOpción inválida. Por favor, intenta de nuevo.\n")

if __name__ == "__main__":
    inicializar_base_datos()
    menu_principal()
    

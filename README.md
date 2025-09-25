
import tkinter as tk  

# Clase para guardar los datos del cliente
class Persona:
    def __init__(self, nombreC, apellidop, apellidom, fechanac, domicilio):
        self.nombreC = nombreC       
        self.apellidop = apellidop            
        self.apellidom = apellidom            
        self.fechanac = fechanac  
        self.domicilio = domicilio 

# Tipo de transacción que se hará
class Transaccion:
    def __init__(self, fecha, descrip, cargo, ab, sal):
        self.fecha = fecha    
        self.descrip = descrip      
        self.cargo = cargo    
        self.ab = ab    
        self.sal = sal    

# Clase de la cuenta bancaria
class BancoCuenta:
    def __init__(self, num):
        self.numeroDEcuenta = num  
        self.saldo = 1000        
        self.movimiento = []     

    # Función para registrar un movimiento (retiro o depósito), el IF indica que si el retiro es mayor al saldo, 
    # este será rechazado,
    # en caso contrario se ajusta al saldo, dentro de este se usa otro If que indica si se hará un retiro o depósito,
    # y a su vez se guarda los movimientos a la lista.
    def mover(self, cantidad, descrip, tipo):
        if tipo == "Retiro" and cantidad > self.saldo:
            self.movimiento.append(Transaccion("2025-09-12", "Cargo rechazado (sin fondos)", 0, 0, self.saldo))
        else:
            if tipo == "Retiro":
                self.saldo -= cantidad
                cargo, ab = cantidad, 0
            else: 
                self.saldo += cantidad
                cargo, ab = 0, cantidad
            self.movimiento.append(Transaccion("2025-09-12", descrip, cargo, ab, self.saldo))

class Interfaz:
    def __init__(self, vent):
        self.vent = vent
        self.vent.title(" Estado de Cuenta Bancaria ")  
        self.vent.configure(bg="#e0ffe0") 
        self.cliente = Persona("", "", "", "", "")  
        self.cuenta = BancoCuenta("")  

        # Crear un contenedor general en forma de grid
        self.frame_general = tk.Frame(vent, bg="#e0ffe0")
        self.frame_general.pack()

        # Posicionar los frames cliente y operaciones uno al lado del otro
        self.fcliente = tk.Frame(self.frame_general, bg="#e0ffe0")   
        self.fcliente.grid(row=0, column=0, padx=20, pady=10, sticky="n")

        self.frame_op = tk.Frame(self.frame_general, bg="#e0ffe0") 
        self.frame_op.grid(row=0, column=1, padx=20, pady=10, sticky="n")

        # Crear los formularios
        self.crear_formulario_cliente()
        self.crear_operaciones()

    # Formularios donde se piden datos y se guardan en diccionarios
    def crear_formulario_cliente(self):
        self.entries = {} 
        campos = ["Nombre", "Apellido paterno", "Apellido materno", "Fecha nacimiento", "Domicilio", "Número de cuenta"]
        for i, c in enumerate(campos):
            tk.Label(self.fcliente, text=c, bg="#e0ffe0", fg="#006600", font=("Comic Sans MS", 10, "bold")).grid(row=i, column=0, sticky="e", pady=2)

            self.entries[c] = tk.Entry(self.fcliente, bg="#ffffff", font=("Comic Sans MS", 10))
            self.entries[c].grid(row=i, column=1, pady=2)

        tk.Button(self.fcliente, text=" Guardar Información y Crear Cuenta ", bg="#66cc66", fg="white", font=("Comic Sans MS", 10, "bold"), command=self.guardar_cliente).grid(row=len(campos), column=0, columnspan=2, pady=10)

    # Aquí se guarda al cliente y los datos, la función marcada con d= es para crear un diccionario con los valores escritos
    # ya por el usuario 
    def guardar_cliente(self):
        d = {k: v.get() for k, v in self.entries.items()}  

        self.cliente = Persona(d["Nombre"], d["Apellido paterno"], d["Apellido materno"], d["Fecha nacimiento"], d["Domicilio"])
        self.cuenta = BancoCuenta(d["Número de cuenta"])
        self.mostrar_estado()

    # Apartado de las operaciones, y a su vez el formato de la ventana
    def crear_operaciones(self):
        tk.Label(self.frame_op, text="Tipo de operación:", bg="#e0ffe0", fg="#006600", font=("Comic Sans MS", 10, "bold")).grid(row=0, column=0, pady=2)

        self.tipo_var = tk.StringVar(value="Retiro")  
        tk.OptionMenu(self.frame_op, self.tipo_var, "Retiro", "Depósito").grid(row=0, column=1, pady=2)

        tk.Label(self.frame_op, text="Cantidad:", bg="#e0ffe0", fg="#006600", font=("Comic Sans MS", 10, "bold")).grid(row=1, column=0, pady=2)
        self.cant = tk.Entry(self.frame_op, bg="#ffffff", font=("Comic Sans MS", 10))
        self.cant.grid(row=1, column=1, pady=2)

        tk.Label(self.frame_op, text="Descripción:", bg="#e0ffe0", fg="#006600", font=("Comic Sans MS", 10, "bold")).grid(row=2, column=0, pady=2)
        self.desc = tk.Entry(self.frame_op, bg="#ffffff", font=("Comic Sans MS", 10))
        self.desc.grid(row=2, column=1, pady=2)

        tk.Button(self.frame_op, text=" Registrar Movimiento ", bg="#66cc66", fg="white", font=("Comic Sans MS", 10, "bold"), command=self.registrar).grid(row=3, column=0, columnspan=2, pady=5)

        self.salida = tk.Text(self.frame_op, width=80, height=20, bg="#f0fff0", fg="#003300", font=("Comic Sans MS", 9))
        self.salida.grid(row=4, column=0, columnspan=2, pady=10)

    def registrar(self):
        try:
            cantidad = float(self.cant.get())  # Convertir a número
        except:
            self.salida.insert("end", "⚠️ Error: La cantidad debe ser numérica.\n") #marca erros si no es numerica la 
            #informacion
            return
        self.cuenta.mover(cantidad, self.desc.get(), self.tipo_var.get())
        self.cant.delete(0, tk.END)
        self.desc.delete(0, tk.END)
        self.mostrar_estado()

    # Aquí solo muestra cómo aparecerán los datos del cliente 
    def mostrar_estado(self):
        self.salida.delete("1.0", tk.END)  # Esta función limpia el texto
        c, cuenta = self.cliente, self.cuenta

        self.salida.insert("end", "------ DATOS DEL CLIENTE ------\n")
        self.salida.insert("end", "Nombre: " + c.nombreC + "\n")
        self.salida.insert("end", "Apellido paterno: " + c.apellidop + "\n")
        self.salida.insert("end", "Apellido materno: " + c.apellidom + "\n")
        self.salida.insert("end", "Fecha de nacimiento: " + c.fechanac + "\n")
        self.salida.insert("end", "Domicilio: " + c.domicilio + "\n\n")

        self.salida.insert("end", "--- DATOS DE LA CUENTA ---\n")
        self.salida.insert("end", "Número de cuenta: " + cuenta.numeroDEcuenta + "\n")
        self.salida.insert("end", "Saldo actual: " + str(cuenta.saldo) + "\n\n")  # el uso del str es para juntar más datos

        self.salida.insert("end", " MOVIMIENTOS \n")
        if cuenta.movimiento:
            for m in cuenta.movimiento:  # acomodo de datos
                self.salida.insert("end", f"Fecha: {m.fecha} | Descripción: {m.descrip} | Cargo: {m.cargo} | Abono: {m.ab} | Saldo: {m.sal}\n")
        else:
            self.salida.insert("end", "No hay movimientos registrados.\n")  # en caso de que no haya movimientos 

# Ejecutar la aplicación
if __name__ == "__main__":
    vent = tk.Tk()
    Interfaz(vent)
    vent.mainloop()


# semanatipoderecorrido
arboles binarios-Tipos de recorrido
using System;

public class Nodo
{
    public string Valor;
    public Nodo Izquierdo, Derecho;

    public Nodo(string valor)
    {
        Valor = valor;
        Izquierdo = Derecho = null;
    }
}

public class ArbolBinario
{
    private Nodo raiz;

    public ArbolBinario()
    {
        raiz = null;
    }

    // Función para insertar un valor en el árbol
    public void Insertar(string valor)
    {
        raiz = InsertarRecursivo(raiz, valor);
    }

    private Nodo InsertarRecursivo(Nodo nodo, string valor)
    {
        if (nodo == null)
        {
            return new Nodo(valor);
        }

        if (string.Compare(valor, nodo.Valor) < 0)
            nodo.Izquierdo = InsertarRecursivo(nodo.Izquierdo, valor);
        else if (string.Compare(valor, nodo.Valor) > 0)
            nodo.Derecho = InsertarRecursivo(nodo.Derecho, valor);

        return nodo;
    }

    // Función para buscar un valor en el árbol
    public bool Buscar(string valor)
    {
        return BuscarRecursivo(raiz, valor) != null;
    }

    private Nodo BuscarRecursivo(Nodo nodo, string valor)
    {
        if (nodo == null || nodo.Valor == valor)
            return nodo;

        if (string.Compare(valor, nodo.Valor) < 0)
            return BuscarRecursivo(nodo.Izquierdo, valor);

        return BuscarRecursivo(nodo.Derecho, valor);
    }

    // Función para eliminar un valor del árbol
    public void Eliminar(string valor)
    {
        raiz = EliminarRecursivo(raiz, valor);
    }

    private Nodo EliminarRecursivo(Nodo nodo, string valor)
    {
        if (nodo == null)
            return nodo;

        if (string.Compare(valor, nodo.Valor) < 0)
            nodo.Izquierdo = EliminarRecursivo(nodo.Izquierdo, valor);
        else if (string.Compare(valor, nodo.Valor) > 0)
            nodo.Derecho = EliminarRecursivo(nodo.Derecho, valor);
        else
        {
            if (nodo.Izquierdo == null)
                return nodo.Derecho;
            else if (nodo.Derecho == null)
                return nodo.Izquierdo;

            nodo.Valor = ObtenerMinimo(nodo.Derecho).Valor;
            nodo.Derecho = EliminarRecursivo(nodo.Derecho, nodo.Valor);
        }

        return nodo;
    }

    private Nodo ObtenerMinimo(Nodo nodo)
    {
        Nodo actual = nodo;
        while (actual.Izquierdo != null)
        {
            actual = actual.Izquierdo;
        }
        return actual;
    }

    // Función para mostrar el árbol en preorden
    public void Mostrar()
    {
        MostrarRecursivo(raiz);
        Console.WriteLine();
    }

    private void MostrarRecursivo(Nodo nodo)
    {
        if (nodo != null)
        {
            Console.Write(nodo.Valor + " ");
            MostrarRecursivo(nodo.Izquierdo);
            MostrarRecursivo(nodo.Derecho);
        }
    }

    // Función para evaluar expresiones (solo si la raíz es un operador)
    public double Evaluar()
    {
        return EvaluarRecursivo(raiz);
    }

    private double EvaluarRecursivo(Nodo nodo)
    {
        if (nodo == null)
            return 0;

        // Si es un número
        if (double.TryParse(nodo.Valor, out double valor))
            return valor;

        // Si es un operador
        double izquierda = EvaluarRecursivo(nodo.Izquierdo);
        double derecha = EvaluarRecursivo(nodo.Derecho);

        switch (nodo.Valor)
        {
            case "+":
                return izquierda + derecha;
            case "-":
                return izquierda - derecha;
            case "*":
                return izquierda * derecha;
            case "/":
                if (derecha == 0)
                    throw new DivideByZeroException("No se puede dividir por cero.");
                return izquierda / derecha;
            default:
                throw new InvalidOperationException("Operador no válido");
        }
    }
}

class Program
{
    static void Main()
    {
        ArbolBinario arbol = new ArbolBinario();
        int opcion;

        do
        {
            Console.WriteLine("\nMenú de operaciones:");
            Console.WriteLine("1. Ingresar expresión y evaluar");
            Console.WriteLine("2. Insertar un valor en el árbol");
            Console.WriteLine("3. Buscar un valor en el árbol");
            Console.WriteLine("4. Eliminar un valor del árbol");
            Console.WriteLine("5. Mostrar árbol");
            Console.WriteLine("6. Salir");
            Console.Write("Seleccione una opción: ");
            opcion = int.Parse(Console.ReadLine());

            switch (opcion)
            {
                case 1:
                    Console.WriteLine("Ingrese una expresión aritmética (por ejemplo, 3 + 5): ");
                    string expresion = Console.ReadLine();
                    if (EsExpresionValida(expresion))
                    {
                        Nodo raiz = CrearArbolDeExpresion(expresion);
                        arbol = new ArbolBinario();
                        arbol.Insertar(expresion);  // Aseguramos que la expresión se inserte como una cadena.
                        try
                        {
                            double resultado = arbol.Evaluar();
                            Console.WriteLine("Resultado: " + resultado);
                        }
                        catch (Exception ex)
                        {
                            Console.WriteLine("Error: " + ex.Message);
                        }
                    }
                    else
                    {
                        Console.WriteLine("Expresión inválida. Asegúrese de ingresar una expresión con el formato correcto.");
                    }
                    break;

                case 2:
                    Console.Write("Ingrese el valor a insertar en el árbol: ");
                    string valorInsertar = Console.ReadLine();
                    arbol.Insertar(valorInsertar);
                    Console.WriteLine($"Valor {valorInsertar} insertado.");
                    break;

                case 3:
                    Console.Write("Ingrese el valor a buscar en el árbol: ");
                    string valorBuscar = Console.ReadLine();
                    bool encontrado = arbol.Buscar(valorBuscar);
                    if (encontrado)
                        Console.WriteLine($"Valor {valorBuscar} encontrado en el árbol.");
                    else
                        Console.WriteLine($"Valor {valorBuscar} no encontrado.");
                    break;

                case 4:
                    Console.Write("Ingrese el valor a eliminar del árbol: ");
                    string valorEliminar = Console.ReadLine();
                    arbol.Eliminar(valorEliminar);
                    Console.WriteLine($"Valor {valorEliminar} eliminado.");
                    break;

                case 5:
                    Console.WriteLine("Mostrando el árbol en preorden:");
                    arbol.Mostrar();
                    break;

                case 6:
                    Console.WriteLine("Saliendo...");
                    break;

                default:
                    Console.WriteLine("Opción no válida.");
                    break;
            }

        } while (opcion != 6);
    }

    // Función para crear el árbol de expresión a partir de una cadena de entrada
    static Nodo CrearArbolDeExpresion(string expresion)
    {
        string[] partes = expresion.Split(' ');

        if (partes.Length != 3)
        {
            Console.WriteLine("La expresión debe tener el formato: <operando1> <operador> <operando2>");
            return null;
        }

        Nodo raiz = new Nodo(partes[1]);
        raiz.Izquierdo = new Nodo(partes[0]);
        raiz.Derecho = new Nodo(partes[2]);

        return raiz;
    }

    // Función para validar la expresión ingresada
    static bool EsExpresionValida(string expresion)
    {
        string[] partes = expresion.Split(' ');
        return partes.Length == 3;
    }
}

link de ejecutable: https://www.programiz.com/online-compiler/4Sq8zisGA67Q9
li

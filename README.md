Implementación el algoritmo Playfair en Python.
Definición de las funciones
Definición de la matriz de cifrado/descifrado con base a la clave.


def crear_matriz_clave(clave):
    # Convertimos la clave a mayúsculas y reemplazamos 'J' por 'I' (Playfair usa solo 25 letras)
    clave = clave.upper().replace("J", "I")
    matriz = []
    usada = set()

    # Agregamos las letras de la clave sin repetir
    for letra in clave:
        if letra not in usada and letra.isalpha():
            matriz.append(letra)
            usada.add(letra)

    # Completamos la matriz con las demás letras del alfabeto que no están en la clave
    for letra in "ABCDEFGHIKLMNOPQRSTUVWXYZ":  # Nota: la 'J' se excluye
        if letra not in usada:
            matriz.append(letra)

    # Devolvemos la matriz en forma de lista de listas (5x5)
    return [matriz[i*5:(i+1)*5] for i in range(5)]
     
Función para encontrar la posicón del caracter dentro de la matriz.


def encontrar_posicion(matriz, letra):
    # Busca la posición (fila, columna) de una letra en la matriz
    for fila in range(5):
        for col in range(5):
            if matriz[fila][col] == letra:
                return fila, col
    return None
     
Función para preparar/limpiar el texto que será cifrado.


def preparar_texto(texto, es_cifrar=True):
    # Convierte el texto a mayúsculas y reemplaza 'J' por 'I'
    texto = texto.upper().replace("J", "I")
    # Elimina todos los caracteres que no son letras
    texto = ''.join(filter(str.isalpha, texto))

    if es_cifrar:
        # Prepara los pares de letras, agregando 'X' si hay letras repetidas
        i = 0
        resultado = ""
        while i < len(texto):
            a = texto[i]
            # Si queda una letra sola al final, se empareja con 'X'
            b = texto[i+1] if i + 1 < len(texto) else "X"
            if a == b:
                # Si el par es de letras iguales, se inserta una 'X' entre ellas
                resultado += a + "X"
                i += 1
            else:
                resultado += a + b
                i += 2
        # Si la longitud del resultado es impar, agregamos 'X' al final
        if len(resultado) % 2 != 0:
            resultado += "X"
        return resultado
    else:
        # Para descifrar, solo se limpian los caracteres no alfabéticos
        return texto
     
Funciuón para determinar los pares de caracteres que establecen las 3 reglas básicas que rigen al algoritmo.


def procesar_pares(mensaje, matriz, cifrar=True):
    resultado = ""
    paso = 1 if cifrar else -1  # Determina si se mueve hacia la derecha o izquierda (cifrar o descifrar)

    # Recorremos el mensaje de dos en dos caracteres
    for i in range(0, len(mensaje), 2):
        a, b = mensaje[i], mensaje[i+1]
        fila_a, col_a = encontrar_posicion(matriz, a)
        fila_b, col_b = encontrar_posicion(matriz, b)

        # Si están en la misma fila
        if fila_a == fila_b:
            resultado += matriz[fila_a][(col_a + paso) % 5]
            resultado += matriz[fila_b][(col_b + paso) % 5]
        # Si están en la misma columna
        elif col_a == col_b:
            resultado += matriz[(fila_a + paso) % 5][col_a]
            resultado += matriz[(fila_b + paso) % 5][col_b]
        # Si forman un rectángulo
        else:
            resultado += matriz[fila_a][col_b]
            resultado += matriz[fila_b][col_a]

    return resultado
     
Función para cifrar cuando el parámetro es igual a 1.


def playfair_cipher(mensaje, clave, modo):
    matriz = crear_matriz_clave(clave)

    if modo == 1:  # Cifrado
        texto_preparado = preparar_texto(mensaje, es_cifrar=True)
        return procesar_pares(texto_preparado, matriz, cifrar=True)
    elif modo == 0:  # Descifrado
        mensaje_limpio = ''.join(filter(str.isalpha, mensaje.upper().replace("J", "I")))
        return procesar_pares(mensaje_limpio, matriz, cifrar=False)
    else:
        raise ValueError("Modo inválido. Usa 1 para cifrar o 0 para descifrar.")
     
Generación aleatoria del mensaje y la clave


def generar_cadena_aleatoria(longitud):
    return ''.join(random.choices(string.ascii_letters, k=longitud))
     
Ejemplo de uso
Para este ejemplo la llave y el mensaje se generarán de forma aleatoria con una longitud máxima de 10, para validar que el programa funcione correctamente con cualquier secuencia de caracteres independientemente de su longitud.

Establecer longitudes aleatorias entre 1 y 10 para la generación del mensaje claro y de la clave.


import random
import string

longitud_mensaje = random.randint(1, 10)
longitud_clave = random.randint(1, 10)
     
Generación aleatoria del mensaje claro y de la clave.


mensaje_claro = generar_cadena_aleatoria(longitud_mensaje)
clave = generar_cadena_aleatoria(longitud_clave)

print(f"Mensaje claro generado: {mensaje_claro}")
print(f"Clave generada: {clave}")
     
Mensaje claro generado: dFnqArNVa
Clave generada: aHecuteU
Generar el cifrado del texto claro con base a la llave correspondiente (Parámetro 1).


mensaje_cifrado = playfair_cipher(mensaje_claro, clave, 1)
print("Cifrado:", mensaje_cifrado)
     
Cifrado: FGLSCOIZEV
Generar el descifrado del texto claro empleando a la llave correspondiente (Parámetro 0).


mensaje_descifrado = playfair_cipher(mensaje_cifrado, clave, 0)
print("Descifrado:", mensaje_descifrado)
     
Descifrado: DFNQARNVAX

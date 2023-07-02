# Contando-silabas-en-poesia-Haiku-con-python
#Proyecto CC112-B / Grupo 02 : Contando sílabas en poesía Haiku
import nltk
from nltk.corpus import cmudict

# nltk nos proporciona un conjunto de herramientas y recursos que ayudan en el análisis, la manipulación y la comprensión del lenguaje humano.

"""Descargamos los corpus cmudict para contar las sílabas de la poesía Haiku y
también descargamos punkt para tokenizar las oraciones (dividir el texto en oraciones individuales)"""
nltk.download('cmudict')
nltk.download('punkt')


""" Vamos a crear un if para separar los dos idiomas """
idioma = input("Seleccione el idioma (Español o Inglés): ")
if idioma == "inglés" or idioma == "ingles":
    # pronunciation_dict nos permite buscar la pronunciación fonética de palabras en inglés y utilizarla para contar el número de sílabas.
    pronunciation_dict = cmudict.dict()

    def contador_silabas(poesia):
        poesia = poesia.lower()
        if poesia in pronunciation_dict:
            pronunciaciones = pronunciation_dict[poesia]  
            contador_silabas = [] 
           
            for pronunciacion in pronunciaciones:
                silabas = []
                
                for elemento in pronunciacion:
                    if elemento[-1].isdigit():  # Verificar si el último carácter es un dígito (representa una sílaba)
                        silabas.append(elemento) 

                contador_silabas.append(len(silabas))  

            primer_contador_silabas = contador_silabas[0]  
            return primer_contador_silabas
        else:
            return -1

    def haiku():
        haiku = []

        # Verso 1:
        while True:
            verso = input("Digite el primer verso (5 sílabas): ")
            verifica_silabas = sum(contador_silabas(poesia) for poesia in nltk.word_tokenize(verso) if poesia not in [',', '.', '?', '¿', '!', '¡'])
            if verifica_silabas == 5:
                haiku.append(verso)
                break
            else:
                print("En este verso hacen falta", 5 - verifica_silabas, "sílabas")

        # Verso 2:
        while True:
            verso = input("Digite el segundo verso (7 sílabas): ")
            verifica_silabas = sum(contador_silabas(poesia) for poesia in nltk.word_tokenize(verso) if poesia not in [',', '.', '?', '¿', '!', '¡'])
            if verifica_silabas == 7:
                haiku.append(verso)
                break
            else:
                print("En este verso hacen falta", 7 - verifica_silabas, "sílabas")

        # Verso 3:
        while True:
            verso = input("Digite el tercer verso (5 sílabas): ")
            verifica_silabas = sum(contador_silabas(poesia) for poesia in nltk.word_tokenize(verso) if poesia not in [',', '.', '?', '¿', '!', '¡'])
            if verifica_silabas == 5:
                haiku.append(verso)
                break
            else:
                print("En este verso hacen falta", 5 - verifica_silabas, "sílabas")
        return haiku

    # Generar un haiku
    haiku_generado = haiku()

    print("\nHaiku generado:")
    for verso in haiku_generado:
        # La primera letra del primer verso va en mayúscula en caso de que sea todo minúscula
        if verso == haiku_generado[0]:
            verso = verso[0].upper() + verso[1:]
        print(verso)

elif idioma == "español":
    #Para estas líneas de códigos utilizamos el GitHub de alejandromunozes (separasilabas): https://github.com/alejandromunozes/separasilabas#
    class char():
        def __init__(self):
            pass

    class char_line():
        def __init__(self, word):
            self.word = word
            self.char_line = [(char, self.char_type(char)) for char in word]
            self.type_line = ''.join(chartype for char, chartype in self.char_line)

        def char_type(self, char):
            if char in set(['a', 'á', 'e', 'é', 'o', 'ó', 'í', 'ú']):
                return 'V'  # strong vowel
            if char in set(['i', 'u', 'ü']):
                return 'v'  # weak vowel
            if char == 'x':
                return 'x'
            if char == 's':
                return 's'
            else:
                return 'c'

        def find(self, finder):
            return self.type_line.find(finder)

        def split(self, pos, where):
            return char_line(self.word[0:pos + where]), char_line(self.word[pos + where:])

        def split_by(self, finder, where):
            split_point = self.find(finder)
            if split_point != -1:
                chl1, chl2 = self.split(split_point, where)
                return chl1, chl2
            return self, False

        def __str__(self):
            return self.word

        def __repr__(self):
            return repr(self.word)

    class silabizer():
        def __init__(self):
            self.grammar = []

        def split(self, chars):
            rules = [
                ('VV', 1), ('cccc', 2), ('xcc', 1), ('ccx', 2), ('csc', 2), ('xc', 1), ('cc', 1), ('vcc', 2), ('Vcc', 2),
                ('sc', 1), ('cs', 1), ('Vc', 1), ('vc', 1), ('Vs', 1), ('vs', 1)
            ]
            for split_rule, where in rules:
                first, second = chars.split_by(split_rule, where)
                if second:
                    if (
                        first.type_line in set(['c', 's', 'x', 'cs'])
                        or second.type_line in set(['c', 's', 'x', 'cs'])
                    ):
                        # print 'skip1', first.word, second.word, split_rule, chars.type_line
                        continue
                    if first.type_line[-1] == 'c' and second.word[0] in set(['l', 'r']):
                        continue
                    if first.word[-1] == 'l' and second.word[-1] == 'l':
                        continue
                    if first.word[-1] == 'r' and second.word[-1] == 'r':
                        continue
                    if first.word[-1] == 'c' and second.word[-1] == 'h':
                        continue
                    return self.split(first) + self.split(second)
            return [chars]

        def __call__(self, word):
            return self.split(char_line(word))

    while True:
        primer_verso = input("Digite el primer verso (5 sílabas): ")
        segundo_verso = input("Digite el segundo verso (7 sílabas): ")
        tercer_verso = input("Digite el tercer verso (5 sílabas): ")

        silabizador = silabizer()

        # Dividir cada verso en palabras y contar las sílabas
        versos = [primer_verso, segundo_verso, tercer_verso]
        num_versos_correctos = 0
        print("\n------ Conteo de sílabas por palabras: ------\n")
        for i, verso in enumerate(versos):
            print("Verso ", i + 1, ": ")
            palabras = verso.split()
            total_silabas = 0
            for palabra in palabras:
                silabas = silabizador(palabra.lower())
                cantidad_silabas = len(silabas)
                total_silabas += cantidad_silabas
                print(f"Palabra: {palabra}//Sílabas: {cantidad_silabas}")
            print(f"Total de sílabas en el verso {i + 1}: {total_silabas}\n")
            if (i == 0 and total_silabas == 5) or (i == 1 and total_silabas == 7) or (i == 2 and total_silabas == 5):
                num_versos_correctos += 1
        if num_versos_correctos == 3:
            print("Los versos digitados cumplen con las reglas de la poesía de Haiku")
            break
        else:
            print("Los versos digitados no cumplen con las reglas de la poesía de Haiku\n")

    # Mostrar los versos juntos
    versos_completos = "\n".join(versos)
    print("Haiku generado:")
    print(versos_completos)

else:
    print("Opción inválida")


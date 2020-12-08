### Otwieranie i zamykanie plików

[Plik ezo](files/ezo_siusta.txt)<br>
[Plik haiku](files/haiku_siusta.txt)

#### Otwarcie pliku
```python
file = open('ezo_siusta.txt')
```

#### Otwarcie i zamknięcie z try-finally
```python
try:
    print(file.read())
finally:
    file.close()
```

#### Otwarcie i zamknięcie z with
Plik jest automatycznie zamykany po wyjściu z with.
```python
with open('ezo_siusta.txt','r')as reader:
    print(reader.read())
```
Plik został otwarty w trybie 'r', czyli tylko do odczytu.<br>
Inne tryby to 'w' - zapisywanie, 'a' - dopisywanie, 'rb' i 'wb' - odczyt i zapis z wykorzystaniem danych bitowych.
#### Sprawdzenie jaki jest typ pliku
```python
type(file)
```

#### Otwarcie pliku jako BufferedReader
```python
with open('ezo_siusta.txt','rb')as buffered_reader:
    print(type(buffered_reader))
```

#### Otwarcie pliku jako BufferedWriter
```python
with open('ezo_siusta.txt','wb')as buffered_writer:
    print(type(buffered_writer))
```

#### Otwarcie pliku jako raw file
```python
with open('ezo_siusta.txt','rb', buffering=0)as raw_file:
    print(type(raw_file))
```
Zwrócony zostaje obiekt klasy FileIO.
#### Odczytanie całego pliku
````python
with open('haiku_siusta.txt','r')as reader:
    print(reader.read())
````

#### Odczytanie pliku z wykorzystaniem readline()
Odczytane zostają pokolei części pliku o długości 5 znaków.
````python
with open('haiku_siusta.txt','r')as reader:
    print(reader.readline(5))
    print(reader.readline(5))
    print(reader.readline(5))
    print(reader.readline(5))
````
#### Odczytanie pliku z wykorzystaniem readlines()
Wiersze pliku zostają zapisane do listy.
```python
with open('haiku_siusta.txt','r')as reader:
    print(reader.readlines())
```

#### Odczytanie wierszy pliku do listy
Inna metoda zapisu do listy niż przez readlines().
```python
with open('haiku_siusta.txt','r')as reader:
    print(list(reader))
```

#### Odczytanie pliku wiersz po wierszu
Iretacja po wierszach pliku.
```python
with open('haiku_siusta.txt','r')as reader:
    line = reader.readline()
    while line != '':
        print(line, end='')
        line = reader.readline()
```

#### Odczytanie pliku wiersz po wierszu z wykorzystaniem readlines()
```python
with open('haiku_siusta.txt','r')as reader:
    for line in reader.readlines():
        print(line, end='')
```

#### Uproszczone przejście przez wszystkie wiersze:
```python
with open('haiku_siusta.txt','r')as reader:
    for line in reader:
        print(line, end='')
```

### Zapisywanie do pliku:
```python
with open('haiku_siusta.txt', 'r') as reader:
    haiku = reader.readlines()

with open('haiku_siusta.txt', 'w') as writer:
    for line in reversed(haiku):
        writer.write(line)
```

### Otwarcie obrazu jako string bajtów
[Wykorzystany obraz](files/travelling_monks_siusta.png)
```python
with open('travelling_monks_siusta.png', 'rb') as byte_reader:
    for i in range (5):
        print(byte_reader.readline(i))
```

### Dos2unix
Systemy DOS-owe używają innego zakończenia wiersza (\r\n) niż unixowe (\n\), co utrudnia poprawne odczytanie pliku.
```python
def str2unix(input_str: str) -> str:
    r_str = input_str.replace('\r\n', '\n')
    return r_str

def dos2unix(source_file: str, dest_file: str):
    with open(source_file, 'r') as reader:
        dos_content = reader.read()
    unix_content = str2unix(dos_content)
    with open(dest_file, 'w') as writer:
        writer.write(unix_content)
    
dos2unix('waka_siusta.txt','unix_waka_siusta.txt')
```
[Plik waka](files/waka_siusta.txt)<br>
[Plik unix_waka](files/unix_waka_siusta.txt)

### Dopisanie do pliku
'a' to tryb operacji na pliku, który pozwala na dopisanie czegoś do końca pliku bez nadpisania zawartości.
```python
with open('waka_siusta.txt', 'a') as append:
    append.write('\n\n-Outomo no Yakamochi')
```

### Praca z dwoma plikami na raz
Można pracować na wielu plikach na raz.
```python
with open('waka_siusta.txt', 'r') as reader, open('reversed_waka_siusta.txt', 'w') as writer:
    waka=reader.readlines()
    writer.writelines(reversed(waka))
```
[Plik reversed_waka](files/reversed_waka_siusta.txt)

### Własna klasa managera plików
```python
class my_file_reader():
    def __init__(self, file_path):
        self.__path = file_path
        self.__file_object = None

    def __enter__(self):
        self.__file_object = open(self.__path)
        return self

    def __exit__(self, type, val, tb):
        self.__file_object.close()
```

### Parsowanie plików PNG
```python
class PngReader():
    #sprawdzenie nagłówka
    _expected_magic = b'\x89PNG\r\n\x1a\n'

    def __init__(self, file_path):
        #sprawdzenie rozszerzenia
        if not file_path.endswith('.png'):
            raise NameError("File must be a '.png' extension")
        self.__path = file_path
        self.__file_object = None

    def __enter__(self):
        self.__file_object = open(self.__path, 'rb')

        magic = self.__file_object.read(8)
        if magic != self._expected_magic:
            raise TypeError("The File is not a properly formatted .png file!")

        return self

    def __exit__(self, type, val, tb):
        self.__file_object.close()

    def __iter__(self):
        # iterator
        return self

    def __next__(self):
        initial_data = self.__file_object.read(4)

        # odczyt danych w "kawałkach"
        if self.__file_object is None or initial_data == b'':
            raise StopIteration
        else:
            # tuple każdego kawałka
            chunk_len = int.from_bytes(initial_data, byteorder='big')
            chunk_type = self.__file_object.read(4)
            chunk_data = self.__file_object.read(chunk_len)
            chunk_crc = self.__file_object.read(4)
            return chunk_len, chunk_type, chunk_data, chunk_crc
```
## Wywołanie parsera
[Wykorzystany obraz](files/travelling_monks_siusta.png)
```python
with PngReader('travelling_monks_siusta.png') as reader:
    for l,t,d,c in reader:
        print(f"{l:05}, {t}, {c}")
```


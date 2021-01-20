## JSON
#### Serializacja
Stworzony i otwarty zostaje plik [data_file](pliki/data_file.json), w którym zapisane zostają dane "data".
```python
with open("data_file.json", "w") as write_file:
    json.dump(data, write_file)

json_string = json.dumps(data)
```
#### Deserializacja
Na przykładzie kart na ręce w Blackjacku: ręka ma typ tupli, ale po zdekodowaniu jest listą.
```python
blackjack_hand = (8, "Q")
encoded_hand = json.dumps(blackjack_hand)
decoded_hand = json.loads(encoded_hand)
```
#### Operacje na danych
Wczytanie danych z pliku do zmiennej z wykorzystaniem json.load():
```python
data = json.load(read_file)
```
Wczytanie danych w pamięci do zmiennej z wykorzystaniem json.loads():
```python
data = json.loads(json_string)
```
Wczytanie danych z linku z wykorzystaniem biblioteki requests i json.loads():
```python
response = requests.get("https://jsonplaceholder.typicode.com/users")
users = json.loads(response.text)
```
Wykorzystanie wczytanych danych. Numery id oraz szerokość geograficzna domu użytkowników wczytanych poprzednim poleceniem zostają skopiowane do listy "lat", która następnie jest sortowana wg odległości od równika.
```python
for user in users:
    lat.append([user['id'],float(user['address']['geo']['lat'])])
closest_to_equator = sorted(lat, key=lambda x: math.fabs(x[1]))
```
Zapisywanie użytkownika znajdującego się najbliżej równika do pliku [Najbliższy równika](pliki/Najbliższy_równika.json):
```python
with open("Najbliższy_równika.json", "w") as data_file:
    json.dump(users[closest_to_equator[0][0]-1], data_file, indent=2)
```
#### Enkodowanie
Funkcja do enkodowania danych do formatu json:
```python
def encode_complex(z):
    if isinstance(z, complex):
        return (z.real, z.imag)
    else:
        type_name = z.__class__.__name__
        raise TypeError(f"Object of type '{type_name}' is not JSON serializable")
```
Silniejsza funkcja do enkodowania:
```python
class ComplexEncoder(json.JSONEncoder):
    def default(self, z):
        if isinstance(z, complex):
            return (z.real, z.imag)
        else:
            return super().default(z)
```
Funkcja do dekodowania danych w formacie json:
```python
def decode_complex(dct):
    if "__complex__" in dct:
        return complex(dct["real"], dct["imag"])
    return dct
```
## CSV
Odczytanie pliku [ramen-ratings.csv](pliki/ramen-ratings.csv):
```python
with open('ramen-ratings.csv') as csv_file:
csv_reader = csv.reader(csv_file, delimiter=',')
```
Odczytanie danych wg kolumn:
```python
for row in csv_reader:
    print(f'\t{row[1]} from {row[4]} specializes in {row[2]}, {row[3]} ramen style and is rated {row[5]}/5.')
```
Odczytanie danych wg słownika:
```python
for row in csv_reader:
    print(f'\t{row["Brand"]} from {row["Country"]} specializes in {row["Variety"]}, {row["Style"]} ramen style and is rated {row["Stars"]}/5.')
```
Zapisywanie do pliku [ramen-ratings2.csv](pliki/ramen-ratings2.csv):
```python
with open('ramen-ratings2.csv', mode='w') as ramen_file:
    ramen_writer = csv.writer(ramen_file, delimiter=',', quotechar='"', quoting=csv.QUOTE_MINIMAL)
    ramen_writer.writerow(['Vifon', 'Spicy beef and kimchi', 'Cup', 'USA', '4.6'])
```
Zapisywanie do pliku [ramen-ratings3.csv](pliki/ramen-ratings3.csv) z wykorzystaniem słownika:
```python
with open('ramen-ratings3.csv', mode='w') as ramen_file:
    fieldnames = ['Brand', 'Variety', 'Style', 'Country', 'Stars']
    ramen_writer = csv.DictWriter(ramen_file, fieldnames=fieldnames)
    ramen_writer.writeheader()
    ramen_writer.writerow({'Brand':'Vifon', 'Variety':'Spicy beef and kimchi', 'Style':'Cup', 'Stars':'4.6'})
    ramen_writer.writerow({'Brand':'Fujiwara', 'Variety':'Seafood with hard noodles and garlic', 'Style':'Bowl', 'Stars':'5'})
```
Odczytanie pliku do DataFrame z wykorzystaniem biblioteki pandas:
```python
df = pandas.read_csv('ramen-ratings.csv')
```


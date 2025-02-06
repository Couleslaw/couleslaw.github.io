## Serializace a deserializace

Serializace je koncept, ze objekt je nejaka reprezentace nejakych dat, ktery se da prevest do nejakeho jineho formatu.

- textovy format - JSON, XML - standardne reprezentace pomoci stromu
- binarni format - ProtocolBuffers

Existuji i formaty, co podporuji slozitejsi grafy, ruzne frameworky s tim umi pracova ruzne, ale vetsinou podporuji DAGy.

- `System.Text.Json.JsonSerializer` ma statickou metodu
  - `string Serialize<T>(T root)` - prevede objekt na JSON, kde `root` je koren toho DAGu
  - umi to praci s libovolnymi grafy, ale defaultne to neumi cykly
  - pouziva prave reflection Jake membery se serializuji?
    - public properties
    - FIELDY TO NESERIALIZUJE
  - da se to docela hezky konfigurovat pomoci `JsonSerializerOptions`

```c#
class Person {
    public string Name { get; set; }
    public int Age { get; set; }
    public List<Car> Cars { get; set; }
}

class Car {
    public string Name { get; set; }
    public List<string> Models { get; set; }
}

Person person = new Person {
    Name = "John",
    Age = 30,
    Cars = new List<Car> {
        new Car { Name = "Ford", Models = new List<string> { "Fiesta", "Focus", "Mustang" } },
        new Car { Name = "BMW", Models = new List<string> { "320", "X3", "X5" } },
        new Car { Name = "Fiat", Models = new List<string> { "500", "Panda" } }
    }
};

string json = JsonSerializer.Serialize(person);
```

```json
{
  "name": "John",
  "age": 30,
  "cars": [
    { "name": "Ford", "models": ["Fiesta", "Focus", "Mustang"] },
    { "name": "BMW", "models": ["320", "X3", "X5"] },
    { "name": "Fiat", "models": ["500", "Panda"] }
  ]
}
```

Deserizaliace je opacny proces.

```c#
string json;  // nacteme nejak ze souboru nebo z webu
Person person = JsonSerializer.Deserialize<Person>(json);
```

Bezne pouziti je, ze chci perzistentne ulozit aktualni stav programu a pak nekdy pozdeji ho obnovit. ALE NEBUDOU TO STEJNE INSTANCE. Jen neco izomorfniho.

## Serializace private fields

```c#
static void AddPrivateFieldsModifier(JsonTypeInfo jsonTypeInfo) {
    if (jsonTypeInfo.Kind != JsonTypeInfoKind.Object)
        return;

    // najde a prida private fields
    var fields = jsonTypeInfo.Type.GetFields(BindingFlags.Instance | BindingFlags.NonPublic);
    foreach (FieldInfo field in fields) {
        JsonPropertyInfo jsonPropertyInfo = jsonTypeInfo.CreateJsonPropertyInfo(field.FieldType, field.Name);
        jsonPropertyInfo.Get = field.GetValue;
        jsonPropertyInfo.Set = field.SetValue;

        jsonTypeInfo.Properties.Add(jsonPropertyInfo);
    }

}
var optionsWithPrivateFields = new JsonSerializerOptions {
    WriteIndented = true,
    TypeInfoResolver = new DefaultJsonTypeInfoResolver {
        Modifiers = { AddPrivateFieldsModifier }
    }
};

Person p = new Person { Name = "John", Age = 30 };

// kdyby mel nejake private fields, tak je to take serializuje
string json = JsonSerializer.Serialize(p, optionsWithPrivateFields));
```

Kdyz ten typ ma nejake auto-implemented vlastnosti, tak by to serializovalo i jejich backing fieldy.

### Deep cloning

Na kazdem objektu je metoda `MemberwiseClone`, ktera vraci melkou kopii. Pomoci reflection a spravnou implementaci serializace private fields je mozne udelat deep clone.

### Corutiny

Corutina je najeka obecny koncept, algoritmus ktery ma nejaky vnitrni stav, bezi potencialne doneckonecna, napriklad enumerator. Muzeme si tento stav ulozit pomoci serializace a pak ho obnovit.

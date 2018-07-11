# multisofting

Permite ordenar un array de objetos por una o mas propiedades.
multisort() puede recibir ilimitados parametros, siendo del 2 en adelante, los nombres de las propiedades a ordenar.


### Helper
```javascript
function multisort() {
  var campos = [],
    n_campos = arguments.length,
    campo,
    name,
    comp;

  var default_comp = function(a, b) {
    if (a === b) return 0;
    return a < b ? -1 : 1;
  };

  var getComp = function(primer, reverse) {
    var dfc = default_comp;
    var comp = default_comp; // closer en scope

    if (primer) {
      comp = function(a, b) {
        return dfc(primer(a), primer(b));
      };
    }

    if (reverse) {
      return function(a, b) {
        return -1 * comp(a, b);
      };
    }

    return comp;
  };

  // preprocess sorting options
  for (var i = 0; i < n_campos; i++) {
    campo = arguments[i];

    if (typeof campo === "string") {
      name = campo;
      comp = default_comp;
    } else {
      name = campo.name;
      comp = getComp(campo.primer, campo.reverse);
    }

    campos.push({
      name: name,
      comp: comp
    });
  }

  // final comparison function
  return function(A, B) {
    var a, b, name, result;

    for (var i = 0; i < n_campos; i++) {
      result = 0;
      campo = campos[i];
      name = campo.name;

      result = campo.comp(A[name], B[name]);
      if (result !== 0) break;
    }

    return result;
  };
}
```

### Ejemplo

```javascript
const books = [
	{Title: 'My Javascript code Returns', Year: 2010},
	{Title: 'Fast and Furious', Year: 2001}
	{Title: 'My javascript code for beginners', Year: 2010},
	{Title: 'Learn Javascript Vol.3', Year: 2008},
	{Title: 'Learn Javascript Vol.1', Year: 2008},
	{Title: 'Declarative programing in Javascript', Year: 2016},
];

let sortBy = {
	name: "Year",
	reverse: true
};
const adicionalSorting = 'Title';

books.sort(multisort(sortBy, adicionalSorting));
```

#### Output
```
[
	{Title: 'Declarative programing in Javascript', Year: 2016},
	{Title: 'My javascript code for beginners', Year: 2010},
	{Title: 'My Javascript code Returns', Year: 2010},
	{Title: 'Learn Javascript Vol.1', Year: 2008},
	{Title: 'Learn Javascript Vol.3', Year: 2008},
	{Title: 'Fast and Furious', Year: 2001}
]
```


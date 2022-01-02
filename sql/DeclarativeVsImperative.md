# Declarative Language vs Imperative Language

## Declarative Language

In a declarative query language, like SQL or relational algebra, you just specify the pattern of the data you want—what conditions the results must meet, and how you want the data to be transformed (e.g., sorted, grouped, and aggregated)—but not how to achieve that goal. It is up to the database system’s query optimizer to decide which indexes and which join methods to use, and in which order to execute various parts
of the query.

```SQL
SELECT * FROM animals WHERE family = 'Sharks';
```

## Imperative Language

An imperative language tells the computer to perform certain operations in a certain order. You can imagine stepping through the code line by line, evaluating conditions, updating variables, and deciding whether to go around the loop one more time.

```python
function getSharks() {
    var sharks = [];
    for (var i = 0; i < animals.length; i++) {
      if (animals[i].family === "Sharks") {
        sharks.push(animals[i]);
      }
    }
  return sharks;
}
```

Ref : Designing Data-Intensive Applications by Martin Kleppman - Chapter 2 - Data Models and Query Languages

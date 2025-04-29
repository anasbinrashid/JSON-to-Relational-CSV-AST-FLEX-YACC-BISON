# JSON to Relational CSV Converter

This tool converts JSON files into relational CSV tables. It processes any valid JSON input and creates appropriate CSV files based on the structure and nesting of objects and arrays—mapping hierarchical data into normalized tables similar to relational databases.

---

## Features

- Parses any valid JSON file up to **30 MB**
- Builds and utilizes an **Abstract Syntax Tree (AST)** to model JSON structure
- Converts nested **JSON objects and arrays** into **relational tables** with `id` and `<parent>_id` keys
- Streams CSV output to avoid large memory buffers
- Handles all standard JSON data types (`string`, `number`, `object`, `array`, `true`, `false`, `null`)
- Prints a readable, indented AST structure (with `--print-ast`)
- Reports **syntax errors** with accurate line and column numbers

---

## ⚙️ Building the Tool

### 🧰 Requirements

Install the required tools using:

```bash
sudo apt update
sudo apt install flex bison build-essential
```

### 🏗️ Compile

Run `make` to build the tool:

```bash
make
```

This will generate an executable named:

```bash
./json2relcsv
```

---

## 🚀 Usage

```bash
./json2relcsv < input.json [--print-ast] [--out-dir DIR]
```

---

## 📌 Conversion Rules

| JSON Structure       | Conversion                                 |
|----------------------|---------------------------------------------|
| Object               | Becomes a row in a table                    |
| Array of objects     | Each item is a row in a child table         |
| Array of scalars     | Converted to a junction table               |
| Scalar               | Becomes a column value                     |
| Nested object        | Gets a foreign key reference to a subtable  |
| All rows             | Include an `id` field and foreign keys      |

---

## 🧪 Examples

### Input:

```json
{
  "movie": "Inception",
  "genres": ["Action", "Sci-Fi", "Thriller"]
}
```

### Output:

**movies.csv**

```
id,movie
1,Inception
```

**genres.csv**

```
movie_id,index,value
1,0,Action
1,1,Sci-Fi
1,2,Thriller
```

---

## 🐞 Error Reporting

- Reports the **first** syntax or lexical error with exact `line:column`
- Cleanly exits with a non-zero code
- Example:

```bash
Error: Unexpected token at line 4, column 12
```

---

## 🧼 Code Quality

- Clean, modular C code with Valgrind-tested memory safety
- Uses efficient streaming logic for large JSON files
- Flexible design for potential extensions 

---


## 📚 License

This tool is developed for educational purposes. Feel free to reuse or modify with credit.

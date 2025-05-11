# JSON to Relational CSV Converter

A utility that converts JSON documents into a set of relational CSV files, automatically detecting schema and maintaining relationships between entities.

## Overview

This tool parses JSON input and transforms it into a set of normalized CSV files suitable for importing into relational databases. It automatically detects the schema from the JSON structure, creating separate tables for objects, arrays, and handling nested relationships.

## Features

- Automatic schema detection from JSON structure
- Handles nested objects and arrays
- Maintains relationships with foreign keys
- Creates normalized CSV output
- Preserves array ordering with sequence numbers
- Built-in AST visualization for debugging

## Requirements

- GCC or compatible C compiler
- Flex (for lexical analysis)
- Bison (for parsing)
- Make

## Building

To build the project, run:

```bash
make clean
make
```

This will compile the source code and generate the `json2relcsv` executable.

## Usage

```
./json2relcsv [--print-ast] [--out-dir DIR]
```

Options:
- `--print-ast`: Print the Abstract Syntax Tree for debugging
- `--out-dir DIR`: Specify output directory for CSV files (default: current directory)

The program reads JSON from standard input and writes CSV files to the output directory.

## Example

```bash
echo '{"store":{"name":"Tech Shop","location":"Downtown","employees":[{"id":"E1","name":"John","position":"Manager"},{"id":"E2","name":"Sara","position":"Sales"}],"inventory":{"categories":["Electronics","Accessories"]}}}' | ./json2relcsv
```

This will generate the following CSV files:
- `root.csv` - The root object
- `store.csv` - The store object
- `employees.csv` - The employees array elements
- `inventory.csv` - The inventory object
- `categories.csv` - The categories array elements

## Design Notes

### Architecture

The program is structured around several key components:

1. **Parser (Flex/Bison)** - Parses JSON input into an Abstract Syntax Tree (AST)
2. **AST Module** - Defines the structure for representing JSON in memory
3. **Schema Detection** - Analyzes the AST to determine the relational schema
4. **CSV Generation** - Extracts data from the AST and writes CSV files

### Data Flow

1. JSON is parsed from stdin into an in-memory AST
2. The schema is detected by traversing the AST
3. Tables and columns are created based on the schema
4. Data is extracted from the AST into table rows
5. CSV files are generated from the table data

### Schema Detection Logic

- Each unique object structure becomes a table
- Object properties become columns
- Nested objects generate separate tables with foreign keys
- Arrays of objects create child tables with parent references
- Arrays of scalar values create junction tables

### Relation Mapping

The tool creates the following types of relations:

- **One-to-One**: Nested objects within objects
- **One-to-Many**: Arrays of objects
- **Many-to-Many**: Arrays of scalar values (via junction tables)

### Column Types

- **ID**: Primary key columns
- **Foreign Key**: References to parent objects
- **Index**: Array position for preserving order
- **String/Number/Boolean/Null**: Data type columns

## File Structure

- `ast.c/h` - AST representation and operations
- `csv_gen.c/h` - CSV file generation
- `schema.c/h` - Schema detection and management
- `main.c` - Main program logic
- `scanner.l` - Flex lexer specification (not included)
- `parser.y` - Bison parser specification (not included)


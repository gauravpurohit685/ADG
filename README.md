# Machine Learning Based Time Complexity Analyzer — Features Extraction Module

This project is the compiler-based feature extraction component of a machine-learning-based time-complexity analyzer.

Its primary responsibility is parsing Python and Java source code, traversing the Abstract Syntax Tree (AST), extracting structural code features (such as loops, nested loop depths, conditionals, recursion, function call counts, and AST depth), and returning them in JSON format for consumption by downstream machine-learning components.

## Architecture

```text
Source Code
     ↓
Lexical Analysis & Parsing (AST Parsing via Python `ast` / Java `javalang`)
     ↓
AST / Parse Tree
     ↓
Feature Extraction (AST Visitor & Traversal)
     ↓
Extracted Feature Vector (JSON)
```

- **Lexical Analysis & Parsing**: Source code is tokenized and parsed into an Abstract Syntax Tree (AST) using Python's built-in `ast` module or the `javalang` library for Java.
- **AST / Parse Tree**: A hierarchical representation of the syntactic structure of the code.
- **Feature Extraction**: AST visitors traverse the tree and collect structural metadata.
- **JSON Output**: Extracted feature metrics are serialized as a JSON response.

## Supported Languages

- **Python**: Parsed using Python's built-in `ast` module.
- **Java**: Parsed using the `javalang` library.

## Extracted Features

The API extracts and returns the following static feature metrics:

- `loop_count`: Total number of loops (`for` + `while`).
- `for_loop_count`: Number of `for` loops.
- `while_loop_count`: Number of `while` loops.
- `max_loop_depth`: Maximum depth of nested loops (higher depths suggest higher polynomial complexities).
- `nested_loop_count`: Count of loops nested inside other loops.
- `if_count`: Total number of `if` statements.
- `function_count`: Total number of functions defined.
- `method_count`: Total number of methods defined (primarily Java).
- `function_call_count`: Total number of function/method invocations.
- `recursive`: Boolean flag indicating if self-recursive function calls exist.
- `recursive_call_count`: Number of recursive calls detected.
- `ast_depth`: Maximum depth of the abstract syntax tree.
- `statement_count`: Total number of statements parsed.
- `condition_count`: Total number of conditional checks.
- `linear_loop_count`: Loops iterating linearly.
- `logarithmic_loop_count`: Loops with multiplicative/divisional updates typical in logarithmic complexities.

## Installation

```bash
cd Features

# Create a virtual environment
python -m venv venv

# Activate virtual environment (Windows)
venv\Scripts\activate

# Activate virtual environment (Linux/macOS)
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

## Running the Server

Start the FastAPI backend server:

```bash
uvicorn app.main:app --reload
```

The server will run at `http://127.0.0.1:8000`.

## API Endpoints

### 1. Health Check
`GET /api/health`

**Response:**
```json
{
  "status": "ok"
}
```

### 2. Analyze Python Code
`POST /api/analyze/python`

**Request Body:**
```json
{
  "code": "for i in range(n):\n    print(i)"
}
```

**Response Body:**
```json
{
  "language": "python",
  "features": {
    "loop_count": 1,
    "for_loop_count": 1,
    "while_loop_count": 0,
    "max_loop_depth": 1,
    "nested_loop_count": 0,
    "if_count": 0,
    "function_count": 0,
    "method_count": 0,
    "function_call_count": 1,
    "recursive": false,
    "recursive_call_count": 0,
    "ast_depth": 12,
    "statement_count": 2,
    "condition_count": 0,
    "linear_loop_count": 1,
    "logarithmic_loop_count": 0
  }
}
```

### 3. Analyze Java Code
`POST /api/analyze/java`

**Request Body:**
```json
{
  "code": "for(int i=0; i<n; i++) { System.out.println(i); }"
}
```

**Response Body:**
```json
{
  "language": "java",
  "features": {
    "loop_count": 1,
    "for_loop_count": 1,
    "while_loop_count": 0,
    "max_loop_depth": 1,
    "nested_loop_count": 0,
    "if_count": 0,
    "function_count": 1,
    "method_count": 1,
    "function_call_count": 1,
    "recursive": false,
    "recursive_call_count": 0,
    "ast_depth": 27,
    "statement_count": 3,
    "condition_count": 0,
    "linear_loop_count": 1,
    "logarithmic_loop_count": 0
  }
}
```

## cURL Examples

**Python Analysis:**
```bash
curl -X POST "http://127.0.0.1:8000/api/analyze/python" \
     -H "Content-Type: application/json" \
     -d '{"code":"def factorial(n):\n    if n == 0:\n        return 1\n    return n * factorial(n-1)"}'
```

**Java Analysis:**
```bash
curl -X POST "http://127.0.0.1:8000/api/analyze/java" \
     -H "Content-Type: application/json" \
     -d '{"code":"void test() { int i = 1; while(i < 10) { i *= 2; } }"}'
```

## Testing

Run unit tests using `pytest`:

```bash
pytest
```

## Project Limitations

- Static analysis cannot execute arbitrary runtime semantics or infer dynamic runtime values (e.g. Python `eval()` or Java reflection).
- Heuristics serve as feature vector inputs to machine learning models rather than formal complexity proofs.

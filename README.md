# LogLens — Hybrid Log Classification Framework

LogLens is a hybrid log classification system that combines rule-based pattern matching, machine learning, and Large Language Models (LLMs) to classify log messages efficiently. It uses different classification strategies depending on whether a log matches a known pattern and whether sufficient labeled training data is available.

## Classification Workflow

1. **Regex-Based Classification**
   - Handles known, predictable log patterns using predefined regular expressions.
   - Provides a fast and deterministic classification path for patterns covered by existing rules.

2. **Sentence Transformers + Logistic Regression**
   - Used when sufficient labeled training data is available for classification.
   - Sentence Transformers generate semantic embeddings of log messages.
   - A Logistic Regression classifier predicts the corresponding log category using the learned patterns in the labeled dataset.

3. **LLM-Based Classification**
   - Used as a fallback when sufficient labeled training data is unavailable.
   - Leverages the contextual understanding of a Large Language Model to classify log messages that cannot be reliably handled by the existing rules or trained classifier.

![LogLens Architecture](resources/arch.png)

## Project Structure

```text
LogLens/
├── training/       # Model training and regex classification logic
├── models/         # Saved classification models and related artifacts
├── resources/      # Architecture diagrams, datasets, and test resources
├── server.py       # FastAPI application and API endpoints
├── requirements.txt
├── .env.example    # Example environment configuration
└── README.md
```

*Note: The structure above illustrates the main project components. Actual files and directories may vary.*

## Prerequisites

- Python 3.14
- pip
- A Groq API key for LLM-based classification
- The project's required model artifacts and datasets

## Setup Instructions

### 1. Clone the Repository

```bash
git clone <repository-url>
cd LogLens
```

Replace `<repository-url>` with your repository's Git URL.

### 2. Create and Activate a Virtual Environment

**macOS / Linux**

```bash
python3.14 -m venv .venv
source .venv/bin/activate
```

**Windows**

```powershell
py -3.14 -m venv .venv
.venv\Scripts\Activate.ps1
```

### 3. Install Dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Configure Environment Variables

Create a `.env` file in the project root:

```dotenv
GROQ_API_KEY=your_groq_api_key
GROQ_MODEL=openai/gpt-oss-120b
```

Replace `your_groq_api_key` with your actual Groq API key. Keep `.env` out of version control and never commit API keys.

### 5. Start the FastAPI Server

```bash
python -m uvicorn server:app --reload
```

The server will start locally at `http://127.0.0.1:8000`.

## API Documentation

Once the server is running, use the following endpoints:

- **Swagger UI:** [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)
- **ReDoc:** [http://127.0.0.1:8000/redoc](http://127.0.0.1:8000/redoc)

The root URL (`/`) is available only if a root route has been defined in `server.py`.

## Usage

LogLens provides a FastAPI endpoint for classifying log data. The classification endpoint accepts a CSV file containing log entries.

The CSV input must contain the following columns:

| Column | Description |
|---|---|
| `source` | Identifies the source of the log, such as an application or service. |
| `log_message` | Contains the log message to classify. |

The output is a CSV file containing the original log data and an additional column:

| Column | Description |
|---|---|
| `target_label` | The predicted category assigned to each log entry. |

To submit a file, open the [Swagger UI](http://127.0.0.1:8000/docs), locate the classification endpoint, and follow its request schema to upload the CSV.

## Technology Stack

- **Backend:** FastAPI, Uvicorn
- **Pattern Matching:** Regular Expressions (Regex)
- **Text Embeddings:** Sentence Transformers
- **Machine Learning:** Scikit-learn, Logistic Regression
- **LLM Integration:** Groq API
- **Data Processing:** Pandas, NumPy

## Security

- Store API keys and credentials in environment variables.
- Keep `.env` out of Git using `.gitignore`.
- Do not expose API keys in source code, logs, or public repositories.

## Future Improvements

- Add confidence-based routing between the trained classifier and the LLM.
- Evaluate classification precision, recall, and F1-score for each category.
- Add structured logging, exception handling, and API input validation.
- Monitor inference latency and LLM API usage.






